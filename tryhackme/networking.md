# NETWORKING

<figure><img src="../.gitbook/assets/image (300).png" alt=""><figcaption></figcaption></figure>

## A. Networking Concepts

### 1. Introduction&#x20;

#### Learning Objectives :&#x20;

* ISO OSI network model
* IP addresses, subnets, and routing
* TCP, UDP, and port numbers
* How to connect to an open TCP port from the command line



### 2. OSI model

<figure><img src="../.gitbook/assets/image (301).png" alt=""><figcaption></figcaption></figure>

#### Khái niệm chung

* OSI (Open Systems Interconnection) là mô hình khái niệm do ISO phát triển.
* Mục đích: mô tả cách thức truyền thông trong mạng máy tính, giúp hiểu sâu các nguyên tắc mạng.
* Gồm **7 tầng (layers)**, đánh số từ 1 (Physical) đến 7 (Application).
* Mnemonic dễ nhớ: _“Please Do Not Throw Spinach Pizza Away”_.
* Hiểu và nhớ số thứ tự tầng rất quan trọng (ví dụ: _layer 3 switch, layer 7 firewall_).



1. **Physical Layer**

<figure><img src="../.gitbook/assets/image (303).png" alt=""><figcaption></figcaption></figure>

* Xử lý **kết nối vật lý** giữa các thiết bị.
* Định nghĩa phương tiện truyền dẫn (cáp, ăng-ten) và cách biểu diễn nhị phân 0 và 1.
* Dữ liệu có thể truyền dưới dạng **tín hiệu điện, quang hoặc sóng vô tuyến**.
* Ví dụ:
  * Cáp Ethernet, cáp quang
  * Sóng WiFi (2.4 GHz, 5 GHz, 6 GHz)



2. **Data Link Layer**

* Đảm bảo **truyền dữ liệu giữa các nút trong cùng một đoạn mạng (network segment)**.
* Định nghĩa cách các hệ thống trên cùng một môi trường truyền thông thỏa thuận để giao tiếp.
* Sử dụng **địa chỉ MAC (Media Access Control)**:
  * 6 byte (48 bit) dưới dạng số thập lục phân
  * 3 byte đầu tiên nhận diện nhà sản xuất thiết bị
* Mỗi khung dữ liệu (frame) thường có:
  * **Địa chỉ MAC nguồn**
  * **Địa chỉ MAC đích**
* Ví dụ:
  * Ethernet (802.3)
  * WiFi (802.11)

![https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719848867222.svg](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719848867222.svg)

<figure><img src="../.gitbook/assets/image (304).png" alt=""><figcaption></figcaption></figure>



3. #### Network Layer

* Cho phép **giao tiếp giữa các mạng khác nhau**.
* Cung cấp **địa chỉ logic** và **định tuyến** để xác định đường đi tốt nhất.
* Ví dụ: một công ty có nhiều văn phòng ở các thành phố khác nhau — tầng này đảm nhiệm việc kết nối chúng.
* Ví dụ giao thức:
  * IP (Internet Protocol)
  * ICMP (Internet Control Message Protocol)
  * VPN (IPSec, SSL/TLS VPN)

<figure><img src="../.gitbook/assets/image (305).png" alt=""><figcaption></figcaption></figure>

4. #### Transport Layer

* Đảm bảo **giao tiếp đầu cuối (end-to-end)** giữa các ứng dụng.
* Nhiệm vụ:
  * Phân mảnh dữ liệu (segmentation)
  * Kiểm soát luồng dữ liệu (flow control)
  * Phát hiện và sửa lỗi (error detection & correction)
* Ví dụ:
  * **TCP** (Transmission Control Protocol): tin cậy, có kết nối
  * **UDP** (User Datagram Protocol): nhanh, không kết nối



5. Session layer

* Quản lý **phiên giao tiếp (session)** giữa các ứng dụng trên các thiết bị khác nhau.
* Chức năng:
  * Thiết lập, duy trì và kết thúc phiên
  * Đồng bộ trao đổi dữ liệu
  * Phục hồi khi xảy ra lỗi truyền thông
* Ví dụ:
  * NFS (Network File System)
  * RPC (Remote Procedure Call)



6. **Presentation layer**&#x20;

* Đảm bảo dữ liệu ở dạng mà **tầng ứng dụng có thể hiểu được**.
* Xử lý:
  * **Mã hóa ký tự** (ASCII, Unicode)
  * **Nén dữ liệu** (giảm kích thước để truyền tải)
  * **Mã hóa/giải mã dữ liệu** (bảo mật)
* Ví dụ:
  * Định dạng hình ảnh: JPEG, PNG, GIF
  * MIME (Multipurpose Internet Mail Extensions) trong email
  * MPEG trong nén video



7. Application layer
   * Cung cấp **dịch vụ trực tiếp cho ứng dụng người dùng**.
   * Ví dụ dịch vụ:
     * Trình duyệt web (HTTP/HTTPS)
     * Truyền tệp (FTP)
     * Thư điện tử (SMTP, IMAP, POP3)
     * Phân giải tên miền (DNS)



| Layer Number | Layer Name         | Main Function                                         | Example Protocols and Standards           |
| ------------ | ------------------ | ----------------------------------------------------- | ----------------------------------------- |
| Layer 7      | Application layer  | Providing services and interfaces to applications     | HTTP, FTP, DNS, POP3, SMTP, IMAP          |
| Layer 6      | Presentation layer | Data encoding, encryption, and compression            | Unicode, MIME, JPEG, PNG, MPEG            |
| Layer 5      | Session layer      | Establishing, maintaining, and synchronising sessions | NFS, RPC                                  |
| Layer 4      | Transport layer    | End-to-end communication and data segmentation        | UDP, TCP                                  |
| Layer 3      | Network layer      | Logical addressing and routing between networks       | IP, ICMP, IPSec                           |
| Layer 2      | Data link layer    | Reliable data transfer between adjacent nodes         | Ethernet (802.3), WiFi (802.11)           |
| Layer 1      | Physical layer     | Physical data transmission media                      | Electrical, optical, and wireless signals |

<figure><img src="../.gitbook/assets/image (306).png" alt=""><figcaption></figcaption></figure>



### 3. TCP/IP Model

TCP/IP là viết tắt của **Transmission Control Protocol/Internet Protocol** và được phát triển vào những năm 1970 bởi **Bộ Quốc phòng Hoa Kỳ (DoD)**. Có thể bạn sẽ thắc mắc tại sao DoD lại tạo ra một mô hình như vậy. Một trong những điểm mạnh của mô hình này là nó cho phép mạng vẫn tiếp tục hoạt động ngay cả khi một số phần của mạng ngừng hoạt động, ví dụ như do một cuộc tấn công quân sự. Khả năng này có được một phần nhờ vào thiết kế của các giao thức định tuyến, vốn có thể thích ứng khi **cấu trúc liên kết mạng (network topology)** thay đổi.

Trong phần trình bày về mô hình ISO OSI, chúng ta đã đi từ dưới lên trên, từ tầng 1 đến tầng 7. Trong nội dung này, hãy thử nhìn theo một hướng khác, từ trên xuống dưới. Theo thứ tự từ trên xuống dưới, chúng ta có:

* **Application Layer (Tầng Ứng dụng):** Trong mô hình TCP/IP, ba tầng của OSI gồm **Application, Presentation và Session** (tức các tầng 5, 6, và 7) được gộp chung thành **Application Layer**.
* **Transport Layer (Tầng Vận chuyển):** Đây là tầng 4.
* **Internet Layer (Tầng Internet):** Đây là tầng 3. Tầng Network trong mô hình OSI được gọi là Internet Layer trong mô hình TCP/IP.
* **Link Layer (Tầng Liên kết):** Đây là tầng 2.

<figure><img src="../.gitbook/assets/image (307).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (309).png" alt=""><figcaption></figcaption></figure>

<br>

### 4. IP Addresses and Subnets

<figure><img src="../.gitbook/assets/image (310).png" alt=""><figcaption></figcaption></figure>

Khi bạn nghe đến từ **địa chỉ IP**, bạn có thể nghĩ đến một địa chỉ như **192.168.0.1** hoặc một địa chỉ ít phổ biến hơn, chẳng hạn **172.16.159.243**. Trong cả hai trường hợp, bạn đều đúng. Cả hai đều là địa chỉ IP; cụ thể là **địa chỉ IPv4 (IP phiên bản 4)**.

Mỗi máy chủ (host) trong mạng cần có một **định danh duy nhất** để các máy khác có thể giao tiếp với nó. Nếu không có định danh duy nhất, máy chủ đó sẽ không thể được tìm thấy một cách rõ ràng. Khi sử dụng bộ giao thức TCP/IP, chúng ta cần gán một địa chỉ IP cho mỗi thiết bị kết nối vào mạng.

Một ví dụ dễ hình dung là địa chỉ IP giống như **địa chỉ bưu điện tại nhà bạn**. Địa chỉ bưu điện cho phép bạn nhận thư và bưu kiện từ khắp nơi trên thế giới. Hơn nữa, nó có thể xác định ngôi nhà của bạn một cách rõ ràng; nếu không, bạn sẽ không thể mua hàng online!

Như bạn có thể đã biết, hiện nay chúng ta có **IPv4 và IPv6 (IP phiên bản 6)**. IPv4 vẫn là phổ biến nhất, và khi gặp một tài liệu nhắc đến IP mà không ghi rõ phiên bản, ta thường mặc định đó là IPv4.

Vậy, một địa chỉ IP được tạo thành từ gì? Một địa chỉ IP gồm **bốn octet**, tức **32 bit**. Vì mỗi octet gồm 8 bit, nó có thể biểu diễn một số thập phân trong khoảng từ **0 đến 255**. Một địa chỉ IP được minh họa như hình dưới đây.

> Một địa chỉ IP được tạo thành từ **4 octet (byte)** và mỗi octet biểu diễn một số thập phân trong khoảng **0–255**.

Ở mức đơn giản hóa, giá trị **0 và 255** được dành riêng lần lượt cho **địa chỉ mạng (network address)** và **địa chỉ quảng bá (broadcast address)**. Ví dụ: **192.168.1.0** là địa chỉ mạng, trong khi **192.168.1.255** là địa chỉ broadcast. Gửi dữ liệu đến địa chỉ broadcast nghĩa là gửi đến **tất cả các máy trong mạng**. Với một phép tính đơn giản, bạn có thể kết luận rằng chúng ta không thể có nhiều hơn **4 tỷ địa chỉ IPv4 duy nhất**. Về mặt toán học, con số này xấp xỉ **2^32**, vì chúng ta có 32 bit. Tuy nhiên, đây chỉ là xấp xỉ vì chưa tính đến các địa chỉ mạng và broadcast.

***

#### Kiểm tra cấu hình mạng của bạn

Bạn có thể kiểm tra địa chỉ IP trên dòng lệnh **MS Windows** bằng lệnh:

```
ipconfig
```

Trên các hệ thống **Linux** và **UNIX**, bạn có thể dùng lệnh:

```
ifconfig
```

hoặc:

```
ip address show
```

viết gọn thành:

```
ip a s
```

Ví dụ đầu ra khi dùng `ifconfig`:

```
user@TryHackMe$ ifconfig
[...]
wlo1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.66.89  netmask 255.255.255.0  broadcast 192.168.66.255
        inet6 fe80::73e1:ca5e:3f93:b1b3  prefixlen 64  scopeid 0x20<link>
        ether cc:5e:f8:02:21:a7  txqueuelen 1000  (Ethernet)
        RX packets 19684680  bytes 18865072842 (17.5 GiB)
        RX errors 0  dropped 364  overruns 0  frame 0
        TX packets 14439678  bytes 8773200951 (8.1 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Kết quả trên cho biết:

* Địa chỉ IP của host (laptop) là **192.168.66.89**
* Subnet mask là **255.255.255.0**
* Địa chỉ broadcast là **192.168.66.255**

Bây giờ, so sánh với lệnh `ip a s`:

```
user@TryHackMe$ ip a s
[...]
4: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether cc:5e:f8:02:21:a7 brd ff:ff:ff:ff:ff:ff
    altname wlp3s0
    inet 192.168.66.89/24 brd 192.168.66.255 scope global dynamic noprefixroute wlo1
       valid_lft 36795sec preferred_lft 36795sec
    inet6 fe80::73e1:ca5e:3f93:b1b3/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

Kết quả trên cho biết:

* Địa chỉ IP của host (laptop) là **192.168.66.89/24**
* Địa chỉ broadcast là **192.168.66.255**

Nếu bạn thắc mắc, subnet mask **255.255.255.0** cũng có thể viết thành **/24**. Ký hiệu **/24** có nghĩa là **24 bit bên trái** trong địa chỉ IP được giữ nguyên trong toàn bộ mạng con (subnet). Nói cách khác, **3 octet bên trái** giống nhau trong cả subnet; vì vậy, ta có thể tìm thấy các địa chỉ từ **192.168.66.1 đến 192.168.66.254**. Như đã đề cập, **192.168.66.0 và 192.168.66.255** lần lượt là **địa chỉ mạng** và **địa chỉ broadcast**.

***

#### Địa chỉ Private

Khi nói về địa chỉ IP, cần nhắc đến hai loại địa chỉ thường gặp:

1. **Địa chỉ IP công cộng (Public IP)**
2. **Địa chỉ IP riêng (Private IP)**

RFC 1918 định nghĩa ba dải địa chỉ IP riêng sau:

* **10.0.0.0 – 10.255.255.255 (10/8)**
* **172.16.0.0 – 172.31.255.255 (172.16/12)**
* **192.168.0.0 – 192.168.255.255 (192.168/16)**

Như ví dụ trước, địa chỉ IP công cộng giống như **địa chỉ bưu điện của ngôi nhà bạn**. Địa chỉ IP riêng thì khác: nó **không thể trực tiếp kết nối ra ngoài Internet**. Nó giống như một **thành phố hoặc khu biệt lập**, nơi mọi căn hộ đều có số và có thể gửi thư cho nhau, nhưng không liên hệ được với bên ngoài.

Để một địa chỉ IP riêng có thể truy cập Internet, **router** phải có địa chỉ IP công cộng và hỗ trợ **NAT (Network Address Translation)**. Hiện tại, bạn chưa cần hiểu chi tiết về NAT vì chúng ta sẽ học kỹ hơn ở phần sau.

📌 **Lưu ý:** Nên ghi nhớ các dải địa chỉ IP riêng này. Nếu không, bạn có thể thấy một địa chỉ như **10.1.33.7** hoặc **172.31.33.7** và cố truy cập nó từ mạng công cộng, điều đó là không khả thi.

***

#### Định tuyến (Routing)

Một **router** giống như **bưu điện địa phương**: bạn đưa bưu kiện cho họ, và họ biết cách chuyển phát đi đâu.

<figure><img src="../.gitbook/assets/image (311).png" alt=""><figcaption></figcaption></figure>

Nếu xét sâu hơn:

* Khi bạn gửi một bưu kiện đến một thành phố hay quốc gia khác, bưu điện sẽ kiểm tra địa chỉ và quyết định gửi tiếp đến đâu.
* Nếu là gửi ra nước ngoài, thường sẽ có một trung tâm xử lý tất cả các bưu kiện quốc tế.

Trong thuật ngữ kỹ thuật, **router chuyển tiếp các gói dữ liệu đến mạng phù hợp**. Thông thường, một gói dữ liệu sẽ đi qua nhiều router trước khi đến đích cuối cùng. Router hoạt động ở **tầng 3 (Network Layer)**, kiểm tra địa chỉ IP và gửi gói tin đến **mạng hoặc router tốt nhất**, giúp nó tiến gần hơn đến đích.

<figure><img src="../.gitbook/assets/image (312).png" alt=""><figcaption></figcaption></figure>



### 5. UDP and TCP

#### Giao thức Vận chuyển: UDP và TCP

**Giao thức IP** cho phép chúng ta liên lạc với một máy chủ (host) đích trong mạng; máy chủ này được định danh bằng **địa chỉ IP**. Tuy nhiên, chúng ta cần thêm các giao thức cho phép **các tiến trình (process) trên các máy chủ mạng** có thể giao tiếp với nhau. Có hai giao thức vận chuyển để thực hiện điều đó: **UDP và TCP**.

***

#### UDP

**UDP (User Datagram Protocol)** cho phép chúng ta kết nối tới một **tiến trình cụ thể** trên máy đích. UDP là một giao thức **đơn giản, không kết nối (connectionless)** hoạt động ở **tầng vận chuyển (Layer 4)**.

* **Không kết nối** có nghĩa là UDP không cần thiết lập kết nối trước khi gửi dữ liệu.
* UDP cũng **không cung cấp cơ chế xác nhận** rằng gói tin đã được gửi thành công hay chưa.

Địa chỉ IP chỉ xác định **máy chủ (host)**, do đó ta cần một cơ chế để xác định **tiến trình gửi và nhận**. Điều này được thực hiện bằng **số cổng (port number)**.

* Một số cổng chiếm **2 octet**, do đó có phạm vi từ **1 đến 65535** (cổng 0 được dành riêng).
* Con số **65535** được tính bằng biểu thức **2^16 − 1**.

👉 Một ví dụ trong đời sống thực tương tự UDP là **dịch vụ bưu điện thông thường** (không có xác nhận giao hàng).

* Nghĩa là **không có gì đảm bảo gói UDP được nhận thành công**, giống như khi gửi một bưu kiện qua dịch vụ bưu điện cơ bản mà không có xác nhận giao hàng.
* Với bưu điện, điều này có nghĩa là **chi phí thấp hơn** so với dịch vụ có xác nhận.
* Với UDP, điều này có nghĩa là **tốc độ nhanh hơn** so với một giao thức vận chuyển có xác nhận.

Nhưng nếu chúng ta muốn một giao thức vận chuyển **có cơ chế xác nhận gói tin được nhận**, thì giải pháp là sử dụng **TCP thay vì UDP**.

***

#### TCP

**TCP (Transmission Control Protocol)** là một giao thức vận chuyển **có kết nối (connection-oriented)**. Nó sử dụng nhiều cơ chế khác nhau để đảm bảo **dữ liệu được truyền tin cậy** giữa các tiến trình trên các máy chủ mạng. Giống UDP, TCP cũng hoạt động ở **tầng 4**.

* Vì **có kết nối**, TCP yêu cầu phải **thiết lập một kết nối TCP** trước khi dữ liệu được gửi đi.
* Trong TCP, mỗi **octet dữ liệu** có một **số thứ tự (sequence number)**; nhờ đó, bộ nhận có thể dễ dàng xác định gói nào bị mất hoặc bị trùng lặp.
* Bộ nhận sau đó sẽ **gửi lại số xác nhận (acknowledgement number)** để thông báo rằng nó đã nhận thành công octet cuối cùng.

***

#### Bắt tay 3 bước (Three-Way Handshake)

<figure><img src="../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>

Một kết nối TCP được thiết lập bằng cơ chế gọi là **bắt tay 3 bước** (_three-way handshake_). Hai cờ quan trọng được sử dụng:

* **SYN (Synchronize)**
* **ACK (Acknowledgment)**

Quy trình:

1. **Gói SYN**: Client khởi tạo kết nối bằng cách gửi một gói SYN tới server. Gói này chứa **số thứ tự ban đầu** được client chọn ngẫu nhiên.
2. **Gói SYN-ACK**: Server phản hồi bằng một gói SYN-ACK, trong đó bổ sung **số thứ tự ban đầu** được server chọn ngẫu nhiên.
3. **Gói ACK**: Client gửi gói ACK để xác nhận đã nhận được gói SYN-ACK từ server.

Khi quá trình này hoàn tất, kết nối TCP được thiết lập và dữ liệu có thể bắt đầu truyền đi một cách đáng tin cậy.

<figure><img src="../.gitbook/assets/image (529).png" alt=""><figcaption></figcaption></figure>



### 6. Encapsulation

#### Đóng gói (Encapsulation)

Trước khi kết thúc, cần giải thích một khái niệm quan trọng khác: **đóng gói (encapsulation)**. Trong ngữ cảnh này, đóng gói nghĩa là **mỗi tầng sẽ thêm một phần header (và đôi khi cả trailer)** vào đơn vị dữ liệu nhận được, rồi gửi đơn vị dữ liệu “đã được đóng gói” xuống tầng bên dưới.

**Đóng gói** là khái niệm then chốt vì nó cho phép mỗi tầng tập trung vào đúng chức năng riêng. Trong sơ đồ minh họa, quy trình gồm 4 bước:

1. **Dữ liệu ứng dụng (Application data):**
   * Bắt đầu từ khi người dùng nhập dữ liệu vào ứng dụng.
   * Ví dụ: bạn soạn email hoặc tin nhắn rồi bấm gửi.
   * Ứng dụng định dạng dữ liệu này và gửi xuống tầng bên dưới – **tầng vận chuyển**.
2. **Đoạn hoặc datagram của tầng vận chuyển (Transport segment/datagram):**
   * Tầng vận chuyển (TCP/UDP) thêm thông tin header thích hợp để tạo thành **TCP segment** hoặc **UDP datagram**.
   * Đơn vị dữ liệu này được chuyển xuống tầng mạng.
3. **Gói tin mạng (Network packet):**
   * Tầng mạng (Internet layer) thêm **IP header** vào segment/datagram nhận được.
   * Lúc này ta có **gói tin IP (IP packet)**, rồi tiếp tục gửi xuống tầng liên kết dữ liệu.
4. **Khung dữ liệu liên kết (Data link frame):**
   * Ethernet hoặc WiFi nhận gói tin IP và thêm header + trailer phù hợp, tạo thành **frame**.

👉 Tóm gọn:

* **Dữ liệu ứng dụng** → đóng gói thành **TCP segment/UDP datagram**.
* **Segment/Datagram** → đóng gói thành **IP packet**.
* **IP packet** → đóng gói thành **frame Ethernet/WiFi**.

Quá trình này sẽ được **mở gói (decapsulation)** theo chiều ngược lại ở phía nhận cho đến khi rút ra dữ liệu ứng dụng gốc.

<figure><img src="../.gitbook/assets/image (530).png" alt=""><figcaption></figcaption></figure>

***

#### Vòng đời của một gói tin (The Life of a Packet)

Ví dụ với thao tác bạn tìm kiếm một phòng trên **TryHackMe**:

1. Bạn nhập truy vấn tìm kiếm và nhấn enter.
2. Trình duyệt, qua **HTTPS**, tạo ra **HTTP request** và đẩy xuống tầng vận chuyển.
3. **TCP** thực hiện **bắt tay 3 bước** để thiết lập kết nối với máy chủ TryHackMe.
   * Sau khi kết nối thành công, HTTP request chứa truy vấn được đóng gói thành các TCP segment rồi chuyển xuống tầng Internet.
4. **Tầng IP** thêm địa chỉ IP nguồn (máy bạn) và IP đích (máy chủ TryHackMe), rồi chuyển xuống tầng liên kết.
5. **Tầng liên kết** thêm header + trailer thích hợp (Ethernet/WiFi) → gửi gói tin đến router.
6. **Router** bỏ header + trailer của tầng liên kết, kiểm tra IP đích và định tuyến đến đường đi tiếp theo.
   * Quá trình này lặp lại qua nhiều router cho đến khi đến mạng đích.
7. Ở mạng đích, các bước **mở gói** được thực hiện ngược lại, cho đến khi máy chủ web nhận được dữ liệu ứng dụng (HTTP request).

***

👉 Với cách dịch lại này, mình đã thay toàn bộ “bao gói” thành **“đóng gói”** (chuẩn hơn trong ngữ cảnh mạng), và thêm “mở gói (decapsulation)” cho phần phía nhận.

<figure><img src="../.gitbook/assets/image (531).png" alt=""><figcaption></figcaption></figure>



### 7. Telnet

**Giao thức TELNET (Teletype Network)** là một giao thức mạng dùng để kết nối từ xa với terminal. Nói đơn giản hơn, **telnet** (một client TELNET) cho phép bạn **kết nối và giao tiếp với một hệ thống từ xa**, đồng thời nhập các lệnh dạng văn bản. Mặc dù ban đầu được sử dụng cho mục đích **quản trị từ xa**, chúng ta có thể dùng telnet để kết nối đến **bất kỳ máy chủ nào đang lắng nghe trên một cổng TCP**.

Trên máy ảo mục tiêu, có nhiều dịch vụ khác nhau đang chạy. Chúng ta sẽ thử nghiệm với ba dịch vụ sau:

* **Echo server:** Máy chủ này phản hồi lại mọi thứ bạn gửi đến nó. Mặc định, nó lắng nghe trên **cổng 7**.
* **Daytime server:** Máy chủ này mặc định lắng nghe trên **cổng 13** và phản hồi lại ngày và giờ hiện tại.
* **Web (HTTP) server:** Máy chủ này mặc định lắng nghe trên **cổng TCP 80** và cung cấp các trang web.

Trước khi tiếp tục, cần lưu ý rằng **echo server và daytime server** được xem là **rủi ro bảo mật** và **không nên chạy trong thực tế**; tuy nhiên, ở đây chúng ta khởi động chúng chỉ để minh họa cách giao tiếp với máy chủ bằng telnet.

Trong ví dụ dưới đây trên terminal, chúng ta kết nối đến máy ảo mục tiêu thông qua **cổng TCP số 7 của echo server**.\
👉 Để đóng kết nối, nhấn đồng thời tổ hợp phím **CTRL + ]**.



```bash
┌──(kali㉿kali)-[~]
└─$ telnet 10.201.9.74 13
Trying 10.201.9.74...
Connected to 10.201.9.74.
Escape character is '^]'.
Tue Sep  9 12:08:03 PM UTC 2025
Connection closed by foreign host.

//Kết nối tới Daytime server (port 13)
//Daytime server chỉ gửi về ngày/giờ hiện tại, sau đó tự động đóng kết nối.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$  telnet 10.201.9.74 80
Trying 10.201.9.74...
Connected to 10.201.9.74.
Escape character is '^]'.

HTTP/1.0 400 Bad Request
Content-Type: text/html
Content-Length: 345
Connection: close
Date: Tue, 09 Sep 2025 12:08:29 GMT
Server: lighttpd/1.4.63

<?xml version="1.0" encoding="iso-8859-1"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
         "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
 <head>
  <title>400 Bad Request</title>
 </head>
 <body>
  <h1>400 Bad Request</h1>
 </body>
</html>
Connection closed by foreign host.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~]
└─$ 


```

Vây nên ta rút ra dc 1 vài điều :&#x20;

* Echo (port 7) → gửi gì, nhận lại y hệt → minh họa giao tiếp 2 chiều.
* Daytime (port 13) → nhận thông tin một chiều (ngày giờ), rồi server tự cắt kết nối.
* HTTP (port 80) → minh họa cách client (bạn) phải gửi **request hợp lệ** thì server mới trả về **response**.

<figure><img src="../.gitbook/assets/image (533).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (532).png" alt=""><figcaption></figcaption></figure>



## B. Networking Essentials

### 1. DHCP: Give Me My Network Settings

Bạn đến quán cà phê yêu thích của mình, gọi một tách đồ uống nóng quen thuộc và mở laptop. Laptop của bạn kết nối vào WiFi của quán và **tự động cấu hình mạng**, nhờ đó bạn có thể bắt đầu làm việc với một phòng mới trên TryHackMe. Bạn không hề nhập bất kỳ **IP address** nào, nhưng thiết bị đã sẵn sàng. Vậy điều này diễn ra như thế nào?

***

#### Những cấu hình mạng cơ bản cần có

Bất cứ khi nào muốn truy cập một mạng, tối thiểu ta cần cấu hình các thông số sau:

* **IP address** cùng với **subnet mask**
* **Router (hoặc gateway)**
* **DNS server**

Khi kết nối thiết bị vào một mạng mới, các cấu hình trên phải được đặt theo đúng mạng mới. Việc cấu hình thủ công là một lựa chọn tốt, đặc biệt cho **servers**. Bởi vì servers thường không di chuyển sang mạng khác (bạn đâu có mang **domain controller** đến quán cà phê rồi kết nối vào WiFi ở đó). Ngoài ra, các thiết bị khác cũng cần kết nối tới servers và kỳ vọng tìm thấy chúng ở **IP address** cố định.

***

#### Lợi ích của cấu hình tự động

Một cơ chế tự động cấu hình cho các thiết bị kết nối mạng mang lại nhiều lợi ích:

1. Giúp tránh phải tự tay cấu hình từng thiết bị – đặc biệt quan trọng với **mobile devices**.
2. Tránh tình trạng **xung đột địa chỉ (address conflict)**, khi hai thiết bị được cấu hình cùng một IP address. Một xung đột như vậy sẽ khiến các host liên quan không thể dùng tài nguyên mạng, bao gồm cả nội bộ và Internet.

Giải pháp chính là sử dụng **Dynamic Host Configuration Protocol (DHCP)**.

* DHCP là một **application-level protocol** chạy trên **UDP**.
* **DHCP server** lắng nghe trên **UDP port 67**, còn **DHCP client** gửi từ **UDP port 68**.
* Smartphone và laptop của bạn đều được cấu hình dùng DHCP theo mặc định.

<figure><img src="../.gitbook/assets/image (297).png" alt=""><figcaption></figcaption></figure>

***

#### Chu trình DORA trong DHCP

Trong giao thức DHCP, **DORA** viết tắt của:

* **Discover**
* **Offer**
* **Request**
* **Acknowledge**

Quy trình bốn bước:

1. **DHCP Discover:** Client broadcast gói **DHCPDISCOVER** để tìm **DHCP server** trong mạng.
2. **DHCP Offer:** Server phản hồi bằng gói **DHCPOFFER**, đưa ra một **IP address** khả dụng cho client.
3. **DHCP Request:** Client gửi gói **DHCPREQUEST** để xác nhận rằng nó chấp nhận IP được offer.
4. **DHCP Acknowledge:** Server phản hồi gói **DHCPACK**, xác nhận địa chỉ IP đó được cấp cho client.

👉 Ví dụ: Laptop gửi DHCP Discover → Server trả DHCP Offer → Laptop gửi DHCP Request → Server gửi DHCP Acknowledge.

<figure><img src="../.gitbook/assets/image (298).png" alt=""><figcaption></figcaption></figure>

***

#### Phân tích packet capture

Ví dụ sau là kết quả bắt gói (packet capture) cho thấy quy trình DORA. Ở đây, client nhận địa chỉ **192.168.66.133**:

```
user@TryHackMe$ tshark -r DHCP-G5000.pcap -n
    1   0.000000      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Discover - Transaction ID 0xfb92d53f
    2   0.013904 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP Offer    - Transaction ID 0xfb92d53f
    3   4.115318      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Request  - Transaction ID 0xfb92d53f
    4   4.228117 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP ACK      - Transaction ID 0xfb92d53f
```

Những điểm có thể nhận thấy:

* Client bắt đầu **chưa có cấu hình IP**; nó chỉ có **MAC address**.
* Ở gói số 1 và 3 (DHCP Discover và DHCP Request), client vẫn chưa có IP nên gửi từ **0.0.0.0** đến địa chỉ broadcast **255.255.255.255**.
* Ở tầng liên kết, client gửi đến **broadcast MAC address ff:ff:ff:ff:ff:ff** (không hiển thị trong output trên).
* DHCP server gửi DHCP Offer với một IP khả dụng và thông tin cấu hình mạng kèm theo, sử dụng **MAC address** của client làm đích.

***

#### Kết quả cuối cùng

Sau khi hoàn tất quy trình DHCP, thiết bị của chúng ta sẽ nhận được đầy đủ cấu hình để truy cập mạng, thậm chí cả Internet. Cụ thể, DHCP server sẽ cung cấp:

* **IP address được cấp phát (leased IP)** để truy cập tài nguyên mạng.
* **Gateway** để định tuyến các gói ra ngoài mạng cục bộ.
* **DNS server** để phân giải tên miền (phần này sẽ được nói chi tiết sau).

<figure><img src="../.gitbook/assets/image (299).png" alt=""><figcaption></figcaption></figure>



### 2. ARP: Bridging Layer 3 Addressing to Layer 2 Addressing



Trong **Networking Concepts room**, chúng ta đã nói rằng khi hai host giao tiếp qua mạng, một **IP packet** sẽ được **đóng gói (encapsulated)** bên trong một **data link frame** khi nó di chuyển ở **layer 2**. Hãy nhớ rằng, hai data link layer phổ biến nhất chúng ta sử dụng là **Ethernet (IEEE 802.3)** và **WiFi (IEEE 802.11)**. Bất cứ khi nào một host cần giao tiếp với một host khác trên cùng mạng Ethernet hoặc WiFi, nó phải gửi IP packet bên trong một **data link frame**.

Mặc dù host biết **IP address** của máy đích, nó vẫn cần tra cứu **MAC address** của máy đích để tạo ra **data link header** đúng.

***

#### Nhắc lại về MAC address

**MAC address** là một số 48-bit, thường được biểu diễn dưới dạng **hexadecimal**. Ví dụ:

* `7C:DF:A1:D3:8C:5C`
* `44:DF:65:D8:FE:6C`\
  đều là MAC address trong mạng của tôi.

Tuy nhiên, các thiết bị trong cùng một mạng Ethernet không cần biết MAC address của nhau mọi lúc; chúng chỉ cần biết **khi thực sự giao tiếp**. Mọi thứ đều xoay quanh **IP address**.

Ví dụ: Bạn kết nối thiết bị của mình vào một mạng. Nếu mạng này có **DHCP server**, thiết bị sẽ tự động cấu hình để sử dụng một **gateway (router)** và một **DNS server** cụ thể. Như vậy, thiết bị biết **IP address của DNS server** để phân giải tên miền, và biết **IP address của router** khi cần gửi gói tin ra Internet. Trong toàn bộ tình huống này, **không một MAC address nào được tiết lộ**. Tuy nhiên, hai thiết bị trong cùng một Ethernet **không thể giao tiếp** nếu không biết MAC address của nhau.

***

#### Ethernet frame

Trong ảnh chụp màn hình dưới đây, ta thấy một **IP packet** nằm trong **Ethernet frame**. **Ethernet frame header** bao gồm:

* **Destination MAC address**
* **Source MAC address**
* **Type** (IPv4 trong trường hợp này)

(Wireshark hiển thị destination MAC, source MAC và loại giao thức trong Ethernet frame. IP packet được encapsulated bên trong Ethernet frame này.)

<figure><img src="../.gitbook/assets/image (534).png" alt=""><figcaption></figcaption></figure>

***

#### Address Resolution Protocol (ARP)

**ARP** cho phép tìm ra MAC address của một thiết bị khác trong Ethernet.

Ví dụ: Một host có **IP address 192.168.66.89** muốn giao tiếp với một host khác có **IP address 192.168.66.1**. Nó gửi một **ARP Request**, hỏi host có địa chỉ 192.168.66.1 trả lời.

* **ARP Request** được gửi từ MAC address của host yêu cầu đến **broadcast MAC address** `ff:ff:ff:ff:ff:ff`.
* Sau đó, **ARP Reply** đến rất nhanh, host 192.168.66.1 phản hồi bằng **MAC address** của nó.
* Từ thời điểm này, hai host có thể trao đổi **data link frames**.

Ví dụ bắt gói với **tshark**:

```
user@TryHackMe$ tshark -r arp.pcapng -Nn
    1 0.000000000 cc:5e:f8:02:21:a7 → ff:ff:ff:ff:ff:ff ARP 42 Who has 192.168.66.1? Tell 192.168.66.89
    2 0.003566632 44:df:65:d8:fe:6c → cc:5e:f8:02:21:a7 ARP 42 192.168.66.1 is at 44:df:65:d8:fe:6c
```

Nếu dùng **tcpdump**, gói tin sẽ hiển thị khác một chút, sử dụng thuật ngữ **ARP Request** và **ARP Reply**:

```
user@TryHackMe$ tcpdump -r arp.pcapng -n -v
17:23:44.506615 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.66.1 tell 192.168.66.89, length 28
17:23:44.510182 ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.168.66.1 is-at 44:df:65:d8:fe:6c, length 28
```

📌 Lưu ý: Một **ARP Request/Reply** **không được encapsulated trong UDP hay IP packet**, mà được encapsulated **trực tiếp trong Ethernet frame**.

(Wireshark cho thấy một ARP Reply được encapsulated trực tiếp trong Ethernet frame.)

<figure><img src="../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>

***

#### Vị trí của ARP trong mô hình OSI

* **ARP** thường được coi là **tầng 2 (Data Link Layer)** vì nó xử lý **MAC address**.
* Một số người lại cho rằng nó thuộc **tầng 3 (Network Layer)** vì nó hỗ trợ cho hoạt động của IP.

Điều quan trọng nhất cần nhớ là:\
👉 **ARP cho phép chuyển đổi từ địa chỉ tầng 3 (IP) sang địa chỉ tầng 2 (MAC).**

<figure><img src="../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>



### 3. ICMP: Troubleshooting Networks

#### Internet Control Message Protocol (ICMP)

**ICMP** chủ yếu được sử dụng cho **chẩn đoán mạng** và **báo cáo lỗi**. Có hai lệnh phổ biến dựa trên ICMP, cực kỳ hữu ích trong việc **khắc phục sự cố mạng** và **bảo mật mạng**:

* **ping:** Lệnh này dùng ICMP để kiểm tra khả năng kết nối đến một hệ thống đích và đo **round-trip time (RTT)**. Nói cách khác, nó cho ta biết hệ thống đích có hoạt động (alive) và phản hồi có thể đến được hệ thống của chúng ta.
* **traceroute:** Lệnh này có tên **traceroute** trên Linux và UNIX-like, còn trên Windows gọi là **tracert**. Nó dùng ICMP để khám phá đường đi (route) từ host của bạn đến hệ thống đích.



***

#### Ping

<figure><img src="../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

Bạn có thể chưa từng chơi **ping-pong**, nhưng nhờ ICMP, bạn có thể “chơi” nó với máy tính!

* **ping** gửi đi một **ICMP Echo Request (ICMP Type 8)**.\
  _(Hình trong Wireshark: ICMP Echo Request được encapsulated trong một IP packet)_.
* Máy tính đích phản hồi bằng một **ICMP Echo Reply (ICMP Type 0)**.\
  _(Hình trong Wireshark: ICMP Echo Reply được encapsulated trong một IP packet)_.

<figure><img src="../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

Tuy nhiên, có nhiều yếu tố khiến chúng ta không nhận được phản hồi:

* Hệ thống đích **offline hoặc shutdown**
* **Firewall** trên đường đi có thể chặn gói tin cần thiết cho ping

Ví dụ dưới đây sử dụng tùy chọn `-c 4` để ping gửi **4 gói** rồi dừng:

```
user@TryHackMe$ ping 192.168.11.1 -c 4
PING 192.168.11.1 (192.168.11.1) 56(84) bytes of data.
64 bytes from 192.168.11.1: icmp_seq=1 ttl=63 time=11.2 ms
64 bytes from 192.168.11.1: icmp_seq=2 ttl=63 time=3.81 ms
64 bytes from 192.168.11.1: icmp_seq=3 ttl=63 time=3.99 ms
64 bytes from 192.168.11.1: icmp_seq=4 ttl=63 time=23.4 ms

--- 192.168.11.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 3.805/10.596/23.366/7.956 ms
```

Kết quả cho thấy **không có packet loss**. Ngoài ra, nó tính được **RTT tối thiểu, trung bình, tối đa** và **độ lệch chuẩn (mdev)**.

***

#### Traceroute

Làm sao để buộc mọi router giữa hệ thống của ta và hệ thống đích phải “lộ diện”?

* Trong Internet Protocol có một trường gọi là **Time-to-Live (TTL)**, cho biết số lượng router tối đa mà một gói tin có thể đi qua trước khi bị loại bỏ.
* Mỗi router khi chuyển tiếp sẽ **giảm TTL đi 1**. Khi TTL giảm về **0**, router sẽ **drop** gói tin và gửi về một **ICMP Time Exceeded message (ICMP Type 11)**.\
  _(Lưu ý: “time” ở đây đo bằng số hop (số router), không phải giây)._

Ví dụ kết quả khi chạy **traceroute** đến `example.com`:

```
user@TryHackMe$ traceroute example.com
traceroute to example.com (93.184.215.14), 30 hops max, 60 byte packets
 1  _gateway (192.168.66.1)  4.414 ms  4.342 ms  4.320 ms
 2  192.168.11.1 (192.168.11.1)  5.849 ms  5.830 ms  5.811 ms
 3  100.104.0.1 (100.104.0.1)  11.130 ms  11.111 ms  11.093 ms
 4  10.149.1.45 (10.149.1.45)  6.156 ms  6.138 ms  6.120 ms
 5  * * *
 6  * * *
 7  * * *
 8  172.16.48.1 (172.16.48.1)  5.667 ms  8.165 ms  6.861 ms
 9  ae81.edge4.Marseille1.Level3.net (212.73.201.45)  50.811 ms  52.857 ms 213.242.116.233 (213.242.116.233)  52.798 ms
10  NTT-level3-Marseille1.Level3.net (4.68.68.150)  93.351 ms  79.897 ms  79.804 ms
11  ae-9.r20.parsfr04.fr.bb.gin.ntt.net (129.250.3.38)  62.935 ms  62.908 ms  64.313 ms
12  ae-14.r21.nwrknj03.us.bb.gin.ntt.net (129.250.4.194)  141.816 ms  141.782 ms  141.757 ms
13  ae-1.a02.nycmny17.us.bb.gin.ntt.net (129.250.3.17)  145.786 ms ae-1.a03.nycmny17.us.bb.gin.ntt.net (129.250.3.128)  141.701 ms  147.586 ms
14  ce-0-3-0.a02.nycmny17.us.ce.gin.ntt.net (128.241.1.14)  148.692 ms ce-3-3-0.a03.nycmny17.us.ce.gin.ntt.net (128.241.1.90)  141.615 ms ce-0-3-0.a02.nycmny17.us.ce.gin.ntt.net (128.241.1.14)  148.168 ms
15  ae-66.core1.nyd.edgecastcdn.net (152.195.69.133)  141.100 ms ae-65.core1.nyd.edgecastcdn.net (152.195.68.133)  140.360 ms ae-66.core1.nyd.edgecastcdn.net (152.195.69.133)  140.638 ms
16  93.184.215.14 (93.184.215.14)  140.574 ms  140.543 ms  140.514 ms
17  93.184.215.14 (93.184.215.14)  140.488 ms  139.397 ms  141.854 ms
```

Trong kết quả trên:

* Một số router **không phản hồi** (hiện dấu `* * *`), nghĩa là drop gói mà không gửi ICMP.
* Router thuộc ISP của bạn có thể phản hồi và cho thấy **private IP address**.
* Một số router khác phản hồi với **public IP address**, từ đó có thể tra cứu **domain name** và xác định vị trí địa lý.
* Luôn có khả năng gói **ICMP Time Exceeded** bị chặn và không đến được hệ thống của bạn.

<figure><img src="../.gitbook/assets/image (540).png" alt=""><figcaption></figcaption></figure>



### 4. Routing

Hãy xem xét sơ đồ mạng dưới đây. Nó chỉ có ba mạng; tuy nhiên, làm thế nào Internet biết cách chuyển một gói tin từ **Network 1** đến **Network 2** hoặc **Network 3**? Mặc dù đây là một sơ đồ đã được đơn giản hóa quá mức, chúng ta vẫn cần một thuật toán để xác định cách kết nối **Network 1** với **Network 2**, **Network 3** và ngược lại.

_(Ba mạng được kết nối với Internet thông qua router riêng của mỗi mạng)._

![Three networks are connected to the Internet through its own router.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719849271800.svg)

Giờ hãy xem một sơ đồ chi tiết hơn. Internet thực tế gồm hàng triệu **routers** và hàng tỷ thiết bị. Mạng dưới đây chỉ là một phần rất nhỏ của Internet. Người dùng di động có thể truy cập web server; tuy nhiên, để điều này xảy ra, **mỗi router trên đường đi cần gửi gói tin qua liên kết (link) thích hợp**. Hiển nhiên, có nhiều hơn một đường đi (route) kết nối giữa người dùng di động và web server. Do đó, chúng ta cần một **routing algorithm** để router biết nên chọn liên kết nào.

_(Một mạng với sáu router cho phép nhiều đường đi giữa các hosts để giao tiếp.)_

<figure><img src="../.gitbook/assets/image (541).png" alt=""><figcaption></figcaption></figure>

Các thuật toán định tuyến chi tiết nằm ngoài phạm vi của phòng học này; tuy nhiên, chúng ta sẽ mô tả ngắn gọn một số **routing protocols** để bạn quen với tên gọi của chúng:

* **OSPF (Open Shortest Path First):**\
  OSPF là một giao thức định tuyến cho phép các routers chia sẻ thông tin về **network topology** và tính toán các đường đi hiệu quả nhất cho việc truyền dữ liệu.
  * Các routers trao đổi cập nhật về trạng thái các liên kết và mạng được kết nối.
  * Nhờ vậy, mỗi router có một bản đồ đầy đủ của mạng và có thể xác định đường đi tốt nhất đến bất kỳ đích nào.
* **EIGRP (Enhanced Interior Gateway Routing Protocol):**\
  EIGRP là một giao thức định tuyến độc quyền của Cisco, kết hợp các đặc điểm của nhiều thuật toán định tuyến khác nhau.
  * Routers chia sẻ thông tin về các mạng mà chúng có thể tiếp cận và chi phí (như băng thông hoặc độ trễ) liên quan đến các route đó.
  * Từ đó, router chọn đường đi hiệu quả nhất để truyền dữ liệu.
* **BGP (Border Gateway Protocol):**\
  BGP là giao thức định tuyến chính được sử dụng trên **Internet**.
  * Nó cho phép các mạng khác nhau (chẳng hạn mạng của các ISP – Internet Service Providers) trao đổi thông tin định tuyến và thiết lập đường đi cho dữ liệu giữa các mạng này.
  * BGP đảm bảo dữ liệu có thể được định tuyến hiệu quả trên Internet, ngay cả khi đi qua nhiều mạng khác nhau.
* **RIP (Routing Information Protocol):**\
  RIP là một giao thức định tuyến đơn giản, thường dùng trong các mạng nhỏ.
  * Routers chạy RIP sẽ chia sẻ thông tin về các mạng mà chúng có thể đến được và số **hops** (số router trung gian) cần đi qua để đến đó.
  * Mỗi router xây dựng **routing table** dựa trên thông tin này, chọn đường đi có **ít hops nhất** để đến mỗi đích.

<figure><img src="../.gitbook/assets/image (542).png" alt=""><figcaption></figcaption></figure>



### 5. NAT

Như đã thảo luận trong **Networking Concepts room**, chúng ta đã tính toán rằng **IPv4** có thể hỗ trợ tối đa khoảng **4 tỷ thiết bị**. Với sự gia tăng nhanh chóng số lượng thiết bị kết nối Internet – từ máy tính, smartphone cho đến camera an ninh và máy giặt – có thể thấy rằng **không gian địa chỉ IPv4** sẽ nhanh chóng bị cạn kiệt.

Một giải pháp cho tình trạng **cạn kiệt địa chỉ** chính là **Network Address Translation (NAT)**.

***

#### Ý tưởng của NAT

Nguyên lý của NAT là sử dụng **một địa chỉ IP công cộng (public IP address)** để cung cấp quyền truy cập Internet cho nhiều **địa chỉ IP riêng (private IP addresses)**.

* Nói cách khác, nếu một công ty có 20 máy tính, NAT cho phép tất cả 20 máy tính này truy cập Internet chỉ với **một địa chỉ IP công cộng duy nhất**, thay vì cần đến 20 địa chỉ công cộng.
* (_Lưu ý kỹ thuật:_ số lượng IP address luôn được biểu diễn dưới dạng lũy thừa của 2. Do đó, chính xác thì NAT sẽ **dự trữ 2 địa chỉ IP công cộng** thay vì 32. Như vậy, bạn sẽ tiết kiệm được 30 địa chỉ IP công cộng.)

***

#### NAT khác với định tuyến thông thường

Khác với **routing**, vốn chỉ đơn giản là chuyển tiếp các gói tin đến host đích, thì **routers hỗ trợ NAT** cần có cơ chế theo dõi **các kết nối đang diễn ra**.

* Do đó, router chạy NAT sẽ duy trì một **bảng ánh xạ (translation table)** để dịch địa chỉ mạng giữa **mạng nội bộ (internal/private network)** và **mạng bên ngoài (external/public network)**.
* Thông thường, mạng nội bộ dùng **private IP range**, còn mạng bên ngoài dùng **public IP range**.

***

#### Minh họa NAT

Trong sơ đồ dưới đây, nhiều thiết bị truy cập Internet thông qua một router hỗ trợ NAT. Router duy trì một **bảng ánh xạ** giữa **địa chỉ IP + port nội bộ** và **địa chỉ IP + port bên ngoài**.

Ví dụ:

* Laptop thiết lập một kết nối đến web server.
* Từ góc nhìn của laptop, kết nối này được tạo từ **IP 192.168.0.129** với **TCP source port 15401**.
* Nhưng với web server, kết nối lại hiển thị như đến từ **212.3.4.5** với **TCP port 19273**.

👉 Việc dịch địa chỉ này do router thực hiện **hoàn toàn trong suốt (seamless)** đối với người dùng.

<figure><img src="../.gitbook/assets/image (543).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (544).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (545).png" alt=""><figcaption></figcaption></figure>



### 6. Closing Notes

<figure><img src="../.gitbook/assets/image (547).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (548).png" alt=""><figcaption></figcaption></figure>



## C. Networking Core Protocols

<figure><img src="../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

### 1. DNS: Remembering Addresses

Bạn có nhớ **IP address** của các website yêu thích của mình không? Trừ khi đó là một **private IP address** của một thiết bị trong mạng cục bộ, còn lại thì hầu như không ai cần phải ghi nhớ IP address cả. Điều này là nhờ có **Domain Name System (DNS)** – hệ thống chịu trách nhiệm ánh xạ chính xác một **domain name** sang một **IP address**.

***

#### DNS hoạt động như thế nào

* **DNS** hoạt động ở **Application Layer (Layer 7)** của mô hình **ISO OSI**.
* Mặc định, **DNS traffic** sử dụng **UDP port 53**, và **TCP port 53** được dùng làm dự phòng.
* Có nhiều loại **DNS records**, tuy nhiên trong phần này chúng ta tập trung vào 4 loại:

1. **A record (Address record):**
   * Ánh xạ một **hostname** sang một hoặc nhiều **IPv4 addresses**.
   * Ví dụ: `example.com` có thể được phân giải thành `172.17.2.172`.
2. **AAAA record:**
   * Giống như **A record**, nhưng dành cho **IPv6**.
   * Lưu ý: nó là **AAAA (quad-A)**, bởi vì **AA** hay **AAA** vốn để chỉ kích cỡ pin, còn **AAA** cũng có thể viết tắt của **Authentication, Authorization, Accounting** – không liên quan đến DNS.
3. **CNAME record (Canonical Name):**
   * Ánh xạ một **domain name** sang một **domain name khác**.
   * Ví dụ: `www.example.com` có thể ánh xạ sang `example.com` hoặc thậm chí `example.org`.
4. **MX record (Mail Exchange):**
   * Chỉ định **mail server** chịu trách nhiệm xử lý email cho một domain.

***

#### Ứng dụng thực tế

* Khi bạn gõ `example.com` trên trình duyệt, trình duyệt sẽ truy vấn **DNS server** để tìm **A record**.
* Khi bạn gửi email tới `test@example.com`, **mail server** sẽ truy vấn **DNS server** để tìm **MX record**.

***

#### Tra cứu DNS trên dòng lệnh

Nếu muốn tra cứu **IP address** của một domain từ command line, bạn có thể dùng công cụ **nslookup**.

Ví dụ:

```
user@TryHackMe$ nslookup www.example.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   www.example.com
Address: 93.184.215.14
Name:   www.example.com
Address: 2606:2800:21f:cb07:6820:80da:af6b:8b2c
```

Kết quả trên tạo ra **4 gói tin**. Dưới đây là chi tiết khi phân tích bằng **tshark**:

```
user@TryHackMe$ tshark -r dns-query.pcapng -Nn
    1 0.000000000 192.168.66.89 → 192.168.66.1 DNS 86 Standard query 0x2e0f A www.example.com OPT
    2 0.059049584 192.168.66.1 → 192.168.66.89 DNS 102 Standard query response 0x2e0f A www.example.com A 93.184.215.14 OPT
    3 0.059721705 192.168.66.89 → 192.168.66.1 DNS 86 Standard query 0x96e1 AAAA www.example.com OPT
    4 0.101568276 192.168.66.1 → 192.168.66.89 DNS 114 Standard query response 0x96e1 AAAA www.example.com AAAA 2606:2800:21f:cb07:6820:80da:af6b:8b2c OPT
```

* Gói 1 và 3: Client gửi **DNS query** cho **A record** và **AAAA record** của `www.example.com`.
* Gói 2 và 4: Server phản hồi với **DNS response**, lần lượt trả về địa chỉ **IPv4 (93.184.215.14)** và **IPv6 (2606:2800:21f:cb07:6820:80da:af6b:8b2c)**.

<figure><img src="../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

### 2. WHOIS

Trong nhiệm vụ trước, chúng ta đã tìm hiểu cách một **domain name** được phân giải thành một **IP address**. Tuy nhiên, để việc này xảy ra, cần có người có **thẩm quyền thiết lập các DNS records** (như A, AAAA, MX và các record khác) cho domain. Người nào đăng ký domain name sẽ có quyền này.

Ví dụ: nếu bạn đăng ký **example.com**, bạn có thể thiết lập bất kỳ **DNS record hợp lệ** nào cho **example.com**.

<figure><img src="../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>

***

#### Đăng ký domain name

* Bạn có thể đăng ký bất kỳ domain name nào còn khả dụng, với thời hạn **một hoặc nhiều năm**.
* Bạn cần **thanh toán phí hàng năm**, và bắt buộc phải cung cấp **thông tin liên hệ chính xác** với tư cách là **registrant (người đăng ký)**.
* Những thông tin này sẽ trở thành một phần dữ liệu trong **WHOIS records** và được công khai.

(_Lưu ý:_ Mặc dù viết hoa, **WHOIS** không phải là từ viết tắt. Nó được phát âm là _who is_).

Nếu bạn muốn đăng ký domain nhưng **không muốn tiết lộ thông tin cá nhân công khai**, bạn có thể sử dụng dịch vụ **privacy protection**, dịch vụ này sẽ ẩn toàn bộ thông tin liên hệ của bạn khỏi WHOIS records.

***

#### WHOIS records

Bạn có thể tra cứu **WHOIS records** của bất kỳ domain name đã đăng ký nào bằng:

* Các dịch vụ trực tuyến, hoặc
* Công cụ dòng lệnh **whois** (có sẵn trên Linux và nhiều hệ thống khác).

Một **WHOIS record** thường cung cấp thông tin về đơn vị đăng ký domain, bao gồm:

* Tên, số điện thoại, email, địa chỉ
* Ngày tạo domain, ngày cập nhật gần nhất, ngày hết hạn

Trong ảnh chụp màn hình minh họa, bạn có thể thấy:

* Khi record lần đầu tiên được tạo
* Khi record được cập nhật gần nhất
* Tên, địa chỉ, số điện thoại, email của registrant

***

#### Ví dụ WHOIS record

Trong kết quả bên dưới, ta dùng lệnh `whois` để tra cứu một domain, nhưng WHOIS record của nó đã được ẩn bằng dịch vụ **privacy protection**:

```
user@TryHackMe$ whois [REDACTED].com
[...]
Domain Name: [REDACTED].COM
Registry Domain ID: [REDACTED]
Registrar WHOIS Server: whois.godaddy.com
Registrar URL: https://www.godaddy.com
Updated Date: 2017-07-05T16:02:43Z
Creation Date: 1993-04-02T00:00:00Z
Registrar Registration Expiration Date: 2026-10-20T14:56:17Z
Registrar: GoDaddy.com, LLC
Registrar IANA ID: 146
Registrar Abuse Contact Email: abuse@godaddy.com
Registrar Abuse Contact Phone: +1.4806242505
[...]
Registrant Name: Registration Private
Registrant Organization: Domains By Proxy, LLC
Registrant Street: DomainsByProxy.com
[...]
```

<figure><img src="../.gitbook/assets/image (296).png" alt=""><figcaption></figcaption></figure>

### 3. HTTP(S): Accessing the Web

Khi bạn mở trình duyệt web, bạn chủ yếu sử dụng các giao thức **HTTP** và **HTTPS**.

* **HTTP** là viết tắt của _Hypertext Transfer Protocol_.
* Chữ **S** trong **HTTPS** là viết tắt của _Secure_ (an toàn).

Giao thức này dựa trên **TCP** và định nghĩa cách **trình duyệt web** giao tiếp với **web server**.

***

#### Một số lệnh (methods) phổ biến mà trình duyệt thường gửi tới web server:

* **GET**: lấy dữ liệu từ server, ví dụ như một file HTML hoặc một hình ảnh.
* **POST**: cho phép gửi dữ liệu mới lên server, chẳng hạn như gửi form hoặc upload file.
* **PUT**: dùng để tạo một tài nguyên mới trên server hoặc cập nhật/ghi đè thông tin hiện có.
* **DELETE**: như tên gọi, được dùng để xóa một file hoặc tài nguyên cụ thể trên server.

Thông thường, **HTTP** và **HTTPS** lần lượt sử dụng các **TCP ports 80 và 443**. Ngoài ra, cũng có thể dùng các cổng khác ít phổ biến hơn, chẳng hạn **8080** và **8443**.





```bash
canhieu@DESKTOP-DBGES7N:~$ telnet 10.201.105.73 80
Trying 10.201.105.73...
Connected to 10.201.105.73.
Escape character is '^]'.
GET /flag.html HTTP/1.1
Host: 10.201.105.73

HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Sun, 14 Sep 2025 04:28:37 GMT
Content-Type: text/html
Content-Length: 478
Last-Modified: Thu, 27 Jun 2024 07:28:15 GMT
Connection: keep-alive
ETag: "667d148f-1de"
Accept-Ranges: bytes

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hidden Message</title>
    <style>
        body {
            background-color: white;
            color: white;
            font-family: Arial, sans-serif;
        }
        .hidden-text {
            font-size: 1px;
        }
    </style>
</head>
<body>
    <div class="hidden-text">THM{TELNET-HTTP}</div>
</body>
</html>
```



### 4. FTP: Transferring Files

#### FTP khác gì với HTTP?

* **HTTP** được thiết kế để lấy **trang web**.
* **FTP (File Transfer Protocol)** được thiết kế để **truyền tải tập tin** (upload/download).
* Vì mục đích tối ưu cho file, FTP thường **hiệu quả và nhanh hơn HTTP** khi truyền file trong cùng điều kiện.

***

#### Các lệnh cơ bản trong FTP

* **USER** → nhập tên đăng nhập.
* **PASS** → nhập mật khẩu.
* **RETR (Retrieve)** → tải một file từ server về client.
* **STOR (Store)** → tải một file từ client lên server.

📌 **FTP server mặc định lắng nghe ở cổng TCP 21**.

* Khi truyền file, FTP sẽ mở thêm **một kết nối riêng biệt** giữa client ↔ server cho dữ liệu.

***

#### Ví dụ thao tác FTP

Trong ví dụ, người dùng chạy lệnh:

```bash
ftp 10.201.105.73
```

để kết nối tới FTP server tại địa chỉ IP trên.

Các bước:

1. Dùng username `anonymous` để đăng nhập.
2. Không cần nhập mật khẩu (server cho phép anonymous login).
3. Dùng lệnh `ls` để xem danh sách file trên server. → Có các file: `coffee.txt`, `flag.txt`, `tea.txt`.
4. Dùng lệnh `type ascii` để chuyển chế độ truyền file sang **ASCII** (vì file dạng văn bản).
5. Dùng lệnh `get coffee.txt` để tải file về.

Kết quả:

* File `coffee.txt` đã được tải về, nhưng có cảnh báo vì lỗi linefeed (không ảnh hưởng nhiều nếu chỉ đọc text).
* Cuối cùng dùng `quit` để thoát.

```bash
user@TryHackMe$ ftp 10.201.105.73
Connected to 10.201.105.73 (10.201.105.73).
220 (vsFTPd 3.0.5)
Name (10.201.105.73:strategos): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
227 Entering Passive Mode (10,10,41,192,134,10).
150 Here comes the directory listing.
-rw-r--r--    1 0        0            1480 Jun 27 08:03 coffee.txt
-rw-r--r--    1 0        0              14 Jun 27 08:04 flag.txt
-rw-r--r--    1 0        0            1595 Jun 27 08:05 tea.txt
226 Directory send OK.
ftp> type ascii
200 Switching to ASCII mode.
ftp> get coffee.txt
local: coffee.txt remote: coffee.txt
227 Entering Passive Mode (10,10,41,192,57,100).
150 Opening BINARY mode data connection for coffee.txt (1480 bytes).
WARNING! 47 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
1480 bytes received in 8e-05 secs (18500.00 Kbytes/sec)
ftp> quit
221 Goodbye.
```

***

#### Quan sát bằng Wireshark

* Khi xem bằng Wireshark:
  * **Lệnh từ client** hiển thị màu **đỏ**.
  * **Phản hồi từ server** hiển thị màu **xanh**.
* Ví dụ: khi bạn gõ `ls` trên client → client thật sự gửi lệnh **LIST** đến server.
* Ngoài ra, mỗi khi lấy danh sách thư mục (`ls`) hay tải file (`get coffee.txt`), FTP sẽ mở **kết nối dữ liệu riêng biệt** cho việc truyền tải (khác với kết nối điều khiển ở port 21).

<figure><img src="../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>



### 5. SMTP: Sending Email

#### SMTP là gì?

* Giống như HTTP (duyệt web) và FTP (tải file), việc **gửi email** cũng cần một giao thức riêng.
* **SMTP (Simple Mail Transfer Protocol)** định nghĩa cách:
  * Mail client (ứng dụng email) giao tiếp với mail server.
  * Mail server giao tiếp với mail server khác để chuyển thư.

📌 Ví dụ thực tế: giống như bạn ra bưu điện gửi bưu kiện → khai báo người gửi, người nhận, trình giấy tờ (nếu cần), rồi mới gửi gói hàng. SMTP hoạt động tương tự như vậy.

***

#### Các lệnh cơ bản trong SMTP

* **HELO** hoặc **EHLO** → mở đầu phiên làm việc SMTP.
* **MAIL FROM** → chỉ định địa chỉ email của người gửi.
* **RCPT TO** → chỉ định địa chỉ email của người nhận.
* **DATA** → báo rằng client sẽ bắt đầu gửi nội dung email.
* **"." (dấu chấm một mình trên một dòng)** → kết thúc nội dung email.

📌 SMTP server mặc định lắng nghe ở **TCP port 25**.

***

#### Ví dụ gửi email bằng Telnet

1.  Kết nối đến server SMTP:

    ```bash
    telnet 10.201.105.73 25
    ```
2.  Trao đổi lệnh:

    ```
    canhieu@DESKTOP-DBGES7N:~$ telnet 10.201.105.73 25
    Trying 10.201.105.73...
    Connected to 10.201.105.73.
    Escape character is '^]'.
    220 ip-10-201-105-73.ec2.internal ESMTP Exim 4.95 Ubuntu Sun, 14 Sep 2025 06:11:39 +0000
    HELO client.thm
    250 ip-10-201-105-73.ec2.internal Hello client.thm [10.17.11.244]
    MAIL FROM:<user@client.thm>
    250 OK
    RCPT TO:<strategos@server.thm>
    250 Accepted
    DATA
    354 Enter message, ending with "." on a line by itself
    From: user@client.thm
    To: strategos@server.thm
    Subject: Test mail via telnet

    Hello, this is a test email sent via telnet!
    .

    250 OK id=1uxfzX-0000Q5-M8
    500 unrecognized command
    QUIT
    221 ip-10-201-105-73.ec2.internal closing connection
    Connection closed by foreign host.
    ```

👉 Server phản hồi `250 OK` ở các bước thành công, và `221 closing connection` khi kết thúc.

***

#### Quan sát bằng Wireshark

* Wireshark hiển thị quá trình trao đổi:
  * **Client** gửi lệnh (màu đỏ).
  * **Server** trả lời (màu xanh).

<figure><img src="../.gitbook/assets/image (288).png" alt=""><figcaption></figcaption></figure>

Bạn sẽ thấy rõ từng gói tin chứa `HELO`, `MAIL FROM`, `RCPT TO`, `DATA`, nội dung email, rồi kết thúc bằng dấu `"."`.

***

#### Kết luận

* Sau khi học HTTP, FTP, và SMTP bạn đã nắm được cách **protocol text-based** hoạt động.
* Từ đây, việc học các giao thức tương tự như **POP3** (nhận email) và **IMAP** (đồng bộ email) sẽ trở nên dễ dàng.



### 6. POP3: Receiving Email

#### POP3 là gì?

* **POP3 (Post Office Protocol version 3)** là giao thức giúp **client (ứng dụng email)** lấy email từ **mail server** về máy cục bộ.
* Cách hình dung:
  * **SMTP** = bạn mang thư ra bưu điện để gửi đi.
  * **POP3** = bạn mở hộp thư tại nhà để nhận thư mới.

<figure><img src="../.gitbook/assets/image (289).png" alt=""><figcaption></figcaption></figure>

👉 Nói ngắn gọn:

* **SMTP** dùng để **gửi mail**.
* **POP3** dùng để **nhận mail**.

***

#### Một số lệnh POP3 cơ bản

* `USER <username>` → gửi tên đăng nhập.
* `PASS <password>` → gửi mật khẩu.
* `STAT` → hiển thị số lượng thư và tổng dung lượng.
* `LIST` → liệt kê tất cả thư cùng kích thước.
* `RETR <message_number>` → tải thư theo số thứ tự.
* `DELE <message_number>` → đánh dấu thư để xóa.
* `QUIT` → kết thúc phiên, áp dụng thay đổi (nếu có).

📌 POP3 server mặc định chạy trên **TCP port 110**.

***

#### Ví dụ kết nối POP3 qua Telnet

1.  Kết nối:

    ```bash
    telnet 10.201.105.73 110
    ```

    Server trả về:

    ```
    +OK [XCLIENT] Dovecot (Ubuntu) ready.
    ```
2.  Đăng nhập:

    ```
    USER strategos
    +OK
    PASS 
    +OK Logged in.
    ```
3.  Kiểm tra số thư:

    ```
    STAT
    +OK 3 1264
    ```
4.  Liệt kê thư:

    ```
    LIST
    +OK 3 messages:
    1 407
    2 412
    3 445
    .
    ```
5.  Đọc thư số 3:

    ```
    RETR 3
    +OK 445 octets
    From: user@client.thm
    To: strategos@server.thm
    Subject: Telnet email

    Hello. I am using telnet to send you an email!
    .
    ```
6.  Thoát:

    ```
    QUIT
    +OK Logging out.
    ```

***

#### Bảo mật trong POP3

* POP3 truyền dữ liệu **dạng text thuần** → rất dễ bị nghe lén.

<figure><img src="../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

* Kẻ tấn công bắt gói tin (Wireshark, tcpdump) có thể thấy:
  * **Username**
  * **Password**
  * **Nội dung email**
* Vì vậy hiện nay thường dùng **POP3S (POP3 over SSL/TLS)** chạy trên **port 995** để mã hóa kết nối.

***

#### Thông tin đăng nhập để thử nghiệm

* **Username**: `linda`
* **Password**: `Pa$$123`



### 7. IMAP: Synchronizing Email

POP3 là đủ khi làm việc từ một thiết bị, ví dụ như ứng dụng email yêu thích của bạn trên máy tính để bàn. Tuy nhiên, điều gì sẽ xảy ra nếu bạn muốn kiểm tra email của mình từ máy tính văn phòng và từ laptop hoặc điện thoại thông minh? Trong trường hợp này, bạn cần một giao thức cho phép đồng bộ hóa thư thay vì xóa thư sau khi đã tải xuống. Một giải pháp để duy trì hộp thư đồng bộ trên nhiều thiết bị là Internet Message Access Protocol (IMAP).

IMAP cho phép đồng bộ hóa các thư đã đọc, đã di chuyển và đã xóa. IMAP khá tiện lợi khi bạn kiểm tra email qua nhiều ứng dụng khác nhau. Không giống như POP3, vốn có xu hướng giảm thiểu dung lượng lưu trữ trên máy chủ vì email được tải xuống và xóa khỏi máy chủ từ xa, IMAP có xu hướng sử dụng nhiều dung lượng lưu trữ hơn vì email được giữ lại trên máy chủ và được đồng bộ trên các ứng dụng email.

Các lệnh giao thức IMAP phức tạp hơn so với các lệnh của POP3. Chúng tôi liệt kê một vài ví dụ dưới đây:

* `LOGIN <username> <password>` xác thực người dùng
* `SELECT <mailbox>` chọn thư mục hộp thư để làm việc
* `FETCH <mail_number> <data_item_name>` ví dụ `fetch 3 body[]` để lấy thư số 3, bao gồm header và body
* `MOVE <sequence_set> <mailbox>` di chuyển các thư đã chỉ định sang một hộp thư khác
* `COPY <sequence_set> <data_item_name>` sao chép các thư đã chỉ định sang một hộp thư khác
* `LOGOUT` đăng xuất

Biết rằng máy chủ IMAP lắng nghe mặc định trên cổng TCP 143, chúng ta sẽ dùng telnet để kết nối đến cổng 143 của 10.201.105.73 và lấy bức thư mà chúng ta đã gửi trong bài trước.

***

#### Terminal

```
user@TryHackMe$ telnet 10.10.41.192 143
Trying 10.10.41.192...
Connected to 10.10.41.192.
Escape character is '^]'.
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ STARTTLS AUTH=PLAIN] Dovecot (Ubuntu) ready.
A LOGIN strategos
A OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY PREVIEW STATUS=SIZE SAVEDATE LITERAL+ NOTIFY SPECIAL-USE] Logged in
B SELECT inbox
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 4 EXISTS
* 0 RECENT
* OK [UNSEEN 2] First unseen.
* OK [UIDVALIDITY 1719824692] UIDs valid
* OK [UIDNEXT 5] Predicted next UID
B OK [READ-WRITE] Select completed (0.001 + 0.000 secs).
C FETCH 3 body[]
* 3 FETCH (BODY[] {445}
Return-path: <user@client.thm>
Envelope-to: strategos@server.thm
Delivery-date: Thu, 27 Jun 2024 16:19:35 +0000
Received: from [10.11.81.126] (helo=client.thm)
        by example.thm with smtp (Exim 4.95)
        (envelope-from <user@client.thm>)
        id 1sMrpq-0001Ah-UT
        for strategos@server.thm;
        Thu, 27 Jun 2024 16:19:35 +0000
From: user@client.thm
To: strategos@server.thm
Subject: Telnet email

Hello. I am using telnet to send you an email!
)
C OK Fetch completed (0.001 + 0.000 secs).
D LOGOUT
* BYE Logging out
D OK Logout completed (0.001 + 0.000 secs).
Connection closed by foreign host.
```



## D. Networking Secure Protocols

### 1. Intro&#x20;

#### Learning Objectives

Upon finishing this room, you will learn about:

* SSL/TLS
* How to secure existing plaintext protocols:
  * HTTP
  * SMTP
  * POP3
  * IMAP
* How SSH replaced the plaintext TELNET
* How VPN creates a secure network over an insecure one



### 2. TLS

#### 1. Thời kỳ chưa có mã hóa

* Trước đây, chỉ cần một công cụ packet-capturing (như Wireshark) là kẻ tấn công có thể đọc được toàn bộ **chat, email, mật khẩu** trên mạng.
* Kẻ tấn công bật **promiscuous mode** để thu tất cả gói tin, kể cả không gửi tới máy của hắn.
* Dữ liệu đăng nhập (password) được gửi **dạng cleartext** → người dùng không có cách nào bảo vệ.

***

#### 2. Sự ra đời của SSL/TLS

* **1995**: Netscape giới thiệu **SSL 2.0**, giải pháp mã hóa truyền thông.
* **1999**: IETF phát triển **TLS 1.0**, nâng cấp từ SSL 3.0.
* **2018**: Ra mắt **TLS 1.3**, cải thiện mạnh về hiệu năng và bảo mật.
* Ý chính: mất hơn **20 năm phát triển liên tục** để đến TLS 1.3 hiện nay.

***

#### 3. Chức năng của TLS

* **Tầng hoạt động**: Transport Layer (theo mô hình OSI).
* **Mục tiêu**:
  * **Confidentiality (bảo mật thông tin)** → không ai đọc được dữ liệu.
  * **Integrity (toàn vẹn dữ liệu)** → không ai sửa đổi được dữ liệu.
* Ví dụ: không thể có **online shopping, banking, email** an toàn nếu không có TLS.

***

#### 4. Sự mở rộng của TLS

* TLS được tích hợp vào nhiều giao thức → thêm chữ **S (Secure)**:
  * HTTP → **HTTPS**
  * DNS → **DoT (DNS over TLS)**
  * MQTT → **MQTTS**
  * SIP → **SIPS**
* Trong phần tiếp theo (theo nội dung gốc) sẽ tìm hiểu các giao thức: **HTTPS, SMTPS, POP3S, IMAPS**.

***

#### 5. Chứng chỉ số (TLS Certificates)

* Để server (hoặc client) xác thực danh tính, cần **chứng chỉ TLS**:
  * Admin tạo **CSR (Certificate Signing Request)**.
  * Gửi CSR cho **CA (Certificate Authority)**.
  * CA xác minh → cấp **chứng chỉ số**.
* Trình duyệt có sẵn danh sách **CA tin cậy** (giống như con dấu của các cơ quan).
* Có thể:
  * **Mua chứng chỉ** (thường trả phí hàng năm).
  * **Let’s Encrypt** cấp miễn phí.
  * **Tự ký (self-signed)** → không được tin cậy vì không có bên thứ ba xác nhận.

<figure><img src="../.gitbook/assets/image (550).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (552).png" alt=""><figcaption></figcaption></figure>



### 3. HTTPS

#### 1. HTTP và vấn đề bảo mật

<figure><img src="../.gitbook/assets/image (553).png" alt=""><figcaption></figcaption></figure>

* **HTTP mặc định dùng TCP port 80**.
* Quy trình cơ bản để lấy trang qua HTTP:
  1. **TCP three-way handshake** giữa client và server.
  2. **Trao đổi HTTP request/response** (ví dụ: `GET / HTTP/1.1`).
* Toàn bộ lưu lượng HTTP được gửi **dạng cleartext** → kẻ tấn công dễ dàng đọc được trên Wireshark.
* Việc theo dõi gói tin cho thấy: handshake (1), request/response (2), rồi **TCP connection termination** (3).

<figure><img src="../.gitbook/assets/image (554).png" alt=""><figcaption></figcaption></figure>

***

#### 2. HTTPS = HTTP Over TLS

* **HTTPS** (Hypertext Transfer Protocol Secure) = HTTP + TLS, mặc định dùng port 443.
* Quy trình cơ bản khi truy cập HTTPS:
  1. **TCP three-way handshake** với server.
  2. **TLS handshake** để thiết lập phiên mã hóa.
  3. **Trao đổi HTTP request/response** nhưng dữ liệu được gói trong TLS → Wireshark chỉ hiển thị **Application Data**.

<figure><img src="../.gitbook/assets/image (555).png" alt=""><figcaption></figcaption></figure>

* Do TLS mã hóa, khi theo dõi gói tin chỉ thấy dữ liệu “gibberish” (không đọc được nội dung).

<figure><img src="../.gitbook/assets/image (556).png" alt=""><figcaption></figcaption></figure>

***

#### 3. Giải mã HTTPS với khóa riêng

<figure><img src="../.gitbook/assets/image (557).png" alt=""><figcaption></figcaption></figure>

* Muốn đọc được nội dung HTTPS → cần **private key** của server.
* Khi Wireshark có khóa giải mã:
  * **TCP handshake và TLS handshake vẫn như cũ.**
  * Sau khi thiết lập TLS, có thể nhìn thấy các **HTTP GET/response** giống như HTTP thường.
* Như vậy, dữ liệu thực chất vẫn là HTTP, chỉ được ẩn đi nhờ lớp mã hóa TLS.

<figure><img src="../.gitbook/assets/image (558).png" alt=""><figcaption></figcaption></figure>

***

#### 4. Ý nghĩa chính

* **TLS bảo vệ HTTP** bằng cách:
  * Không thay đổi **tầng dưới** (TCP, IP giữ nguyên).
  * Không thay đổi **ứng dụng** (HTTP vẫn hoạt động như cũ).
  * Chỉ thêm **lớp bảo mật** ở giữa → đảm bảo tính **confidentiality** và **integrity**.
* Nhờ vậy, mọi ứng dụng web hiện nay (online banking, shopping, email…) có thể hoạt động an toàn mà không cần viết lại toàn bộ giao thức.

<figure><img src="../.gitbook/assets/image (559).png" alt=""><figcaption></figcaption></figure>



### 4. SMTPS, POP3S, and IMAPS

#### 1. Nguyên tắc chung

* Cách thêm **TLS** vào **SMTP, POP3, IMAP** **tương tự** như với HTTP.
* Khi thêm TLS, giao thức được thêm chữ **S (Secure)** ở cuối:
  * HTTP → **HTTPS**
  * SMTP → **SMTPS**
  * POP3 → **POP3S**
  * IMAP → **IMAPS**
* Cách thức hoạt động giống hệt HTTPS:
  * TCP handshake → TLS handshake → trao đổi dữ liệu ứng dụng (nhưng dữ liệu được mã hóa).

***

#### 2. Các cổng mặc định (không bảo mật)

| Protocol | Port mặc định |
| -------- | ------------- |
| HTTP     | 80            |
| SMTP     | 25            |
| POP3     | 110           |
| IMAP     | 143           |

***

#### 3. Các cổng mặc định (có bảo mật TLS)

| Protocol | Port mặc định (TLS) |
| -------- | ------------------- |
| HTTPS    | 443                 |
| SMTPS    | 465 và 587          |
| POP3S    | 995                 |
| IMAPS    | 993                 |

***

<figure><img src="../.gitbook/assets/image (560).png" alt=""><figcaption></figcaption></figure>



### 5. SSH

#### 1. TELNET và vấn đề bảo mật

* **TELNET**: giao thức tiện lợi để đăng nhập và quản trị hệ thống từ xa.
* Nhược điểm lớn: **gửi toàn bộ dữ liệu dạng cleartext** → dễ bị nghe lén, lộ username/password.
* Điều này dẫn đến nhu cầu một giải pháp an toàn hơn.

***

#### 2. Sự ra đời của SSH

* **1995**: Tatu Ylönen phát triển **SSH-1** (freeware).
* Cùng năm đó, Netscape ra mắt **SSL 2.0**.
* **1996**: SSH-2 ra đời, an toàn hơn.
* **1999**: OpenBSD phát hành **OpenSSH** (open-source).
* Ngày nay, hầu hết các SSH client đều dựa trên thư viện & code của **OpenSSH**.

***

#### 3. Ưu điểm chính của OpenSSH

* **Secure authentication**: ngoài password, còn hỗ trợ **public key** và **two-factor authentication**.
* **Confidentiality**: mã hóa end-to-end, ngăn chặn nghe lén; cảnh báo khi có server key mới để chống **MITM attack**.
* **Integrity**: bảo đảm dữ liệu không bị thay đổi nhờ cơ chế mã hóa kiểm tra toàn vẹn.
* **Tunneling**: tạo “tunnel” để truyền các giao thức khác qua SSH → hoạt động như **VPN**.
* **X11 Forwarding**: cho phép chạy ứng dụng đồ họa từ xa (Unix-like system).

***

#### 4. Cách sử dụng SSH

* Cú pháp cơ bản:
  * `ssh username@hostname`
  * Nếu username trùng với username đang đăng nhập → chỉ cần `ssh hostname`.
* Đăng nhập:
  * Nếu dùng password → nhập thủ công.
  * Nếu dùng **public key authentication** → đăng nhập tự động (không cần nhập pass).
* Ví dụ chạy Wireshark với giao diện đồ họa từ xa:
  * `ssh 192.168.124.148 -X`
  * (Yêu cầu máy local có sẵn hệ thống hỗ trợ GUI).

<figure><img src="../.gitbook/assets/image (561).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (562).png" alt=""><figcaption></figcaption></figure>



### 6. SFTP and FTPS

| Cleartext Protocol | Port | Secure Protocol                | Port |
| ------------------ | ---- | ------------------------------ | ---- |
| **Telnet**         | 23   | **SSH**                        | 22   |
| **SMTP**           | 25   | **SMTP (Submission with TLS)** | 587  |
| **HTTP**           | 80   | **HTTPS**                      | 443  |
| **POP3**           | 110  | **POP3S**                      | 995  |
| **IMAP**           | 143  | **IMAPS**                      | 993  |
| **FTP**            | 21   | **FTPS (Implicit)**            | 990  |



### 7. VPN

#### 1. Khái niệm VPN

* **VPN (Virtual Private Network)**: cho phép kết nối các văn phòng, chi nhánh từ xa với **trụ sở chính** thông qua hạ tầng Internet.
* Thiết bị trong mạng từ xa khi kết nối VPN sẽ hoạt động **như thể đang ở trong mạng nội bộ** tại trụ sở chính.
* VPN được xem là giải pháp **kinh tế, tiện lợi** so với việc xây dựng hạ tầng riêng.

***

#### 2. Nhu cầu ra đời của VPN

* TCP/IP được thiết kế để **đảm bảo truyền tải gói tin** (packet delivery, routing, retransmission), **không đảm bảo**:
  * **Bảo mật (confidentiality)** → dữ liệu có thể bị lộ.
  * **Toàn vẹn (integrity)** → dữ liệu có thể bị thay đổi.
* VPN giải quyết bằng cách tạo một **kết nối riêng (Private)** trên nền Internet công cộng.

***

#### 3. Mô hình hoạt động của VPN

* **VPN site-to-site**: kết nối **mạng chi nhánh** đến **mạng chính** qua VPN server ở trụ sở.
  * **VPN client** trong chi nhánh → **mã hóa traffic** → gửi đến trụ sở chính qua **VPN tunnel** (đường hầm mã hóa).
  * Khi đến VPN server, traffic được **giải mã** rồi chuyển tiếp trong mạng nội bộ.

<figure><img src="../.gitbook/assets/image (563).png" alt=""><figcaption></figcaption></figure>





* **VPN remote-access**: người dùng cá nhân (laptop, PC) chạy **VPN client** để kết nối trực tiếp với trụ sở.

<figure><img src="../.gitbook/assets/image (564).png" alt=""><figcaption></figcaption></figure>

***

#### 4. Đặc điểm khi dùng VPN

* **Tất cả traffic** thường được định tuyến qua VPN tunnel.
  * ISP hoặc bên thứ 3 chỉ thấy **lưu lượng đã mã hóa** → khó kiểm duyệt.
  * Website/dịch vụ chỉ thấy **IP của VPN server**, không thấy IP thật của client.
* **Ứng dụng thực tế**:
  * Truy cập dịch vụ bị hạn chế địa lý (ví dụ: VPN đến Nhật → Google Search hiện giao diện tiếng Nhật).
  * Bảo mật truy cập Internet tại môi trường không an toàn (quán cà phê, sân bay).

<figure><img src="../.gitbook/assets/image (565).png" alt=""><figcaption></figcaption></figure>

***

#### 5. Giới hạn và rủi ro của VPN

* Không phải VPN nào cũng **route toàn bộ traffic**: có VPN chỉ cho phép truy cập mạng nội bộ, không đi Internet.
* Một số VPN có thể **rò rỉ IP thật** (IP leak, DNS leak). → cần kiểm tra thêm (DNS leak test).
* **Vấn đề pháp lý**: ở một số quốc gia, việc dùng VPN là **bất hợp pháp hoặc bị hạn chế** → cần kiểm tra luật địa phương.













