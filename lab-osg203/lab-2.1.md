# Lab 2.1

### 1. The simplest Linux commands can be used without options

* `ls` → Liệt kê nội dung thư mục hiện tại.
* `pwd` → Hiển thị đường dẫn thư mục hiện tại (print working directory).
* `cd ...` → Chuyển sang thư mục cha. Prompt đổi để phản ánh thư mục mới.
* `ls` (sau khi cd) → Liệt kê nội dung thư mục cha, **khác** với danh sách ban đầu.

***

### 2. Type each of these commands individually

* `hostname` → Hiển thị tên máy tính.
* `arch` → Hiển thị kiến trúc CPU (x86\_64, armv7l,…).
* `uname` → Hiển thị thông tin kernel Linux.
* `date` → Hiển thị ngày giờ hệ thống.

**Chạy chung bằng `;`**: `hostname; arch; uname; date` → Kết quả giống như chạy từng lệnh riêng, chỉ khác là in liền nhau.

***

### 3. Enter whoami and then who

* `whoami` → In tên user hiện tại (ví dụ: `student`).
* `who` → Hiển thị tất cả user đang đăng nhập, kèm TTY, thời gian đăng nhập, host.

**Khác biệt:** `whoami` chỉ ra **bạn là ai**, còn `who` cho thấy **ai đang trong hệ thống**.

***

### 4. Type cd \~/FILES followed by more/less

* `more addresses.txt` → Hiển thị file theo trang, chỉ cuộn xuống, thoát `q`.
* `less addresses.txt` → Hiển thị file theo trang, có thể cuộn lên/xuống, tìm kiếm `/pattern`.
* `more /etc/passwd` vs `less /etc/passwd` → Khác biệt cũng giống trên: `less` linh hoạt hơn.

***

### 5. Type which … and whereis …

* `which arch` → `/usr/bin/arch` (có trong PATH).
* `which ip` → `/usr/sbin/ip` hoặc `/sbin/ip` (nếu có trong PATH).
* `which systemd` → Không ra kết quả, vì `systemd` không trong PATH.
* `whereis arch` → Trả về binary + man page, ví dụ: `/usr/bin/arch /usr/share/man/man1/arch.1.gz`.

**Khác biệt:** `which` chỉ tìm **binary trong PATH**, `whereis` trả về **binary + doc + source**.\
**Giải thích systemd:** binary nằm trong `/usr/lib/systemd` (ngoài PATH), nên `which` không thấy.

***

### 6. Explore commands that use options

* `cd ~/DUMMY-DIRECTORY`
* `ls` → chỉ tên file.
* `ls -l` → thêm quyền, owner/group, kích thước, ngày giờ.
  * 3 thông tin mới: **permissions**, **owner/group**, **size/date**.
* `ps` → các tiến trình hiện tại của user.
* `ps T` → tiến trình gắn với terminal hiện tại.
* `ps a` → tiến trình có terminal của tất cả users.
* `ps u` → hiển thị theo dạng “user-oriented”: thêm %CPU, %MEM, STAT,…
* `ps x` → bao gồm cả tiến trình không có terminal.

**Cái nhiều cột nhất:** `ps u` (hoặc `ps aux` nếu dùng cú pháp BSD).\
**Thêm `| more/less`** → xem theo trang, có header: USER, PID, %CPU, %MEM, VSZ, RSS, TTY, STAT, START, TIME, COMMAND. Những cột này **không có** trong `ps T`.

***

### 7. To see what a command does (man)

* `man` = manual (trang hướng dẫn).
* `man ps` → Trang mô tả `ps`, các tùy chọn, ví dụ.
  * **Tóm tắt**: `ps` hiển thị tiến trình, hỗ trợ nhiều định dạng, nhiều tùy chọn để lọc và trình bày.
* Tùy chọn:
  * `a`: liệt kê tiến trình có terminal của tất cả users.
  * `u`: thêm cột user, CPU, memory.
  * `x`: bao gồm tiến trình không có terminal.
  * `T`: chỉ tiến trình gắn với TTY hiện tại.
* `man ls` →
  * `-a`: hiển thị file ẩn.
  * `-r`: đảo ngược thứ tự.
  * `-R`: liệt kê đệ quy thư mục con.
* `man who` →
  * `-b`: hiển thị thời gian boot gần nhất.
  * `-H`: in header cho cột.
* `man tar` → Xem hướng dẫn nén/giải nén.
  * **Synopsis**: cho thấy nhiều cách dùng lệnh tar (tạo, giải nén, liệt kê…) và các tùy chọn đi kèm.

***

### 8. Other forms of help

* `man alias` vs `help alias`:
  * `man alias` → nằm trong trang Bash built-in tổng hợp, ít chi tiết.
  * `help alias` → tập trung hơn, dễ hiểu hơn.
* `help man` → Không hoạt động (man không phải built-in).
* `man -h | less` → usage ngắn, khác với `man man` đầy đủ.
* `--help` với `cd`, `pwd`, `tar`, `who`, `whoami`:
  * `tar`, `who`, `whoami` → có trang `--help`.
  * `cd`, `pwd` (built-in) → chỉ ra usage ngắn hoặc không hỗ trợ.

***

### 9. apropos

* `apropos "virtual memory"` → Trả về ít lệnh, liên quan đúng chủ đề (vd: `vmstat`).
* `apropos virtual` / `apropos memory` → nhiều lệnh, không chính xác lắm.
* `apropos "memory virtual"` → không có kết quả.
* `apropos manual` → Liệt kê lệnh liên quan đến man pages.

***

### 10. history

* `history` → In danh sách lệnh đã chạy (số dòng tùy HISTSIZE).
* `!!` → chạy lại lệnh vừa rồi. Nếu là `history` thì nó chạy lại `history` → trông như “không có gì”.
* `!1` → chạy lại lệnh số 1.
* `!n` → chạy lại lệnh có số n trong danh sách.
* `!m` → chạy lại lệnh `man …` gần nhất.
* `!p` → chạy lại lệnh `ps …` gần nhất.
* `!h` → chạy lại lệnh `help …` gần nhất.
* Cách ngắn nhất để gọi lại lệnh help cuối: `!h`.
* `history` lần nữa → danh sách dài thêm vì đã nhập thêm lệnh.

***

### 11. Variables

* `env` → Liệt kê tất cả biến.
  * `PWD` = thư mục hiện tại.
  * `HOME` = thư mục home.
  * `USER` = tên user.
  * `HOST` (hoặc `HOSTNAME`) = tên máy.
  * `PS1` = mẫu prompt.
*   Tạo biến:

    ```bash
    FIRST="Tên"
    LAST="Họ"
    ```
* `echo $FIRST $LAST` → In ra tên đầy đủ.
* `echo $FIRST $LAST $USER $HOME` → In đầy đủ First Last Username Home.

***

### 12. Alias

* `alias` → xem danh sách alias (nhiều alias màu mặc định).
* Tạo alias:
  * `alias home='cd ~'`
  * `alias ll='ls -lr'` (long listing, reverse order)
  * `alias ..='cd ..'`
  * `alias '*'='cd /'`
  * `alias info='pwd; whoami; arch; date'`
* `unalias home` → alias `home` biến mất.
* `unalias *` → lỗi vì `*` bị shell expand.
* `unalias '*'` → thành công.
* `alias` → xem lại các alias mình định nghĩa.

