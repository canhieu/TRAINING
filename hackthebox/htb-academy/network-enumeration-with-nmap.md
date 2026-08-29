# Network Enumeration with Nmap

## Enumeration

* **Enumeration (liệt kê/khai thác thông tin) là phần quan trọng nhất.**
  * Nghệ thuật, độ khó và mục tiêu ở giai đoạn này **không** phải là chiếm quyền truy cập ngay—mà là **xác định mọi con đường** khả dĩ để tấn công mục tiêu.
  * Mục tiêu: tìm và liệt kê tất cả cách ta có thể tấn công một mục tiêu.
* **Không chỉ là công cụ — quan trọng là biết dùng thông tin.**
  * Công cụ chỉ có ích nếu ta biết cách sử dụng kết quả từ chúng.
  * Công cụ là công cụ; chúng không thể thay thế kiến thức và sự tỉ mỉ của con người.
  * Ở giai đoạn này, cần **tương tác chủ động** với từng dịch vụ để xem dịch vụ trả về thông tin gì và mở ra những khả năng nào.
* **Hiểu biết về dịch vụ và cú pháp giao tiếp là thiết yếu.**
  * Cần nắm rõ cách hoạt động của dịch vụ và cú pháp mà nó dùng để giao tiếp, để tương tác hiệu quả.
* **Mục đích của giai đoạn: mở rộng kiến thức và thích nghi.**
  * Mục tiêu là nâng cao hiểu biết về kỹ thuật, giao thức và cách chúng hoạt động.
  * Học cách xử lý thông tin mới và ghép nối với kiến thức đã có.
  * Enumeration = thu thập càng nhiều thông tin càng tốt — thông tin nhiều thì dễ tìm vector tấn công hơn.
* **Minh họa bằng ví dụ thực tế (ẩn dụ chìa khóa):**
  * Nếu bạn tìm chìa khóa trong nhà mà chỉ được bảo “ở phòng khách” thì mất thời gian.
  * Nếu được mô tả chi tiết như “trên kệ trắng cạnh TV, ngăn thứ ba” thì sẽ nhanh hơn nhiều.
  * Tương tự, thông tin càng chi tiết thì việc tìm lỗ hổng càng dễ.
* **Hai nguồn chính giúp ta vào hệ thống:**
  1. **Các chức năng/tài nguyên** cho phép tương tác với mục tiêu và/hoặc cung cấp thêm thông tin.
  2. **Thông tin** (từ hệ thống/dịch vụ) giúp lộ thêm dữ liệu quan trọng để truy cập mục tiêu.
* **Khi quét/kiểm tra, ta tìm đúng hai khả năng đó.**
  * Phần lớn thông tin thu được đến từ **misconfiguration** (cấu hình sai) hoặc **bỏ bê an ninh** của dịch vụ.
  * Những sai sót này thường do thiếu hiểu biết hoặc tư duy an ninh sai (ví dụ: chỉ trông cậy vào firewall, GPO, cập nhật liên tục — đôi khi không đủ).
* **Enumeration là then chốt — nhưng thường bị hiểu sai.**
  * Nhiều người nghĩ chỉ cần chạy nhiều công cụ hơn, nhưng thường vấn đề là **không biết cách tương tác với dịch vụ** và không biết thông tin nào quan trọng.
  * Thiếu hiểu biết về dịch vụ khiến người ta dậm chân tại chỗ; nếu họ bỏ vài giờ tìm hiểu dịch vụ đó, họ có thể tiết kiệm nhiều giờ hoặc ngày để đạt mục tiêu.
* **Enumeration thủ công rất quan trọng.**
  * Nhiều công cụ quét giúp nhanh và tiện, nhưng không thể thay thế sự thâm nhập thủ công.
  * Công cụ tự động có hạn chế: ví dụ, nhiều công cụ đặt **timeout** chờ phản hồi từ dịch vụ.
    * Nếu dịch vụ chậm, công cụ có thể đánh dấu port/dịch vụ là **closed / filtered / unknown**.
    * Trong trường hợp “closed” mà Nmap không hiển thị, ta có thể bỏ lỡ một dịch vụ hữu ích.
    * Một port/dịch vụ bị đánh dấu sai có thể làm chúng ta tốn thời gian rất nhiều trước khi tìm thấy con đường truy cập.

## Introduction to Nmap

* **Giới thiệu về Nmap**
  * Network Mapper (Nmap) là một công cụ mã nguồn mở dùng để phân tích mạng và kiểm toán an ninh, được viết bằng C, C++, Python và Lua.
  * Mục đích: quét mạng để xác định host nào đang online, dịch vụ và ứng dụng nào chạy (nơi có thể sẽ xác định được tên và phiên bản).
  * Có thể phát hiện hệ điều hành và phiên bản trên các host.
  * Ngoài các tính năng khác, Nmap còn có các khả năng quét giúp xác định xem bộ lọc gói, tường lửa, hoặc hệ thống phát hiện xâm nhập (IDS) có được cấu hình hay không.
* **Trường hợp sử dụng (Use Cases)**
  * Công cụ được dùng rộng rãi bởi quản trị mạng và chuyên gia an ninh CNTT để:
    * Kiểm toán khía cạnh an ninh của mạng.
    * Mô phỏng bài kiểm tra xâm nhập (penetration tests).
    * Kiểm tra cấu hình tường lửa và IDS.
  * Những nhiệm vụ cụ thể:
    * Map mạng (vẽ sơ đồ mạng).
    * Phân tích phản hồi.
    * Xác định các cổng mở.
    * Đánh giá lỗ hổng.
* **Kiến trúc Nmap (Nmap Architecture)**
  * Nmap cung cấp nhiều loại scan khác nhau để thu các kết quả khác nhau về mục tiêu.
  * Về cơ bản, Nmap có thể chia theo các kỹ thuật quét:
    * Phát hiện host (Host discovery).
    * Quét cổng (Port scanning).
    * Liệt kê/dò dịch vụ và nhận diện dịch vụ (Service enumeration and detection).
    * Nhận diện hệ điều hành (OS detection).
    * Tương tác có thể lập kịch bản với dịch vụ mục tiêu (Nmap Scripting Engine — NSE).
*   **Cú pháp (Syntax)**

    * Cú pháp Nmap khá đơn giản:

    ```
    nmap <scan types> <options> <target>
    ```

    * Ví dụ giao diện gợi ý:

    ```
    0xlc13n@htb[/htb]$ nmap <scan types> <options> <target>
    ```
*   **Kỹ thuật quét (Scan Techniques)**

    * Nmap hỗ trợ nhiều kỹ thuật quét, tạo các kiểu kết nối khác nhau và sử dụng các gói có cấu trúc khác nhau.
    * Khi chạy `nmap --help`, ta sẽ thấy danh sách các kỹ thuật:

    ```
    0xlc13n@htb[/htb]$ nmap --help

    <SNIP>
    SCAN TECHNIQUES:
      -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
      -sU: UDP Scan
      -sN/sF/sX: TCP Null, FIN, and Xmas scans
      --scanflags <flags>: Customize TCP scan flags
      -sI <zombie host[:probeport]>: Idle scan
      -sY/sZ: SCTP INIT/COOKIE-ECHO scans
      -sO: IP protocol scan
      -b <FTP relay host>: FTP bounce scan
    <SNIP>
    ```

    * **Ví dụ giải thích một phương pháp phổ biến — TCP SYN scan (-sS):**
      * TCP-SYN scan (`-sS`) là một trong các mặc định phổ biến và cũng là một trong những phương pháp quét được ưa dùng.
      * Đặc điểm:
        * Gửi một gói chỉ có flag **SYN**.
        * **Không** hoàn tất handshake 3 bước — tức là không thiết lập kết nối TCP đầy đủ với port được quét.
        * Cho phép quét hàng nghìn port mỗi giây (tùy điều kiện).
      * Phản hồi và cách Nmap đánh giá:
        * Nếu mục tiêu trả về gói **SYN-ACK** → Nmap coi port là **open**.
        * Nếu mục tiêu trả về gói **RST** → port được coi là **closed**.
        * Nếu Nmap **không nhận được gói trả lời** → hiển thị là **filtered** (bị lọc) —
        * &#x20;tuỳ cấu hình tường lửa, một số gói có thể bị thả hoặc bỏ qua.
*   **Ví dụ minh họa (mô phỏng một scan SYN lên localhost)**

    * Lệnh:

    ```
    sudo nmap -sS localhost
    ```

    * Ví dụ kết quả (mã nguồn ví dụ):

    ```
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-11 22:50 UTC
    Nmap scan report for localhost (127.0.0.1)
    Host is up (0.000010s latency).
    Not shown: 996 closed ports
    PORT     STATE SERVICE
    22/tcp   open  ssh
    80/tcp   open  http
    5432/tcp open  postgresql
    5901/tcp open  vnc-1

    Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
    ```

    * **Giải thích kết quả:**
      * Trong ví dụ, có **bốn cổng TCP mở**: 22, 80, 5432, 5901.
      * Cột đầu: **số cổng**.
      * Cột hai: **trạng thái dịch vụ** (open/closed/filtered).
      * Cột cuối: **tên dịch vụ** (ví dụ: ssh, http, postgresql, vnc-1) — tên dịch vụ do Nmap suy đoán/khớp theo cổng thường dùng.



## &#x20;**Host Enumeration**

### Host Discovery (Phát hiện host)

* **Mục đích chung**
  * Khi tiến hành penetration test nội bộ cho cả mạng của một công ty, việc **đầu tiên** cần làm là có cái nhìn tổng quát về những hệ thống nào đang online — những hệ thống mà ta có thể tương tác.
  * Để chủ động phát hiện các host này trên mạng, ta dùng các tùy chọn _host discovery_ của Nmap.
  * Nmap cung cấp nhiều tùy chọn để xác định liệu mục tiêu có “sống” hay không. **Phương pháp phát hiện host hiệu quả nhất** thường dùng là gửi **ICMP echo requests** (ping), và phần sau sẽ đi sâu vào điều này.
* **Luôn lưu kết quả quét**
  * Nên **lưu mọi lần quét** (scan). Kết quả lưu được dùng để so sánh, làm tài liệu và báo cáo.
  * Các công cụ khác nhau đôi khi cho kết quả khác nhau — lưu lại giúp phân biệt “công cụ nào cho kết quả gì”.
*   **Quét một dải mạng (Scan Network Range)**

    * Ví dụ quét toàn bộ dải 10.129.2.0/24, chỉ phát hiện host (không quét port), lưu output ở nhiều định dạng, rồi tách ra địa chỉ IP online:

    ```bash
    sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5
    ```

    * Kết quả (ví dụ):

    ```
    10.129.2.4
    10.129.2.10
    10.129.2.11
    10.129.2.18
    10.129.2.19
    10.129.2.20
    10.129.2.28
    ```

    * Giải thích các tùy chọn:
      * `10.129.2.0/24` — dải mạng mục tiêu.
      * `-sn` — tắt port scanning (chỉ ping / host discovery).
      * `-oA tnet` — lưu kết quả ở **tất cả** các định dạng (normal, xml, grepable) với tiền tố `tnet`.
    * Lưu ý: phương pháp này chỉ hiệu quả nếu firewall của host cho phép các kiểu ping mặc định; nếu không, cần dùng kỹ thuật khác (xem phần Firewall và IDS Evasion).
*   **Quét từ danh sách IP (Scan IP List)**

    * Nếu được cung cấp một file danh sách IP (thường gặp trong bài test nội bộ), ta có thể để Nmap đọc danh sách đó:
    * Ví dụ nội dung `hosts.lst`:

    ```
    10.129.2.4
    10.129.2.10
    10.129.2.11
    10.129.2.18
    10.129.2.19
    10.129.2.20
    10.129.2.28
    ```

    * Lệnh quét với file:

    ```bash
    sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
    ```

    * Kết quả ví dụ:

    ```
    10.129.2.18
    10.129.2.19
    10.129.2.20
    ```

    * Giải thích:
      * `-iL hosts.lst` — đọc danh sách targets từ file `hosts.lst`.
      * Trong ví dụ, chỉ **3/7** host trả lời — có thể các host khác chặn ICMP/ARP nên Nmap không thấy phản hồi và đánh dấu là inactive.
*   **Quét nhiều IP riêng lẻ (Scan Multiple IPs)**

    * Ta có thể trực tiếp liệt kê các IP trên command line:

    ```bash
    sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20 | grep for | cut -d" " -f5
    ```

    * Nếu các IP liên tiếp, có thể dùng dấu gạch nối trong octet:

    ```bash
    sudo nmap -sn -oA tnet 10.129.2.18-20 | grep for | cut -d" " -f5
    ```

    * Kết quả đều trả về:

    ```
    10.129.2.18
    10.129.2.19
    10.129.2.20
    ```
*   **Quét một IP đơn (Scan Single IP)**

    * Trước khi quét port và service của 1 host, cần kiểm tra host đó **alive** hay không:

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host
    ```

    * Kết quả ví dụ:

    ```
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-14 23:59 CEST
    Nmap scan report for 10.129.2.18
    Host is up (0.087s latency).
    MAC Address: DE:AD:00:00:BE:EF
    Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
    ```

    * Giải thích:
      * Khi dùng `-sn`, Nmap **tự động** dùng ping scan theo mặc định bằng **ICMP Echo Requests** (`-PE`) — nhưng thực tế trước khi gửi ICMP, Nmap **thường gửi ARP ping** (trong LAN) và nhận ARP reply, nên nhiều lần ta không thấy ICMP trong lần quét đầu.
*   **Kiểm tra packet-level (--packet-trace)**

    * Để xem chính xác Nmap đã gửi/nhận gói nào, thêm `--packet-trace`:

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace
    ```

    * Ví dụ output cho thấy ARP:

    ```
    SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
    RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
    Nmap scan report for 10.129.2.18
    Host is up (0.023s latency).
    MAC Address: DE:AD:00:00:BE:EF
    Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
    ```

    * Thông tin tùy chọn:
      * `-PE` — ép Nmap dùng ICMP Echo requests để ping.
      * `--packet-trace` — hiển thị tất cả gói đã gửi và nhận (dùng để debug/kiểm tra kỹ).
*   **Hiện lý do (--reason)**

    * Muốn biết vì sao Nmap đánh dấu host là “up”, dùng `--reason` để Nmap giải thích:

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host -PE --reason
    ```

    * Ví dụ output:

    ```
    SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
    RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
    Nmap scan report for 10.129.2.18
    Host is up, received arp-response (0.028s latency).
    MAC Address: DE:AD:00:00:BE:EF
    Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
    ```

    * `--reason` giúp ta hiểu căn cứ Nmap dùng để đánh giá trạng thái.
*   **Tắt ARP ping để ép dùng ICMP (--disable-arp-ping)**

    * Vì Nmap thường ưu tiên ARP trong LAN, nếu muốn **bắt buộc** gửi ICMP echo requests thay vì ARP, dùng `--disable-arp-ping`:

    ```bash
    sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping
    ```

    * Ví dụ output (hiện ICMP echo request/reply):

    ```
    SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
    RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
    Nmap scan report for 10.129.2.18
    Host is up (0.086s latency).
    MAC Address: DE:AD:00:00:BE:EF
    Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
    ```
* **Bài học rút ra**
  * Chi tiết quan trọng: một **ICMP echo request** có thể cho biết host alive và giúp xác định hệ thống.
  * Tuy nhiên, do cơ chế ưu tiên ARP trong LAN hoặc do firewall chặn ICMP, kết quả host discovery có thể khác nhau — vì vậy:
    * Kiểm tra bằng nhiều phương pháp (ARP, ICMP, TCP ping, UDP ping, v.v.).
    * Dùng `--packet-trace` và `--reason` để hiểu hành vi thực tế.
    * Lưu kết quả (để so sánh) và linh hoạt thay đổi kỹ thuật khi firewall/IDS can thiệp.
* **Tham khảo thêm**
  * Chi tiết chiến lược host discovery: `https://nmap.org/book/host-discovery-strategies.html`



### Host and Port Scanning

Mục tiêu của quét host/port là xác định các cổng mở, dịch vụ đang chạy, phiên bản dịch vụ, thông tin dịch vụ trả về và hệ điều hành. Có vài phương pháp quét (TCP connect, SYN, UDP, v.v.) — mỗi phương pháp khác nhau về độ chính xác, tốc độ và mức độ “lộ” (stealth). Kết quả quét trả về trạng thái cổng (open/closed/filtered/...). Hiểu cách Nmap gửi/nhận gói giúp giải thích kết quả.

#### Những thông tin cần lấy từ hệ thống

* Cổng mở và dịch vụ (Open ports & services)
* Phiên bản dịch vụ (Service versions)
* Thông tin do dịch vụ cung cấp (banner/info)
* Hệ điều hành (OS fingerprinting)

#### Các trạng thái có thể trả về cho một cổng (tóm gọn)

Có 6 trạng thái chính mà Nmap/scan có thể báo:

| State            | Mô tả (ngắn gọn)                                                                            |
| ---------------- | ------------------------------------------------------------------------------------------- |
| open             | Kết nối tới cổng được chấp nhận (TCP/UDP/SCTP).                                             |
| closed           | Cổng đóng — TCP trả về RST. Vẫn có thể dùng để kiểm tra host còn sống.                      |
| filtered         | Không xác định được open/closed — thường do firewall drop packet hoặc không có phản hồi.    |
| unfiltered       | Chỉ xuất hiện trong TCP-ACK scan — port truy cập được nhưng không xác định open hay closed. |
| open\|filtered   | Không nhận được phản hồi — khả năng do firewall/packet filter che giấu trạng thái.          |
| closed\|filtered | Chỉ xuất hiện trong IP ID idle scans — không thể xác định closed hay filtered.              |



#### Phát hiện cổng TCP mở

* Mặc định Nmap quét top 1000 TCP port bằng SYN scan (`-sS`) khi chạy với quyền root; nếu không có root thì dùng TCP connect scan (`-sT`) mặc định.
* Có thể chỉ định cổng riêng lẻ `-p 22,25,80`, dải `-p 22-445`, top ports `--top-ports=10`, tất cả `-p-`, hoặc nhanh `-F` (top 100).

**Ví dụ: Scanning Top 10 TCP Ports**<br>

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 --top-ports=10 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
--top-ports=10  Scans the specified top ports that have been defined as most frequent.
```

#### Theo dõi (trace) các gói để hiểu SYN scan&#x20;

* Dùng `--packet-trace` cùng `-Pn -n --disable-arp-ping` để xem chính xác Nmap gửi gì và nhận gì (SYN gửi, RST/ACK trả về,...).
* RST+ACK từ mục tiêu thường chỉ ra cổng **closed**.

**Ví dụ: Nmap - Trace the Packets**<br>

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:39 CEST
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44  seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40  seq=0 win=0
Nmap scan report for 10.129.2.28
Host is up (0.014s latency).

PORT   STATE  SERVICE
21/tcp closed ftp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p 21    Scans only the specified port.
--packet-trace  Shows all packets sent and received.
-n  Disables DNS resolution.
--disable-arp-ping   Disables ARP ping.
```

#### Connect Scan (TCP Connect `-sT`)&#x20;

* Thực hiện đầy đủ TCP three-way handshake (SYN → SYN/ACK → ACK). Nếu nhận SYN/ACK — cổng **open**; nếu RST — **closed**.
* Ưu: chính xác, “lành tính” với dịch vụ (không gây lỗi dịch vụ).
* Nhược: dễ bị ghi log, không thầm lặng (không stealth). Chậm hơn SYN scan.
* Dùng khi cần độ chính xác hoặc khi firewall cản inbound SYN nhưng cho phép kết nối outbound.

**Ví dụ: Connect Scan trên TCP port 443**

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:26 CET
CONN (0.0385s) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (0.0396s) TCP localhost > 10.129.2.28:443 => Connected
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds
```

#### Trạng thái “filtered” và cách nhận biết (tóm gọn)

* `filtered` thường do firewall **drop** (không trả lời) hoặc **reject** (trả ICMP unreachable).
* Khi bị drop: Nmap phải retry nhiều lần (thời gian quét tăng).
* Khi firewall **reject** cổng, có thể nhận được ICMP type 3/code 3 (port unreachable) — chỉ ra rằng firewall hoặc hệ thống trả về unreachable.

**Ví dụ: port 139 (drop) và port 445 (reject bằng ICMP)**

```
... (ví dụ quét port 139, kết quả filtered; và ví dụ quét port 445, trả ICMP unreachable) ...
```

#### Phát hiện cổng UDP (tóm gọn)

* UDP là không kết nối (stateless) → không có three-way handshake → không có ACK. Vì vậy: quét UDP (`-sU`) **chậm hơn** nhiều và thường trả `open|filtered` nếu không có phản hồi.
* Nếu ứng dụng UDP trả phản hồi → Nmap có thể đánh dấu `open` (ví dụ UDP response).
* Nếu nhận ICMP port-unreachable (type 3/code 3) → cổng **closed**.
* Do nhiều UDP service không phản hồi khi nhận gói rỗng, quét UDP hay để lại nhiều cổng ở trạng thái `open|filtered`.

**Ví dụ: UDP scan (-sU) và các ví dụ đầu ra**

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -F -sU

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
-F  Scans top 100 ports.
-sU Performs a UDP scan.
```

và các ví dụ `-sU -Pn -n --disable-arp-ping --packet-trace -p 137` / `-p 100` / `-p 138` trong tài liệu gốc&#x20;

#### Version Scan (`-sV`)&#x20;

* Dùng `-sV` để gửi probe tới port mở để nhận banner, xác định tên dịch vụ và phiên bản (service/version detection).
* Thời gian quét có thể tăng vì Nmap thử nhiều probe/đọc phản hồi. Kết quả hữu ích để mapping dịch vụ và tìm CVE tương ứng.

**Ví dụ: Version Scan trên port 445**

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
...
PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: Ubuntu

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds
```

<figure><img src="../../.gitbook/assets/image (234).png" alt=""><figcaption></figcaption></figure>

<br>

<figure><img src="../../.gitbook/assets/image (233).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (235).png" alt=""><figcaption></figcaption></figure>

→ Vì port 445 đang mở (dịch vụ `microsoft-ds`), script này có thể trả về hostname, OS, và workgroup.



### Saving the Results

#### Different Formats

Trong quá trình quét, luôn lưu kết quả để so sánh các phương pháp quét khác nhau. Nmap có thể lưu kết quả ở 3 định dạng:

* Normal output (`-oN`) với phần mở rộng `.nmap`
* Grepable output (`-oG`) với phần mở rộng `.gnmap`
* XML output (`-oX`) với phần mở rộng `.xml`

Tùy chọn `-oA` lưu kết quả ở cả 3 định dạng cùng lúc. Ví dụ lệnh:

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -oA target

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 12:14 CEST
Nmap scan report for 10.129.2.28
Host is up (0.0091s latency).
Not shown: 65525 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
25/tcp    open  smtp
80/tcp    open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 10.22 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-oA target    Saves the results in all formats, starting the name of each file with 'target'.
```

Nếu không chỉ định đường dẫn đầy đủ, các file sẽ được lưu vào thư mục hiện tại.

```
0xlc13n@htb[/htb]$ ls

target.gnmap target.xml  target.nmap
```

#### Normal Output

```
0xlc13n@htb[/htb]$ cat target.nmap

# Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28
Nmap scan report for 10.129.2.28
Host is up (0.053s latency).
Not shown: 4 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
25/tcp open  smtp
80/tcp open  http
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

# Nmap done at Tue Jun 16 12:15:03 2020 -- 1 IP address (1 host up) scanned in 10.22 seconds
```

#### Grepable Output

```
0xlc13n@htb[/htb]$ cat target.gnmap

# Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28
Host: 10.129.2.28 ()    Status: Up
Host: 10.129.2.28 ()    Ports: 22/open/tcp//ssh///, 25/open/tcp//smtp///, 80/open/tcp//http///    Ignored State: closed (4)
# Nmap done at Tue Jun 16 12:14:53 2020 -- 1 IP address (1 host up) scanned in 10.22 seconds
```

#### XML Output

```
0xlc13n@htb[/htb]$ cat target.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE nmaprun>
<?xml-stylesheet href="file:///usr/local/bin/../share/nmap/nmap.xsl" type="text/xsl"?>
<!-- Nmap 7.80 scan initiated Tue Jun 16 12:14:53 2020 as: nmap -p- -oA target 10.129.2.28 -->
<nmaprun scanner="nmap" args="nmap -p- -oA target 10.129.2.28" start="12145301719" startstr="Tue Jun 16 12:15:03 2020" version="7.80" xmloutputversion="1.04">
<scaninfo type="syn" protocol="tcp" numservices="65535" services="1-65535"/>
<verbose level="0"/>
<debugging level="0"/>
<host starttime="12145301719" endtime="12150323493"><status state="up" reason="arp-response" reason_ttl="0"/>
<address addr="10.129.2.28" addrtype="ipv4"/>
<address addr="DE:AD:00:00:BE:EF" addrtype="mac" vendor="Intel Corporate"/>
<hostnames>
</hostnames>
<ports><extraports state="closed" count="4">
<extrareasons reason="resets" count="4"/>
</extraports>
<port protocol="tcp" portid="22"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ssh" method="table" conf="3"/></port>
<port protocol="tcp" portid="25"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="smtp" method="table" conf="3"/></port>
<port protocol="tcp" portid="80"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="http" method="table" conf="3"/></port>
</ports>
<times srtt="52614" rttvar="75640" to="355174"/>
</host>
<runstats><finished time="12150323493" timestr="Tue Jun 16 12:14:53 2020" elapsed="10.22" summary="Nmap done at Tue Jun 16 12:15:03 2020; 1 IP address (1 host up) scanned in 10.22 seconds" exit="success"/><hosts up="1" down="0" total="1"/>
</runstats>
</nmaprun>
```

#### Style sheets

Với XML output, có thể chuyển sang HTML để dễ đọc bằng `xsltproc`:

```
0xlc13n@htb[/htb]$ xsltproc target.xml -o target.html
```

Mở `target.html` trong trình duyệt sẽ hiển thị báo cáo Nmap dạng HTML trực quan.

<figure><img src="../../.gitbook/assets/image (236).png" alt=""><figcaption></figcaption></figure>

### Service Enumeration



Điều quan trọng là xác định chính xác ứng dụng và phiên bản của nó. Thông tin này giúp ta dò tìm các lỗ hổng đã biết và phân tích mã nguồn (nếu có) cho đúng phiên bản. Một số phiên bản chính xác cho phép tìm kiếm exploit phù hợp với dịch vụ và hệ điều hành mục tiêu.

#### Service Version Detection

Nên chạy quét cổng nhanh trước để có cái nhìn sơ bộ về các cổng có sẵn — giảm lưu lượng và rủi ro bị phát hiện/khóa. Sau đó có thể chạy quét toàn bộ cổng (`-p-`) trong nền và dùng quét xác định phiên bản (`-sV`) trên các cổng đã mở.

Một lần quét đầy đủ có thể rất lâu; trong quá trình quét, nhấn \[Space Bar] để Nmap hiển thị trạng thái quét.

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:44 CEST
[Space Bar]
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 3.64% done; ETC: 19:45 (0:00:53 remaining)
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-sV    Performs service version detection on specified ports.
```

Có thể dùng `--stats-every=5s` để Nmap hiển thị tiến độ theo khoảng thời gian định trước (s: giây, m: phút).

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV --stats-every=5s

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:46 CEST
Stats: 0:00:05 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 13.91% done; ETC: 19:49 (0:00:31 remaining)
Stats: 0:00:10 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 39.57% done; ETC: 19:48 (0:00:15 remaining)
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-sV    Performs service version detection on specified ports.
--stats-every=5s    Shows the progress of the scan every 5 seconds.
```

Tăng mức verbosity (`-v` / `-vv`) sẽ hiển thị cổng vừa phát hiện ngay khi Nmap bắt gặp chúng.

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -v 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:03 CEST
NSE: Loaded 45 scripts for scanning.
Initiating ARP Ping Scan at 20:03
Scanning 10.129.2.28 [1 port]
Completed ARP Ping Scan at 20:03, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:03
Completed Parallel DNS resolution of 1 host. at 20:03, 0.02s elapsed
Initiating SYN Stealth Scan at 20:03
Scanning 10.129.2.28 [65535 ports]
Discovered open port 995/tcp on 10.129.2.28
Discovered open port 80/tcp on 10.129.2.28
Discovered open port 993/tcp on 10.129.2.28
Discovered open port 143/tcp on 10.129.2.28
Discovered open port 25/tcp on 10.129.2.28
Discovered open port 110/tcp on 10.129.2.28
Discovered open port 22/tcp on 10.129.2.28
<SNIP>
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-sV    Performs service version detection on specified ports.
-v    Increases the verbosity of the scan, which displays more detailed information.
```

#### Banner Grabbing

Sau khi quét xong, Nmap liệt kê các cổng TCP mở cùng dịch vụ và phiên bản (nếu thu được). Nmap chủ yếu dựa vào banner; nếu không nhận dạng được từ banner, Nmap dùng cơ chế so khớp signature, điều này làm kéo dài thời gian quét.

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:00 CEST
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).
Not shown: 65525 closed ports
PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open     smtp         Postfix smtpd
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
110/tcp   open     pop3         Dovecot pop3d
139/tcp   filtered netbios-ssn
143/tcp   open     imap         Dovecot imapd (Ubuntu)
445/tcp   filtered microsoft-ds
993/tcp   open     ssl/imap     Dovecot imapd (Ubuntu)
995/tcp   open     ssl/pop3     Dovecot pop3d
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-sV    Performs service version detection on specified ports.
```

Nếu Nmap bỏ sót thông tin, banner thô hoặc các gói trả về có thể chứa thông tin nhiều hơn. Ví dụ sau cho thấy Nmap ghi một dòng nsock info, nhưng banner đầy đủ từ SMTP chứa tên distro.

```
0xlc13n@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 20:10 CEST
<SNIP>
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
Service scan match (Probe NULL matched with NULL line 3104): 10.129.2.28:25 is smtp.  Version: |Postfix smtpd|||
NSOCK INFO [0.4200s] nsock_iod_delete(): nsock_iod_delete (IOD #1)
Nmap scan report for 10.129.2.28
Host is up (0.076s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
Scanning Options    Description
10.129.2.28    Scans the specified target.
-p-    Scans all ports.
-sV    Performs service version detection on specified ports.
-Pn    Disables ICMP Echo requests.
-n    Disables DNS resolution.
--disable-arp-ping    Disables ARP ping.
--packet-trace    Shows all packets sent and received.
```

Khi server gửi banner sau handshake, có thể quan sát bằng cách kết nối thủ công (ví dụ `nc`) và bắt gói bằng `tcpdump`.

```
0xlc13n@htb[/htb]$ sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
```

```
0xlc13n@htb[/htb]$  nc -nv 10.129.2.28 25

Connection to 10.129.2.28 port 25 [tcp/*] succeeded!
220 inlane ESMTP Postfix (Ubuntu)
```

Ví dụ tcpdump (hiển thị ba bước handshake và banner sau đó):

```
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 1, win 2058, options [nop,nop,TS val 331260304 ecr 1800383922], length 0
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 36, win 2058, options [nop,nop,TS val 331260368 ecr 1800383985], length 0
```

Ba dòng đầu là three-way handshake (SYN, SYN-ACK, ACK). Dòng thứ tư là PSH-ACK chứa banner do server gửi. Dòng cuối cùng là ACK từ client xác nhận đã nhận dữ liệu.....







