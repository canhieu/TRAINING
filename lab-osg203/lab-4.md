# Lab 4

#### 1. Launching and Controlling Processes

**a) Mở thêm cửa sổ terminal**

* Lệnh: `gnome-terminal`
* Lệnh: `xterm`
* Nếu chưa có, hệ thống hỏi cài đặt, gõ y, mật khẩu root: \*\*\*\* :)) .
* Khi quay lại cửa sổ gốc, terminal bị khóa cho đến khi đóng `xterm`.

<figure><img src="../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

Câu hỏi: What happens when you attempt to use that terminal window?\
Trả lời: Không thể gõ lệnh vì terminal gốc bị tiến trình `xterm` chiếm foreground.

Câu hỏi: Sau khi đóng xterm, có thể dùng lại terminal gốc không?\
Trả lời: Có, terminal gốc hoạt động bình thường.

***

**b) Suspend tiến trình**

* Mở lại `xterm`.
* Trong terminal gốc, nhấn `Ctrl+Z`.

<figure><img src="../.gitbook/assets/image (586).png" alt=""><figcaption></figcaption></figure>

Câu hỏi: What message do you receive?\
Trả lời: Hiện thông báo `[1]+ Stopped xterm`.

Câu hỏi: Attempt to use the xterm window. What happens?\
Trả lời: Không dùng được, vì tiến trình đã bị dừng (suspended).

***

**c) Quản lý jobs**

* Gõ `jobs`, thấy danh sách jobs.
* Mở `man jobs`, sau đó `Ctrl+Z`.
* Mở `top`, sau đó `Ctrl+Z`.
* Gõ `jobs`.

<figure><img src="../.gitbook/assets/image (251).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (253).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (254).png" alt=""><figcaption></figcaption></figure>



Câu hỏi: What is displayed?\
Trả lời: Hiển thị danh sách jobs đang bị stopped, mỗi job có số thứ tự, trạng thái và tên lệnh.

* Resume job bằng `fg %n`.
* Resume job mới nhất chỉ cần `fg`.
* Thoát `man` bằng `q`.
* Sau khi resume và thoát, jobs còn lại được cập nhật.

***

**d) Chạy tiến trình nền**

* Lệnh: `xterm &`

<figure><img src="../.gitbook/assets/image (255).png" alt=""><figcaption></figcaption></figure>

Câu hỏi: How does this differ from entering xterm without &?\
Trả lời: Với dấu &, `xterm` chạy nền nên terminal gốc vẫn sử dụng bình thường.

* Lệnh: `find ~ -empty &`
* Khi kết thúc, nhấn Enter để thấy prompt xuất hiện.

<figure><img src="../.gitbook/assets/image (256).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (257).png" alt=""><figcaption></figcaption></figure>

Câu hỏi: What is displayed?\
Trả lời: Prompt mới hiện ra cùng thông báo hoàn thành job.

***

**e) Nhiều xterm**

* Mở `xterm`, nhấn `Ctrl+Z`.
* Gõ `jobs`.

Câu hỏi: How do the listings differ?\
Trả lời: Một job ở trạng thái Running, một job ở trạng thái Stopped.

* Resume bằng `fg`, thoát `xterm`.
* `Running` nghĩa là tiến trình chạy nền.
* `Stopped` nghĩa là tiến trình bị treo.

***

**f) Ứng dụng GUI**

* Mở Calculator từ menu hoặc gõ `gnome-calculator`.
* Gõ `jobs`.

Câu hỏi: What is displayed?\
Trả lời: Không có gì hiển thị vì jobs chỉ quản lý tiến trình con của shell.

Câu hỏi: Why do you not see information on the two GUI processes?\
Trả lời: Vì chúng không gắn với shell nên không nằm trong danh sách jobs.

***

**g) Dùng bg**

* Mở `xterm`, `Ctrl+Z`.
* Mở `man jobs`, `Ctrl+Z`.
* Mở `top`, `Ctrl+Z`.
* Gõ `jobs`.
* Lệnh: `bg %3`

Câu hỏi: What happens?\
Trả lời: Tiến trình `top` tiếp tục chạy ở nền.

* Lệnh: `bg %1`

Câu hỏi: Has xterm’s status changed?\
Trả lời: Có, từ Stopped thành Running (background).

***

#### 2. Monitoring Processes

**a) top**

* Mở `top`.

Câu hỏi: What is the %CPU usage of top?\
Trả lời: Rất nhỏ, thường dưới 1%.

* Trong terminal khác: `find / -empty`
* Trong top, tiến trình `find` thay đổi vị trí liên tục.

Câu hỏi: Why is it moving?\
Trả lời: Vì scheduler sắp xếp lại tiến trình theo mức sử dụng CPU và trạng thái.

Câu hỏi: As it moves, what happens to its %CPU and %MEM?\
Trả lời: %CPU tăng giảm tùy thời điểm, %MEM nhỏ và ổn định.

Câu hỏi: After find ends, what happens to the find entry?\
Trả lời: Nó biến mất khỏi danh sách.

***

**b) systemd**

Câu hỏi: Why does systemd have a PID of 1?\
Trả lời: Vì systemd là tiến trình đầu tiên khi kernel khởi động.

Câu hỏi: Process có PID lớn nhất là gì, PID bao nhiêu, User nào?\
Trả lời: Là tiến trình do user tạo gần nhất, ví dụ `bash` hoặc `top`, PID lớn nhất, User là người đăng nhập.

***

**c) Thông số VIRT, RES, SHR**

* VIRT: dung lượng bộ nhớ ảo.
* RES: dung lượng bộ nhớ thực sử dụng trong RAM.
* SHR: dung lượng bộ nhớ chia sẻ.

Câu hỏi: What are the values for systemd?\
Trả lời: Xem trong top, ví dụ: VIRT \~ 10M, RES \~ 1M, SHR \~ 500K (con số thay đổi theo hệ thống).

***

**d) System Monitor**

* Mở bằng `gnome-system-monitor`.
* So sánh với top: cùng dữ liệu nhưng hiển thị trực quan bằng GUI.

***

**e) Preferences**

Câu hỏi: Which fields are already selected?\
Trả lời: PID, User, %CPU, %MEM, Command, State, Priority, Resident memory, Virtual memory.

Câu hỏi: What fields differ from top?\
Trả lời: System Monitor có thêm Owner, Command line; top có STAT và TTY.

***

**f) Điều khiển top**

* `u Student`: không thấy process nào.
* `u root`: hiện process của root.
* `u`: hiển thị lại toàn bộ.
* `d 0.5`: thay đổi refresh rate thành 0.5s.
* `f` → thêm cột TTY.

Câu hỏi: Is there a column for Tty now?\
Trả lời: Có.

Câu hỏi: Which processes are in a TTY other than ?\
Trả lời: Các tiến trình gắn với terminal khác hoặc ? nếu không gắn terminal.

***

**g) ps command**

* `ps`: chỉ liệt kê process hiện tại trong shell.
* `ps a`: hiển thị process của tất cả users gắn TTY.
* `ps u`: thêm thông tin chi tiết về user, CPU, memory.
* `ps au`: kết hợp cả hai.

VSZ = bộ nhớ ảo, RSS = bộ nhớ thực, STAT = trạng thái tiến trình.

* Ss: sleeping, session leader.
* R+: running, foreground.
* Sl: sleeping, multi-threaded.
* Ssl: sleeping, multi-threaded, session leader.
* `ps ax`: tất cả process, kể cả không gắn TTY.
* `ps ax -H`: hiển thị quan hệ cha-con.
* `ps axef`: hiển thị cây ASCII.

Câu hỏi: Với gdm, process nào spawn process nào?\
Trả lời: gdm chính khởi tạo các gdm-child và các tiến trình GNOME khác.

Câu hỏi: ps ax –H của chính nó có history gì?\
Trả lời: Nó được spawn bởi bash → ps → ps ax –H.

***

#### 3. Priorities

**a) nice và renice**

* Lệnh: `nice -n -20 find / -empty` → PR cao, NI = -20.
* Lấy PID của top: `renice -n 10 -p PID`.

Câu hỏi: What response did you get?\
Trả lời: Thông báo priority được thay đổi.

* Thử `renice -n -10 -p PID`.

Câu hỏi: What happens?\
Trả lời: Báo lỗi nếu không phải root, vì chỉ root mới giảm giá trị nice.

***

**b) Đổi priority trong System Monitor**

* Chuột phải → Change Priority.
* Có các mức: Very High, High, Normal, Low, Very Low.
* Custom range: -20 đến 19.

***

#### 4. Killing Processes

**a) kill**

* Mở `xterm &`, dùng `ps u` lấy PID.
* Lệnh: `kill -s 9 PID` → xterm bị kill ngay.

Câu hỏi: What happens?\
Trả lời: Cửa sổ xterm đóng, terminal gốc hiển thị prompt.

* Lệnh: `kill -s 1 PID` → không chết ngay.
* Cần `SIGKILL (9)` để buộc kết thúc.

Câu hỏi: Why did –s 9 kill it but not 1?\
Trả lời: Vì SIGKILL không thể bị chặn, còn SIGHUP chỉ gửi tín hiệu treo.

Trong System Monitor, chuột phải vào tiến trình → có Stop, Continue, Kill, Change Priority.

***

**b) killall**

* Mở hai xterm.
* Lệnh: `killall xterm` → cả hai bị kill.
* Mở hai xterm nữa.
* Lệnh: `killall -i xterm` → hỏi xác nhận trước khi kill từng cái.

Câu hỏi: Why might root use killall?\
Trả lời: Để kết thúc tất cả tiến trình của một user hoặc một loại process.
