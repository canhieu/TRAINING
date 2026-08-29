---
hidden: true
---

# Baitap1

### 1.1 Cài đặt và sử dụng BurpSuite

#### 1.1.1 Cài đặt và làm quen công cụ BurpSuite

Phiên bản cài đặt: BurpSuite bản mới nhất =)) (nen dung ban pro de tan dung toida -)) )

Các bước cài đặt:

* lên trang của portswigger tải bản mới nhất

<figure><img src=".gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (675).png" alt=""><figcaption></figcaption></figure>

#### 1.1.2 Cài đặt Proxy cho trình duyệt

Trình duyệt sử dụng: Firefox / Chrome

BurpSuite mặc định lắng nghe trên: 127.0.0.1:8080

Các bước cấu hình proxy trên Firefox:

1. Mở Firefox, vào Settings > Network Settings > Settings
2. Chọn "Manual proxy configuration"
3. Nhập HTTP Proxy: 127.0.0.1, Port: 8080
4. Tích chọn "Also use this proxy for HTTPS"
5. Nhấn OK để lưu

Các bước cấu hình proxy trên Chrome:

1. Chrome sử dụng proxy của hệ thống, nên cần cấu hình trong Windows Settings
2. Hoặc sử dụng extension như FoxyProxy để dễ dàng bật/tắt proxy
3. Cấu hình proxy address: 127.0.0.1, port: 8080

\[CẦN ẢNH: Screenshot cấu hình proxy trên trình duyệt]

***

#### 1.1.3 Cài đặt Certificate cho các trình duyệt khác nhau

Lý do cần cài certificate: BurpSuite hoạt động như một Man-in-the-Middle proxy. Để intercept HTTPS traffic, BurpSuite tạo certificate giả cho mỗi website. Browser cần trust CA certificate của Burp để không hiện cảnh báo bảo mật.

Tải CA Certificate từ Burp:

1. Đảm bảo proxy đang chạy và browser đã cấu hình proxy
2. Truy cập http://burp hoặc http://127.0.0.1:8080
3. Click "CA Certificate" để tải file cacert.der

Chrome / Edge (sử dụng Windows Certificate Store):

1. Đổi tên cacert.der thành cacert.crt
2. Double-click file, chọn "Install Certificate"
3. Chọn "Local Machine" > "Place all certificates in the following store"
4. Browse và chọn "Trusted Root Certification Authorities"
5. Hoàn tất và restart browser

Firefox (có Certificate Store riêng):

1. Vào Settings > Privacy & Security > Certificates > View Certificates
2. Tab "Authorities" > Import
3. Chọn file cacert.der
4. Tích "Trust this CA to identify websites"
5. OK và restart Firefox

\[CẦN ẢNH: Screenshot quá trình import certificate vào browser]

***

#### 1.1.4 Các chức năng cơ bản của BurpSuite

| Chức năng | Mô tả                                                                | Công dụng trong Pentest                                                               |
| --------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Proxy     | Chặn và chỉnh sửa HTTP/HTTPS request/response giữa browser và server | Xem, sửa đổi request trước khi gửi; phân tích response; bypass client-side validation |
| Target    | Quản lý thông tin về target, bao gồm sitemap và scope                | Mapping cấu trúc website, xác định attack surface, quản lý phạm vi test               |
| Intruder  | Tự động hóa các cuộc tấn công với payload tùy chỉnh                  | Brute force, fuzzing, enumeration, parameter tampering                                |
| Repeater  | Gửi lại request thủ công và xem response                             | Test thủ công các vulnerability, điều chỉnh payload, debug exploit                    |
| Decoder   | Encode/Decode dữ liệu với nhiều định dạng                            | Decode data bị obfuscate, encode payload, chuyển đổi giữa các format                  |
| Comparer  | So sánh 2 request/response                                           | Tìm điểm khác biệt giữa các response, phát hiện subtle changes                        |
| Sequencer | Phân tích tính ngẫu nhiên của token                                  | Đánh giá chất lượng session token, CSRF token                                         |
| Logger    | Ghi log tất cả HTTP traffic                                          | Review lại traffic, tìm kiếm request cũ, audit trail                                  |

***

### 1.2 Cấu trúc HTTP Request/Response

#### 1.2.1 Thành phần của một HTTP Request

```
GET /path/to/resource?param=value HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml
Cookie: session=abc123
Content-Type: application/x-www-form-urlencoded
Content-Length: 27
                                            <-- Blank Line (CRLF)
username=admin&password=123                 <-- Body (nếu có)
```

Phân tích chi tiết:

| Thành phần   | Mô tả                                                   | Ví dụ                                      |
| ------------ | ------------------------------------------------------- | ------------------------------------------ |
| Request Line | Dòng đầu tiên chứa Method, URI, và HTTP Version         | GET /index.html HTTP/1.1                   |
| Headers      | Các cặp key-value cung cấp thông tin bổ sung về request | Host: example.com, User-Agent: Mozilla/5.0 |
| Blank Line   | Dòng trống (CRLF) phân tách headers và body             | (empty line)                               |
| Body         | Dữ liệu gửi kèm request (thường có trong POST/PUT)      | username=admin\&password=123               |

***

#### 1.2.2 Thành phần của một HTTP Response

```
HTTP/1.1 200 OK
Date: Fri, 03 Jan 2026 13:00:00 GMT
Server: Apache/2.4.41
Content-Type: text/html; charset=UTF-8
Content-Length: 1234
Set-Cookie: session=xyz789; HttpOnly; Secure
                                            <-- Blank Line (CRLF)
<!DOCTYPE html>
<html>
<head><title>Example</title></head>
<body>...</body>
</html>
```

Phân tích chi tiết:

| Thành phần  | Mô tả                                                          | Ví dụ                                    |
| ----------- | -------------------------------------------------------------- | ---------------------------------------- |
| Status Line | Dòng đầu tiên chứa HTTP Version, Status Code, và Reason Phrase | HTTP/1.1 200 OK                          |
| Headers     | Các cặp key-value cung cấp thông tin về response               | Content-Type: text/html, Set-Cookie: ... |
| Blank Line  | Dòng trống (CRLF) phân tách headers và body                    | (empty line)                             |
| Body        | Nội dung response (HTML, JSON, binary, etc.)                   | ...                                      |

***

#### 1.2.3 Chú ý các Blank Line trong cấu trúc

Blank Line là gì?

Blank Line là một dòng trống chỉ chứa CRLF (Carriage Return + Line Feed: \r\n). Trong HTTP, blank line có vai trò quan trọng để phân tách phần headers và body.

Vị trí của Blank Line trong HTTP Request:

Nằm ngay sau header cuối cùng, trước phần body (nếu có). Nếu request không có body (GET), blank line vẫn phải có ở cuối.

Vị trí của Blank Line trong HTTP Response:

Nằm giữa header cuối cùng và phần body của response.

Tại sao Blank Line quan trọng?

* HTTP parser dựa vào blank line để biết khi nào headers kết thúc và body bắt đầu
* Nếu thiếu blank line, server/client không thể parse đúng message
* Có thể bị khai thác trong các cuộc tấn công như HTTP Request Smuggling

***

#### 1.2.4 Tìm hiểu và Demo các HTTP Method

| Method  | Mô tả                                                | Ví dụ thực tế                            |
| ------- | ---------------------------------------------------- | ---------------------------------------- |
| GET     | Lấy dữ liệu từ server, không có body, idempotent     | Truy cập trang web, tải file             |
| POST    | Gửi dữ liệu lên server để xử lý, có body             | Đăng nhập, submit form, upload file      |
| PUT     | Tạo mới hoặc thay thế hoàn toàn resource, idempotent | Update toàn bộ thông tin user            |
| DELETE  | Xóa resource trên server                             | Xóa bài viết, xóa tài khoản              |
| HEAD    | Giống GET nhưng chỉ trả về headers, không có body    | Check file tồn tại, lấy metadata         |
| OPTIONS | Lấy danh sách methods được hỗ trợ                    | CORS preflight request                   |
| PATCH   | Cập nhật một phần resource                           | Đổi password mà không đổi username       |
| TRACE   | Echo lại request để debug                            | Kiểm tra proxy chain (thường bị disable) |

\[CẦN ẢNH: Screenshot các HTTP Method được capture trong BurpSuite - ít nhất GET và POST]

***

#### 1.2.5 Các HTTP Header

Các Header thường gặp:

| Header         | Loại (Request/Response) | Mô tả                                                     |
| -------------- | ----------------------- | --------------------------------------------------------- |
| Host           | Request                 | Xác định domain name của server (bắt buộc trong HTTP/1.1) |
| User-Agent     | Request                 | Thông tin về browser/client gửi request                   |
| Accept         | Request                 | Loại content mà client có thể xử lý                       |
| Content-Type   | Cả hai                  | Định dạng của body (MIME type)                            |
| Content-Length | Cả hai                  | Kích thước body tính bằng bytes                           |
| Authorization  | Request                 | Thông tin xác thực (Basic, Bearer token, etc.)            |
| Cookie         | Request                 | Gửi cookies đã lưu từ server                              |
| Set-Cookie     | Response                | Server yêu cầu browser lưu cookie                         |
| Cache-Control  | Cả hai                  | Chỉ thị về caching cho browser và proxy                   |
| Connection     | Cả hai                  | Kiểm soát kết nối (keep-alive, close)                     |

Tìm hiểu kỹ Host Header:

Host Header là gì?

Host Header là header bắt buộc trong HTTP/1.1, chứa domain name (và có thể cả port) của server mà client muốn kết nối.

Vai trò của Host Header:

* Cho phép một IP address host nhiều website (Virtual Hosting)
* Server dựa vào Host header để routing request đến đúng website
* Tạo absolute URL cho các redirect và link generation

Tại sao Host Header quan trọng trong bảo mật?

* Server có thể trust Host header để generate URLs, dẫn đến các lỗ hổng
* Nếu không validate đúng, attacker có thể inject malicious host
* Ảnh hưởng đến password reset links, redirects, cache behavior

Các cuộc tấn công liên quan đến Host Header:

1. Host Header Injection - Inject malicious host vào password reset email
2. Web Cache Poisoning - Cache response với malicious host header
3. Server-Side Request Forgery (SSRF) - Bypass access control bằng Host header
4. Virtual Host Bruteforce - Enumerate hidden virtual hosts

Ví dụ Host Header vulnerability:

```
POST /password-reset HTTP/1.1
Host: evil.com    <-- Attacker controlled
...

Server generates: https://evil.com/reset?token=abc123
--> Token bị gửi về server của attacker
```

***

#### 1.2.6 Cookie và các Flag của Cookie

Cookie là gì?

Cookie là một đoạn dữ liệu nhỏ (\~4KB) được server gửi về browser qua header Set-Cookie. Browser sẽ lưu trữ và tự động gửi lại cookie trong các request tiếp theo đến cùng domain.

Cấu trúc của một Cookie:

```
Set-Cookie: sessionId=abc123; Path=/; Domain=.example.com; Expires=Fri, 03 Jan 2027 00:00:00 GMT; Secure; HttpOnly; SameSite=Strict
```

Các Flag của Cookie:

| Flag            | Mô tả                                                                         | Ảnh hưởng bảo mật                         |
| --------------- | ----------------------------------------------------------------------------- | ----------------------------------------- |
| Secure          | Cookie chỉ được gửi qua HTTPS                                                 | Ngăn cookie bị intercept trên HTTP (MITM) |
| HttpOnly        | JavaScript không thể truy cập cookie                                          | Bảo vệ khỏi XSS cookie stealing           |
| SameSite        | Kiểm soát cookie có được gửi trong cross-site request không (Strict/Lax/None) | Bảo vệ khỏi CSRF attacks                  |
| Path            | Cookie chỉ gửi cho các request matching path                                  | Giới hạn scope của cookie                 |
| Domain          | Xác định domain nào nhận được cookie                                          | Kiểm soát cookie scope giữa subdomains    |
| Expires/Max-Age | Thời điểm/thời gian cookie hết hạn                                            | Session cookie vs Persistent cookie       |

Câu hỏi: Vì sao website có thể lưu phiên đăng nhập của người dùng?

Trả lời:

Website lưu phiên đăng nhập thông qua cơ chế Session và Cookie:

1. Khi user đăng nhập thành công:
   * Server tạo một Session ID duy nhất và lưu thông tin user vào server-side session storage
   * Server gửi Session ID về browser qua header Set-Cookie
2. Browser lưu trữ cookie:
   * Browser lưu cookie vào storage của domain tương ứng
   * Cookie có thể là session cookie (xóa khi đóng browser) hoặc persistent (có Expires)
3. Các request tiếp theo:
   * Browser tự động gửi cookie trong header Cookie cho mọi request đến domain đó
   * Server đọc Session ID từ cookie, tra cứu session storage để xác định user
4. Kết quả:
   * User không cần đăng nhập lại cho đến khi session hết hạn hoặc logout
   * Server stateless vẫn có thể identify user qua stateful session mechanism

***

#### 1.2.7 HTTP Status Code

Phân loại Status Code:

| Nhóm | Ý nghĩa                                     | Ví dụ                                              |
| ---- | ------------------------------------------- | -------------------------------------------------- |
| 1xx  | Informational - Request đã nhận, đang xử lý | 100 Continue, 101 Switching Protocols              |
| 2xx  | Success - Request thành công                | 200 OK, 201 Created, 204 No Content                |
| 3xx  | Redirection - Cần thêm action để hoàn thành | 301 Moved Permanently, 302 Found, 304 Not Modified |
| 4xx  | Client Error - Lỗi từ phía client           | 400 Bad Request, 401 Unauthorized, 404 Not Found   |
| 5xx  | Server Error - Lỗi từ phía server           | 500 Internal Server Error, 502 Bad Gateway         |

Các Status Code thường gặp:

| Code | Tên                   | Mô tả chi tiết                                               |
| ---- | --------------------- | ------------------------------------------------------------ |
| 200  | OK                    | Request thành công, response chứa kết quả                    |
| 201  | Created               | Resource mới đã được tạo thành công (thường sau POST/PUT)    |
| 301  | Moved Permanently     | Resource đã chuyển vĩnh viễn sang URL mới, browser nên cache |
| 302  | Found                 | Redirect tạm thời, browser không nên cache                   |
| 400  | Bad Request           | Request sai cú pháp hoặc không hợp lệ                        |
| 401  | Unauthorized          | Chưa xác thực, cần login                                     |
| 403  | Forbidden             | Đã xác thực nhưng không có quyền truy cập                    |
| 404  | Not Found             | Resource không tồn tại                                       |
| 500  | Internal Server Error | Lỗi server không xác định                                    |
| 502  | Bad Gateway           | Proxy/Gateway nhận response không hợp lệ từ upstream server  |
| 503  | Service Unavailable   | Server tạm thời không khả dụng (overload, maintenance)       |

***

### 1.3 Cấu trúc của một URL

Tham khảo: https://www.rfc-editor.org/rfc/rfc1738.html

Cấu trúc tổng quát:

```
scheme://[user:password@]host[:port]/path[?query][#fragment]
```

Phân tích các thành phần:

| Thành phần | Mô tả                                                | Ví dụ                             |
| ---------- | ---------------------------------------------------- | --------------------------------- |
| Scheme     | Giao thức sử dụng                                    | http, https, ftp, mailto          |
| Username   | Tên đăng nhập (optional, deprecated)                 | admin trong ftp://admin:pass@host |
| Password   | Mật khẩu (optional, deprecated, insecure)            | pass trong ftp://admin:pass@host  |
| Host       | Tên miền hoặc IP address                             | www.example.com, 192.168.1.1      |
| Port       | Cổng kết nối (optional, default: 80/443)             | :8080, :3000                      |
| Path       | Đường dẫn đến resource trên server                   | /products/item.html               |
| Query      | Tham số truy vấn (sau dấu ?)                         | ?id=123\&category=books           |
| Fragment   | Vị trí trong trang (sau dấu #, không gửi lên server) | #section-2                        |

Ví dụ phân tích URL thực tế:

URL mẫu: https://user:pass@www.example.com:8443/products/search?q=laptop\&sort=price#reviews

Phân tích:

* Scheme: https
* Username: user
* Password: pass
* Host: www.example.com
* Port: 8443
* Path: /products/search
* Query: q=laptop\&sort=price
* Fragment: reviews

***

### 1.4 Web Functionality

Nguồn: "The Web Application Hacker's Handbook"

Các thành phần chức năng của Web Application:

Server-side functionality:

* Web Server: Xử lý HTTP request (Apache, Nginx, IIS)
* Application Server: Chạy business logic (Tomcat, Node.js, PHP-FPM)
* Database: Lưu trữ dữ liệu (MySQL, PostgreSQL, MongoDB)
* Backend Framework: Xử lý routing, authentication (Django, Express, Laravel)
* APIs: RESTful, GraphQL, SOAP endpoints

Client-side functionality:

* HTML: Cấu trúc nội dung trang web
* CSS: Styling và layout
* JavaScript: Logic phía client, DOM manipulation, AJAX
* Browser Storage: Cookies, LocalStorage, SessionStorage
* Frontend Framework: React, Vue, Angular

State và Sessions:

* HTTP là stateless - mỗi request độc lập
* Session management: Cookies, URL rewriting, hidden form fields
* Authentication tokens: JWT, Session ID
* CSRF tokens để bảo vệ form submission

***

### 1.5 Encoding Schemes

Nguồn: "The Web Application Hacker's Handbook"

#### URL Encoding

Mô tả:

URL Encoding (Percent Encoding) chuyển đổi các ký tự đặc biệt thành format %HH (HH là mã hex của ký tự). Được sử dụng khi gửi dữ liệu qua URL vì URL chỉ cho phép một số ký tự an toàn.

Bảng mã thường gặp:

| Ký tự | Mã URL Encoding |
| ----- | --------------- |
| Space | %20 hoặc +      |
| <     | %3C             |
| >     | %3E             |
| #     | %23             |
| %     | %25             |
| &     | %26             |
| =     | %3D             |
| /     | %2F             |
| ?     | %3F             |
| '     | %27             |
| "     | %22             |

#### HTML Encoding

Mô tả:

HTML Encoding chuyển đổi các ký tự có ý nghĩa đặc biệt trong HTML thành HTML entities để hiển thị đúng trên trang web và ngăn chặn XSS.

Các entity thường gặp:

| Ký tự | HTML Entity |
| ----- | ----------- |
| <     | <           |
| >     | >           |
| &     | &           |
| "     | "           |
| '     | ' hoặc '    |
| /     | /           |

#### Base64 Encoding

Mô tả:

Base64 là phương pháp encoding binary data thành ASCII string sử dụng 64 ký tự (A-Z, a-z, 0-9, +, /). Padding với =. Thường dùng cho: truyền binary qua text protocol, encode credentials, embed images.

Ví dụ:

```
Text: "Hello World"
Base64: "SGVsbG8gV29ybGQ="

Text: "admin:password"
Base64: "YWRtaW46cGFzc3dvcmQ="
```

#### Unicode Encoding

Mô tả:

Unicode Encoding biểu diễn ký tự bằng code point. Có nhiều format: UTF-8, UTF-16, escape sequence (\uXXXX). Có thể bị lợi dụng để bypass filter (Unicode normalization attacks).

Ví dụ:

```
Ký tự: <
Unicode escape: \u003c
HTML numeric: &#60; hoặc &#x3c;
UTF-8 bytes: 0x3C
```

***

### Trả lời các câu hỏi

#### Câu 1: Tại sao chúng ta phải dùng BurpSuite để thực hiện pentest web?

Trả lời:

BurpSuite là công cụ không thể thiếu trong pentest web vì những lý do sau:

1. Intercept và modify traffic: Browser thông thường không cho phép xem hoặc sửa đổi HTTP request/response. BurpSuite cho phép chúng ta can thiệp vào giữa quá trình giao tiếp client-server, từ đó có thể:
   * Xem toàn bộ dữ liệu được gửi đi và nhận về
   * Sửa đổi parameters, headers, cookies trước khi gửi
   * Bypass các kiểm tra client-side (JavaScript validation)
2. Phát hiện vulnerability: Nhiều lỗ hổng không thể phát hiện chỉ bằng browser:
   * Hidden parameters trong form
   * API endpoints không hiển thị trên giao diện
   * Logic flaws trong business process
3. Automation: BurpSuite cung cấp các công cụ tự động hóa:
   * Intruder cho brute force và fuzzing
   * Scanner (Pro version) tự động tìm vulnerabilities
   * Repeater để test nhanh các payload
4. Phân tích và documentation: Lưu lại toàn bộ traffic, dễ dàng review và tạo báo cáo.

***

#### Câu 2: Khi chúng ta đánh giá Ứng dụng Web, có nghĩa là chúng ta đánh giá những gì?

Trả lời:

Khi đánh giá bảo mật ứng dụng web, chúng ta đánh giá các khía cạnh sau:

1. Input Validation:
   * Ứng dụng có validate và sanitize input từ user không?
   * Có khả năng bị SQL Injection, XSS, Command Injection không?
2. Authentication (Xác thực):
   * Cơ chế đăng nhập có an toàn không?
   * Password policy có đủ mạnh không?
   * Có chống brute force không?
   * Multi-factor authentication?
3. Authorization (Phân quyền):
   * User có thể truy cập tài nguyên không thuộc quyền của họ không?
   * IDOR (Insecure Direct Object Reference)?
   * Privilege escalation?
4. Session Management:
   * Session token có đủ random và an toàn không?
   * Cookie flags có được set đúng không (HttpOnly, Secure, SameSite)?
   * Session fixation, session hijacking?
5. Business Logic:
   * Có thể bypass các bước trong quy trình không?
   * Race conditions?
   * Số lượng, giá cả có thể bị manipulate không?
6. Configuration:
   * HTTP headers bảo mật (CSP, X-Frame-Options, HSTS)?
   * Thông tin nhạy cảm bị lộ (error messages, server version)?
   * Các file/directory nhạy cảm có thể truy cập?
7. Data Protection:
   * Dữ liệu nhạy cảm có được mã hóa không?
   * HTTPS có được enforce không?
   * Sensitive data trong URL, logs?
8. Third-party Components:
   * Có sử dụng thư viện/framework có lỗ hổng không?
   * Dependencies có được cập nhật không?

***

#### Câu 3: Chu trình xử lý của trình duyệt từ khi nhập địa chỉ website trên thanh URL tới khi render toàn bộ nội dung website

Trả lời chi tiết từng bước:

Bước 1: Parsing URL

Khi user nhập URL vào thanh địa chỉ (ví dụ: https://www.example.com/page), browser phân tích URL thành các thành phần:

* Protocol: https
* Host: www.example.com
* Path: /page
* Port: 443 (mặc định cho HTTPS)

Browser cũng kiểm tra HSTS preload list xem domain có yêu cầu HTTPS không.

Bước 2: DNS Resolution

Browser cần chuyển đổi domain name thành IP address:

1. Kiểm tra browser DNS cache
2. Kiểm tra OS DNS cache
3. Kiểm tra file hosts (/etc/hosts hoặc C:\Windows\System32\drivers\etc\hosts)
4. Query đến DNS resolver (thường là của ISP hoặc 8.8.8.8)
5. DNS resolver thực hiện recursive query:
   * Root DNS server → .com TLD server → example.com authoritative server
6. Nhận được IP address (ví dụ: 93.184.216.34)

Bước 3: TCP Connection (Three-way Handshake)

Browser thiết lập kết nối TCP đến server:

1. Client gửi SYN packet đến server
2. Server trả lời bằng SYN-ACK packet
3. Client gửi ACK packet để xác nhận

Kết nối TCP được thiết lập thành công.

Bước 4: TLS Handshake (nếu HTTPS)

Với HTTPS, cần thêm bước mã hóa:

1. Client Hello: Browser gửi các cipher suites được hỗ trợ, random number
2. Server Hello: Server chọn cipher suite, gửi certificate
3. Certificate Verification: Browser xác minh certificate với CA
4. Key Exchange: Trao đổi khóa để tạo session key
5. Finished: Cả hai bên xác nhận, bắt đầu encrypted communication

Bước 5: Gửi HTTP Request

Browser gửi HTTP GET request:

```
GET /page HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 ...
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: session=abc123
```

Bước 6: Server xử lý và trả về Response

Server nhận request và xử lý:

1. Web server (Nginx/Apache) nhận request
2. Routing đến application server phù hợp
3. Application xử lý logic, query database nếu cần
4. Generate HTML response
5. Gửi response về client

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 12345
Content-Encoding: gzip
...

<!DOCTYPE html>...
```

Bước 7: Browser nhận Response

Browser nhận và xử lý response:

1. Kiểm tra status code (200 OK, 301 redirect, 404 not found, etc.)
2. Giải nén nếu cần (gzip, br)
3. Kiểm tra Content-Type để xác định cách xử lý
4. Lưu cookies từ Set-Cookie header

Bước 8: Parsing HTML và xây dựng DOM

Browser parse HTML và xây dựng Document Object Model:

1. Tokenization: Chuyển HTML thành tokens
2. Tree construction: Xây dựng DOM tree từ tokens
3. Khi gặp external resources, thêm vào queue để tải

Bước 9: Tải các resources bổ sung

Browser tải các resources được reference trong HTML:

1. CSS files: Tải và xây dựng CSSOM (CSS Object Model)
2. JavaScript files: Tải, parse và execute (có thể block rendering)
3. Images, fonts, videos: Tải parallel
4. Các request này cũng đi qua các bước 2-7

Lưu ý:

* CSS block rendering (phải có CSSOM trước khi render)
* JavaScript block parsing (trừ khi có async/defer)

Bước 10: Render và hiển thị

Quá trình rendering:

1. Render Tree: Kết hợp DOM + CSSOM, loại bỏ elements không hiển thị (display: none)
2. Layout (Reflow): Tính toán vị trí và kích thước của mỗi element
3. Paint: Vẽ các pixels lên layers
4. Composite: Ghép các layers lại thành final image
5. Hiển thị lên màn hình

Sau đó, JavaScript có thể tiếp tục chạy, handle user interactions, và update DOM dynamically.

Sơ đồ tóm tắt:

```
User nhập URL
      ↓
[1] Parse URL
      ↓
[2] DNS Lookup (domain → IP)
      ↓
[3] TCP Handshake (SYN → SYN-ACK → ACK)
      ↓
[4] TLS Handshake (nếu HTTPS)
      ↓
[5] Gửi HTTP Request
      ↓
[6] Server xử lý, trả Response
      ↓
[7] Browser nhận Response
      ↓
[8] Parse HTML → DOM Tree
      ↓
[9] Tải CSS, JS, Images
      ↓
[10] Render Tree → Layout → Paint → Display
```

***

### Tài liệu tham khảo

1. The Web Application Hacker's Handbook (2nd Edition)
2. RFC 1738 - Uniform Resource Locators (URL): https://www.rfc-editor.org/rfc/rfc1738.html
3. BurpSuite Documentation: https://portswigger.net/burp/documentation
4. MDN Web Docs - HTTP: https://developer.mozilla.org/en-US/docs/Web/HTTP

***
