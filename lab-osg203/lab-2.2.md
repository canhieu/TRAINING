# Lab 2.2

### 1. Shortcuts: `~`, Tab Completion, Wildcards

* `cd ~` → về thư mục home của user hiện tại.
* `cd ~zappaf` → về thư mục home của user `zappaf`, prompt đổi theo đường dẫn này.
* `pwd` → hiển thị đường dẫn tuyệt đối hiện tại.
* `cd ~foxr/HUMOR` → chuyển sang thư mục `HUMOR` của user `foxr`.

**Tab completion:**

* `cat b<tab>` → tự động hoàn thành thành `bumperstickers`.
* `cat s<tab>` → có tiếng beep vì nhiều file bắt đầu bằng `s`.
* `cat s<tab><tab>` → liệt kê tất cả file bắt đầu bằng `s`.
* `cat sm<tab>` → hoàn thành thành tên file duy nhất bắt đầu bằng `sm...`.

**Khác biệt khi Tab:**

* Trong `/usr/bin`, `cat c<tab><tab>` sẽ liệt kê danh sách rất dài các file bắt đầu bằng `c` (vì thư mục lớn).

**Wildcards:**

* `ls *.txt` → chỉ liệt kê file có đuôi `.txt`.
* `ls aa?.*` → `?` đại diện cho đúng **1 ký tự** → `aa1.abC` khớp, nhưng `aa10.bBC` không.
* `ls foo?` → chỉ hiển thị file bắt đầu bằng `foo` và thêm đúng 1 ký tự (nên có `foo2`, `foo3` nhưng không có `foo`).
* `ls *.[bc]*` → hiển thị file có phần mở rộng bắt đầu bằng `b` hoặc `c`. `aaa.abc` không xuất hiện vì phần mở rộng của nó là `abc` (bắt đầu bằng `a`).

***

### 2. Command Line Editing

* `control+p` → lặp lại lệnh trước đó.
* `control+b` → di chuyển con trỏ sang trái.
* `escape+b` → di chuyển con trỏ sang trái từng từ.
* Khi đổi `file1.txt` thành `file2.dat`, ta tận dụng phím tắt để chỉnh sửa nhanh, thay vì gõ lại toàn bộ.

**Một số phím khác:**

* `control+a` → về đầu dòng.
* `control+e` → về cuối dòng.
* `control+k` → xóa từ vị trí con trỏ đến hết dòng.
* `control+y` → dán lại nội dung vừa xóa (yank).
* `escape+f` → sang phải theo từng từ.
* `control+d` → xóa ký tự tại con trỏ hoặc thoát shell nếu dòng rỗng.
* `escape+d` → xóa từ vị trí con trỏ đến hết từ hiện tại.

***

### 3. Pipes (`|`)

* `ls | more` → hiển thị danh sách theo trang, dừng sau mỗi màn hình.
  * Space: sang trang mới.
  * Enter: xuống một dòng.
  * `s`: xuống nửa trang.
  * `q`: thoát.
* `ls | less` → tương tự `more` nhưng có thêm di chuyển bằng phím mũi tên, PgUp, Home, End.
* `ls | sort –r | less` → sắp xếp ngược thứ tự rồi hiển thị phân trang.
* `cat c* | grep "f$" | sort` →
  * `cat c*` → nối tất cả file bắt đầu bằng `c`.
  * `grep "f$"` → lọc dòng kết thúc bằng `f`.
  * `sort` → sắp xếp kết quả.
* `ls | wc –l` → đếm số file trong thư mục.
* `ls c* | wc –l` → đếm số file bắt đầu bằng `c`.

***

### 4. Redirection

* `ls > ~/etc-files.txt` → ghi đè danh sách file vào `etc-files.txt`.
* `ls >> ~/etc-files.txt` → nối thêm vào file.
* `less < ~/etc-files.txt` = `less ~/etc-files.txt` (không khác biệt).
* `wc << quit` → nhập từ bàn phím cho đến khi gõ `quit`, sau đó hiển thị số dòng/từ/ký tự.
* `wc <<` + `Ctrl+D` → kết thúc nhập bằng EOF.
* `cat << quit > list.txt` → nhập dữ liệu từ bàn phím, lưu vào `list.txt`.
* `cat << quit | sort > list2.txt` → nhập dữ liệu, sort rồi lưu vào `list2.txt`.

***

### 5. Variables và Aliases

* `X=0` → tạo biến trong shell hiện tại.
* Khi mở subshell (`bash`), `X` không còn (không thừa kế).
* `export X` → biến trở thành **environment variable**, có trong subshell.
* Khi đổi `X=1` trong subshell, giá trị không ảnh hưởng ngược ra outer shell.

**.bashrc vs .bash\_profile:**

* `.bashrc` → cấu hình cho **mỗi lần mở shell**.
* `.bash_profile` → chạy khi login vào hệ thống (điều chỉnh môi trường ban đầu).

**Alias:**

* `alias me='ps aux | grep Student'` → tạo alias `me` để tìm process của user Student.
* Ban đầu không hoạt động ngay vì chưa reload.
* `source .bashrc` → nạp lại file cấu hình. Sau đó chạy `me` sẽ hoạt động.
