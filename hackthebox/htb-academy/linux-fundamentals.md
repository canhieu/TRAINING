# Linux Fundamentals

<figure><img src="../../.gitbook/assets/image (495).png" alt=""><figcaption></figcaption></figure>

## I. Cấu trúc Linux trong An ninh mạng

### Tổng quan

Linux là một hệ điều hành (Operating System – OS) được dùng rộng rãi trên máy tính cá nhân, máy chủ, và cả thiết bị di động. Trong lĩnh vực **an ninh mạng**, Linux được coi là một nền tảng cốt lõi nhờ tính **ổn định, linh hoạt và mã nguồn mở**. Để trở thành một chuyên gia bảo mật, việc nắm vững **lịch sử, triết lý, kiến trúc và hệ thống tệp (filesystem hierarchy)** của Linux là bắt buộc – đây chính là “bài học lái xe” đầu tiên trước khi bạn thực sự tham gia vận hành hệ thống.

***

### Linux là gì?

Linux là một **hệ điều hành**, tương tự như Windows, macOS, iOS hoặc Android. OS là lớp phần mềm trung gian quản lý toàn bộ tài nguyên phần cứng, cho phép phần mềm giao tiếp với CPU, RAM, thiết bị lưu trữ, mạng, v.v.

Linux có nhiều **bản phân phối (distribution/distro)** như Ubuntu, Debian, Fedora, RedHat, Kali, Parrot OS… Mỗi distro tối ưu cho các mục đích khác nhau, đặc biệt trong bảo mật và kiểm thử thâm nhập (pentest).

***

### Lịch sử phát triển

* **1970**: Ken Thompson & Dennis Ritchie (AT\&T) phát triển Unix.
* **1977**: BSD (Berkeley Software Distribution) ra đời nhưng vướng kiện tụng với AT\&T.
* **1983**: Richard Stallman khởi xướng **Dự án GNU**, đặt nền móng cho phần mềm tự do (Free Software) và giấy phép **GPL (General Public License)**.
* **1991**: Linus Torvalds, một sinh viên Phần Lan, phát triển kernel Linux đầu tiên như một dự án cá nhân.

Hiện nay, Linux kernel đã có **hơn 23 triệu dòng mã nguồn**, được cấp phép GPLv2. Android – hệ điều hành phổ biến nhất thế giới – cũng được xây dựng trên Linux kernel.

***

### Vì sao Linux quan trọng trong bảo mật?

* **Mã nguồn mở** → Có thể kiểm tra, sửa đổi, vá lỗi nhanh chóng.
* **Bảo mật cao hơn Windows** (ít malware hơn, được cập nhật thường xuyên).
* **Ổn định và hiệu năng cao** → phù hợp cho máy chủ và hệ thống lớn.
* **Tùy biến linh hoạt** → tối ưu cho các môi trường bảo mật, ví dụ như Parrot OS hay Kali Linux.

Tuy nhiên, Linux có đường cong học tập cao, đòi hỏi kiến thức về command line và không hỗ trợ driver phần cứng rộng như Windows.

***

### Triết lý Linux

Triết lý thiết kế Linux xoay quanh **đơn giản, mô-đun, mở**:

1. **Mọi thứ là file** – kể cả thiết bị và cấu hình.
2. **Chương trình nhỏ, chuyên biệt** – mỗi tool làm tốt một việc.
3. **Ghép nối công cụ (pipe)** – kết hợp nhiều tool nhỏ thành nhiệm vụ phức tạp.
4. **Tránh UI giam giữ người dùng** – Linux ưu tiên shell/terminal.
5. **Cấu hình dạng text file** – dễ đọc, dễ sửa, dễ quản lý.

Ví dụ: `/etc/passwd` chứa thông tin user.



***

### Thành phần chính của Linux

| Thành phần              | Mô tả                                                                            |
| ----------------------- | -------------------------------------------------------------------------------- |
| **`Bootloader`**        | Đoạn mã điều khiển quá trình khởi động OS (ví dụ: GRUB).                         |
| **`Kernel`**            | Lõi hệ điều hành, quản lý CPU, RAM, I/O và tài nguyên hệ thống.                  |
| **`Daemon`**            | Các tiến trình chạy nền, phục vụ tác vụ hệ thống (cron, logging, dịch vụ mạng…). |
| **`Shell`**             | Giao diện dòng lệnh, cho phép user tương tác với kernel (Bash, Zsh, Fish…).      |
| **`Graphics server`**   | Hệ thống con đồ họa (X server) để chạy ứng dụng GUI.                             |
| **`Window Manager/DE`** | Giao diện đồ họa: GNOME, KDE, MATE…                                              |
| **`Utilities`**         | Công cụ/hệ thống tiện ích hỗ trợ (ls, grep, iptables…).                          |

***

### Kiến trúc Linux

Linux được phân tầng như sau:

| layer                | Mô tả                                                      |
| -------------------- | ---------------------------------------------------------- |
| **`Hardware`**       | Phần cứng: CPU, RAM, HDD, NIC…                             |
| **`Kernel`**         | Quản lý và ảo hóa tài nguyên, xử lý xung đột tiến trình.   |
| **`Shell`**          | CLI cho phép người dùng nhập lệnh để điều khiển kernel.    |
| **`System Utility`** | Cung cấp đầy đủ chức năng của hệ điều hành cho người dùng. |

***

### Hệ thống tệp (Filesystem Hierarchy – FHS)

<figure><img src="../../.gitbook/assets/image (496).png" alt=""><figcaption></figcaption></figure>

Cấu trúc thư mục Linux dạng cây, bắt đầu từ **/** (root):

| Thư mục  | Mô tả                                           |
| -------- | ----------------------------------------------- |
| `/`      | Root filesystem, chứa file cần để khởi động OS. |
| `/bin`   | Lệnh cơ bản (ls, cat, cp…).                     |
| `/sbin`  | Lệnh quản trị hệ thống.                         |
| `/boot`  | Kernel, bootloader và file khởi động.           |
| `/etc`   | File cấu hình hệ thống & ứng dụng.              |
| `/dev`   | Thiết bị phần cứng (dạng file).                 |
| `/home`  | Thư mục riêng của từng user.                    |
| `/root`  | Home của root user.                             |
| `/lib`   | Thư viện chia sẻ cần cho hệ thống.              |
| `/usr`   | Ứng dụng, thư viện, tài liệu, manpage.          |
| `/var`   | File thay đổi thường xuyên (log, mail, cron).   |
| `/tmp`   | File tạm, xóa khi reboot.                       |
| `/mnt`   | Điểm mount tạm của filesystem.                  |
| `/media` | Mount thiết bị rời (USB, CD).                   |
| `/opt`   | Ứng dụng/phần mềm bên thứ ba.                   |
| `/proc`  | Thông tin runtime của tiến trình/kernel.        |
| `/sys`   | Thông tin runtime về thiết bị & kernel.         |



## II. Linux Distributions

### Khái niệm

**Linux distributions (distros)** là các hệ điều hành được xây dựng dựa trên **Linux kernel**. Mỗi distro được tối ưu cho mục đích riêng – từ **server, embedded system, desktop** cho đến **mobile device**. Có thể hình dung distro giống như các chi nhánh của cùng một công ty:

* **Cùng nhân sự cốt lõi** → các thành phần hệ thống (kernel, shell, daemon, filesystem...).
* **Cùng cơ cấu tổ chức** → kiến trúc Linux.
* **Cùng văn hóa** → triết lý Linux.\
  Nhưng mỗi distro lại cung cấp bộ **package, configuration, tool** khác nhau để phù hợp nhu cầu từng nhóm người dùng.

Một số distro phổ biến: **Ubuntu, Fedora, CentOS, Debian, Red Hat Enterprise Linux (RHEL)**.

***

### Ưu điểm khi sử dụng Linux distros

* **Miễn phí & mã nguồn mở** → phù hợp desktop user.
* **Tùy biến cao** → tối ưu theo use case cụ thể.
* **Ổn định, bảo mật, cập nhật thường xuyên** → phù hợp cho server.
* **Phù hợp với security** → mã nguồn mở cho phép kiểm tra, tối ưu và cấu hình theo nhu cầu riêng.

Linux distros có thể dùng trong:

* **Web server**
* **Mobile device**
* **Embedded system**
* **Cloud computing**
* **Desktop computing**

***

### Các distro thông dụng trong Cyber Security

| Distro              | Đặc điểm                                                                                  |
| ------------------- | ----------------------------------------------------------------------------------------- |
| **ParrotOS**        | Dựa trên Debian, tập trung vào security, privacy & development.                           |
| **Ubuntu**          | Phổ biến cho desktop, dễ dùng cho beginner.                                               |
| **Debian**          | Ổn định, bảo mật, lâu dài → phù hợp cho server & embedded system.                         |
| **Raspberry Pi OS** | Tối ưu cho phần cứng nhỏ gọn Raspberry Pi.                                                |
| **CentOS**          | Miễn phí, ổn định, phù hợp enterprise server.                                             |
| **BackBox**         | Distro bảo mật, tối ưu cho pentesting.                                                    |
| **BlackArch**       | Dựa trên Arch, có nhiều security tool nâng cao.                                           |
| **Pentoo**          | Dựa trên Gentoo, tập trung vào penetration testing.                                       |
| **Kali Linux**      | Distro nổi tiếng nhất cho security specialist, chứa hàng trăm công cụ pentest & forensic. |

***

### Debian – Distro tiêu biểu

| Đặc điểm                    | Mô tả                                                                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Ổn định & tin cậy**       | Debian nổi tiếng nhờ độ ổn định, phù hợp desktop, server và embedded system.                                                    |
| **Package management**      | Sử dụng **APT (Advanced Package Tool)** để quản lý package, update & security patch. Có thể cập nhật thủ công hoặc tự động.     |
| **Learning curve**          | Khó hơn so với Ubuntu/Fedora, nhưng linh hoạt và cho phép tùy biến sâu.                                                         |
| **Control**                 | Người dùng nâng cao có thể kiểm soát hệ thống ở mức chi tiết.                                                                   |
| **Long-term support (LTS)** | Bản phát hành LTS được hỗ trợ tới 5 năm (update & security patch).                                                              |
| **Security track record**   | Mặc dù từng có **vulnerability**, Debian community luôn phát hành patch nhanh chóng. Cam kết mạnh mẽ về **security & privacy**. |

#### Nhận xét

Debian là một distro **đa năng, đáng tin cậy và an toàn**, phù hợp cho nhiều mục đích, đặc biệt trong môi trường **cyber security**.



## III. Introduction to Shell

### Tại sao phải học Shell?

Trong lĩnh vực **cyber security**, việc thành thạo **Linux shell** là bắt buộc.

* **Server Linux** được dùng rất nhiều (đặc biệt là **web server**) vì ổn định, ít lỗi hơn Windows server.
* Để kiểm soát hệ thống hiệu quả, cần nắm vững **shell** – thành phần cốt lõi của Linux.

Ví dụ: khi bạn mới chuyển từ Windows sang Linux, giao diện làm việc chính sẽ là **terminal** thay vì GUI quen thuộc.

***

### Shell & Terminal

| Thuật ngữ        | Mô tả                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------------- |
| **Shell**        | Giao diện dòng lệnh (CLI) cho phép người dùng nhập lệnh để tương tác trực tiếp với **kernel**. |
| **Terminal**     | Ứng dụng hoặc cửa sổ cung cấp giao diện nhập/xuất (I/O) text để làm việc với shell.            |
| **Console**      | Thường chỉ màn hình ở chế độ text (không phải cửa sổ trong GUI).                               |
| **Command Line** | Cách người dùng nhập lệnh vào shell để điều khiển hệ thống.                                    |

👉 Có thể hình dung **shell giống như một GUI dạng text**: bạn nhập lệnh để điều hướng thư mục, thao tác file, lấy thông tin hệ thống… nhưng với nhiều quyền năng hơn GUI thông thường.

<figure><img src="../../.gitbook/assets/image (497).png" alt=""><figcaption></figcaption></figure>

***

### Terminal Emulators

**Terminal emulator** là phần mềm giả lập chức năng của terminal, cho phép dùng các chương trình text-based trong môi trường **GUI**.

* **Terminal emulator** = “bàn lễ tân ảo”: bạn gửi yêu cầu (lệnh) tới shell.
* Có thể mở nhiều CLI trong cùng một terminal (giống như có nhiều “bàn lễ tân” cùng lúc).
* Dùng khi bạn làm việc remote, thay thế cho terminal vật lý.

<figure><img src="../../.gitbook/assets/image (498).png" alt=""><figcaption></figcaption></figure>

#### Multiplexer (ví dụ: Tmux)

* Cho phép **chia terminal thành nhiều cửa sổ/pane**.
* Làm việc ở nhiều thư mục, session, workspace cùng lúc.
* Hữu ích trong pentest khi cần chạy song song nhiều tool (ví dụ: BloodHound, Impacket, SecLists…).

***

### Các loại Shell

| Shell                                 | Đặc điểm                                                                                                                     |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Bash (Bourne-Again Shell)**         | Phổ biến nhất, thuộc GNU project. Hầu hết task trong GUI đều có thể làm bằng Bash. Cho phép script hóa, tự động hóa process. |
| **Tcsh/Csh**                          | Shell kiểu C, có cú pháp giống ngôn ngữ lập trình C.                                                                         |
| **Ksh (KornShell)**                   | Tối ưu cho script phức tạp, kết hợp đặc điểm của Csh và Bash.                                                                |
| **Zsh**                               | Linh hoạt, nhiều tính năng nâng cao (auto-completion, theme).                                                                |
| **Fish (Friendly Interactive Shell)** | Tương tác trực quan, cú pháp dễ đọc, hỗ trợ highlight và gợi ý lệnh.                                                         |

👉 Với security specialist, **Bash và Zsh** là hai lựa chọn phổ biến nhất.

***

#### Kết luận

* **Shell = công cụ mạnh nhất trong Linux** để kiểm soát hệ thống.
* **Terminal emulator & multiplexer** (như Tmux) giúp tối ưu hiệu suất làm việc.
* Nắm vững **Bash** (và ít nhất một shell khác như Zsh) sẽ mở ra khả năng script hóa, automation và quản trị hệ thống hiệu quả trong môi trường security.



## IV. Bash Prompt Description

### Khái niệm cơ bản

* **Bash prompt** là chuỗi ký tự hiển thị trong terminal, báo hiệu hệ thống đã sẵn sàng nhận lệnh.
* Theo mặc định, prompt thường hiển thị:
  * **username** (ai đang đăng nhập)
  * **hostname** (tên máy tính)
  * **current working directory** (thư mục hiện tại)
* Sau prompt, con trỏ (cursor) sẽ nhấp nháy chờ bạn nhập lệnh.

Ví dụ prompt mặc định:

<figure><img src="../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>

```
<username>@<hostname><current working directory>$
```

<figure><img src="../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>

Khi ở **home directory**, thư mục được ký hiệu bằng dấu ngã `~`:

```
<username>@<hostname>[~]$
```

* **Dấu `$`** → user bình thường.
* **Dấu `#`** → root user.

Ví dụ:

<figure><img src="../../.gitbook/assets/image (502).png" alt=""><figcaption></figcaption></figure>

***

### Prompt khi thiếu thông tin

Trong một số trường hợp (ví dụ upload shell trên target machine), prompt có thể không hiển thị username/hostname/path vì **biến môi trường PS1** chưa được cấu hình đúng.

* **Unprivileged user prompt**:

```
$
```

* **Privileged (root) prompt**:

```
#
```

***

### Biến môi trường **PS1**

* **PS1 variable** quyết định format của prompt trong Linux.
* Có thể **customize** để hiển thị: username, hostname, thư mục, màu sắc, IP, thời gian…
* Điều này đặc biệt hữu ích trong **penetration test**, giúp bạn theo dõi chính xác bối cảnh làm việc.
* Việc customize thường được thực hiện trong file cấu hình **.bashrc**.

***

### Special Characters trong PS1

<figure><img src="../../.gitbook/assets/image (503).png" alt=""><figcaption></figcaption></figure>

| Ký hiệu        | Ý nghĩa                                 |
| -------------- | --------------------------------------- |
| `\d`           | Ngày (Mon Feb 6)                        |
| `\D{%Y-%m-%d}` | Ngày định dạng (YYYY-MM-DD)             |
| `\H`           | Full hostname                           |
| `\j`           | Số lượng job do shell quản lý           |
| `\n`           | Newline                                 |
| `\r`           | Carriage return                         |
| `\s`           | Tên shell                               |
| `\t`           | Thời gian hiện tại 24h (HH:MM:SS)       |
| `\T`           | Thời gian hiện tại 12h (HH:MM:SS)       |
| `\@`           | Thời gian (am/pm)                       |
| `\u`           | Username hiện tại                       |
| `\w`           | Full path của current working directory |

***

### Ứng dụng trong security

* Hiển thị **full path** thay vì chỉ tên thư mục → tránh nhầm lẫn khi di chuyển giữa nhiều system.
* Thêm **IP target** vào prompt → tiện theo dõi khi pentest nhiều host.
* Dùng **script** hoặc kiểm tra file **.bash\_history** để lưu toàn bộ command (tài liệu hóa & phân tích sau).



## V. Getting Help trong Linux

### Tại sao cần học cách tìm trợ giúp?

* Trong Linux, chúng ta sẽ thường xuyên gặp **command/tool mới** hoặc các **option/parameter** mà không nhớ được hết.
* Việc biết cách tự tra cứu sẽ giúp:
  * Hiểu rõ chức năng của tool trước khi sử dụng.
  * Tìm ra **trick** hoặc cách dùng nâng cao.
  * Giảm rủi ro khi chạy lệnh không mong muốn trong môi trường **security**.

***

### Các cách phổ biến để lấy trợ giúp

| Phương pháp   | Mô tả                                                      | Ví dụ          |
| ------------- | ---------------------------------------------------------- | -------------- |
| **man pages** | Xem **manual pages** – tài liệu chi tiết cho từng command. | `man ls`       |
| **--help**    | Hiển thị hướng dẫn nhanh, liệt kê option/parameter.        | `ls --help`    |
| **-h**        | Một số tool hỗ trợ tùy chọn **-h** thay cho `--help`.      | `curl -h`      |
| **apropos**   | Tìm kiếm keyword trong mô tả các man pages.                | `apropos sudo` |

***

### Ví dụ minh họa

#### 1. Dùng **ls**

```bash
canhieu@htb[/htb]$ ls
Desktop  Documents  Downloads  Music  Pictures
```

👉 `ls` = **list directory contents**, hiển thị file/thư mục trong thư mục hiện tại.

#### 2. Xem man page

```bash
canhieu@htb[/htb]$ man ls
```

Hiển thị tài liệu chi tiết:

```
NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...
```

#### 3. Xem help nhanh

```bash
canhieu@htb[/htb]$ ls --help
```

Hiển thị tùy chọn chính:

```
-a, --all        do not ignore entries starting with .
-A, --almost-all do not list implied . and ..
...
```

#### 4. Với tool khác (curl)

```bash
canhieu@htb[/htb]$ curl -h
```

👉 Một số tool cho kết quả giống `--help`, nhưng ngắn gọn hơn.

#### 5. Tìm bằng apropos

```bash
canhieu@htb[/htb]$ apropos sudo
```

Kết quả:

```
sudo (8)        - execute a command as another user
sudo.conf (5)   - configuration for sudo front end
sudoers (5)     - default sudo security policy plugin
...
```

***

### Công cụ hỗ trợ ngoài lệnh cơ bản

* 🔗 [explainshell.com](https://explainshell.com/?utm_source=chatgpt.com) → Giải thích từng phần của một lệnh dài, rất hữu ích cho beginner.
* **.bash\_history** → Kiểm tra lại lệnh đã chạy.
* **script** → Ghi log toàn bộ session, phục vụ tài liệu hóa & forensic.

### Lời khuyên cho security specialist

* Luôn xem **man pages** trước khi dùng tool quan trọng (ví dụ: `nmap`, `tcpdump`, `iptables`).
* Dùng **--help/-h** khi chỉ cần liệt kê nhanh các option.
* Tập thói quen **apropos** để tìm tool phù hợp thay vì nhớ hết command.
* Ghi chú & lưu lại kết quả command để phục vụ **reporting trong pentest/DFIR**.



## VI. System Information

### Mục tiêu

* Làm quen với việc thu thập **thông tin hệ thống** bằng command-line.
* Quan trọng cho:
  * **Linux administration** (quản trị hệ thống).
  * **Security assessment** (đánh giá bảo mật).
  * **Privilege escalation** (leo thang đặc quyền).
  * **Incident response** (ứng phó sự cố).

👉 Thông tin này giúp phát hiện **cấu hình sai (misconfiguration)** hoặc **vulnerability** có thể khai thác.

***

### Các command cơ bản để lấy thông tin hệ thống

| Command    | Mô tả                                          |
| ---------- | ---------------------------------------------- |
| `whoami`   | Hiển thị username hiện tại.                    |
| `id`       | Trả về UID, GID và group membership.           |
| `hostname` | In/thiết lập tên máy tính.                     |
| `uname`    | In thông tin OS & phần cứng.                   |
| `pwd`      | Hiển thị thư mục làm việc hiện tại.            |
| `ifconfig` | Quản lý & hiển thị cấu hình network interface. |
| `ip`       | Hiển thị/chỉnh sửa routing, interface, tunnel. |
| `netstat`  | Hiển thị trạng thái kết nối mạng.              |
| `ss`       | Công cụ hiện đại để phân tích socket.          |
| `ps`       | Hiển thị trạng thái process.                   |
| `who`      | Ai đang login vào hệ thống.                    |
| `env`      | Hiển thị biến môi trường.                      |
| `lsblk`    | Liệt kê block device.                          |
| `lsusb`    | Liệt kê USB device.                            |
| `lsof`     | Liệt kê các file đang mở.                      |
| `lspci`    | Liệt kê PCI device.                            |

***

### Kết nối từ xa bằng SSH

* **SSH (Secure Shell)** = protocol chuẩn để quản lý máy chủ Linux từ xa.
* Ưu điểm: **an toàn, nhẹ, không cần GUI**.

Cú pháp:

```bash
ssh <username>@<IP>
```

Ví dụ:

```bash
canhieu@htb[/htb]$ ssh htb-student@10.10.10.10
```

***

### Ví dụ thực hành

#### 1. `hostname`

```bash
canhieu@DESKTOP-DBGES7N:~$ hostname
DESKTOP-DBGES7N
```

👉 Cho biết tên máy đang kết nối.

***

#### 2. `whoami`

```bash
canhieu@DESKTOP-DBGES7N:~$ whoami
canhieu
```

👉 Xác định user hiện tại → quan trọng trong **reverse shell** để biết quyền hạn ban đầu.

***

#### 3. `id`

Lệnh **`id`** mở rộng chức năng của **`whoami`**, cho phép in ra toàn bộ **group membership** và **ID** của user hiện tại.

* Với **penetration tester**, đây là thông tin cực kỳ quan trọng để xác định **quyền truy cập của user** có thể khai thác.
* Với **sysadmin**, lệnh này hỗ trợ việc **audit permission** và **group membership** của account trong hệ thống.

```bash
canhieu@DESKTOP-DBGES7N:~$ id
uid=1000(canhieu) gid=1000(canhieu) groups=1000(canhieu),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),1001(docker)
```

* **uid/gid** → định danh user & group.
* **sudo group** → user có thể chạy lệnh với quyền root → cơ hội **privilege escalation**.
* **adm group** → đọc log nhạy cảm trong `/var/log`.

| Group             | Ý nghĩa                                   | Liên quan đến Security                                                                                                                              |
| ----------------- | ----------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **adm (4)**       | Cho phép đọc log trong `/var/log`.        | Có thể đọc file log chứa credential, token, key → nguy cơ **information disclosure**.                                                               |
| **cdrom (24)**    | Quyền truy cập CD-ROM.                    | Thường ít rủi ro, nhưng có thể mount thiết bị để đọc/ghi dữ liệu.                                                                                   |
| **sudo (27)**     | Cho phép chạy command với quyền **root**. | **Privilege escalation vector** số 1. Nếu user trong group này → cần audit ngay.                                                                    |
| **dip (30)**      | Quản lý network (PPP, dial-up).           | Có thể thay đổi routing, cấu hình mạng → nguy cơ DoS/MITM.                                                                                          |
| **plugdev (46)**  | Cho phép gắn thiết bị USB/hotplug.        | Có thể mount USB, copy dữ liệu, hoặc cắm thiết bị độc hại.                                                                                          |
| **users (100)**   | Group chung cho user thông thường.        | Bình thường không quá rủi ro.                                                                                                                       |
| **docker (1001)** | Cho phép chạy container Docker.           | **Rất nguy hiểm**: user trong group này có thể escape container và truy cập root trên host (`docker run -v /:/mnt --rm -it alpine chroot /mnt sh`). |

***

#### 4. `uname`

Xem thông tin kernel & OS.

```bash
cry0l1t3@htb[/htb]$ uname -a
Linux box 4.15.0-99-generic #100-Ubuntu SMP Wed Apr 22 20:32:56 UTC 2020 x86_64 GNU/Linux
```

* Kernel name: `Linux`
* Hostname: `box`
* Kernel release: `4.15.0-99-generic`
* OS: `GNU/Linux`

👉 Dùng `uname -r` để lấy **kernel release** → phục vụ tra cứu exploit:

```bash
cry0l1t3@htb[/htb]$ uname -r
4.15.0-99-generic
```

Ví dụ: tìm `"4.15.0-99-generic exploit"` có thể dẫn đến kernel vulnerability.



## VII. Navigation&#x20;

### Giới thiệu

* **Navigation** trong Linux giống như việc dùng chuột trong Windows.
* Giúp chúng ta **di chuyển trong hệ thống, thao tác với file & directory**.
* Bao gồm: liệt kê, tạo, di chuyển, chỉnh sửa, xóa, tìm kiếm file/directory.
* Ngoài ra còn có **redirect, file descriptor**, và các phím tắt giúp làm việc nhanh hơn.
* 👉 Nên thực hành trên **VM riêng** và **tạo snapshot** trước khi thử nghiệm.

***

### Xác định vị trí hiện tại

**`pwd`** – Print Working Directory

```bash
cry0l1t3@htb[~]$ pwd
/home/cry0l1t3
```

→ Cho biết thư mục hiện tại.

***

### Liệt kê nội dung thư mục

**`ls`** – list directory contents

```bash
cry0l1t3@htb[~]$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

* Mặc định chỉ hiển thị tên file/thư mục.
* Dùng **option** để có thêm thông tin.

#### `ls -l` – hiển thị chi tiết

```bash
cry0l1t3@htb[~]$ ls -l
total 32
drwxr-xr-x 2 cry0l1t3 htbacademy 4096 Nov 13 17:37 Desktop
...
```

| Cột            | Nội dung                | Mô tả                                       |
| -------------- | ----------------------- | ------------------------------------------- |
| `drwxr-xr-x`   | Loại & permission       | `d` = directory, `rwx` = read/write/execute |
| `2`            | Hard links              | Số hard link tới object                     |
| `cry0l1t3`     | Owner                   | Chủ sở hữu                                  |
| `htbacademy`   | Group                   | Nhóm sở hữu                                 |
| `4096`         | Kích thước (byte/block) | Với directory = metadata size               |
| `Nov 13 17:37` | Thời gian               | Last modified                               |
| `Desktop`      | Tên file/thư mục        |                                             |

📌 Security note: `ls -l` rất quan trọng để kiểm tra **permission**, phát hiện **misconfiguration** (ví dụ world-writable file).

***

#### `ls -la` – hiển thị cả hidden files

```bash
cry0l1t3@htb[~]$ ls -la
total 403188
-rw------- 1 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bash_history
-rw-r--r-- 1 cry0l1t3 htbacademy 4096 Nov 13 17:37 .bashrc
...
```

* Hidden file bắt đầu bằng dấu `.`.
* 📌 Trong pentest: `.bash_history`, `.ssh/`, `.config/` thường chứa **credential, key, token**.

***

#### `ls <path>` – chỉ định đường dẫn

```bash
cry0l1t3@htb[~]$ ls -l /var/
total 52
drwxr-xr-x  2 root root     4096 May 15 18:54 backups
drwxr-xr-x 18 root root     4096 Nov 15 16:55 cache
...
```

👉 Không cần `cd`, có thể liệt kê nội dung thư mục bất kỳ.

***

### Di chuyển giữa thư mục

**`cd` – change directory**

* Đi thẳng vào `/dev/shm`:

```bash
cry0l1t3@htb[~]$ cd /dev/shm
cry0l1t3@htb[/dev/shm]$
```

* Quay lại thư mục trước:

```bash
cry0l1t3@htb[/dev/shm]$ cd -
cry0l1t3@htb[~]$
```

* Lên thư mục cha:

```bash
cry0l1t3@htb[/dev/shm]$ cd ..
cry0l1t3@htb[/dev]$
```

***

### Auto-complete bằng \[TAB]

* Gõ `cd /dev/s` + `[TAB][TAB]`:

```bash
shm/ snd/
```

* Gõ thêm `h` thành `cd /dev/sh` + `[TAB]` → shell tự hoàn thành `/dev/shm`.

👉 Giúp tiết kiệm thời gian, tránh sai cú pháp.

***

### Dấu `.` và `..`

```bash
cry0l1t3@htb[/dev/shm]$ ls -la /dev/shm
total 0
drwxrwxrwt  2 root root   40 May 15 18:31 .
drwxr-xr-x 17 root root 4000 May 14 20:45 ..
```

* `.` = thư mục hiện tại.
* `..` = thư mục cha.

***

### Xóa nội dung terminal

* Dùng `clear`:

```bash
cry0l1t3@htb[/dev]$ cd shm && clear
```

* Shortcut: `[Ctrl] + [L]`.

***

### History

* Dùng `↑` hoặc `↓` → duyệt lệnh đã chạy.
* Dùng `[Ctrl] + [R]` → search lệnh cũ theo từ khóa.
* 📌 Trong pentest: `history` và file `.bash_history` thường chứa **password, token, private key** → cực kỳ giá trị.

<figure><img src="../../.gitbook/assets/image (504).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (505).png" alt=""><figcaption></figcaption></figure>



## VIII. Working with Files and Directories

### Khác biệt giữa Windows và Linux

* Trong **Windows**, ta dùng **GUI (Explorer)** để tìm, mở, chỉnh sửa file.
* Trong **Linux**, ta thao tác trực tiếp bằng **terminal command**:
  * Nhanh hơn, hiệu quả hơn.
  * Có thể áp dụng **regex**, **chaining command**, **redirect** → xử lý file hàng loạt.
* Đây là lý do quản trị hệ thống & pentester thường ưu tiên CLI thay vì GUI.

***

### Tạo file và thư mục

#### `touch` – Tạo file rỗng

```bash
canhieu@htb[/htb]$ touch info.txt
```

#### `mkdir` – Tạo directory

```bash
canhieu@htb[/htb]$ mkdir Storage
```

#### `mkdir -p` – Tạo directory cha lồng nhau

```bash
canhieu@htb[/htb]$ mkdir -p Storage/local/user/documents
```

Kiểm tra cấu trúc bằng **tree**:

```bash
canhieu@htb[/htb]$ tree .
.
├── info.txt
└── Storage
    └── local
        └── user
            └── documents

4 directories, 1 file
```

📌 **Note Security**: Directory structure có thể tiết lộ cách hệ thống tổ chức dữ liệu. Với attacker, việc hiểu layout giúp tìm file config, credential.

***

### Tạo file trong thư mục cụ thể

```bash
canhieu@htb[/htb]$ touch ./Storage/local/user/userinfo.txt
```

```bash
canhieu@htb[/htb]$ tree .
.
├── info.txt
└── Storage
    └── local
        └── user
            ├── documents
            └── userinfo.txt

4 directories, 2 files
```

👉 Dấu `.` = thư mục hiện tại.

***

### Di chuyển & đổi tên file

#### `mv` – Move hoặc Rename

* Đổi tên file:

```bash
canhieu@htb[/htb]$ mv info.txt information.txt
```

* Di chuyển file:

```bash
canhieu@htb[/htb]$ mv information.txt Storage/
```

***

### Tạo & copy file

* Tạo file `readme.txt`:

```bash
canhieu@htb[/htb]$ touch readme.txt
```

* Di chuyển nhiều file vào thư mục:

```bash
canhieu@htb[/htb]$ mv information.txt readme.txt Storage/
```

Cấu trúc sau khi di chuyển:

```bash
canhieu@htb[/htb]$ tree .
.
└── Storage
    ├── information.txt
    ├── local
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt
```

* Copy file sang thư mục khác:

```bash
canhieu@htb[/htb]$ cp Storage/readme.txt Storage/local/
```

Cấu trúc:

```bash
canhieu@htb[/htb]$ tree .
.
└── Storage
    ├── information.txt
    ├── local
    │   ├── readme.txt
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt
```

📌 **Note Security**: Copy/move file có thể bị lạm dụng để **che giấu file độc hại** trong thư mục hợp pháp. Audit filesystem thường kiểm tra các hành vi này.

***

### Redirection & Text Editors

Ngoài các thao tác cơ bản:

* **Redirection (`>`, `>>`, `<`)**: dùng để tạo, ghi đè, append nội dung file.
* **Editor**: `vim`, `nano` cho chỉnh sửa trực tiếp.
* **Script automation**: kết hợp nhiều lệnh để quản lý file hàng loạt.



## XI. Editing Files trong Linux

### Giới thiệu

* Sau khi biết cách **tạo file/directory**, bước tiếp theo là **chỉnh sửa nội dung file**.
* Trên Linux có nhiều text editor: **Vi, Vim, Nano**.
* Trong security, chỉnh sửa file quan trọng (như config, log, passwd) là kỹ năng cốt lõi để:
  * **Kiểm tra cấu hình**.
  * **Chèn backdoor** (trong pentest).
  * **Sửa lỗi permission/misconfiguration** (khi làm sysadmin).

***

### Nano Editor

#### Tạo và mở file bằng Nano

```bash
canhieu@htb[/htb]$ nano notes.txt
```

* Tạo và mở file `notes.txt` trong Nano.
* Giao diện đơn giản, dễ dùng cho beginner.

#### Giao diện Nano

<figure><img src="../../.gitbook/assets/image (506).png" alt=""><figcaption></figcaption></figure>

```
GNU nano 2.9.3                                    notes.txt
Here we can type everything we want and make our notes.▓

^G Get Help    ^O Write Out   ^W Where Is    ^K Cut Text    ^J Justify     ^C Cur Pos
^X Exit        ^R Read File   ^\ Replace     ^U Uncut Text  ^T To Spell    ^_ Go To Line
```

* `^` = phím **CTRL**.
* Ví dụ: `[CTRL+W]` = search.

#### Tìm kiếm trong Nano

* `[CTRL+W]` → nhập từ khóa → ENTER.
* Nhấn tiếp `[CTRL+W] + ENTER` để nhảy đến kết quả tiếp theo.

#### Lưu & thoát

* `[CTRL+O]` → Save file.
* `[CTRL+X]` → Exit editor.

#### Xem nội dung file

```bash
canhieu@htb[/htb]$ cat notes.txt
Here we can type everything we want and make our notes.
```

***

### File quan trọng trong Security

* **/etc/passwd**
  * Chứa thông tin user: `username`, `UID`, `GID`, `home directory`.
  * Trước đây lưu cả **password hash**, nhưng nay hash được tách sang `/etc/shadow`.
  * Nếu permission của `/etc/passwd` hoặc `/etc/shadow` bị sai → nguy cơ **privilege escalation**.

📌 **Security Note**: Trong pentest, việc kiểm tra quyền truy cập file hệ thống (`ls -l /etc/passwd`, `ls -l /etc/shadow`) rất quan trọng để phát hiện **misconfiguration**.

***

### Vim Editor

#### Khái niệm

* **Vim** = Vi IMproved, một text editor mạnh mẽ, phổ biến trong Linux.
* Tuân thủ triết lý Unix: “nhiều tool nhỏ, mỗi tool làm tốt một việc”.
* Có thể kết hợp với tool khác (**grep, awk, sed**) → rất linh hoạt trong automation & security scripting.

#### Mở Vim

```bash
canhieu@htb[/htb]$ vim
```

***

#### Các **mode** trong Vim

| Mode                  | Mô tả                                                                                |
| --------------------- | ------------------------------------------------------------------------------------ |
| **Normal**            | Mặc định khi mở Vim. Mọi phím nhập vào là **command**, không phải text.              |
| **Insert**            | Chèn text vào buffer (giống editor bình thường).                                     |
| **Visual**            | Chọn đoạn text, sau đó có thể copy, delete, replace.                                 |
| **Command (Ex mode)** | Nhập lệnh ở dòng dưới cùng (bắt đầu bằng `:`), ví dụ `:q` thoát, `:wq` lưu và thoát. |
| **Replace**           | Ghi đè text cũ bằng text mới.                                                        |
| **Ex**                | Chế độ nâng cao, thực thi nhiều lệnh mà không quay lại Normal mode.                  |

📌 **Note Security**: Vim thường có mặt trên mọi hệ thống Linux. Nếu attacker có shell nhưng không có nano, vi/vim gần như chắc chắn tồn tại. Có thể tận dụng để **xem & chỉnh sửa file hệ thống**.

***

#### Thoát khỏi Vim

* `:q` → thoát.
* `:wq` → lưu rồi thoát.

***

### Vimtutor

Vim có chương trình training tích hợp:

```bash
canhieu@htb[/htb]$ vimtutor
```

* Hướng dẫn cơ bản để thực hành thao tác.
* Thời gian học \~25-30 phút.
* Rất khuyến nghị cho beginner vì Vim ban đầu khá khó nhưng cực kỳ hiệu quả về sau.

***

### Kết luận

* **Nano**: dễ dùng, tốt cho beginner, nhanh để chỉnh sửa file đơn giản.
* **Vim**: mạnh mẽ, phổ biến, cần học nhưng cực kỳ hữu ích cho sysadmin & pentester.
* Pentester cần quen với cả hai, đặc biệt là Vim vì gần như luôn có sẵn.
* Các file hệ thống quan trọng (**/etc/passwd, /etc/shadow, /etc/sudoers**) phải luôn được audit permission → sai sót có thể dẫn đến **privilege escalation**.



## X. Find Files and Directories trong Linux

### Tầm quan trọng

* Khi đã truy cập vào hệ thống Linux (qua SSH hoặc reverse shell), một bước **cốt lõi trong enumeration** là tìm kiếm:
  * **Configuration files** (chứa credential, API key, password).
  * **Script của user/admin** (chứa lệnh nhạy cảm, hardcoded secret).
  * **Hidden directories hoặc binary**.
* Không thể ngồi duyệt thủ công → cần dùng tool hỗ trợ.

***

### `which` – Xác định đường dẫn của program

```bash
canhieu@htb[/htb]$ which python
/usr/bin/python
```

* Trả về đường dẫn đầy đủ của binary được thực thi.
* Nếu không tồn tại → không trả về gì.

📌 **Security Note**: Dùng `which` để kiểm tra các công cụ quan trọng cho pentest có tồn tại không:

* `python`, `gcc`, `nc` (netcat), `curl`, `wget`.
* Nếu có → attacker có thể dùng để **chạy exploit, download payload, hoặc compile code** ngay trên target.

***

### `find` – Tìm file & directory nâng cao

#### Cú pháp

```bash
find <location> <options>
```

#### Ví dụ

```bash
canhieu@htb[/htb]$ find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null
```

#### Output mẫu

```
-rw-r--r-- 1 root root 136392 Apr 25 20:29 /usr/src/linux-headers.../auto.conf
-rw-r--r-- 1 root root  82290 Apr 25 20:29 /usr/src/linux-headers.../tristate.conf
-rw-r--r-- 1 root root  25086 Mar  4 22:04 /etc/dnsmasq.conf
-rw-r--r-- 1 root root  21254 May  2 11:59 /etc/sqlmap/sqlmap.conf
...
```

#### Giải thích option

| Option                | Mô tả                                                                 |
| --------------------- | --------------------------------------------------------------------- |
| `-type f`             | Chỉ tìm **file** (có thể thay `d` = directory).                       |
| `-name *.conf`        | Lọc theo tên (tất cả file kết thúc bằng `.conf`).                     |
| `-user root`          | Chỉ lấy file owner = root.                                            |
| `-size +20k`          | File lớn hơn 20 KiB.                                                  |
| `-newermt 2020-03-03` | File mới hơn ngày 03/03/2020.                                         |
| `-exec ls -al {} \;`  | Với mỗi file tìm được, thực thi `ls -al`. `{}` = placeholder kết quả. |
| `2>/dev/null`         | Redirect STDERR vào `/dev/null` để ẩn lỗi (ví dụ Permission denied).  |

📌 **Security Note**: `find` là vũ khí cực mạnh để pentester **locate sensitive file** nhanh chóng. Ví dụ:

* `find / -perm -4000 -type f 2>/dev/null` → tìm SUID binary → vector **privilege escalation**.
* `find / -name id_rsa 2>/dev/null` → tìm private SSH key.
* `find / -name "*.log" -size +1M` → tìm log lớn có thể chứa data nhạy cảm.

***

### `locate` – Tìm file bằng database

#### Update database

```bash
canhieu@htb[/htb]$ sudo updatedb
```

#### Tìm nhanh file `.conf`

```bash
canhieu@htb[/htb]$ locate *.conf
/etc/GeoIP.conf
/etc/NetworkManager/NetworkManager.conf
/etc/UPower/UPower.conf
/etc/adduser.conf
...
```

* **Nhanh hơn `find`** vì tra cứu từ database (`/var/lib/mlocate/mlocate.db`).
* Nhưng **ít filter hơn** → không lọc được theo size, owner, date…

📌 **Security Note**: Nếu attacker có quyền chạy `updatedb` (hoặc database chưa được cập nhật), `locate` có thể tiết lộ vị trí nhạy cảm mà `find` cần nhiều thời gian mới ra.

***

### So sánh nhanh `find` vs `locate`

| Command    | Ưu điểm                                                                                    | Nhược điểm                                                 |
| ---------- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
| **find**   | Rất mạnh, nhiều filter (size, date, permission, owner). Dùng cho **enumeration chi tiết**. | Chạy lâu, có thể spam “Permission denied”.                 |
| **locate** | Tìm cực nhanh (tra cứu DB).                                                                | Không có nhiều filter, kết quả phụ thuộc DB đã update chưa |

<figure><img src="../../.gitbook/assets/image (509).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (510).png" alt=""><figcaption></figcaption></figure>



## XI. File Descriptors và Redirections trong Linux

### Khái niệm cơ bản

* **File Descriptor (FD)** = định danh duy nhất do **kernel** quản lý, đại diện cho một kết nối I/O (file, socket, pipe…).
* Trong Windows khái niệm này gọi là **file handle**.
* Ví dụ: như “số vé giữ áo” → OS biết file nào đang mở và điều khiển dữ liệu đi đâu.

#### FD mặc định trong Linux

| FD    | Tên    | Loại dữ liệu                        |
| ----- | ------ | ----------------------------------- |
| **0** | STDIN  | Input (bàn phím, file)              |
| **1** | STDOUT | Output chuẩn (hiển thị ra terminal) |
| **2** | STDERR | Output lỗi                          |

***

### STDIN và STDOUT

Ví dụ với `cat`:

Xanh là input từ bàn phím , Đỏ là output trả ra terminal

<figure><img src="../../.gitbook/assets/image (511).png" alt=""><figcaption></figcaption></figure>

```bash
htb-student@nixfund$ cat
SOME INPUT
SOME INPUT
```

* Input từ bàn phím = **STDIN (FD 0)**.
* Output in lại trên terminal = **STDOUT (FD 1)**.

***

### STDOUT và STDERR

Ví dụ với `find`:

<figure><img src="../../.gitbook/assets/image (512).png" alt=""><figcaption></figcaption></figure>

```bash
canhieu@htb[/htb]$ find /etc/ -name shadow
/etc/shadow
find: ‘/etc/...’: Permission denied
```

* `/etc/shadow` → **STDOUT (FD 1)**.
* “Permission denied” → **STDERR (FD 2)**.

Ẩn error bằng redirect:

```bash
canhieu@htb[/htb]$ find /etc/ -name shadow 2>/dev/null
/etc/shadow
```

***

### Redirect STDOUT ra file

```bash
canhieu@htb[/htb]$ find /etc/ -name shadow 2>/dev/null > results.txt
canhieu@htb[/htb]$ cat results.txt
/etc/shadow
```

* STDOUT (`/etc/shadow`) ghi vào `results.txt`.
* STDERR bị loại bỏ.

***

### Redirect STDOUT và STDERR ra file riêng

<figure><img src="../../.gitbook/assets/image (513).png" alt=""><figcaption></figcaption></figure>

```bash
canhieu@htb[/htb]$ find /etc/ -name shadow 2> stderr.txt 1> stdout.txt
```

* **stdout.txt** → chứa STDOUT.
* **stderr.txt** → chứa lỗi Permission denied.

***

### Redirect STDIN từ file

```bash
canhieu@htb[/htb]$ cat < stdout.txt
/etc/shadow
```

* STDIN lấy từ file thay vì bàn phím.

***

### Append STDOUT thay vì overwrite

```bash
canhieu@htb[/htb]$ find /etc/ -name passwd >> stdout.txt 2>/dev/null
canhieu@htb[/htb]$ cat stdout.txt
/etc/shadow
/etc/pam.d/passwd
/etc/cron.daily/passwd
/etc/passwd
```

* `>>` → append vào file (không ghi đè).

***

### Redirect input stream bằng EOF

<figure><img src="../../.gitbook/assets/image (514).png" alt=""><figcaption></figcaption></figure>

```bash
canhieu@htb[/htb]$ cat << EOF > stream.txt
Hack The Box
EOF
canhieu@htb[/htb]$ cat stream.txt
Hack The Box
```

* `<< EOF` → nhập nhiều dòng đến khi gặp `EOF`.
* Dùng để viết nhanh nội dung file.

***

### Pipes (|)

* Dùng STDOUT của command này làm STDIN cho command khác.

Ví dụ lọc bằng `grep`:

<figure><img src="../../.gitbook/assets/image (515).png" alt=""><figcaption></figcaption></figure>

Kết hợp nhiều lệnh:

```bash
canhieu@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd | wc -l
6
```

👉 Tìm file `.conf` liên quan đến systemd, sau đó đếm số kết quả.

***

### Ứng dụng trong Security

* **Ẩn lỗi**: `2>/dev/null` giúp tránh noise khi enumerate file (`find / -perm -4000`).
* **Lưu kết quả**: redirect STDOUT vào file để phân tích offline (`> loot.txt`).
* **Chia tách log**: ghi lỗi vào file riêng (`2> errors.log`).
* **Automation**: pipe chuỗi lệnh → chạy nhanh thay vì nhiều bước thủ công.
* **Privilege Escalation**: ghi script hoặc payload trực tiếp vào file qua `cat << EOF`.

<figure><img src="../../.gitbook/assets/image (516).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (517).png" alt=""><figcaption></figcaption></figure>

* `dpkg -l`: liệt kê tất cả các gói.
* `grep '^ii'`: lọc ra các gói đã được cài đặt (dòng bắt đầu bằng `ii`).



## XII. Filter Contents

### Ý tưởng chính

* Ta đọc/nhìn file **trực tiếp trên command line** (không mở editor) bằng các **pager**: `more`, `less`.
* Sau đó lọc/xử lý nội dung bằng các công cụ dòng lệnh mạnh như: `head`, `tail`, `sort`, `grep`, `cut`, `tr`, `column`, `awk`, `sed`, `wc`.
* Đây là kỹ năng thiết yếu khi xử lý **log lớn**, **output dài**, hoặc cần **tự động hóa**.

***

### Pager: `more` vs `less`

#### `more`

```bash
canhieu@htb[/htb]$ cat /etc/passwd | more
```

Mở nội dung `/etc/passwd` trong **pager**; bắt đầu từ đầu file, cuộn trang bằng phím Space/Enter, thoát bằng **Q**.\
Sau khi thoát, **nội dung vẫn “in” trên terminal**.

Ví dụ hiển thị (rút gọn):

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
--More--
```

#### `less` (nhiều tính năng hơn)

```bash
canhieu@htb[/htb]$ less /etc/passwd
```

Giao diện tương tự, nhưng tính năng tìm kiếm/di chuyển phong phú; thoát **Q**.\
Khác `more`: khi thoát, **màn hình trở lại như cũ** (không giữ lại output).

Ví dụ:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
<SNIP>
:
```

***

### Xem một phần đầu/cuối file

#### `head` – 10 dòng đầu (mặc định)

```bash
canhieu@htb[/htb]$ head /etc/passwd
```

#### `tail` – 10 dòng cuối (mặc định)

```bash
canhieu@htb[/htb]$ tail /etc/passwd
```

Ví dụ `tail` (rút gọn):

```
miredo:x:115:65534::/var/run/miredo:/usr/sbin/nologin
...
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

> Mẹo: `head -n 20 file`, `tail -n 50 file` để chỉ định số dòng.

***

### Sắp xếp: `sort`

Sắp xếp theo thứ tự bảng chữ cái (hoặc số với `-n`):

```bash
canhieu@htb[/htb]$ cat /etc/passwd | sort
```

Giờ danh sách user đã **alphabetical**, không còn bắt đầu bằng `root`.

***

### Tìm kiếm mẫu: `grep`

Lọc theo **pattern** (regex), ví dụ tìm user có shell `/bin/bash`:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep "/bin/bash"

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```

Loại trừ (`-v`) các dòng chứa `false` hoặc `nologin`:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin"

root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
postgres:x:111:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

***

### Tách trường theo delimiter: `cut`

`/etc/passwd` dùng dấu `:` làm delimiter. Lấy **trường 1** (username):

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1

root
sync
postgres
mrb3n
cry0l1t3
htb-student
```

***

### Thay ký tự: `tr`

Đổi dấu `:` thành khoảng trắng:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "
```

Ví dụ output:

```bash
root x 0 0 root /root /bin/bash
sync x 4 65534 sync /bin /bin/sync
postgres x 111 117 PostgreSQL administrator,,, /var/lib/postgresql /bin/bash
mrb3n x 1000 1000 mrb3n /home/mrb3n /bin/bash
cry0l1t3 x 1001 1001  /home/cry0l1t3 /bin/bash
htb-student x 1002 1002  /home/htb-student /bin/bash
```

***

### Định dạng dạng bảng: `column -t`

Căn cột gọn gàng để dễ đọc:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t
```

Ví dụ (rút gọn):

```
root         x  0     0      root               /root                  /bin/bash
sync         x  4     65534  sync               /bin                   /bin/sync
postgres     x  111   117    PostgreSQL ...     /var/lib/postgresql    /bin/bash
...
```

***

### Lấy cột linh hoạt: `awk`

In **cột 1** và **cột cuối** (`$NF`):

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}'
```

Output (rút gọn):

```bash
root /bin/bash
sync /bin/sync
postgres /bin/bash
mrb3n /bin/bash
cry0l1t3 /bin/bash
htb-student /bin/bash
```

***

### Thay thế chuỗi bằng regex: `sed`

Thay mọi `bin` thành `HTB`:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'

root /HTB/bash
sync /HTB/sync
postgres /HTB/bash
...
```

***

### Đếm số dòng/ký tự/từ: `wc`

Đếm **số dòng** (`-l`) kết quả lọc:

```bash
canhieu@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | wc -l
6
```

***

### Bảng tóm tắt công cụ lọc

| Công cụ  | Tác dụng nhanh                 | Ví dụ hay dùng                    |
| -------- | ------------------------------ | --------------------------------- |
| `more`   | Xem từng trang, giữ output lại | \`cat file                        |
| `less`   | Pager mạnh, không giữ output   | `less file`                       |
| `head`   | Lấy đầu file                   | `head -n 20 file`                 |
| `tail`   | Lấy cuối file                  | `tail -f log` (theo dõi realtime) |
| `sort`   | Sắp xếp                        | `sort -n`, `sort -u`              |
| `grep`   | Lọc theo pattern               | `grep -i`, `grep -v`, `grep -E`   |
| `cut`    | Cắt trường theo delimiter      | `cut -d: -f1,7`                   |
| `tr`     | Đổi/loại ký tự                 | `tr -d '\r'`                      |
| `column` | Căn cột đẹp                    | `column -t`                       |
| `awk`    | Chọn/calc cột                  | `awk -F: '{print $1,$3,$7}'`      |
| `sed`    | Thay/đổi dòng theo regex       | `sed -n '1,10p'`                  |
| `wc`     | Đếm dòng/từ/ký tự              | `wc -l`                           |

> Security note: khi phân tích **log, passwd, cron, config**, chuỗi lệnh dùng `grep | awk | sort | uniq | wc -l` giúp trích lọc nhanh IOC, user hợp lệ, shell, quyền…

***

### Bài tập (gợi ý lệnh mẫu)

> File dùng: `/etc/passwd`. Bạn có thể chạy trực tiếp trên máy lab. Dưới đây là **gợi ý** nhằm giữ tinh thần “tự luyện” (mình không in sẵn output).

1. **Một dòng chứa username `cry0l1t3`**

```bash
grep '^cry0l1t3:' /etc/passwd
```

2. **Chỉ các username**

```bash
cut -d: -f1 /etc/passwd
```

3. **Username `cry0l1t3` và UID của user đó**

```bash
grep '^cry0l1t3:' /etc/passwd | awk -F: '{print $1,$3}'
```

4. **Username `cry0l1t3` và UID, ngăn cách bằng dấu phẩy**

```bash
grep '^cry0l1t3:' /etc/passwd | awk -F: '{print $1","$3}'
```

5. **Username `cry0l1t3`, UID và shell, ngăn cách dấu phẩy**

```bash
grep '^cry0l1t3:' /etc/passwd | awk -F: '{print $1","$3","$7}'
```

6. **Tất cả username với UID và shell, ngăn cách dấu phẩy**

```bash
awk -F: '{print $1","$3","$7}' /etc/passwd
```

7. **(6) nhưng loại bỏ dòng có `nologin` hoặc `false`**

```bash
awk -F: '!/nologin|false/ {print $1","$3","$7}' /etc/passwd
```

8. **(7) và đếm số dòng**

```bash
awk -F: '!/nologin|false/ {print $1","$3","$7}' /etc/passwd | wc -l
```

> Biến thể nếu bạn thích **pipeline kiểu “đọc dễ”**:
>
> * Dùng `grep -v "nologin\|false"` trước `awk`.
> * Dùng `column -t -s,` cuối pipeline để xem gọn đẹp.

***

<figure><img src="../../.gitbook/assets/image (518).png" alt=""><figcaption></figcaption></figure>

```bash
htb-student@nixfund:~$ ps aux | grep proftpd
proftpd   2018  0.0  0.1 126440  3656 ?        Ss   14:29   0:00 proftpd: (accepting connections)
htb-stu+  6885  0.0  0.0  13144  1040 pts/2    S+   16:27   0:00 grep --color=auto proftpd
```

<figure><img src="../../.gitbook/assets/image (519).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (520).png" alt=""><figcaption></figcaption></figure>

```bash
canhieu@DESKTOP-DBGES7N:~$ curl https://www.inlanefreight.com > htb.txt \
  && cat htb.txt | tr " " "\n" \
  | cut -d"'" -f2 | cut -d'"' -f2 \
  | grep "www.inlanefreight.com" \
  | sort -u | wc -l
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 22266    0 22266    0     0  18556      0 --:--:--  0:00:01 --:--:-- 18570
34
```

| Thành phần                                           | Chức năng                                                                                              |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `curl -s https://www.inlanefreight.com`              | Lấy mã nguồn HTML của trang chủ (`-s` để silent, không hiển thị tiến trình)                            |
| `grep -Po "https://www.inlanefreight.com/[^'\"?#]*"` | Tìm kiếm tất cả URL nội bộ trong mã nguồn — cụ thể là các link bắt đầu bằng domain và theo sau là path |
| `sort -u`                                            | Sắp xếp và loại bỏ các đường dẫn trùng nhau (**unique paths**)                                         |
| `wc -l`                                              | Đếm tổng số dòng kết quả → tức là tổng số **path duy nhất**                                            |





## XIII. Regular Expressions (RegEx)

### Giới thiệu

* **RegEx** = tập hợp ký tự và ký hiệu đặc biệt, tạo thành một **search pattern**.
* Dùng để **tìm kiếm, thay thế, phân tích dữ liệu** với độ chính xác cao.
* Có mặt trong nhiều tool và ngôn ngữ: `grep`, `sed`, `awk`, Python, Perl…
* Trong **cybersecurity**, RegEx cực hữu dụng khi lọc log, phân tích file config, tìm credential hoặc IOC (indicator of compromise).

👉 Hãy tưởng tượng RegEx là một **bộ lọc tùy biến cao**: chỉ hiển thị chính xác những gì bạn muốn thấy.

***

### Grouping trong RegEx

RegEx cho phép **gom nhóm (group)** và định nghĩa pattern bằng nhiều kiểu ngoặc:

| Toán tử / Nhóm | Ý nghĩa                                                            | Ví dụ                                         |
| -------------- | ------------------------------------------------------------------ | --------------------------------------------- |
| `(a)`          | **Round brackets** – gom nhóm, áp dụng pattern bên trong cùng nhau | \`(my                                         |
| `[a-z]`        | **Square brackets** – character class, chỉ định tập ký tự          | `[0-9]`, `[A-Za-z]`                           |
| `{1,10}`       | **Curly brackets** – quantifier, chỉ định số lần lặp               | `[0-9]{3}` = 3 chữ số                         |
| \`             | \`                                                                 | OR operator, khớp **một trong hai** biểu thức |
| `.*`           | Chuỗi bất kỳ, dùng như **AND operator** khi nối 2 pattern          | `my.*false`                                   |

***

### Ví dụ cơ bản

#### OR operator

```bash
cry0l1t3@htb:~$ grep -E "(my|false)" /etc/passwd
lxd:x:105:65534::/var/lib/lxd/:/bin/false
pollinate:x:109:1::/var/cache/pollinate:/bin/false
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

* Kết quả hiển thị **dòng chứa `my` hoặc `false`**.

#### AND operator (dùng `.*`)

```bash
cry0l1t3@htb:~$ grep -E "(my.*false)" /etc/passwd
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

* Chỉ ra dòng có **cả `my` và `false` theo đúng thứ tự**.

#### AND operator (dùng grep kép)

```bash
cry0l1t3@htb:~$ grep -E "my" /etc/passwd | grep -E "false"
mysql:x:116:120:MySQL Server,,:/nonexistent:/bin/false
```

***

### Bài tập RegEx (thực hành với `/etc/ssh/sshd_config`)

1.  **Hiển thị tất cả dòng không chứa `#`**

    ```bash
    grep -v "#" /etc/ssh/sshd_config
    ```

    → Lọc bỏ comment, chỉ giữ dòng cấu hình thật.
2.  **Tìm tất cả dòng bắt đầu bằng từ `Permit`**

    ```bash
    grep -E "^Permit" /etc/ssh/sshd_config
    ```

    * `^` = bắt đầu dòng.
    * Ví dụ: `PermitRootLogin`, `PermitEmptyPasswords`.
3.  **Tìm tất cả dòng kết thúc bằng `Authentication`**

    ```bash
    grep -E "Authentication$" /etc/ssh/sshd_config
    ```

    * `$` = kết thúc dòng.
    * Ví dụ: `PasswordAuthentication`.
4.  **Tìm tất cả dòng chứa từ `Key`**

    ```bash
    grep "Key" /etc/ssh/sshd_config
    ```

    * Ví dụ: `AuthorizedKeysFile`, `HostKey`.
5.  **Tìm tất cả dòng bắt đầu bằng `Password` và có chứa `yes`**

    ```bash
    grep -E "^Password.*yes" /etc/ssh/sshd_config
    ```

    * `.*` = bất kỳ ký tự nào, lặp nhiều lần.
    * Bắt dòng như: `PasswordAuthentication yes`.
6.  **Tìm tất cả dòng kết thúc bằng `yes`**

    ```bash
    grep -E "yes$" /etc/ssh/sshd_config
    ```



## XIV. Permission Management trong Linux

### Giới thiệu

* **Permission** trong Linux = hệ thống “chìa khóa” kiểm soát quyền truy cập file & directory.
* Mỗi file/directory có **owner (user)** và **group**.
* Quyền được chia cho **owner**, **group**, và **others** (người khác).
* Các loại quyền cơ bản:
  * **r (read)** – đọc nội dung file, hoặc liệt kê nội dung directory.
  * **w (write)** – sửa file, hoặc tạo/xóa/đổi tên file trong directory.
  * **x (execute)** – chạy file (nếu là script/binary), hoặc **traverse directory** (đi vào thư mục).

📌 **Security note**: Quyền sai có thể dẫn tới **privilege escalation** hoặc **data leakage**.

***

### Traverse directory và quyền execute

Để **đi vào (cd)** một directory, user phải có **x (execute)** permission trên directory đó.

Ví dụ:

```bash
cry0l1t3@htb[/htb]$ ls -l
drw-rw-r-- 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:30 scripts

cry0l1t3@htb[/htb]$ ls -al mydirectory/
ls: cannot access 'mydirectory/script.sh': Permission denied
...
```

* Không có **x permission** → không thể traverse → lỗi _Permission denied_.
* Có **x permission** → có thể đi vào, nhưng để đọc nội dung vẫn cần **r (read)**.

***

### Cấu trúc permission (ls -l)

Ví dụ với `/etc/passwd`:

```bash
cry0l1t3@htb[/htb]$ ls -l /etc/passwd
-rwxrw-r-- 1 root root 1641 May  4 23:42 /etc/passwd
```

Phân tích:

```bash
cry0l1t3@htb[/htb]$ ls -l /etc/passwd

- rwx rw- r--   1 root root 1641 May  4 23:42 /etc/passwd
- --- --- ---   |  |    |    |   |__________|
|  |   |   |    |  |    |    |        |_ Date
|  |   |   |    |  |    |    |__________ File Size
|  |   |   |    |  |    |_______________ Group
|  |   |   |    |  |____________________ User
|  |   |   |    |_______________________ Number of hard links
|  |   |   |_ Permission of others (read)
|  |   |_____ Permissions of the group (read, write)
|  |_________ Permissions of the owner (read, write, execute)
|____________ File type (- = File, d = Directory, l = Link, ... )
```

| Vị trí        | Ý nghĩa                                                |
| ------------- | ------------------------------------------------------ |
| `-`           | File type (`-` = file, `d` = directory, `l` = link, …) |
| `rwx`         | Quyền của **owner** (read, write, execute)             |
| `rw-`         | Quyền của **group**                                    |
| `r--`         | Quyền của **others**                                   |
| `1`           | Số hard link                                           |
| `root`        | Owner                                                  |
| `root`        | Group                                                  |
| `1641`        | File size                                              |
| `May 4`       | Date/time                                              |
| `/etc/passwd` | Tên file                                               |

***

### Chỉnh sửa permission – `chmod`

Có 2 cách:

1. **Symbolic mode**: `u` (user), `g` (group), `o` (others), `a` (all).
   * `+` thêm quyền, `-` gỡ quyền.
2. **Octal mode**: dùng số 3 chữ (0–7) tương ứng `r=4`, `w=2`, `x=1`.

Ví dụ:

```bash
cry0l1t3@htb[/htb]$ ls -l shell
-rwxr-x--x 1 cry0l1t3 htbteam 0 May  4 22:12 shell
```

#### Thêm read cho tất cả

```bash
chmod a+r shell
```

#### Set permission theo octal (754)

```bash
chmod 754 shell
-rwxr-xr-- 1 cry0l1t3 htbteam 0 May  4 22:12 shell
```

#### Bảng chuyển đổi permission ↔ binary ↔ octal

| Quyền | Binary | Octal |
| ----- | ------ | ----- |
| rwx   | 111    | 7     |
| rw-   | 110    | 6     |
| r-x   | 101    | 5     |
| r--   | 100    | 4     |
| ---   | 000    | 0     |

***

### Đổi owner/group – `chown`

Cú pháp:

```bash
chown <user>:<group> <file>
```

Ví dụ:

```bash
cry0l1t3@htb[/htb]$ chown root:root shell && ls -l shell
-rwxr-xr-- 1 root root 0 May  4 22:12 shell
```

***

### Special permissions: SUID, SGID

#### SUID (Set User ID)

* File có SUID khi chạy sẽ **thực thi với quyền của owner**.
* Ký hiệu: `s` thay cho `x` trong cột **user**.

Ví dụ: `/usr/bin/passwd` → cho phép user đổi mật khẩu (chạy với quyền root).

📌 **Nguy hiểm**: Nếu SUID đặt nhầm trên binary có thể spawn shell → **root escalation**.\
→ Tham khảo GTFOBins.

#### SGID (Set Group ID)

* Khi chạy, chương trình lấy quyền group của file.
* Ký hiệu: `s` trong cột **group**.
* Trên directory: file mới tạo sẽ thừa hưởng **group** của thư mục thay vì group hiện tại của user.

***

### Sticky Bit

* Dùng cho **directory chia sẻ** (như `/tmp`).
* Ký hiệu: `t` ở cuối cột **others**.

Ví dụ:

```bash
cry0l1t3@htb[/htb]$ ls -l
drw-rw-r-t 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:30 scripts
drw-rw-r-T 3 cry0l1t3 cry0l1t3 4096 Jan 12 12:32 reports
```

* `t` (lowercase): others có `x` → có thể traverse thư mục.
* `T` (uppercase): others **không có `x`** → không thể traverse.

📌 Ý nghĩa: chỉ owner của file, owner của thư mục, hoặc root mới được xóa/rename file trong thư mục đó.



## XV. User Management trong Linux

### Giới thiệu

Quản lý user là một **nhiệm vụ cốt lõi** của quản trị hệ thống Linux:

* **Tạo user mới** (ví dụ nhân viên mới như _Alex_).
* **Thêm vào group** phù hợp để có quyền truy cập tài nguyên cần thiết.
* **Chạy lệnh với quyền khác** (vd: root, hoặc một user đặc biệt trong hệ thống).

📌 Từ góc độ security:

* User management = **kiểm soát bề mặt tấn công**.
* Sai sót (user thừa quyền, file nhạy cảm readable bởi group không hợp lý) → dễ dẫn đến **privilege escalation** hoặc **data leak**.

***

### Thực thi lệnh với quyền user khác

#### Truy cập file nhạy cảm `/etc/shadow`

```bash
canhieu@htb[/htb]$ cat /etc/shadow
cat: /etc/shadow: Permission denied
```

* `/etc/shadow` chứa **hash password** của toàn bộ user.
* Chỉ root đọc/ghi được.
* Đây là **file critical**: lộ quyền đọc = **thảm họa bảo mật**.

#### Dùng `sudo` để thực thi với quyền root

```bash
canhieu@htb[/htb]$ sudo cat /etc/shadow
root:<SNIP>:18395:0:99999:7:::
daemon:*:17737:0:99999:7:::
bin:*:17737:0:99999:7:::
...
```

* `sudo` = _superuser do_.
* Cho phép user (có trong `/etc/sudoers`) chạy lệnh với quyền user khác (thường là root).
* Best practice: **dùng sudo thay vì login trực tiếp root** để:
  * Có log (ai chạy lệnh gì).
  * Giảm nguy cơ lạm dụng tài khoản root.

👉 Phần về **sudoers & escalation** sẽ được đào sâu hơn trong **Linux Security section**.

***

### Lệnh quản lý user và group

| Lệnh       | Mô tả                                                                                                                  |
| ---------- | ---------------------------------------------------------------------------------------------------------------------- |
| `sudo`     | Thực thi lệnh với quyền user khác (thường là root).                                                                    |
| `su`       | (_substitute user_) – chuyển sang user khác. Nếu không chỉ định → mặc định sang root. Yêu cầu nhập password user đích. |
| `useradd`  | Tạo user mới (hoặc update default cho user mới).                                                                       |
| `userdel`  | Xóa user và file liên quan.                                                                                            |
| `usermod`  | Sửa user hiện tại (đổi group, home directory, shell, …).                                                               |
| `addgroup` | Tạo group mới.                                                                                                         |
| `delgroup` | Xóa group.                                                                                                             |
| `passwd`   | Đặt hoặc đổi mật khẩu user.                                                                                            |

***

### Một số ví dụ thực tế

#### Tạo user mới

```bash
sudo useradd -m -s /bin/bash alex
```

* `-m` → tạo home directory.
* `-s` → chỉ định shell.

#### Đặt mật khẩu

```bash
sudo passwd alex
```

#### Thêm user vào group

```bash
sudo usermod -aG developers alex
```

* `-aG` → append vào group `developers`.

#### Xóa user

```bash
sudo userdel -r alex
```

* `-r` → xóa luôn home directory.

***

### Security góc nhìn

* **Audit user & group**: dùng `id`, `groups`, `getent passwd`, `getent group`.
* **Phát hiện user thừa quyền**:
  * Thuộc group `sudo` hoặc `docker` → có thể escalate.
  * Có shell `/bin/bash` thay vì `/usr/sbin/nologin`.
* **/etc/shadow**: phải **root-only**. Sai permission → attacker lấy hash → brute force/offline crack.
* **sudo/su abuse**: nếu cấu hình sudoers lỏng lẻo (vd: `NOPASSWD:ALL`) → attacker escalate ngay.

<figure><img src="../../.gitbook/assets/image (323).png" alt=""><figcaption></figcaption></figure>





## XVI. Quản lý Gói (Package Management)

Dù là khi làm việc với tư cách **quản trị hệ thống (system administrator)**, duy trì các máy Linux cá nhân tại nhà, hay xây dựng/nâng cấp/duy trì bản phân phối phục vụ **kiểm thử xâm nhập (penetration testing distribution)** mà ta lựa chọn, thì việc nắm vững các **trình quản lý gói (Linux package managers)** có sẵn và những cách sử dụng khác nhau để cài đặt, cập nhật hoặc gỡ bỏ gói phần mềm là cực kỳ quan trọng.

**Gói phần mềm (packages)** là các tệp nén chứa:

* Tệp nhị phân (binaries) của phần mềm,
* Tệp cấu hình (configuration files),
* Thông tin về phụ thuộc (dependencies),
* Cơ chế theo dõi cập nhật/nâng cấp (updates and upgrades).

Các tính năng phổ biến mà hầu hết các hệ thống quản lý gói cung cấp:

* Tải xuống gói phần mềm (Package downloading)
* Giải quyết phụ thuộc (Dependency resolution)
* Định dạng chuẩn cho gói nhị phân (A standard binary package format)
* Vị trí cài đặt và cấu hình thống nhất (Common installation and configuration locations)
* Cấu hình và chức năng bổ sung liên quan đến hệ thống (Additional system-related configuration and functionality)
* Kiểm soát chất lượng (Quality control)

Có nhiều hệ thống quản lý gói hỗ trợ các định dạng như **“.deb”**, **“.rpm”**, và những loại khác. Điều kiện tiên quyết: phần mềm muốn cài đặt phải có sẵn dưới dạng gói tương ứng. Thông thường, gói này được tạo ra, cung cấp và duy trì tập trung trong từng bản phân phối Linux.

Nhờ vậy, phần mềm được tích hợp trực tiếp vào hệ thống, và các thư mục liên quan sẽ được phân bổ trên toàn hệ thống. Phần mềm quản lý gói sẽ trích xuất thông tin cần thiết từ chính gói đó và thực hiện thay đổi để cài đặt thành công.

Nếu phần mềm quản lý gói nhận thấy cần thêm các gói phụ thuộc (dependencies) để chương trình chạy đúng mà chưa có sẵn, nó sẽ:

* Thông báo cho quản trị viên, hoặc
* Tự động tải các gói phụ thuộc còn thiếu từ **repository** và cài đặt trước.

Nếu một phần mềm đã cài đặt bị gỡ bỏ, hệ thống quản lý gói sẽ dựa trên thông tin gói đó, cập nhật theo cấu hình, rồi xóa các tệp liên quan.

Một số **trình quản lý gói phổ biến**:

| Lệnh         | Mô tả                                                                                                                                                                             |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **dpkg**     | Công cụ cài đặt, xây dựng, gỡ bỏ và quản lý gói Debian. Giao diện thân thiện hơn của dpkg là **aptitude**.                                                                        |
| **apt**      | Cung cấp giao diện dòng lệnh cấp cao cho hệ thống quản lý gói.                                                                                                                    |
| **aptitude** | Thay thế cho apt, cũng là giao diện cấp cao cho trình quản lý gói.                                                                                                                |
| **snap**     | Cài đặt, cấu hình, làm mới và gỡ bỏ gói snap. Snaps cho phép phân phối an toàn các ứng dụng và tiện ích mới nhất cho đám mây, máy chủ, máy tính để bàn, và IoT.                   |
| **gem**      | Công cụ front-end cho **RubyGems**, trình quản lý gói chuẩn của Ruby.                                                                                                             |
| **pip**      | Trình cài đặt gói cho Python, khuyến nghị dùng khi cài gói Python không có trong kho Debian. Hỗ trợ lấy từ Git, Mercurial, Bazaar; ghi log chi tiết và tránh lỗi cài đặt dở dang. |
| **git**      | Hệ thống quản lý phiên bản phân tán, nhanh, mở rộng tốt, với tập lệnh phong phú, hỗ trợ cả thao tác cấp cao lẫn truy cập sâu vào nội bộ.                                          |

👉 Rất khuyến nghị: hãy tạo một **máy ảo (VM)** cục bộ để thử nghiệm. Ví dụ, cài đặt **git** bằng `apt`.

***

### &#x20;Advanced Package Tool (APT)

Các bản phân phối Linux dựa trên **Debian** sử dụng **APT** để quản lý gói.

* **Gói phần mềm (package)**: là một tệp nén chứa nhiều **“.deb”**.
* **dpkg**: tiện ích để cài chương trình từ file “.deb”.
* **APT**: đơn giản hóa việc cài đặt/cập nhật, vì tự động xử lý **các gói phụ thuộc**.

Nếu cài đặt phần mềm từ một tệp **“.deb”** đơn lẻ, ta có thể gặp lỗi thiếu phụ thuộc và buộc phải tải thêm thủ công. APT sẽ thay ta làm việc đó, gom tất cả dependency và xử lý.

Mỗi bản phân phối Linux duy trì các **repository** (kho phần mềm) được cập nhật thường xuyên. Khi cài hoặc cập nhật phần mềm, hệ thống sẽ truy vấn các repository để tìm gói phù hợp. Repository có thể được phân loại là:

* **stable** (ổn định),
* **testing** (thử nghiệm),
* **unstable** (không ổn định).

Đa số bản phân phối sử dụng repository **stable/main**. Ta có thể kiểm tra nội dung file sau:

```bash
/etc/apt/sources.list
```

Ví dụ với Parrot OS:

```bash
cat /etc/apt/sources.list.d/parrot.list
```

Output:

```bash
# parrot repository
# this file was automatically generated by parrot-mirror-selector
deb http://htb.deb.parrot.sh/parrot/ rolling main contrib non-free
#deb-src https://deb.parrot.sh/parrot/ rolling main contrib non-free
deb http://htb.deb.parrot.sh/parrot/ rolling-security main contrib non-free
#deb-src https://deb.parrot.sh/parrot/ rolling-security main contrib non-free
```

**APT cache**: cơ sở dữ liệu dùng để lưu thông tin các gói đã cài đặt và có thể tra cứu offline.

Ví dụ tìm các gói liên quan đến _impacket_:

```bash
apt-cache search impacket
```

Kết quả:

```
impacket-scripts - Links to useful impacket scripts examples
polenum - Extracts the password policy from a Windows system
python-pcapy - Python interface to the libpcap packet capture library (Python 2)
python3-impacket - Python3 module to easily build and dissect network protocols
python3-pcapy - Python interface to the libpcap packet capture library (Python 3)
```

Xem thêm thông tin chi tiết về một gói:

```bash
apt-cache show impacket-scripts
```

Ví dụ kết quả (rút gọn):

```
Package: impacket-scripts
Version: 1.4
Architecture: all
Maintainer: Kali Developers <devel@kali.org>
Installed-Size: 13
Depends: python3-impacket (>= 0.9.20), python3-ldap3 (>= 2.5.0), python3-ldapdomaindump
Breaks: python-impacket (<< 0.9.18)
Replaces: python-impacket (<< 0.9.18)
Priority: optional
Section: misc
Filename: pool/main/i/impacket-scripts/impacket-scripts_1.4_all.deb
Size: 2080
<SNIP>
```

Liệt kê toàn bộ gói đã cài:

```bash
apt list --installed
```

Ví dụ output:

```
accountsservice/rolling,now 0.6.55-2 amd64 [installed,automatic]
adapta-gtk-theme/rolling,now 3.95.0.11-1 all [installed]
adduser/rolling,now 3.118 all [installed]
adwaita-icon-theme/rolling,now 3.36.1-2 all [installed,automatic]
aircrack-ng/rolling,now 1:1.6-4 amd64 [installed,automatic]
<SNIP>
```

Cài thêm gói còn thiếu:

```bash
canhieu@htb[/htb]$ sudo apt install impacket-scripts -y

Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  impacket-scripts
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 2,080 B of archives.
After this operation, 13.3 kB of additional disk space will be used.
Get:1 https://euro2-emea-mirror.parrot.sh/mirrors/parrot rolling/main amd64 impacket-scripts all 1.4 [2,080 B]
Fetched 2,080 B in 0s (15.2 kB/s)
Selecting previously unselected package impacket-scripts.
(Reading database ... 378459 files and directories currently installed.)
Preparing to unpack .../impacket-scripts_1.4_all.deb ...
Unpacking impacket-scripts (1.4) ...
Setting up impacket-scripts (1.4) ...
Scanning application launchers
Removing duplicate launchers from Debian
Launchers are updated
```

***

### &#x20;Git

<figure><img src="../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

Sau khi cài đặt git, ta có thể sử dụng nó để tải về các công cụ hữu ích từ GitHub. Một ví dụ là dự án **Nishang**.

Trước khi tải, nên tạo thư mục riêng:

```bash
mkdir ~/nishang/ && git clone https://github.com/samratashok/nishang.git ~/nishang
```

Output:

```
Cloning into '/opt/nishang/'...
remote: Enumerating objects: 15, done.
remote: Counting objects: 100% (15/15), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 1691 (delta 4), reused 6 (delta 2), pack-reused 1676
Receiving objects: 100% (1691/1691), 7.84 MiB | 4.86 MiB/s, done.
Resolving deltas: 100% (1055/1055), done.
```

***

### &#x20;DPKG

Ngoài apt, ta có thể tải các gói `.deb` riêng và cài đặt thủ công bằng **dpkg**.

Ví dụ tải **strace** cho Ubuntu 18.04 LTS:

```bash
wget http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb
```

Output:

```
--2020-05-15 03:27:17--  http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb
Resolving archive.ubuntu.com (archive.ubuntu.com)... 91.189.88.142, 91.189.88.152, 2001:67c:1562::18, ...
Connecting to archive.ubuntu.com (archive.ubuntu.com)|91.189.88.142|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 333388 (326K) [application/x-debian-package]
Saving to: ‘strace_4.21-1ubuntu1_amd64.deb’

strace_4.21-1ubuntu1_amd64.deb       100%[===================================================================>] 325,57K  --.-KB/s    in 0,1s    

2020-05-15 03:27:18 (2,69 MB/s) - ‘strace_4.21-1ubuntu1_amd64.deb’ saved [333388/333388]
```

Cài đặt bằng dpkg:

```bash
sudo dpkg -i strace_4.21-1ubuntu1_amd64.deb
```

Output:

```
(Reading database ... 154680 files and directories currently installed.)
Preparing to unpack strace_4.21-1ubuntu1_amd64.deb ...
Unpacking strace (4.21-1ubuntu1) over (4.21-1ubuntu1) ...
Setting up strace (4.21-1ubuntu1) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
```

Kiểm tra chạy thử:

```bash
strace -h
```

Output (rút gọn):

```
usage: strace [-CdffhiqrtttTvVwxxy] [-I n] [-e expr]...
              [-a column] [-o file] [-s strsize] [-P path]...
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
   or: strace -c[dfw] [-I n] [-e expr]... [-O overhead] [-S sortby]
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]

Output format:
  -a column      alignment COLUMN for printing syscall results (default 40)
  -i             print instruction pointer at time of syscall
```



## XVII. Quản lý Dịch vụ và Tiến trình (Service and Process Management)

**Dịch vụ (Services)**, còn được gọi là **daemon**, là những thành phần cơ bản của hệ thống Linux chạy âm thầm ở chế độ nền mà không cần tương tác trực tiếp với người dùng. Chúng thực hiện các tác vụ quan trọng giúp hệ thống vận hành ổn định và cung cấp thêm các chức năng bổ sung.

Thông thường, dịch vụ được chia thành hai loại:

### Dịch vụ hệ thống (System Services)

Đây là các dịch vụ nội bộ cần thiết trong quá trình khởi động hệ thống. Chúng đảm nhiệm các tác vụ liên quan đến phần cứng và khởi tạo các thành phần hệ thống cần thiết để hệ điều hành có thể hoạt động đúng. Có thể so sánh chúng với động cơ và hệ truyền động của một chiếc xe. Chúng khởi động khi bạn bật chìa khóa và là điều kiện bắt buộc để xe chạy. Nếu thiếu, xe sẽ không thể hoạt động.

### Dịch vụ do người dùng cài đặt (User-Installed Services)

Đây là các dịch vụ được bổ sung bởi người dùng, thường bao gồm các ứng dụng máy chủ và các tiến trình nền khác cung cấp tính năng cụ thể. Có thể so sánh chúng như hệ thống điều hòa hay định vị GPS của xe hơi. Không bắt buộc để xe chạy, nhưng giúp bổ sung tiện ích và nâng cao trải nghiệm theo nhu cầu.

Daemon thường được nhận diện bởi chữ **d** ở cuối tên chương trình, ví dụ như **sshd** (SSH daemon) hoặc **systemd**. Giống như một chiếc xe cần cả thành phần lõi và tính năng bổ sung, một hệ thống Linux cũng sử dụng cả dịch vụ hệ thống và dịch vụ cài đặt thêm để vận hành hiệu quả.

### Mục tiêu khi quản lý dịch vụ hoặc tiến trình

* Khởi động/khởi động lại một dịch vụ hoặc tiến trình
* Dừng một dịch vụ hoặc tiến trình
* Xem tình trạng hoạt động hiện tại hoặc trước đó
* Bật/tắt một dịch vụ khi khởi động hệ thống
* Tìm một dịch vụ hoặc tiến trình cụ thể

Hầu hết các bản phân phối Linux hiện đại sử dụng **systemd** làm hệ thống khởi tạo (init system). Đây là tiến trình đầu tiên được khởi chạy trong quá trình boot và được gán **PID 1**. Mọi tiến trình trong Linux đều được gán một **PID** và có thể xem dưới thư mục **/proc/**, nơi lưu thông tin chi tiết của từng tiến trình. Một tiến trình cũng có thể có **PPID** (Parent Process ID), chỉ ra rằng nó được khởi chạy bởi một tiến trình khác (tiến trình cha), từ đó trở thành tiến trình con.

***

## Systemctl

Sau khi cài đặt OpenSSH trên máy ảo, ta có thể khởi động dịch vụ bằng lệnh:

```bash
systemctl start ssh
```

Kiểm tra trạng thái dịch vụ:

```bash
systemctl status ssh
```

Ví dụ kết quả:

```
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-05-14 15:08:23 CEST; 24h ago
   Main PID: 846 (sshd)
   Tasks: 1 (limit: 4681)
   CGroup: /system.slice/ssh.service
           └─846 /usr/sbin/sshd -D
```

Để dịch vụ tự động chạy khi khởi động lại hệ thống, ta kích hoạt bằng:

```bash
systemctl enable ssh
```

Sau khi reboot, OpenSSH sẽ tự động chạy. Có thể kiểm tra bằng lệnh:

```bash
ps -aux | grep ssh
```

Liệt kê tất cả dịch vụ hiện tại:

```bash
systemctl list-units --type=service
```

Nếu dịch vụ không khởi động do lỗi, có thể xem log bằng **journalctl**:

```bash
journalctl -u ssh.service --no-pager
```

***

## Quản lý tiến trình với Kill

Một tiến trình có thể ở các trạng thái:

* Đang chạy (Running)
* Đang chờ (Waiting)
* Dừng (Stopped)
* Zombie (dừng nhưng vẫn còn trong bảng tiến trình)

Các lệnh quản lý tiến trình: **kill, pkill, pgrep, killall**. Khi muốn điều khiển, ta gửi tín hiệu (**signal**) đến tiến trình.

Xem danh sách tín hiệu:

```bash
kill -l
```

Các tín hiệu phổ biến:

| Signal       | Mô tả                                                      |
| ------------ | ---------------------------------------------------------- |
| 1 (SIGHUP)   | Gửi khi terminal điều khiển đóng.                          |
| 2 (SIGINT)   | Gửi khi nhấn \[Ctrl] + C để ngắt tiến trình.               |
| 3 (SIGQUIT)  | Gửi khi nhấn \[Ctrl] + D để thoát.                         |
| 9 (SIGKILL)  | Buộc dừng tiến trình ngay lập tức, không dọn dẹp.          |
| 15 (SIGTERM) | Yêu cầu kết thúc chương trình.                             |
| 19 (SIGSTOP) | Dừng chương trình, không thể xử lý thêm.                   |
| 20 (SIGTSTP) | Gửi khi nhấn \[Ctrl] + Z để tạm dừng, có thể tiếp tục sau. |

Ví dụ: nếu một tiến trình bị treo, ta buộc dừng bằng:

```bash
kill -9 <PID>
```

***

## Đưa tiến trình ra nền (Background a Process)

Đôi khi cần đưa tiến trình ra chạy nền để tiếp tục sử dụng terminal. Thao tác \[Ctrl + Z] sẽ gửi tín hiệu SIGTSTP để tạm dừng tiến trình.

Ví dụ:

```bash
ping -c 10 www.hackthebox.eu
vim tmpfile
[Ctrl + Z]
```

Liệt kê tiến trình nền:

```bash
jobs
```

Đưa tiến trình đang tạm dừng sang chế độ chạy nền:

```bash
bg
```

Hoặc chạy trực tiếp ở nền bằng ký tự **&**:

```bash
ping -c 10 www.hackthebox.eu &
```

***

## Đưa tiến trình về foreground

Xem các tiến trình nền:

```bash
jobs
```

Đưa tiến trình có ID cụ thể về foreground để tương tác lại:

```bash
fg <ID>
```

***

## Chạy nhiều lệnh cùng lúc

Có ba cách phân tách lệnh:

* Dấu chấm phẩy (;)
* Dấu AND kép (&&)
* Dấu pipe (|)

Sự khác biệt là cách xử lý kết quả lệnh trước.

Ví dụ dùng **;**:

```bash
echo '1'; echo '2'; echo '3'
```

Ví dụ lỗi vẫn chạy tiếp:

```bash
echo '1'; ls MISSING_FILE; echo '3'
```

Ví dụ dùng **&&** (nếu có lỗi thì dừng):

```bash
echo '1' && ls MISSING_FILE && echo '3'
```

Dấu **|** (pipe) phụ thuộc vào kết quả đầu ra của lệnh trước.

















