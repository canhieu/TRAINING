# Lab 11.2

### 1. Cài đặt phần mềm bằng rpm

**Bước 1:** Tải gói `emacs-common` từ rpmfind.net.

```bash
cd ~/Downloads
ls
```

**Output:**

```
emacs-common-26.1-5.el8.x86_64.rpm
```

***

**Bước 2:** Cài đặt bằng rpm.

```bash
rpm -i emacs-common-26.1-5.el8.x86_64.rpm
```

**Output:**

```
error: Failed dependencies:
        libXaw.so.7()(64bit) is needed by emacs-common-26.1-5.el8.x86_64
```

**Câu hỏi:** Dependency là gì?\
**Trả lời:** Dependency là gói thư viện cần thiết để phần mềm có thể chạy được (ví dụ libXaw.so.7).

***

**Bước 3:** Cài dependency còn thiếu.

```bash
rpm -i libXaw-1.0.13-10.el8.x86_64.rpm
```

**Output:**

```
Preparing...                          ################################# [100%]
Updating / installing...
   1:libXaw-1.0.13-10.el8             ################################# [100%]
```

Sau đó cài lại gói emacs-common:

```bash
rpm -i emacs-common-26.1-5.el8.x86_64.rpm
```

**Output:**

```
Preparing...                          ################################# [100%]
Updating / installing...
   1:emacs-common-26.1-5.el8          ################################# [100%]
```

***

### 2. Cài đặt phần mềm bằng dnf

**Bước 1:** Kiểm tra yum.

```bash
which yum
```

**Output:**

```
/usr/bin/yum
```

```bash
ls -l /usr/bin/yum
```

**Output:**

```
lrwxrwxrwx. 1 root root 5 Jul  7  2023 /usr/bin/yum -> dnf-3
```

```bash
man yum
```

**Output (dòng đầu):**

```
Yum is deprecated and has been replaced by DNF.
```

***

**Bước 2:** Cài đặt emacs bằng dnf.

```bash
dnf install emacs
```

**Output:**

```
Last metadata expiration check: 0:01:08 ago on Sat 08 Nov 2025 13:42:31.
Dependencies resolved.
================================================================================
 Package                     Arch    Version                     Repository
================================================================================
Installing:
 emacs                       x86_64  26.1-5.el8                  appstream
Installing dependencies:
 emacs-common                x86_64  26.1-5.el8                  appstream
 emacs-filesystem            noarch  26.1-5.el8                  baseos
 libXaw                      x86_64  1.0.13-10.el8               appstream
Upgrading:
 glibc                       x86_64  2.28-251.el8_10             baseos
Transaction Summary
================================================================================
Install  4 Packages
Upgrade  1 Package
Total download size: 92 M
Is this ok [y/N]: y
```

**Câu hỏi:**

* Các package được cài: emacs, emacs-common, emacs-filesystem, libXaw
* Package được nâng cấp: glibc
* Tổng kích thước: 92 MB

***

**Bước 3:** Kiểm tra vị trí cài đặt.

```bash
which emacs
```

**Output:**

```
/usr/bin/emacs
```

***

### 3. Cài đặt phần mềm qua Software GUI

**Bước 1:** Mở Software (Activities → Software).

**Bước 2:**

* Communication & News → Firefox → 2 lựa chọn: Launch, Remove
* Productivity → LibreOffice, Calendar, Clocks (LibreOffice có dấu ✓, tức đã cài sẵn)
* Graphics & Photography → GNU Image Manipulator (GIMP) → chọn Install

**Sau khi cài đặt:** có 2 tùy chọn Launch và Remove.

***

**Bước 3:** Kiểm tra GIMP bằng terminal.

```bash
dnf list *gimp*
```

**Output:**

```
Installed Packages
gimp.x86_64                2.10.30-2.el8             @appstream
Available Packages
gimp-help-en.noarch        2.10.0-1.el8              appstream
```

```bash
which gimp
```

**Output:**

```
/usr/bin/gimp
```

```bash
dnf erase gimp
```

**Output:**

```
Removed:
  gimp.x86_64 2.10.30-2.el8
Freed space: 160 M
Is this ok [y/N]: y
```

```bash
which gimp
```

**Output:**

```
/usr/bin/which: no gimp in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin)
```

***

### 4. Quản lý repository và cập nhật hệ thống

**Bước 1:** Trong Software → Repositories → có 3 repo, 1 repo bị Disabled.\
Để bật lại: chọn repo → nhấn Enable (yêu cầu mật khẩu root).

**Bước 2:** Kiểm tra cập nhật qua terminal.

```bash
dnf check-update | wc -l
```

**Output:**

```
27
```

Các repo hiển thị: baseos, appstream, extras.

```bash
dnf update
```

**Output:**

```
Dependencies resolved.
================================================================================
 Package                      Arch     Version                 Repository
================================================================================
Upgrading:
 kernel                       x86_64   4.18.0-553.16.1.el8_10  baseos
 python3-libs                 x86_64   3.6.8-65.el8_10          baseos
 ... (24 more)
Transaction Summary
================================================================================
Upgrade  26 Packages
Total download size: 312 M
Is this ok [y/N]: n
```

**Trả lời:** Có 26 package cần nâng cấp, tổng kích thước 312 MB.

***

**Cập nhật chọn lọc:**

```bash
dnf update sssd*
```

**Output:**

```
Upgrading:
 sssd.x86_64  2.8.2-2.el8  baseos
 sssd-ad.x86_64  2.8.2-2.el8  baseos
 sssd-common.x86_64  2.8.2-2.el8  baseos
Is this ok [y/N]: y
```

***

### 5. Cài đặt phần mềm từ mã nguồn (make, gcc)

**Bước 1:** Cài công cụ cần thiết.

```bash
dnf -y install gcc make
```

**Output:**

```
Installed:
  gcc.x86_64 8.5.0-21.el8
  make.x86_64 1:4.2.1-11.el8
```

```bash
which gcc
which make
```

**Output:**

```
/usr/bin/gcc
/usr/bin/make
```

***

**Bước 2:** Tải ví dụ từ web.

```bash
cd ~/Downloads
wget www.nku.edu/~foxr/make-example.tar.gz
tar xvfz make-example.tar.gz
cd make-example
```

**Output (rút gọn):**

```
main.c
prog1.c
prog2.c
prog1.h
prog2.h
makefile
```

**Tùy chọn tar:**\
x – extract, v – verbose, f – file, z – gzip.

***

**Bước 3:** Biên dịch chương trình.

```bash
make
```

**Output:**

```
gcc -c prog1.c
gcc -c prog2.c
gcc -o prog1 prog1.o
gcc -o prog2 prog2.o
```

```bash
ls
```

**Output:**

```
main.c  prog1  prog1.c  prog1.o  prog2  prog2.c  prog2.o  makefile
```

Chạy chương trình:

```bash
./prog1
```

**Output:**

```
Program 1 is running
```

```bash
./prog2
```

**Output:**

```
Program 2 is running
```

Cài đặt:

```bash
make install
```

**Output:**

```
install -m 755 prog1 /usr/local/bin
install -m 755 prog2 /usr/local/bin
```

Kiểm tra:

```bash
which prog1
```

**Output:**

```
/usr/local/bin/prog1
```

***

### 6. Cài đặt GNU Backgammon từ source

**Bước 1:** Tải và giải nén.

```bash
cd ~/Downloads
tar -xzf gnubg-1.07.001.tar.gz
cd gnubg-1.07.001
```

**Bước 2:** Tạo Makefile và cài đặt.

```bash
./configure --prefix=/usr/local/bg
make
make install
```

**Output (rút gọn):**

```
checking for gcc... gcc
...
config.status: creating Makefile
...
Installing programs under /usr/local/bg
```

***

**Bước 3:** Kiểm tra cài đặt và chạy game.

```bash
cd /usr/local/bg/bin
./gnubg
```

**Output:**

```
warning: could not load weights, neural network disabled
GNU Backgammon 1.07.001
No game.
```

Chạy lại với tùy chọn không dùng weights:

```bash
./gnubg -n
```

**Output:**

```
GNU Backgammon 1.07.001 (No weights)
(No game) 
```

Bắt đầu ván chơi:

```
new game
roll
quit
```

***

**Dọn dẹp:**

```bash
cd ~/Downloads
rm -fr gnubg* make-example*
```

***

**Kết thúc:**

```bash
shutdown now
```
