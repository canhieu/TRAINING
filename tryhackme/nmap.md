---
hidden: true
---

# NMAP

## A. Nmap Live Host Discovery

### 1. Intro

* **Mục tiêu khi khảo sát mạng**:
  * Xác định hệ thống nào đang hoạt động.
  * Xác định dịch vụ nào đang chạy (phòng sau).
* **Chuỗi 4 phòng về Nmap (thuộc module Network Security)**:
  1. Nmap Live Host Discovery
  2. Nmap Basic Port Scans
  3. Nmap Advanced Port Scans
  4. Nmap Post Port Scans
* **Ý nghĩa bước phát hiện host**:
  * Bước cần thiết trước khi quét cổng.
  * Tránh lãng phí thời gian và tạo nhiễu khi quét hệ thống offline.
* **Các phương pháp Nmap dùng để phát hiện host sống**:
  * **ARP scan** → gửi ARP request.
  * **ICMP scan** → gửi ICMP request.
  * **TCP/UDP ping scan** → gửi gói tin tới cổng TCP/UDP để xác định host.
* **Công cụ liên quan**:
  * **arp-scan** và **masscan**, có chức năng tương tự một phần với Nmap.
* **Thông tin về Nmap**:
  * Tác giả: Gordon Lyon (Fyodor).
  * Ra đời: 1997.
  * Tên đầy đủ: Network Mapper.
  * Miễn phí, mã nguồn mở, giấy phép GPL.
  * Công cụ chuẩn ngành để:
    * Lập bản đồ mạng.
    * Xác định host sống.
    * Phát hiện dịch vụ chạy.
  * Có **Nmap Scripting Engine (NSE)** → mở rộng tính năng: từ fingerprinting đến khai thác lỗ hổng.
  * Một phiên quét Nmap thường gồm nhiều bước (tùy tham số dòng lệnh).

<figure><img src="../.gitbook/assets/image (239).png" alt=""><figcaption></figcaption></figure>



### 2. Subnetworks

* **Định nghĩa Network segment**: nhóm máy tính kết nối qua cùng một _medium_ vật lý (ví dụ: switch Ethernet hoặc Wi-Fi AP).
* **Định nghĩa Subnetwork (subnet)**: thường tương đương một hoặc nhiều network segment được nối với nhau và cấu hình dùng cùng router — **segment = kết nối vật lý**, **subnet = kết nối logic**.
* **Ví dụ sơ đồ**: có bốn network segment/subnet; hệ thống của bạn sẽ nối vào một trong các subnet đó.
* **Subnet có dải địa chỉ IP riêng** và được kết nối tới mạng lớn hơn qua router; có thể có firewall áp chính sách bảo mật.

<figure><img src="../.gitbook/assets/image (240).png" alt=""><figcaption></figcaption></figure>

* **Hai loại subnet trong ví dụ**:
  * `/16` (netmask 255.255.0.0) → \~65.000 host.
  * `/24` (netmask 255.255.255.0) → \~250 host.
* **Ghi chú học thêm**: tham khảo Task 2 trong phòng _Intro to LAN_ nếu cần ôn subnetting.
* **Active reconnaissance — phát hiện host**: nếu bạn ở cùng subnet mục tiêu, scanner thường dùng **ARP** để phát hiện host đang hoạt động (ARP lấy MAC để liên lạc ở lớp liên kết).
* **Giới hạn ARP**: ARP là giao thức lớp liên kết nên **không được router chuyển tiếp** — ARP chỉ hoạt động trong cùng một subnet/link-layer.
* **Khi ở subnet khác**: các gói từ scanner tới subnet khác sẽ được **router chuyển tiếp**, nhưng các ARP query **không thể vượt qua router**, nên ARP không phát hiện host ở subnet khác.



<figure><img src="../.gitbook/assets/image (241).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (242).png" alt=""><figcaption></figcaption></figure>



&#x20;![](<../.gitbook/assets/image (243).png>)![](<../.gitbook/assets/image (244).png>)











### 3. Enumerating Targets

* **Mục tiêu (targets) trước khi quét**: cần chỉ định rõ các target sẽ quét — có thể là **danh sách**, **dải**, hoặc **subnet**.
* **Ví dụ chỉ định target**:
  * **Danh sách (list)**: `MACHINE_IP scanme.nmap.org example.com` — sẽ quét 3 địa chỉ IP/tên.
  * **Dải (range)**: `10.11.12.15-20` — sẽ quét 6 địa chỉ: `10.11.12.15`, `10.11.12.16`, …, `10.11.12.20`.
  * **Subnet**: `MACHINE_IP/30` — sẽ quét 4 địa chỉ IP.
* **Dùng file làm input**: `nmap -iL list_of_hosts.txt` (đọc danh sách target từ file).
* **Xem trước danh sách host mà Nmap sẽ quét**: `nmap -sL TARGETS`
  * Tùy chọn này **không quét** nhưng liệt kê chi tiết các host.
  * Nmap sẽ cố gắng **reverse-DNS resolution** để lấy tên host — tên có thể tiết lộ thông tin cho pentester.
  * Nếu **không muốn Nmap truy vấn DNS**, thêm `-n`.

<figure><img src="../.gitbook/assets/image (237).png" alt=""><figcaption></figcaption></figure>

```bash
canhieu@DESKTOP-DBGES7N:~$ nmap -sL -n 10.10.12.13/29
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-06 04:13 UTC
Nmap scan report for 10.10.12.8
Nmap scan report for 10.10.12.9
Nmap scan report for 10.10.12.10
Nmap scan report for 10.10.12.11
Nmap scan report for 10.10.12.12
Nmap scan report for 10.10.12.13
Nmap scan report for 10.10.12.14
Nmap scan report for 10.10.12.15
Nmap done: 8 IP addresses (0 hosts up) scanned in 0.00 seconds
```

```
25*256=6400
```

### 4. Discovering Live Hosts



* **Lớp giao thức dùng để phát hiện host (từ dưới lên trên):**
  * Link Layer: **ARP**
  * Network Layer: **ICMP**
  * Transport Layer: **TCP** và **UDP**
* **ARP (Link Layer):**
  * Mục đích duy nhất: gửi frame tới địa chỉ broadcast trên network segment để hỏi “ai có IP x này?” và nhận lại MAC (địa chỉ phần cứng).
  * Trên cùng một subnet, ARP thường được gửi **trước** khi ping ICMP.
* **ICMP (Network Layer):**
  * Có nhiều loại; _ICMP ping_ dùng **Type 8 (Echo Request)** và **Type 0 (Echo Reply)**.
  * Dùng để kiểm tra host trả lời hay không — nhưng có thể bị tường lửa chặn.
* **TCP & UDP (Transport Layer):**
  * Dù là lớp transport, scanner có thể gửi gói đặc chế tới các cổng TCP/UDP phổ biến để xem có phản hồi (ví dụ SYN, RST, ICMP unreachable, …).
  * Phương pháp này **hiệu quả khi ICMP bị chặn**.
* **Ghi chú về thứ tự trên cùng một subnet:**
  * Nếu ping một hệ thống cùng subnet, thường cần **ARP trước rồi mới ICMP Echo** để có MAC phục vụ liên lạc link-layer.

<figure><img src="../.gitbook/assets/image (238).png" alt=""><figcaption></figcaption></figure>













