---
description: >-
  Learn about and exploit each of the OWASP Top 10 vulnerabilities; the 10 most
  critical web security risks.
---

# OWASP Top 10(tryhackme)



Task 1&#x20;Introduction
------------------

#### Các chủ đề OWASP bao gồm:

* **Injection** – Tấn công chèn mã độc
* **Broken Authentication** – Xác thực không an toàn
* **Sensitive Data Exposure** – Rò rỉ dữ liệu nhạy cảm
* **XML External Entity (XXE)** – Thực thể XML bên ngoài
* **Broken Access Control** – Kiểm soát truy cập bị phá vỡ
* **Security Misconfiguration** – Cấu hình bảo mật sai
* **Cross-site Scripting (XSS)** – Tấn công kịch bản xuyên trang
* **Insecure Deserialization** – Giải tuần tự không an toàn
* **Components with Known Vulnerabilities** – Thành phần có lỗ hổng đã biết
* **Insufficient Logging & Monitoring** – Ghi log và giám sát không đầy đủ



## Task 2 Accessing machines&#x20;

<figure><img src="../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>



Task 3&#x20;\[Severity 1] Injection
-----------------------------



### 1. Injection là gì?

* Lỗi xảy ra khi dữ liệu đầu vào của người dùng bị hiểu như lệnh hoặc tham số thật sự bởi ứng dụng.
* Phụ thuộc vào công nghệ sử dụng và cách xử lý đầu vào.

### 2. Các loại Injection phổ biến

* **SQL Injection**:\
  Dữ liệu người dùng được đưa vào câu truy vấn SQL → kẻ tấn công có thể đọc, sửa, xóa dữ liệu.
* **Command Injection**:\
  Dữ liệu người dùng được chèn vào lệnh hệ thống → kẻ tấn công có thể thực thi lệnh trên máy chủ.

### 3. Mục tiêu của kẻ tấn công

* Truy cập, chỉnh sửa, xóa dữ liệu nhạy cảm trong cơ sở dữ liệu (ví dụ: thông tin cá nhân, mật khẩu).
* Chiếm quyền điều khiển hệ thống, từ đó mở rộng phạm vi tấn công.

### 4. Cách phòng chống Injection

* **Danh sách cho phép (allow list)**:\
  So sánh đầu vào với danh sách giá trị hợp lệ. Nếu không hợp lệ thì từ chối.
* **Loại bỏ ký tự nguy hiểm (stripping)**:\
  Xóa các ký tự có thể gây thay đổi trong xử lý dữ liệu (như `'`, `;`, `"`...).
* **Sử dụng thư viện hỗ trợ**:\
  Dùng các thư viện đã được kiểm chứng để xử lý đầu vào an toàn.

### 5. Ký tự nguy hiểm là gì?

* Là bất kỳ ký tự hoặc dữ liệu nào có thể làm thay đổi logic xử lý của ứng dụng hoặc hệ thống.



Task 4&#x20;\[Severity 1] OS Command Injection
----------------------------------------

### 1. Command Injection là gì?

* Là lỗ hổng bảo mật web xảy ra khi **mã phía server** (như PHP) thực hiện **gọi lệnh hệ thống** (system call).
* Kẻ tấn công lợi dụng lỗ hổng này để **thực thi lệnh hệ điều hành** trên máy chủ.

### 2. Mức độ nguy hiểm

* Một số lệnh đơn giản như `whoami` hoặc đọc file có vẻ không quá nghiêm trọng.
* Tuy nhiên, **Command Injection mở ra rất nhiều khả năng tấn công**, đặc biệt là:
  *   **Tạo reverse shell**:\
      Giúp kẻ tấn công chiếm quyền điều khiển máy chủ với quyền của user đang chạy web server.

      Ví dụ:\
      `; nc -e /bin/bash`\
      (Một số phiên bản `netcat` không hỗ trợ tùy chọn `-e`).
  * Có thể dùng **danh sách shell thay thế** nếu lệnh trên không khả dụng.

### 3. Hậu quả khi bị khai thác

* Sau khi tạo được **foothold** (chỗ đứng ban đầu), kẻ tấn công sẽ:
  * **Liệt kê (enumerate)** hệ thống.
  * **Tìm cách pivot** (di chuyển sang các hệ thống khác liên quan).



Task 5&#x20;\[Severity 1] Command Injection Practical
-----------------------------------------------

<figure><img src="../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>



Trang web mà tryhackme cấp cho ta là 1 chall về OS Command .

Nhu ví dụ bên dưới tôi sử dụng `whoami` để kiểm tra xem hệ thống web này đang chạy bằng user nào          ⇒ thì bên phía back-end đã xử lý và trả về kết quả như ảnh

<figure><img src="../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

Và đây cũng chính là đáp án của câu hỏi số 3&#x20;

<figure><img src="../.gitbook/assets/image (349).png" alt=""><figcaption></figcaption></figure>



Tiếp đến tôi sẽ sử dụng lệnh `ls -l`  để liệt kê các file trên hệ thống kèm theo hiển thị phân quyền của các file và folder

<figure><img src="../.gitbook/assets/image (350).png" alt=""><figcaption></figcaption></figure>

Và ta nhận thấy file `.txt`  kia khá là kì lạ vậy nên đáp án của câu 1 sẽ là :&#x20;

<figure><img src="../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>



Tiếp theo tôi sẽ tiến hành đọc file `etc/passwd`&#x20;

<figure><img src="../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>

và câu hỏi là `How many non-root/non-service/non-daemon users are there?`&#x20;

Dựa vào dữ liệu trong file `/etc/passwd` ta nhận thấy là ko tồn tại tài khoản user bình thường nào&#x20;

nó có định dạng như sau :&#x20;

```
username:x:UID:GID:comment:home_directory:shell
```

<figure><img src="../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (354).png" alt=""><figcaption></figcaption></figure>

Để biết được phiên bản ubuntu đang chạy trên hệ thống , ta sử dụng lệnh `cat /etc/proc`

Từ nội dung trả về thì đây là phiên bản `18.04.4`

<figure><img src="../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (356).png" alt=""><figcaption></figcaption></figure>

Từ file  `/etc/passwd` ta cũng biết được user's shell&#x20;

<figure><img src="../.gitbook/assets/image (357).png" alt=""><figcaption></figcaption></figure>



Câu cuối hơi xamlul =)))

<figure><img src="../.gitbook/assets/image (358).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (359).png" alt=""><figcaption></figcaption></figure>



Task 6&#x20;\[Severity 2] Broken Authentication
-----------------------------------------



<figure><img src="../.gitbook/assets/image (453).png" alt=""><figcaption></figcaption></figure>



### 1. Khái niệm

* **Authentication (Xác thực)**: Xác minh danh tính người dùng, thường qua **tên đăng nhập + mật khẩu**.
* **Session Management (Quản lý phiên)**: Sử dụng **session cookie** để theo dõi trạng thái người dùng vì HTTP/HTTPS là giao thức **stateless**.

### 2. Mối nguy nếu xác thực bị phá vỡ

* Kẻ tấn công có thể **chiếm tài khoản** của người khác.
* Truy cập được **dữ liệu nhạy cảm** tùy mục đích của ứng dụng.

### 3. Các lỗ hổng phổ biến

* **Brute force**: Thử nhiều kết hợp username/password để tìm thông tin đúng.
* **Weak credentials**: Cho phép mật khẩu yếu như `password1` → dễ đoán.
* **Weak session cookies**: Cookie dễ đoán giá trị → kẻ tấn công tự tạo cookie giả và truy cập.

### 4. Biện pháp phòng chống

* **Chính sách mật khẩu mạnh**: Yêu cầu độ dài tối thiểu, ký tự đặc biệt, chữ hoa, chữ thường…
* **Khóa tài khoản tạm thời** sau một số lần đăng nhập thất bại → giảm nguy cơ brute force.
* **Multi-Factor Authentication (MFA)**: Kết hợp nhiều yếu tố (mật khẩu + mã OTP trên điện thoại).



Task 7&#x20;\[Severity 2] Broken Authentication Practical
---------------------------------------------------

<figure><img src="../.gitbook/assets/image (454).png" alt=""><figcaption></figcaption></figure>

Bài yêu cầu khai thác một **lỗi logic trong cơ chế xác thực** của ứng dụng web.\
Cụ thể, ứng dụng cho phép **đăng ký lại tên người dùng đã tồn tại** chỉ bằng cách thêm một ký tự khoảng trắng ở đầu hoặc cuối tên.

Trong ví dụ:

* Tài khoản `darren` đã tồn tại.
* Khi đăng ký với tên `" darren"` (có khoảng trắng ở đầu), hệ thống tạo tài khoản mới nhưng lại cho quyền truy cập và dữ liệu giống tài khoản gốc `darren`.
* Mục tiêu: Đăng ký với biến thể tên có khoảng trắng để truy cập được nội dung (flag) trong tài khoản `darren`.



Khi truy cập vào bài lab thì ta có trang form login như hình :&#x20;

<figure><img src="../.gitbook/assets/image (455).png" alt=""><figcaption></figcaption></figure>



Sau khi tạo 1 tài khoản bất kì để đăng nhập thì tôi nhận được thông điệp :&#x20;

<figure><img src="../.gitbook/assets/image (456).png" alt=""><figcaption></figcaption></figure>



Và ta dựa vào mô tả của đề bài và tạo 1 tài khoản mới cũng là `daren` nhưng có thêm khoảng trắng ở đầu

`" daren"`&#x20;



<figure><img src="../.gitbook/assets/image (457).png" alt=""><figcaption></figcaption></figure>



Và bây giờ ta cũng sẽ áp dụng cách tương tự với đối tượng `arthur`

<figure><img src="../.gitbook/assets/image (458).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (459).png" alt=""><figcaption></figcaption></figure>



Task 8&#x20;\[Severity 3] Sensitive Data Exposure (Introduction)
----------------------------------------------------------

"**Sensitive Data Exposure**" là khi ứng dụng web vô tình để lộ dữ liệu nhạy cảm.

* Dữ liệu này có thể là **thông tin khách hàng** (tên, ngày sinh, tài chính) hoặc **thông tin kỹ thuật** (username, mật khẩu).
* Ở mức phức tạp, tấn công có thể thông qua **Man-in-the-Middle**, lợi dụng mã hóa yếu để đọc dữ liệu truyền đi.
* Tuy nhiên, nhiều trường hợp đơn giản hơn, dữ liệu có thể bị truy cập trực tiếp trên webserver mà không cần kiến thức mạng nâng cao.



Task 9&#x20;\[Severity 3] Sensitive Data Exposure (Supporting Material 1)
-------------------------------------------------------------------

Cơ sở dữ liệu (database) thường được dùng để lưu trữ lượng lớn dữ liệu, dễ truy cập từ nhiều nơi cùng lúc.

* Phổ biến nhất là **SQL database** (ví dụ: MySQL, MariaDB), nhưng cũng có **NoSQL**.
* Ngoài dạng server, còn có **flat-file database** (cơ sở dữ liệu lưu thành 1 file trên máy).

Vấn đề bảo mật:

* Nếu **flat-file database** được đặt trong thư mục gốc website (có thể truy cập trực tiếp qua trình duyệt), kẻ tấn công có thể **tải về** và đọc toàn bộ dữ liệu nhạy cảm.
* Đây là một dạng **Sensitive Data Exposure**.

Ví dụ thường gặp:

* **SQLite database** – có thể truy vấn bằng công cụ `sqlite3` trên terminal.
* Khi tải file về, bạn có thể mở và truy vấn dữ liệu trực tiếp trên máy của mình.

<figure><img src="../.gitbook/assets/image (460).png" alt=""><figcaption></figcaption></figure>





Task 10&#x20;\[Severity 3] Sensitive Data Exposure (Supporting Material 2)
-------------------------------------------------------------------

Kali Linux đi kèm sẵn nhiều công cụ bẻ khóa hash khác nhau — nếu bạn đã biết cách sử dụng thì có thể dùng. Tuy nhiên, các công cụ này **nằm ngoài phạm vi** của bài học này.

Thay vào đó, ta sẽ dùng **công cụ trực tuyến: Crackstation**.

* Đây là trang web cực kỳ hiệu quả trong việc crack các mã băm yếu.
* Nếu là các mã băm phức tạp hơn, ta cần công cụ chuyên sâu hơn.
* Trong thử thách hôm nay, tất cả các mã băm crack được đều là **MD5 yếu**, nên Crackstation xử lý rất tốt.



p/s: Nhưng tôi thích dùng Hashes hơn 🐧🐧🐧 Vậy nên tôi sẽ lấy ví dụ về Hashes



Dưới đây là 1 ví dụ về công cụ bẻ khóa hàm băm, hàm băm bên dưới là mật khẩu của 1 user trong hệ thống linux mà tôi đã từng bẻ khóa để leo quyền.

<figure><img src="../.gitbook/assets/image (461).png" alt=""><figcaption></figcaption></figure>



Task 11&#x20;\[Severity 3] Sensitive Data Exposure (Challenge)
-------------------------------------------------------

<figure><img src="../.gitbook/assets/image (462).png" alt=""><figcaption></figcaption></figure>



Ta sẽ tiến hành truy cập vào chall web :&#x20;

<figure><img src="../.gitbook/assets/image (463).png" alt=""><figcaption></figcaption></figure>



ở câu hỏi đầu tiên a Dev đã để lại 1 ghi chú trên trang web và vô tình tiết lộ đường dẫn đến nơi chứa dữ liệu quan trọng&#x20;

<figure><img src="../.gitbook/assets/image (464).png" alt=""><figcaption></figcaption></figure>

Sau khi dùng công cụ tích hợp sẵn c+ủa burpsuite thì ta cũng tìm ra được đáp án :&#x20;

<figure><img src="../.gitbook/assets/image (465).png" alt=""><figcaption></figcaption></figure>



+Sau khi ta truy cập vào path đã tìm thấy ở câu hỏi 1 , thì ta đã tìm thấy 1 trang chứa các thư mục của server và ta tìm thấy 1  file chứa thông tin về database&#x20;

<figure><img src="../.gitbook/assets/image (466).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (467).png" alt=""><figcaption></figcaption></figure>



Sau khi tải file về , ta nhận được các đoạn hash user id và hash mật khẩu của các user trên trang web&#x20;

<figure><img src="../.gitbook/assets/image (469).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (470).png" alt=""><figcaption></figcaption></figure>

sau khi dùng công cụ đẻ tìm ra mk của admin thì ta đã tìm được mật khẩu :&#x20;

<figure><img src="../.gitbook/assets/image (471).png" alt=""><figcaption></figcaption></figure>



Và sau khi đăng nhập vào ta sẽ nhận được flag :&#x20;

<figure><img src="../.gitbook/assets/image (472).png" alt=""><figcaption></figcaption></figure>





