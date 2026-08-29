# Lab 8.1

### 1. Exploring the Different Types of Files

#### a)

```bash
[root@OSG203 hieucqhe]# cat << quit > file1.txt
> OSG203
> FPTU
> quit
[root@OSG203 hieucqhe]# ln -s file1.txt file2.txt
[root@OSG203 hieucqhe]# ln file1.txt file3.txt
[root@OSG203 hieucqhe]# ls -l
total 32
drwxr-xr-x. 2 hieucqhe hieucqhe     6 Sep 11 15:05 Desktop
drwxr-xr-x. 4 hieucqhe hieucqhe    34 Sep 15 15:57 Documents
drwxr-xr-x. 2 hieucqhe hieucqhe    37 Sep 11 14:56 Downloads
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file1.txt
lrwxrwxrwx. 1 root      root        9 Oct 18 22:40 file2.txt -> file1.txt
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file3.txt
[root@OSG203 hieucqhe]# cat file2.txt
OSG203
FPTU
[root@OSG203 hieucqhe]# cat file3.txt
OSG203
FPTU
[root@OSG203 hieucqhe]# mv file1.txt file4.txt
[root@OSG203 hieucqhe]# ls -l
total 32
drwxr-xr-x. 2 hieucqhe hieucqhe     6 Sep 11 15:05 Desktop
drwxr-xr-x. 4 hieucqhe hieucqhe    34 Sep 15 15:57 Documents
drwxr-xr-x. 2 hieucqhe hieucqhe    37 Sep 11 14:56 Downloads
lrwxrwxrwx. 1 root      root        9 Oct 18 22:40 file2.txt -> file1.txt
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file3.txt
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file4.txt
[root@OSG203 hieucqhe]# cat file2.txt
cat: file2.txt: No such file or directory
[root@OSG203 hieucqhe]# cat file3.txt
OSG203
FPTU
[root@OSG203 hieucqhe]# mv file4.txt file1.txt
[root@OSG203 hieucqhe]# mkdir temp
[root@OSG203 hieucqhe]# cp file1.txt temp/file5.txt
[root@OSG203 hieucqhe]# cd temp/
[root@OSG203 temp]# ln -s file5.txt file6.txt
[root@OSG203 temp]# ln file5.txt file7.txt
[root@OSG203 temp]# ls -l
total 8
-rw-r--r--. 2 root root 12 Oct 18 22:44 file5.txt
lrwxrwxrwx. 1 root root  9 Oct 18 22:46 file6.txt -> file5.txt
-rw-r--r--. 2 root root 12 Oct 18 22:44 file7.txt
[root@OSG203 temp]#
```

**Nhận xét:**

* `file2.txt` là symbolic link, chỉ chứa đường dẫn tới `file1.txt`.
* `file3.txt` là hard link, cùng inode với `file1.txt`.
* Sau khi đổi tên file gốc, symbolic link bị hỏng nhưng hard link vẫn hoạt động.

***

#### b)

```bash
[root@OSG203 hieucqhe]# mkfifo mypipe
[root@OSG203 hieucqhe]# ls -l
total 32
drwxr-xr-x. 2 hieucqhe hieucqhe     6 Sep 11 15:05 Desktop
drwxr-xr-x. 4 hieucqhe hieucqhe    34 Sep 15 15:57 Documents
drwxr-xr-x. 2 hieucqhe hieucqhe    37 Sep 11 14:56 Downloads
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file1.txt
lrwxrwxrwx. 1 root      root        9 Oct 18 22:40 file2.txt -> file1.txt
-rw-r--r--. 2 root      root       12 Oct 18 22:40 file3.txt
[root@OSG203 hieucqhe]# cat /etc/passwd > myfile.txt
[root@OSG203 hieucqhe]# cat /etc/passwd > mypipe
^C
[root@OSG203 hieucqhe]#
```

**Giải thích:**\
`mkfifo` tạo named pipe (loại tệp `p`).\
Khi ghi dữ liệu vào pipe, tiến trình bị treo cho đến khi có tiến trình khác đọc dữ liệu từ đó.

***

#### c) Differences Between Named Pipes and Domain Sockets

**Named Pipe (FIFO):**

* Giao tiếp trong cùng hệ thống tệp.
* Dữ liệu tuần tự FIFO, không có ranh giới thông điệp.

**Domain Socket (AF\_UNIX):**

* Giao tiếp full-duplex, hỗ trợ truyền thông điệp, mô tả tệp, dữ liệu điều khiển.
* Cho phép kết nối nhiều tiến trình cùng lúc.

***

#### d) Block Devices và Character Devices

**Block Devices (b):**

* Truyền dữ liệu theo block (512 bytes hoặc bội số).
* Hỗ trợ truy cập ngẫu nhiên.

**Character Devices (c):**

* Truyền dữ liệu từng byte liên tục.
* Không hỗ trợ truy cập ngẫu nhiên.

Ví dụ: `/dev/sda` là block device; `/dev/tty` là character device.

***

### 2. Inspecting Partitions

4 phân vùng `/dev/sda#` đại diện cho:\
`/`, `/boot`, `/home`, `/var`.

Các hệ thống tệp ảo gồm: `tmpfs`, `devtmpfs` (lưu trên RAM).

`df` hiển thị theo block, `df -h` hiển thị theo đơn vị dễ đọc (KB, MB, GB).

`/etc/fstab`: danh sách hệ thống tệp mount khi khởi động.\
`/etc/mtab`: danh sách hệ thống tệp đang mount (bao gồm fs ảo, nên nhiều hơn).

`/home`, `/var`: ext4, tùy chọn `rw,relatime`.\
`/boot`: vfat hoặc ext2, tùy chọn `rw,relatime,fmask=0022`.

***

### 3. Explore Files and Their Inodes

#### a)

```bash
[root@OSG203 hieucqhe]# touch file{1..3}.txt
[root@OSG203 hieucqhe]# touch file{4..6}.txt
[root@OSG203 hieucqhe]# ls -li
total 24
18219366 drwxr-xr-x. 2 hieucqhe hieucqhe     6 Sep 11 15:05 Desktop
18219367 drwxr-xr-x. 4 hieucqhe hieucqhe    34 Sep 15 15:57 Documents
34700212 drwxr-xr-x. 2 hieucqhe hieucqhe    37 Sep 11 14:56 Downloads
35225359 -rw-r--r--. 1 root root 0 Oct 18 23:01 file1.txt
35225361 -rw-r--r--. 1 root root 0 Oct 18 23:01 file2.txt
35665614 -rw-r--r--. 1 root root 0 Oct 18 23:01 file3.txt
35891807 -rw-r--r--. 1 root root 0 Oct 18 23:01 file4.txt
35891808 -rw-r--r--. 1 root root 0 Oct 18 23:01 file5.txt
35891959 -rw-r--r--. 1 root root 0 Oct 18 23:01 file6.txt
```

#### b)

```bash
[root@OSG203 hieucqhe]# df -i
Filesystem           Inodes  IUsed   IFree IUse% Mounted on
/dev/mapper/cs-root 8910848 155192 8755656    2% /
devtmpfs             203685    496  203189    1% /dev
tmpfs                211155      2  211153    1% /dev/shm
[root@OSG203 hieucqhe]# df -k
Filesystem          1K-blocks    Used Available Use% Mounted on
/dev/mapper/cs-root  17756160 6345136  11411024  36% /
```

`df -i` hiển thị inode, `df -k` hiển thị dung lượng.

***

#### c)

```bash
[root@OSG203 hieucqhe]# stat file1.txt
  File: file1.txt
  Size: 0               Blocks: 0          IO Block: 4096   regular empty file
Device: 253,0   Inode: 35225359    Links: 1
Access: (0644/-rw-r--r--)
Modify: 2025-10-18 23:01:27
Change: 2025-10-18 23:01:27
[root@OSG203 hieucqhe]# cat /etc/passwd >> file1.txt
[root@OSG203 hieucqhe]# stat file1.txt
  File: file1.txt
  Size: 2353            Blocks: 8          IO Block: 4096   regular file
Device: 253,0   Inode: 35225359    Links: 1
Access: (0644/-rw-r--r--)
Modify: 2025-10-18 23:03:42
Change: 2025-10-18 23:03:42
```

***

#### d)

```bash
[root@OSG203 hieucqhe]# cd /
[root@OSG203 /]# stat -f dev
  File: "dev"
    ID: 9b6fb3a293f39809 Namelen: 255     Type: tmpfs
Block size: 4096       Fundamental block size: 4096
Blocks: Total: 203685     Free: 203685     Available: 203685
Inodes: Total: 203685     Free: 203189
[root@OSG203 /]#
```

***

#### e)

```bash
[root@OSG203 /]# stat -c '%n %h' /etc/* | awk '$2>=7{print $1, $2}'
/etc/brltty 7
/etc/containers 7
/etc/dnf 9
/etc/firewalld 8
/etc/lvm 7
/etc/NetworkManager 7
/etc/pki 11
/etc/X11 7
/etc/xdg 7
[root@OSG203 /]#
```

***

### 4. Wrap Up

#### a)

* Thư mục `/root` chỉ cho phép root truy cập (`drwx------`).
* `/boot` chứa kernel (`vmlinuz-*`), `initramfs`, `grub.cfg`.
* `/home` là nơi lưu dữ liệu người dùng, có quyền `drwxr-xr-x`.

#### b)

```bash
[root@OSG203 hieucqhe]# cat < /dev/urandom
[root@OSG203 hieucqhe]# tr -cd '[:alnum:]' < /dev/urandom | head -c10
A8q9DbkL2R
[root@OSG203 hieucqhe]# tr -cd '[:alnum:]' < /dev/urandom | head -c10
wD3nZ7tHqP
[root@OSG203 hieucqhe]# tr -cd '[:alnum:]' < /dev/urandom | head -c10
1K9xA2YzVb
```

Thêm `> /dev/null` → dữ liệu bị loại bỏ.\
`2>/dev/null` → ẩn thông báo lỗi.

```bash
[root@OSG203 hieucqhe]# find /etc -name "*.conf" > ~/foo 2>/dev/null
```

***

#### c)

* `/proc/[PID]`: chứa thông tin tiến trình.
* `/proc/1`: tiến trình `systemd`.
* `/proc/[PID]/fd`: danh sách descriptor (0=STDIN, 1=STDOUT, 2=STDERR).
* `io`: thống kê đọc/ghi.
* `status`: trạng thái, PPID của tiến trình cha.

***

#### d)

* `cmdline`: tham số kernel khi boot (`BOOT_IMAGE=/boot/vmlinuz-... ro quiet splash`).
* `interrupts`: thống kê ngắt phần cứng, ngắt timer xuất hiện nhiều nhất.
* `uptime`: thời gian hoạt động (giây từ lúc khởi động).

***

#### e)

* `/usr/bin`: chương trình người dùng.
* `/usr/sbin`: công cụ quản trị.
* `/bin`, `/sbin` là liên kết tới `/usr/bin` và `/usr/sbin`.
* `/usr/local`: chứa phần mềm tự cài thêm.
