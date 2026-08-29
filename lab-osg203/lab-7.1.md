# Lab 7.1

<figure><img src="../.gitbook/assets/Screenshot 2025-10-14 215346.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (610).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (611).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (612).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (613).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (614).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (615).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (616).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../.gitbook/assets/image (617).png" alt=""><figcaption></figcaption></figure>



#### **Câu hỏi lý thuyết về `useradd`**

* `-m`: tạo thư mục home cho user.\
  **Không dùng khi:** tạo tài khoản hệ thống (service account) không cần home.
* `-c`: thêm ghi chú (GECOS field).
* `-d`: chỉ định thư mục home tùy chọn.
* `-e`: đặt ngày hết hạn tài khoản.
* `-G`: thêm vào nhóm phụ.
* `-s`: chọn shell đăng nhập.
* `-M`: **không** tạo thư mục home.\
  **Dùng khi:** không cần user có home (vd: tài khoản dịch vụ).
* `-u`: chỉ định UID cụ thể.
* `-u -o`: cho phép trùng UID (dùng hiếm, cho tài khoản dùng chung UID).

***

#### **Tạo tài khoản Tommy Mars**

```bash
useradd -m -c "Tommy Mars" marst
```

***

#### **Tạo nhóm cit371**

```bash
groupadd cit371
```

Kiểm tra GID:

```bash
tail /etc/group
```

Ví dụ kết quả:

```
cit371:x:1005:
```

**GID = 1005**

***

#### **Tạo tài khoản Ruth Underwood, thêm vào nhóm cit371**

```bash
useradd -m -c "Ruth Underwood" -G cit371 underwoodr
```

Kiểm tra UID/GID:

```bash
tail /etc/passwd
```

Ví dụ:

```
underwoodr:x:1006:1005:Ruth Underwood:/home/underwoodr:/bin/bash
```

**Giải thích:** UID (1006) là ID người dùng; GID (1005) là ID nhóm `cit371`.\
Chúng khác nhau vì `cit371` không phải nhóm mặc định.

Đặt mật khẩu:

```bash
passwd underwoodr
```

Nhập `xylophone`

**Cảnh báo:**\
Hệ thống cảnh báo “BAD PASSWORD: The password is too simple” nhưng vẫn cho phép đặt.

***

#### **Tạo hai nhóm mới**

```bash
groupadd students
groupadd -g 2000 dummies
```

***

#### **Tạo các user tiếp theo**

**1. Suzie Creamcheese**

```bash
useradd -m -s /usr/bin/tcsh -c "Suzie Creamcheese" creamcheeses
```

**2. Eric Cartman**

```bash
useradd -m -u 1501 -G cit371,students -c "Eric Cartman" cartmane
```

**3. Kate Bush**

```bash
useradd -m -e 2028-01-01 -c "Kate Bush" bushk
```

**4. CIT 371 Student**

```bash
useradd -M -N -c "CIT 371 Student" cit371
```

**Giải thích lỗi:** Nếu chỉ `useradd -M cit371`, hệ thống báo lỗi do trùng tên nhóm với user.\
Dùng thêm `-N` để không tạo private group.

***

#### **Đặt mật khẩu cho 4 user**

```bash
passwd creamcheeses
```

Nhập `xyz12abc`\
Cảnh báo: mật khẩu yếu, vẫn chấp nhận.

```bash
passwd cartmane
```

Nhập `cheesypoofs`\
Cảnh báo tương tự.

```bash
passwd bushk
```

Nhập `9thWave!`\
Mật khẩu mạnh, không cảnh báo.

```bash
passwd cit371
```

Nhập mật khẩu tùy chọn của bạn.\
Nếu quá ngắn hoặc phổ biến, sẽ báo “BAD PASSWORD”.

***

**4. Script tạo tài khoản hàng loạt**

Tạo file script:

```bash
nano create_users.sh
```

Nội dung:

```bash
#!/bin/bash
while read first last; do
    name="$first $last"
    username="${last}${first:0:1}"
    n=$(grep -c "^$username:" /etc/passwd)
    if [ $n -gt 0 ]; then
        username="${username}$((n+1))"
    fi
    useradd -c "$name" -m $username
    password=$(date +%s | sha256sum | head -c10)
    echo "$username:$password" | chpasswd
    echo "$username $password" >> /root/tempPasswords
done < $1
```

Lưu và thoát, sau đó cấp quyền chạy:

```bash
chmod +x create_users.sh
```

Tải file tên:

```bash
wget www.nku.edu/~foxr/accounts.txt
```

Chạy script:

```bash
./create_users.sh accounts.txt
```

Nếu thành công, xem danh sách người dùng mới:

```bash
tail -n 23 /etc/passwd
```

Và danh sách mật khẩu:

```bash
cat /root/tempPasswords
```

**Giải thích lệnh tạo mật khẩu:**

```bash
date +%s | sha256sum | head -c10
```

* `date +%s`: lấy thời gian tính theo giây từ epoch (Unix timestamp).
* `sha256sum`: mã hóa chuỗi bằng thuật toán SHA-256.
* `head -c10`: lấy 10 ký tự đầu làm mật khẩu.

***

**5. Kiểm tra thư mục người dùng**

#### **Kiểm tra thư mục /home**

```bash
ls -l /home
```

Tất cả user đều có thư mục trừ `cit371` (vì dùng `-M` khi tạo).

**Giải thích:** Không có home directory vì ta đã chỉ định không tạo với tùy chọn `-M`.

***

#### **Cách tạo thư mục home**

Các thư mục được tạo bằng cách sao chép từ `/etc/skel`.

Kiểm tra:

```bash
ls -al /etc/skel
ls -al /home/underwoodr
```

**Nội dung /etc/skel:**

```
.bash_logout
.bash_profile
.bashrc
```

**Một thư mục đặc biệt:** `.bashrc` là file cấu hình shell; `.bash_profile` chạy khi login.\
Các file trong `/etc/skel` được sao chép vào home của user mới.

**Khác biệt về quyền:**\
Các file trong `/home/username` thuộc sở hữu của user đó, không phải root.\
Ví dụ: `-rw-r--r-- 1 underwoodr underwoodr ...`

***

#### **Mail spool files**

```bash
ls -l /var/spool/mail
```

Bạn sẽ thấy:

```
root
yourusername
hacketts
dukeg
...
```

**Kích thước:** hầu hết bằng 0 byte (chưa có mail).\
Một mục có thể thuộc về user hệ thống như `postfix` hoặc `chrony`.

**Giải thích:**

* File này được sở hữu bởi `mail` (chương trình hệ thống).
* Group là tên người dùng tương ứng, để giới hạn quyền đọc mail.

***

#### **Kết thúc**

Thoát root và tắt máy:

```bash
exit
shutdown now
```





