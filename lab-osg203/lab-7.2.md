# Lab 7.2

#### 1. Kiểm tra các tệp hệ thống tài khoản

**Xem toàn bộ tệp:**

```bash
cat /etc/passwd
cat /etc/group
sudo cat /etc/shadow
```

**Đếm số lượng tài khoản:**

```bash
cat /etc/passwd | wc -l
```

<figure><img src="../.gitbook/assets/image (230).png" alt=""><figcaption></figcaption></figure>

**Giải thích:**\
Mỗi dòng trong `/etc/passwd` đại diện cho một tài khoản.\
Các UID nhỏ (<1000) là tài khoản hệ thống, UID ≥ 1000 là người dùng thông thường.

**Kiểm tra các tài khoản đặc biệt:**

```bash
grep -E "root|bin|lp|mail" /etc/passwd
```

<figure><img src="../.gitbook/assets/image (231).png" alt=""><figcaption></figcaption></figure>

**Giải thích:**

* root có shell `/bin/bash` và home `/root`
* các tài khoản hệ thống như bin, lp, mail có shell `/sbin/nologin` vì không dùng để đăng nhập.

**Kiểm tra nhóm:**

```bash
grep yourusername /etc/group
grep -E "bin|adm" /etc/group
```

**Xem các dòng cuối của `/etc/shadow`:**

```bash
sudo tail -n 23 /etc/shadow
```

**Giải thích:**

* Số 5 chữ số giữa hai dấu “:” đầu tiên là số ngày từ 1/1/1970 đến lần đổi mật khẩu gần nhất.
* Trường cuối cùng nếu khác `:::` thể hiện ngày hết hạn mật khẩu.

***

#### 2. Quản lý chính sách mật khẩu với `chage` và `passwd`

**Xem thông tin hết hạn của người dùng:**

```bash
sudo chage -l canhieu
```

**Đặt chính sách hết hạn:**

```bash
sudo chage -M 60 -W 5 canhieu
```

**Đặt ngày hết hạn cụ thể và thời gian khóa tài khoản sau khi hết hạn:**

```bash
sudo chage -E 2025-12-14 -I 7 canhieu
```

**Sử dụng `chage` ở chế độ tương tác:**

```bash
sudo chage cit371
```

Điền các giá trị sau:

* Minimum days: 100
* Maximum days: giữ nguyên
* Warning days: 14
* Inactive days: 7
* Expire date: 2026-06-01

**Xác nhận thay đổi:**

```bash
sudo chage -l cit371
```

**Khóa và mở khóa tài khoản:**

```bash
sudo passwd -l bushk
su bushk
sudo passwd -u bushk
```

**Giải thích:**\
Tài khoản bị khóa sẽ không thể đăng nhập hoặc thay đổi mật khẩu.

***

#### 3. Cấu hình PAM (Pluggable Authentication Module)

**Truy cập thư mục cấu hình PAM:**

```bash
cd /etc/pam.d
```

**Xem cấu hình PAM cho atd:**

```bash
cat atd
```

Các module thường gặp:

```
auth required pam_access.so
account required pam_permit.so
session required pam_loginuid.so
```

**Xem nội dung của password-auth:**

```bash
cat /etc/pam.d/password-auth
```

Các module bổ sung thường thấy:

```
pam_unix.so
pam_pwquality.so
pam_deny.so
```

**Xem và chỉnh cấu hình kiểm tra độ mạnh mật khẩu:**

```bash
sudo nano /etc/security/pwquality.conf
```

Các directive quan trọng:

* `minlen` xác định độ dài tối thiểu của mật khẩu.
* `dcredit`, `ocredit`, `ucredit`, `lcredit` cho phép giảm độ dài nếu có ký tự số hoặc đặc biệt.
* Cracklib dictionary được dùng để ngăn mật khẩu yếu.

***

#### 4. Mặc định người dùng và login.defs

**Xem tệp cấu hình mặc định:**

```bash
sudo less /etc/login.defs
```

Các thông số quan trọng:

```bash
grep PASS_ /etc/login.defs
grep -E 'UID_MIN|UID_MAX|GID_MIN|GID_MAX' /etc/login.defs
```

**Giải thích:**

* PASS\_MAX\_DAYS, PASS\_MIN\_DAYS, PASS\_WARN\_AGE, PASS\_MIN\_LEN là các thông số kiểm soát tuổi thọ mật khẩu.
* UID/GID tối thiểu cho người dùng thường bắt đầu từ 1000.
* DEFAULT\_HOME chỉ định việc tạo thư mục home.
* CREATE\_HOME=yes nghĩa là `useradd` tự động tạo thư mục home.

**Xem quyền thư mục home mặc định:**

```bash
grep -E 'HOME_MODE|UMASK' /etc/login.defs
```

**Kiểm tra mặc định của `useradd`:**

```bash
sudo useradd -D
```

**Thay đổi shell mặc định:**

```bash
sudo useradd -D -s /bin/csh
sudo useradd -D -s /bin/bash
```

**Giải thích GROUP=100:**\
Là nhóm mặc định mà `useradd` gán cho người dùng mới (GID 100 thường là nhóm `users`):

```bash
grep ':100:' /etc/group
```

***

#### 5. Cấu hình sudoers

**Mở file sudoers bằng trình kiểm tra lỗi:**

```bash
sudo visudo
```

**Phân tích mục root:**

```
root ALL=(ALL) ALL
```

Điều này cho phép root thực thi mọi lệnh trên mọi máy với quyền của bất kỳ người dùng nào.

**Thêm quyền cho tài khoản của bạn để dùng groupadd:**

```bash
which groupadd
```

Kết quả thường là `/usr/sbin/groupadd`.

Thêm dòng:

```
yourusername ALL=(ALL) /usr/sbin/groupadd
```

**Kiểm tra:**

```bash
sudo groupadd newgroup
```

**Thêm quyền đọc tệp sudoers:**

```bash
which cat
```

Kết quả thường là `/usr/bin/cat`.

Thêm dòng:

```
yourusername ALL=(ALL) /usr/bin/cat /etc/sudoers
```

**Kiểm tra:**

```bash
sudo cat /etc/sudoers
```

***

#### 6. Kết thúc

```bash
exit
shutdown now
```

















