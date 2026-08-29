# Introduction to Networking

## &#x20;**I. Introduction**



### Tổng quan về Mạng (Networking Overview)

Một mạng cho phép hai máy tính giao tiếp với nhau. Có nhiều loại hình topologies (mesh/tree/star), phương tiện truyền dẫn (ethernet/fiber/coax/wireless), và giao thức (TCP/UDP/IPX) có thể được sử dụng để hỗ trợ việc hình thành mạng. Việc hiểu về mạng là rất quan trọng đối với các chuyên gia an ninh mạng bởi vì khi mạng gặp sự cố, lỗi có thể xảy ra âm thầm, khiến chúng ta bỏ lỡ điều gì đó.

Thiết lập một mạng lớn và phẳng (flat network) không quá khó, và nó có thể là một mạng đáng tin cậy ít nhất về mặt vận hành. Tuy nhiên, một mạng phẳng giống như xây một ngôi nhà trên một lô đất và cho rằng nó an toàn chỉ vì có một ổ khóa ở cửa. Bằng cách tạo ra nhiều mạng nhỏ hơn và để chúng giao tiếp với nhau, chúng ta có thể thêm các lớp phòng thủ. Việc di chuyển (pivoting) trong một mạng không khó, nhưng làm điều đó nhanh chóng và âm thầm thì khó, sẽ khiến kẻ tấn công chậm lại. Quay lại ví dụ ngôi nhà, hãy cùng đi qua các ví dụ sau:

***

#### Ví dụ số 1

Xây dựng các mạng nhỏ hơn và đặt Access Control Lists (ACLs) xung quanh chúng giống như đặt một hàng rào quanh ranh giới của tài sản, tạo ra các điểm ra vào cụ thể. Đúng, một kẻ tấn công có thể nhảy qua hàng rào, nhưng điều này trông đáng ngờ và không phổ biến, cho phép nó nhanh chóng bị phát hiện là hoạt động độc hại.\
👉 Tại sao mạng máy in lại nói chuyện với máy chủ thông qua HTTP?

***

#### Ví dụ số 2

Dành thời gian lập bản đồ và ghi chép lại mục đích của từng mạng giống như đặt đèn xung quanh tài sản, đảm bảo rằng mọi hoạt động đều có thể được nhìn thấy.\
👉 Tại sao mạng máy in lại giao tiếp với Internet?

***

#### Ví dụ số 3

Có những bụi cây xung quanh cửa sổ là một sự ngăn cản đối với những người đang cố gắng mở cửa sổ. Giống như Hệ thống phát hiện xâm nhập (Intrusion Detection Systems – IDS) như Suricata hoặc Snort là một biện pháp ngăn cản việc quét mạng.\
👉 Tại sao một cuộc port scan lại bắt nguồn từ mạng máy in?

Các ví dụ này nghe có vẻ ngớ ngẩn, và thật là lẽ thường khi chặn một máy in khỏi tất cả những điều trên. Tuy nhiên, nếu máy in nằm trong một mạng phẳng “/24” và nhận địa chỉ qua DHCP, thì rất khó để đặt ra các loại giới hạn này cho chúng.

***

### Câu chuyện – Sai sót của một Pentester

Hầu hết các mạng sử dụng subnet `/24`, đến mức nhiều Pentester sẽ đặt subnet mask này (255.255.255.0) mà không kiểm tra. Mạng `/24` cho phép các máy tính nói chuyện với nhau miễn là ba octet đầu của địa chỉ IP giống nhau (ví dụ: 192.168.1.xxx). Việc đặt subnet mask thành `/25` sẽ chia dải này làm đôi, và máy tính sẽ chỉ có thể nói chuyện với các máy tính trong “nửa của nó”.

Chúng tôi đã từng thấy các báo cáo Pentest trong đó người đánh giá tuyên bố rằng một Domain Controller ngoại tuyến, trong khi thực tế nó chỉ ở một mạng khác. Cấu trúc mạng lúc đó như sau:

* Server Gateway: 10.20.0.1/25
* Domain Controller: 10.20.0.10/25
* Client Gateway: 10.20.0.129/25
* Client Workstation: 10.20.0.200/25
* Pentester IP: 10.20.0.252/24 (Gateway trỏ về 10.20.0.1)

Pentester đã giao tiếp với các Client Workstations và nghĩ rằng họ đã làm rất tốt vì họ đã đánh cắp được mật khẩu workstation qua Impacket. Tuy nhiên, do không hiểu mạng, họ không bao giờ thoát khỏi Client Network để tiếp cận các mục tiêu “giá trị cao” hơn như database servers. Hy vọng rằng nếu đoạn này nghe có vẻ khó hiểu, bạn có thể quay lại sau khi hoàn thành module và hiểu rõ hơn!

***

### Thông tin cơ bản (Basic Information)

<figure><img src="../../.gitbook/assets/image (322).png" alt=""><figcaption></figcaption></figure>

Hãy xem sơ đồ cấp cao sau đây về cách thiết lập Work From Home có thể hoạt động.

(Sơ đồ hiển thị một mạng gia đình với router kết nối smartphone, notebook, PC và một mạng công ty với router kết nối switch, webserver, IP phone, printer, và hai client host. Cả hai mạng kết nối đến một ISP, với DNS và truy cập Internet.)

Toàn bộ Internet được xây dựng trên nhiều mạng con nhỏ hơn, như được minh họa với “Home Network” và “Company Network”. Chúng ta có thể hình dung mạng giống như việc giao thư hoặc gói hàng từ một máy tính gửi đi và máy tính khác nhận.

Giả sử trong một kịch bản, chúng ta muốn truy cập một trang web công ty từ “Home Network.” Khi đó, chúng ta trao đổi dữ liệu với trang web của công ty nằm trong “Company Network.” Giống như gửi thư hoặc gói hàng, chúng ta biết địa chỉ nơi gói hàng cần đến. Địa chỉ trang web hoặc URL (Uniform Resource Locator) mà chúng ta nhập vào trình duyệt cũng được gọi là Fully Qualified Domain Name (FQDN).

Sự khác biệt giữa URL và FQDN là:

* Một **FQDN** (ví dụ: [www.hackthebox.eu](http://www.hackthebox.eu)) chỉ định địa chỉ của “tòa nhà”
* Một **URL** (ví dụ: [https://www.hackthebox.eu/example?floor=2\&office=dev\&employee=17](https://www.hackthebox.eu/example?floor=2\&office=dev\&employee=17)) chỉ định cả “tầng,” “phòng,” “hộp thư” và “nhân viên” mà gói hàng hướng đến.

Chúng ta sẽ bàn chi tiết về các khái niệm này trong các phần sau.

Điều quan trọng là: chúng ta biết địa chỉ nhưng không biết vị trí địa lý chính xác. Trong tình huống này, “bưu điện” có thể xác định vị trí, sau đó chuyển tiếp gói hàng đến đúng nơi. Do đó, bưu điện của chúng ta chuyển tiếp gói hàng đến bưu điện trung tâm, tức là ISP.

Router tại nhà chính là “bưu điện” mà chúng ta dùng để kết nối Internet.

Khi chúng ta gửi gói tin qua router, nó sẽ được chuyển đến ISP. ISP này tra sổ địa chỉ (DNS) để tìm xem địa chỉ nằm ở đâu và trả về tọa độ chính xác (IP). Khi biết vị trí chính xác, gói tin sẽ được gửi trực tiếp tới đó qua ISP.

Sau khi web server nhận được gói tin chứa yêu cầu, nó sẽ gửi trả dữ liệu hiển thị website thông qua router của “Company Network” đến địa chỉ trả lời (IP của chúng ta).

***

### Điểm bổ sung (Extra Points)

Trong sơ đồ trên, tôi hy vọng mạng công ty bao gồm **5 mạng riêng biệt**:

* **Web Server** nên đặt trong **DMZ (Demilitarized Zone)** vì client từ Internet có thể khởi tạo kết nối, dễ bị tấn công. Đặt riêng mạng cho phép quản trị viên thêm lớp bảo vệ.
* **Workstations** nên tách mạng riêng, tốt nhất mỗi máy có firewall chặn giao tiếp ngang hàng. Nếu workstation và server chung mạng, tấn công spoofing hoặc MITM sẽ dễ xảy ra.
* **Switch và Router** nên nằm trong **Administration Network**. Điều này ngăn workstation theo dõi hoặc giả mạo các giao thức như OSPF. Tôi từng Pentest và thấy OSPF quảng bá ra toàn mạng, bất kỳ ai cũng có thể gửi gói tin giả mạo để MITM.
* **IP Phones** nên tách mạng riêng. Điều này ngăn việc máy tính nghe lén, đồng thời đảm bảo chất lượng (latency/lag) trong cuộc gọi.
* **Printers** nên tách mạng riêng. Nghe có vẻ lạ, nhưng bảo mật máy in gần như bất khả thi. Windows có thể bị lừa thực hiện NTLMv2 authentication khi in, dẫn tới lộ mật khẩu. Ngoài ra, máy in lưu trữ nhiều tài liệu nhạy cảm và có thể bị dùng làm điểm bám trụ.

***

### Câu chuyện vui (Fun Story)

Trong thời COVID, tôi được giao làm Physical Penetration Test ở bang khác, trong khi bang của tôi bị phong tỏa. Công ty mục tiêu chỉ có rất ít nhân viên làm việc tại văn phòng. Tôi quyết định mua một chiếc máy in đắt tiền, cài reverse shell vào đó. Khi máy in được kết nối vào mạng, nó sẽ gửi shell về cho tôi (tức remote access). Tôi sau đó gửi chiếc máy in này cho công ty và kèm email phishing cảm ơn nhân viên đã đến văn phòng, giải thích rằng máy in sẽ giúp họ in và scan tài liệu nhanh hơn để làm việc tại nhà. Máy in lập tức được cắm vào, và máy tính của Domain Admin đã “tốt bụng” gửi luôn thông tin đăng nhập của ông ta cho máy in!

Nếu công ty này có một mạng được thiết kế an toàn, cuộc tấn công này sẽ không thể xảy ra vì:

* Máy in không được phép kết nối Internet
* Workstation không được phép nói chuyện với máy in qua port 445
* Máy in không được phép khởi tạo kết nối đến workstation (ngoại trừ một số trường hợp đặc biệt như gửi mail scan qua mail server)



## &#x20;**II. Networking Structure**

## Các loại mạng (Network Types)

Mỗi mạng được cấu trúc theo cách khác nhau và có thể được thiết lập độc lập. Vì lý do này, những loại hình và cấu trúc (types and topologies) đã được phát triển để phân loại các mạng. Khi đọc về tất cả các loại mạng, có thể bạn sẽ bị “quá tải thông tin” vì một số loại mạng được định nghĩa dựa trên **phạm vi địa lý**. Trong thực tế, một số thuật ngữ hiếm khi được nghe đến. Do đó, phần này sẽ được chia thành **Thuật ngữ thông dụng (Common Terms)** và **Thuật ngữ sách vở (Book Terms)**.

Các “thuật ngữ sách vở” cũng nên biết, vì đã từng có một trường hợp được ghi nhận rằng một **email server không thể gửi email đi xa hơn 500 dặm**. Tuy nhiên, bạn không cần phải thuộc lòng chúng, trừ khi đang ôn thi về mạng máy tính.

***

### Thuật ngữ thông dụng (Common Terminology)

| Loại mạng                              | Định nghĩa                              |
| -------------------------------------- | --------------------------------------- |
| **Wide Area Network (WAN)**            | Internet                                |
| **Local Area Network (LAN)**           | Mạng nội bộ (Ví dụ: Nhà hoặc Văn phòng) |
| **Wireless Local Area Network (WLAN)** | Mạng nội bộ truy cập qua Wi-Fi          |
| **Virtual Private Network (VPN)**      | Kết nối nhiều mạng thành một LAN        |

***

#### WAN

WAN (Wide Area Network) thường được gọi là **Internet**. Khi làm việc với thiết bị mạng, ta thường thấy có **WAN Address** và **LAN Address**. Địa chỉ WAN là địa chỉ được truy cập từ Internet. Tuy nhiên, WAN không chỉ là Internet; một WAN chỉ đơn giản là sự kết nối của nhiều LAN.

Nhiều công ty lớn hoặc cơ quan chính phủ có thể có một “Internal WAN” (cũng gọi là **Intranet**, **Airgap Network**, v.v.).

Nói chung, cách chính để xác định một mạng có phải WAN hay không:

* Dùng **giao thức định tuyến WAN** như BGP
* Nếu sơ đồ IP sử dụng không nằm trong dải **RFC 1918** (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

***

#### LAN / WLAN

LAN (Local Area Network) và WLAN (Wireless Local Area Network) thường gán địa chỉ IP nằm trong dải sử dụng nội bộ (RFC 1918: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

Trong một số trường hợp (ví dụ: một số trường đại học hoặc khách sạn), bạn có thể được gán **địa chỉ IP Internet** trực tiếp khi kết nối LAN, nhưng điều này ít phổ biến hơn.

Thực chất, LAN và WLAN không có sự khác biệt gì ngoài việc WLAN cho phép truyền dữ liệu **không cần dây**. Về an ninh, WLAN là một phân loại quan trọng.

***

#### VPN

Có **3 loại chính của VPN (Virtual Private Network)**, nhưng tất cả đều có cùng mục tiêu: khiến người dùng cảm giác như mình đang “cắm dây” trực tiếp vào một mạng khác.

**1. Site-to-Site VPN**

* Cả client và server đều là **thiết bị mạng** (thường là Router hoặc Firewall).
* Chia sẻ toàn bộ dải mạng.
* Thường dùng để kết nối nhiều chi nhánh công ty qua Internet, để chúng giao tiếp như thể ở cùng một LAN.

**2. Remote Access VPN**

* Máy tính client tạo một giao diện ảo (virtual interface) hoạt động như thể đang ở trong mạng công ty.
* Hack The Box dùng **OpenVPN**, tạo ra một TUN Adapter giúp truy cập các phòng lab.

Điểm quan trọng khi phân tích VPN này là **routing table** được tạo khi tham gia VPN.

* Nếu VPN chỉ tạo route cho một số mạng cụ thể (ví dụ: 10.10.10.0/24), đây gọi là **Split-Tunnel VPN**. Nghĩa là kết nối Internet không đi qua VPN.
* Điều này rất tốt cho Hack The Box, vì cho phép vào Lab mà không lo bị giám sát toàn bộ Internet traffic.
* Tuy nhiên, với doanh nghiệp thì split-tunnel VPN thường **không an toàn**, vì nếu máy tính bị nhiễm malware, các biện pháp phát hiện dựa trên mạng sẽ **không hoạt động**, do traffic đi thẳng ra Internet.

**3. SSL VPN**

* Bản chất là VPN thực hiện trong **trình duyệt web**.
* Ngày càng phổ biến vì trình duyệt hiện nay có thể làm gần như mọi thứ.
* Thường sẽ **stream ứng dụng** hoặc **toàn bộ desktop session** vào trình duyệt.
* Ví dụ điển hình: **HackTheBox Pwnbox**.

***

### Thuật ngữ sách vở (Book Terms)

| Loại mạng                                 | Định nghĩa                         |
| ----------------------------------------- | ---------------------------------- |
| **Global Area Network (GAN)**             | Mạng toàn cầu (Internet)           |
| **Metropolitan Area Network (MAN)**       | Mạng khu vực (nhiều LAN)           |
| **Wireless Personal Area Network (WPAN)** | Mạng cá nhân không dây (Bluetooth) |

***

#### GAN

Một mạng toàn cầu như Internet được gọi là **Global Area Network (GAN)**. Tuy nhiên, Internet không phải là mạng máy tính duy nhất thuộc loại này.

Các công ty quốc tế cũng duy trì các mạng biệt lập, trải dài nhiều WAN và kết nối máy tính toàn cầu. GANs sử dụng cơ sở hạ tầng **cáp quang của WAN**, kết nối với nhau qua **cáp ngầm quốc tế dưới biển** hoặc **truyền dẫn vệ tinh**.

***

#### MAN

**Metropolitan Area Network (MAN)** là một mạng viễn thông băng thông rộng kết nối nhiều LAN gần nhau về mặt địa lý.

* Thông thường, đây là các chi nhánh của một công ty được kết nối qua MAN bằng **đường thuê riêng (leased line)**.
* Sử dụng **router hiệu năng cao** và **kết nối quang tốc độ cao**, cho phép thông lượng lớn hơn Internet.
* Tốc độ truyền giữa hai nút xa tương đương như trong một LAN.

Hạ tầng MAN được cung cấp bởi các nhà mạng quốc tế. Các thành phố có MAN có thể được tích hợp vào WAN (khu vực) và GAN (toàn cầu).

***

#### PAN / WPAN

Các thiết bị hiện đại như smartphone, tablet, laptop, PC có thể kết nối ad hoc thành một mạng để trao đổi dữ liệu.

* Nếu qua **cáp** → gọi là **Personal Area Network (PAN)**
* Nếu **không dây** → gọi là **Wireless Personal Area Network (WPAN)**

WPAN dựa trên công nghệ **Bluetooth** hoặc **Wireless USB**.

* Một mạng WPAN tạo qua Bluetooth gọi là **Piconet**.
* Phạm vi PAN/WPAN chỉ vài mét, không phù hợp để kết nối giữa các phòng hay tòa nhà.

Trong bối cảnh **IoT (Internet of Things)**, WPAN được dùng cho các ứng dụng điều khiển/giám sát với **tốc độ thấp**.\
👉 Các giao thức như **Insteon, Z-Wave, ZigBee** được thiết kế riêng cho **nhà thông minh và tự động hóa gia đình**.



## Các cấu trúc mạng (Networking Topologies)

**Network topology** là cách sắp xếp điển hình và sự kết nối vật lý hoặc logic của các thiết bị trong một mạng. Máy tính là các **host** (máy chủ, máy khách) sử dụng mạng một cách chủ động. Ngoài ra, còn có các thành phần mạng như **switches, bridges, routers** (chúng ta sẽ bàn chi tiết ở các phần sau), giữ vai trò phân phối và đảm bảo tất cả các host trong mạng có thể thiết lập kết nối logic với nhau.

Topology của mạng quyết định các thành phần sẽ được sử dụng và phương thức truy cập vào phương tiện truyền dẫn.

* **Physical topology (cấu trúc vật lý):** bố trí dây cáp, vị trí node, cách nối node với dây dẫn (cáp đồng, cáp quang…).
* **Logical topology (cấu trúc logic):** cách tín hiệu hoạt động trên đường truyền, cách dữ liệu đi từ thiết bị này sang thiết bị khác dựa trên kết nối vật lý.

Toàn bộ topology mạng có thể chia thành ba khu vực chính:

***

### 1. Connections (Kết nối)

* **Kết nối có dây:** Cáp đồng trục (coaxial), Cáp quang (fiber), Cáp xoắn đôi (twisted-pair), v.v.
* **Kết nối không dây:** Wi-Fi, Di động (cellular), Vệ tinh (satellite), v.v.

***

### 2. Nodes – Network Interface Controllers (NICs)

* **Thiết bị:** Repeaters, Hubs, Bridges, Switches, Router/Modem, Gateways, Firewalls.

**Network nodes** là các điểm kết nối với phương tiện truyền dẫn, đóng vai trò phát và nhận tín hiệu điện, quang hoặc vô tuyến. Một node có thể gắn vào máy tính, nhưng cũng có loại chỉ có một vi điều khiển nhỏ, thậm chí không có thiết bị lập trình nào.

***

### 3. Classifications (Phân loại)

Topology có thể xem như hình dạng ảo của một mạng. Hình dạng này không nhất thiết phản ánh chính xác bố trí vật lý của các thiết bị. Vì vậy, topology có thể là **vật lý** hoặc **logic**.

Ví dụ: các máy tính trong LAN có thể được xếp vòng tròn trong phòng ngủ, nhưng điều đó không có nghĩa là chúng đang chạy topology hình vòng (ring).

***

## Các loại topology cơ bản

Có 8 loại topology chính:

* Point-to-Point
* Bus
* Star
* Ring
* Mesh
* Tree
* Hybrid
* Daisy Chain

Mạng phức tạp có thể là sự kết hợp (hybrid) của hai hoặc nhiều topology kể trên.

***

### Point-to-Point

<figure><img src="../../.gitbook/assets/image (528).png" alt=""><figcaption></figcaption></figure>

Đây là topology đơn giản nhất: **một kết nối trực tiếp giữa hai host**.

* Có một đường vật lý riêng biệt giữa hai thiết bị.
* Hai thiết bị này giao tiếp trực tiếp với nhau.

Topology này là mô hình cơ bản của **điện thoại truyền thống**.\
⚠️ Không nên nhầm với **P2P (Peer-to-Peer architecture)**.

***

### Bus

<figure><img src="../../.gitbook/assets/image (527).png" alt=""><figcaption></figcaption></figure>

Trong topology **bus**, tất cả host được nối chung vào một đường truyền (ví dụ: cáp đồng trục).

* Mọi host đều có quyền truy cập vào phương tiện truyền dẫn.
* Khi một host gửi, tất cả host khác chỉ có thể nhận và kiểm tra xem dữ liệu có dành cho mình không.
* Không có thiết bị trung tâm điều khiển truyền thông.
* Chỉ **một host** có thể gửi tại một thời điểm.

***

### Star

<figure><img src="../../.gitbook/assets/image (526).png" alt=""><figcaption></figcaption></figure>

Topology **star** có một thành phần trung tâm kết nối với tất cả host.

* Mỗi host có một kết nối riêng tới trung tâm (router, hub hoặc switch).
* Trung tâm này nhận dữ liệu, sau đó chuyển tiếp đến đích.
* Toàn bộ lưu lượng đi qua thành phần trung tâm → tải có thể rất lớn.

***

### Ring

<figure><img src="../../.gitbook/assets/image (525).png" alt=""><figcaption></figcaption></figure>

Topology hình vòng (ring): mỗi host nối vào vòng bằng hai dây:

1. Một dây nhận tín hiệu vào.
2. Một dây truyền tín hiệu ra.

* Mỗi host vừa nhận vừa truyền, tạo thành vòng khép kín.
* Thường **không cần thiết bị trung tâm**.
* Việc điều khiển quyền truy cập được quy định bởi giao thức, ví dụ: **Token Passing**.
* Token (mẫu bit) chạy vòng quanh mạng, trạm nào giữ token mới được truyền dữ liệu.

**Logical ring** đôi khi được xây dựng trên **physical star**, nơi thiết bị trung tâm giả lập việc chuyển token từ cổng này sang cổng khác.

***

### Mesh

<figure><img src="../../.gitbook/assets/image (524).png" alt=""><figcaption></figcaption></figure>

Topology **mesh** không có hình dạng cố định, vì kết nối và định tuyến do nhiều node quyết định. Có 2 dạng chính:

* **Fully Meshed (toàn kết nối):** mỗi host kết nối với mọi host khác.
  * Dùng trong WAN/MAN để đảm bảo độ tin cậy và băng thông.
  * Nếu một router hỏng, các router khác vẫn duy trì hoạt động nhờ nhiều đường kết nối.
  * Mỗi node có cùng chức năng định tuyến, biết các node lân cận và tải mạng.
* **Partially Meshed (kết nối một phần):** một số node chỉ kết nối với một node, số khác có thể kết nối với nhiều node.

***

### Tree

<figure><img src="../../.gitbook/assets/image (523).png" alt=""><figcaption></figcaption></figure>

Topology **tree** là sự mở rộng của star, thường thấy ở mạng lớn như trong tòa nhà công ty.

* Có cả **tree logic** (dựa trên spanning tree) và **tree vật lý** (cáp phân cấp).
* Mạng hiện đại dùng structured cabling theo phân cấp hub/switch cũng là tree.
* Dùng nhiều trong **mạng băng thông rộng** hoặc **MAN (Metropolitan Area Networks)**.

***

### Hybrid

<figure><img src="../../.gitbook/assets/image (522).png" alt=""><figcaption></figcaption></figure>

Topology **hybrid** là sự kết hợp của hai hoặc nhiều topology khác nhau.

* Ví dụ: một mạng tree có thể chứa nhiều star kết nối với nhau qua bus.
* Nếu tree nối với tree khác → vẫn là tree, không phải hybrid.
* Hybrid chỉ tồn tại khi **2 topology cơ bản khác nhau** được kết nối.

***

### Daisy Chain

<figure><img src="../../.gitbook/assets/image (521).png" alt=""><figcaption></figcaption></figure>

Topology **daisy chain** nối nhiều host nối tiếp nhau qua cáp: node này nối node kia → tạo thành một chuỗi.

* Thường được gọi là “daisy-chain configuration” khi nhiều phần cứng nối thành hàng.
* Phổ biến trong **công nghệ tự động hóa (CAN – Controller Area Network)**.
* Đây là sự sắp xếp vật lý, khác với các thủ tục như token passing vốn mang tính logic.
* Tín hiệu đi qua từng node trung gian để đến đích.



## Proxy (Máy chủ ủy nhiệm)

Nhiều người có những quan điểm khác nhau về “proxy” là gì:

* Chuyên gia an ninh thường nghĩ ngay đến HTTP Proxy (Burp Suite) hoặc xoay trục (pivoting) với SOCKS/SSH Proxy (Chisel, ptunnel, sshuttle).
* Lập trình viên web dùng các proxy như Cloudflare hoặc ModSecurity để chặn lưu lượng độc hại.
* Người dùng phổ thông có thể nghĩ proxy dùng để che giấu vị trí và truy cập thư viện Netflix của một quốc gia khác.
* Cơ quan thực thi pháp luật thường gán proxy với hoạt động bất hợp pháp.

Không phải tất cả ví dụ trên đều đúng. Một proxy là khi có một thiết bị hoặc dịch vụ ngồi ở giữa một kết nối và đóng vai trò “trung gian”. Từ “trung gian” là mấu chốt, vì nó hàm ý thiết bị ở giữa phải có khả năng **kiểm tra nội dung** của lưu lượng. Nếu không có khả năng làm trung gian như vậy, thiết bị đó về mặt kỹ thuật là **gateway**, không phải proxy.

Quay lại câu hỏi phía trên, người dùng phổ thông thường hiểu sai về proxy vì họ nhiều khả năng đang dùng **VPN** để che giấu vị trí, mà về mặt kỹ thuật **không phải** proxy. Nhiều người cho rằng cứ khi nào địa chỉ IP thay đổi thì đó là proxy; trong đa số trường hợp, tốt nhất là không cần sửa sai vì đây là một hiểu lầm phổ biến và vô hại. Sửa họ có thể dẫn tới một cuộc tranh luận dài dòng như tabs vs. spaces, emacs vs. vim, hay hóa ra họ dùng nano.

Nếu khó nhớ, hãy lưu ý: proxy hầu như **luôn hoạt động ở Tầng 7 của mô hình OSI**. Có nhiều loại dịch vụ proxy, nhưng các loại chính là:

* Proxy chuyên dụng / Proxy chuyển tiếp (Dedicated Proxy / Forward Proxy)
* Reverse Proxy
* Transparent Proxy

***

### Proxy chuyên dụng / Proxy chuyển tiếp (Dedicated Proxy / Forward Proxy)

<figure><img src="../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

**Forward Proxy** là thứ đa số mọi người hình dung khi nói tới proxy. Forward Proxy là khi một máy khách gửi yêu cầu đến một máy tính khác, và máy tính đó thay máy khách thực hiện yêu cầu.

Ví dụ, trong mạng doanh nghiệp, các máy tính nhạy cảm có thể không có quyền truy cập Internet trực tiếp. Để truy cập website, chúng phải đi qua một proxy (hoặc bộ lọc web). Đây có thể là một tuyến phòng thủ cực mạnh chống mã độc, vì không chỉ cần vượt qua bộ lọc web (việc này có thể “dễ”), mà mã độc còn phải **nhận biết proxy** hoặc dùng một cơ chế C2 phi truyền thống (cách để mã độc nhận chỉ thị). Nếu tổ chức chỉ sử dụng Firefox, khả năng gặp mã độc “biết proxy” là rất thấp.

Trình duyệt như Internet Explorer, Edge, hoặc Chrome mặc định tuân theo “cài đặt Proxy Hệ thống”. Nếu mã độc sử dụng WinSock (Native Windows API), nó nhiều khả năng sẽ “biết proxy” mà không cần thêm mã. Firefox không dùng WinSock mà dùng **libcurl**, cho phép tái sử dụng cùng một mã trên mọi hệ điều hành. Điều này có nghĩa mã độc sẽ cần phải dò tìm Firefox và lấy cài đặt proxy của nó, điều mà mã độc rất hiếm khi làm.

Ngoài ra, mã độc có thể dùng **DNS** làm cơ chế C2, nhưng nếu tổ chức giám sát DNS (rất dễ thực hiện bằng **Sysmon**), kiểu lưu lượng này thường sẽ bị phát hiện nhanh chóng.

Một ví dụ khác về Forward Proxy là **Burp Suite**, vì đa số người dùng dùng nó để chuyển tiếp các yêu cầu HTTP. Tuy nhiên, đây là “dao đa năng” của HTTP Proxies và có thể cấu hình để trở thành **reverse proxy** hoặc **transparent**.

Mô tả sơ đồ Forward Proxy: Host A gửi HTTP request qua một Forward Proxy ra Internet tới Webserver; Host B cũng được kết nối.

***

### Reverse Proxy

<figure><img src="../../.gitbook/assets/image (315).png" alt=""><figcaption></figcaption></figure>

Như tên gọi gợi ý, **reverse proxy** là “đảo ngược” của Forward Proxy. Thay vì được thiết kế để lọc các yêu cầu đi ra, nó lọc các yêu cầu đi vào. Mục tiêu phổ biến của Reverse Proxy là lắng nghe trên một địa chỉ và chuyển tiếp vào một mạng đóng.

Nhiều tổ chức dùng **Cloudflare** vì họ có mạng lưới mạnh mẽ có thể chịu được đa số các cuộc tấn công DDoS. Bằng cách sử dụng Cloudflare, các tổ chức có cách để lọc khối lượng (và loại) lưu lượng được gửi đến máy chủ web của họ.

Các **pentester** sẽ cấu hình reverse proxy trên các điểm cuối đã bị nhiễm. Điểm cuối bị nhiễm sẽ lắng nghe trên một cổng và gửi bất kỳ khách nào kết nối tới cổng đó quay trở về kẻ tấn công thông qua điểm cuối bị nhiễm. Điều này hữu ích để vượt qua tường lửa hoặc né tránh ghi log. Tổ chức có thể có IDS (Intrusion Detection Systems) giám sát các yêu cầu web ra ngoài. Nếu kẻ tấn công có quyền truy cập tổ chức qua SSH, một reverse proxy có thể gửi các yêu cầu web qua **đường hầm SSH** và né được IDS.

Một reverse proxy phổ biến khác là **ModSecurity**, một **WAF** (Web Application Firewall). WAF kiểm tra các yêu cầu web để tìm nội dung độc hại và chặn yêu cầu nếu phát hiện độc hại. Nếu muốn tìm hiểu thêm, nên đọc **ModSecurity Core Rule Set**, vì đó là điểm khởi đầu rất tốt. **Cloudflare** cũng có thể đóng vai trò WAF, nhưng để làm vậy cần cho phép họ **giải mã lưu lượng HTTPS**, điều mà một số tổ chức có thể không muốn.

Mô tả sơ đồ Reverse Proxy: Host A gửi HTTP request qua Internet, đi qua tường lửa, một Reverse Proxy, và tới Webserver; Host B cũng được kết nối.

***

### Transparent / Non-Transparent Proxy

Tất cả các dịch vụ proxy này có thể hoạt động ở chế độ **transparent** hoặc **non-transparent**.

Với **transparent proxy**, máy khách không biết về sự tồn tại của nó. Transparent proxy chặn các yêu cầu liên lạc của máy khách tới Internet và đóng vai trò một thực thể thay thế. Ở phía bên ngoài, transparent proxy cũng giống non-transparent proxy, đều đóng vai trò đối tác giao tiếp.

Nếu là **non-transparent proxy**, chúng ta phải được thông báo về sự tồn tại của nó. Vì mục đích đó, chúng ta và phần mềm muốn sử dụng sẽ được cung cấp một cấu hình proxy chuyên biệt, đảm bảo rằng lưu lượng ra Internet trước tiên được gửi tới proxy. Nếu cấu hình này không tồn tại, chúng ta không thể giao tiếp thông qua proxy. Tuy nhiên, do proxy thường là con đường liên lạc duy nhất tới các mạng khác, nên nếu không có cấu hình proxy tương ứng, việc liên lạc ra Internet thường sẽ bị cắt đứt.



## III. **Networking Workflow**

<figure><img src="../../.gitbook/assets/image (316).png" alt=""><figcaption></figcaption></figure>

\
Mô hình Mạng (Networking Models)

Có hai mô hình mạng mô tả việc truyền thông và trao đổi dữ liệu từ một máy chủ (host) này sang một máy chủ khác, đó là **mô hình ISO/OSI** và **mô hình TCP/IP**. Đây là sự biểu diễn đơn giản hóa của các “tầng” (layers), mô tả cách các bit được truyền đi và được chuyển đổi thành nội dung có thể đọc hiểu.

So sánh mô hình OSI và TCP/IP:

* **OSI** có 7 tầng: Application, Presentation, Session, Transport, Network, Data-Link, và Physical.
* **TCP/IP** có 4 tầng: Application, Transport, Internet, và Link.

***

### Mô hình OSI

Mô hình **OSI** (thường gọi là ISO/OSI layer model) là một mô hình tham chiếu (reference model) dùng để mô tả và định nghĩa giao tiếp giữa các hệ thống. Mô hình tham chiếu này gồm bảy tầng riêng biệt, mỗi tầng đảm nhiệm một nhiệm vụ rõ ràng.

Thuật ngữ **OSI** là viết tắt của **Open Systems Interconnection model**, được công bố bởi **International Telecommunication Union (ITU)** và **International Organization for Standardization (ISO)**. Vì vậy, mô hình OSI thường được gọi là **mô hình phân lớp ISO/OSI**.

***

### Mô hình TCP/IP

**TCP/IP** (Transmission Control Protocol/Internet Protocol) là một thuật ngữ chung để chỉ nhiều giao thức mạng. Các giao thức này chịu trách nhiệm chuyển mạch (switching) và vận chuyển (transport) gói dữ liệu trên Internet. Toàn bộ Internet dựa trên họ giao thức TCP/IP. Tuy nhiên, TCP/IP không chỉ bao gồm hai giao thức TCP và IP, mà thường được dùng như một thuật ngữ chung cho cả một họ giao thức.

Ví dụ: **ICMP (Internet Control Message Protocol)** hoặc **UDP (User Datagram Protocol)** cũng thuộc họ giao thức này. Họ giao thức TCP/IP cung cấp các chức năng cần thiết để vận chuyển và chuyển mạch gói dữ liệu trong mạng riêng (private) hoặc mạng công cộng (public).

***

### ISO/OSI so với TCP/IP

* **TCP/IP** là một giao thức truyền thông cho phép các máy chủ kết nối Internet. Nó đề cập đến **Transmission Control Protocol** được dùng trong và bởi các ứng dụng trên Internet. So với OSI, TCP/IP cho phép các quy tắc bớt nghiêm ngặt hơn, miễn là vẫn tuân theo những hướng dẫn chung.
* **OSI**, ngược lại, là một cổng giao tiếp giữa mạng và người dùng cuối. OSI thường được gọi là mô hình tham chiếu vì nó mới hơn và được sử dụng rộng rãi hơn. Nó cũng được biết đến với các quy tắc nghiêm ngặt và nhiều giới hạn.

***

### Truyền gói tin (Packet Transfers)

<figure><img src="../../.gitbook/assets/image (317).png" alt=""><figcaption></figcaption></figure>

Trong một hệ thống phân tầng, các thiết bị trong một tầng trao đổi dữ liệu ở một định dạng khác gọi là **protocol data unit (PDU)**. Ví dụ: khi ta muốn truy cập một trang web từ máy tính, phần mềm trên máy chủ từ xa trước hết chuyển dữ liệu được yêu cầu đến **Application Layer**. Dữ liệu được xử lý qua từng tầng, mỗi tầng thực hiện chức năng của mình. Sau đó dữ liệu được truyền qua **Physical Layer** của mạng cho đến khi tới máy chủ đích hoặc một thiết bị khác. Dữ liệu lại đi qua các tầng ở phía nhận, mỗi tầng xử lý phần của nó cho đến khi phần mềm ứng dụng cuối cùng có thể sử dụng dữ liệu.

So sánh OSI và TCP/IP kèm theo PDU:

* **OSI** có 7 tầng: Application, Presentation, Session, Transport, Network, Data-Link, Physical.
* **TCP/IP** có 4 tầng: Application, Transport, Internet, Link.
* **PDU** bao gồm: Data, Segment/Datagram, Packet, Frame, và Bit.



Trong quá trình truyền, mỗi tầng thêm một **header** vào PDU từ tầng trên, để kiểm soát và định danh gói tin. Quá trình này gọi là **encapsulation** (đóng gói). Header và dữ liệu kết hợp lại thành PDU cho tầng kế tiếp. Quá trình tiếp tục cho tới Physical Layer hoặc Network Layer, nơi dữ liệu được truyền đến phía nhận. Ở phía nhận, quá trình được đảo ngược, mỗi tầng gỡ bỏ header tương ứng cho đến khi dữ liệu tới được ứng dụng. Quá trình này lặp lại cho đến khi toàn bộ dữ liệu được gửi và nhận thành công.

Sơ đồ truyền gói tin minh họa encapsulation: dữ liệu đi từ Sender sang Receiver qua các tầng: Data, TCP, IP, MAC, và Binary Transmission, kèm theo header và thứ tự.

<figure><img src="../../.gitbook/assets/image (319).png" alt=""><figcaption></figcaption></figure>

***

### Ứng dụng trong Pentesting

Đối với chúng ta – những người làm **penetration testing** – cả hai mô hình tham chiếu đều hữu ích. Với TCP/IP, ta có thể nhanh chóng hiểu cách toàn bộ kết nối được thiết lập. Với OSI, ta có thể phân tích từng phần chi tiết. Điều này đặc biệt quan trọng khi ta nghe lén (sniff) và chặn một loại lưu lượng mạng cụ thể. Lúc đó, ta cần phân tích lưu lượng này theo tầng, chi tiết hơn trong module **Network Traffic Analysis**. Do đó, chúng ta cần làm quen với cả hai mô hình tham chiếu, hiểu và nắm vững chúng ở mức tốt nhất.





## Mô hình OSI (The OSI Model)

Mục tiêu khi định nghĩa tiêu chuẩn **ISO/OSI** là tạo ra một mô hình tham chiếu cho phép các hệ thống kỹ thuật khác nhau có thể giao tiếp qua nhiều thiết bị và công nghệ, đồng thời đảm bảo tính tương thích.

Mô hình OSI sử dụng **7** layers khác nhau, được sắp xếp theo cấu trúc phân cấp, để đạt được mục tiêu này. Các tầng này đại diện cho các giai đoạn trong việc thiết lập mỗi kết nối mà các gói tin truyền đi phải đi qua. Theo cách đó, tiêu chuẩn này được tạo ra để ta có thể trực quan theo dõi cách một kết nối được cấu trúc và hình thành.

***

### Các tầng trong mô hình OSI

| Tầng                | Chức năng                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **7. Application**  | Trong số nhiều nhiệm vụ, tầng này kiểm soát việc nhập và xuất dữ liệu, đồng thời cung cấp các chức năng ứng dụng cho người dùng.                                                                                         |
| **6. Presentation** | Chịu trách nhiệm chuyển đổi cách biểu diễn dữ liệu phụ thuộc vào hệ thống sang một dạng độc lập với ứng dụng, đảm bảo dữ liệu có thể hiểu được trên nhiều nền tảng khác nhau.                                            |
| **5. Session**      | Kiểm soát kết nối logic giữa hai hệ thống, ngăn ngừa sự cố như mất kết nối hoặc gián đoạn.                                                                                                                               |
| **4. Transport**    | Cung cấp khả năng kiểm soát đầu cuối đối với dữ liệu được truyền. Tầng này có thể phát hiện và tránh tình trạng tắc nghẽn, cũng như chia nhỏ (segment) các luồng dữ liệu.                                                |
| **3. Network**      | Thiết lập kết nối trong mạng chuyển mạch kênh (circuit-switched networks) và định tuyến gói tin trong mạng chuyển mạch gói (packet-switched networks). Dữ liệu được truyền qua toàn bộ mạng từ người gửi đến người nhận. |
| **2. Data Link**    | Nhiệm vụ chính là đảm bảo truyền dữ liệu đáng tin cậy và không lỗi trên phương tiện truyền dẫn tương ứng. Các chuỗi bit (bitstreams) từ tầng 1 sẽ được chia thành các khối hoặc frame.                                   |
| **1. Physical**     | Xác định kỹ thuật truyền dẫn được sử dụng, ví dụ: tín hiệu điện, tín hiệu quang, hoặc sóng điện từ. Ở tầng này, việc truyền dữ liệu diễn ra trên đường truyền có dây hoặc không dây.                                     |

***

### Phân loại các tầng

* Các tầng **2–4** (Data Link, Network, Transport) là các tầng **hướng vận chuyển** (transport-oriented).
* Các tầng **5–7** (Session, Presentation, Application) là các tầng **hướng ứng dụng** (application-oriented).

Trong mỗi tầng, các nhiệm vụ được định nghĩa chính xác, và giao diện với tầng liền kề cũng được mô tả rõ ràng. Mỗi tầng cung cấp dịch vụ cho tầng ở phía trên, đồng thời sử dụng dịch vụ của tầng phía dưới để hoàn thành nhiệm vụ của nó.

***

### Quá trình truyền dữ liệu qua các tầng

Khi hai hệ thống giao tiếp, cả bảy tầng của mô hình OSI đều phải được thực hiện **ít nhất hai lần**:

* Một lần ở phía người gửi.
* Một lần ở phía người nhận.

Điều này có nghĩa là một số lượng lớn các tác vụ khác nhau phải được thực hiện trong từng tầng riêng lẻ để đảm bảo **an toàn, độ tin cậy, và hiệu năng** của quá trình truyền thông.

* Khi một ứng dụng gửi gói dữ liệu đến hệ thống khác, dữ liệu sẽ đi từ **tầng 7 xuống tầng 1**.
* Ở phía nhận, gói dữ liệu sẽ được “mở” từ **tầng 1 lên tầng 7** cho đến khi ứng dụng cuối cùng có thể sử dụng.





## Mô hình TCP/IP (The TCP/IP Model)

Mô hình TCP/IP cũng là một mô hình tham chiếu phân tầng, thường được gọi là **Internet Protocol Suite**.

Thuật ngữ **TCP/IP** xuất phát từ hai giao thức:

* **Transmission Control Protocol (TCP)**
* **Internet Protocol (IP)**

Trong mô hình OSI, **IP** nằm ở **tầng Network (Layer 3)**, còn **TCP** nằm ở **tầng Transport (Layer 4)**.

***

### Các tầng trong mô hình TCP/IP

| Tầng               | Chức năng                                                                                                                                                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **4. Application** | Cho phép các ứng dụng truy cập dịch vụ của các tầng bên dưới và định nghĩa các giao thức ứng dụng sử dụng để trao đổi dữ liệu.                                                                                             |
| **3. Transport**   | Chịu trách nhiệm cung cấp dịch vụ phiên (session) dựa trên TCP và dịch vụ datagram dựa trên UDP cho tầng ứng dụng.                                                                                                         |
| **2. Internet**    | Đảm nhiệm việc định địa chỉ host, đóng gói (packaging), và chức năng định tuyến (routing).                                                                                                                                 |
| **1. Link**        | Đảm nhiệm việc đưa gói TCP/IP xuống môi trường mạng và nhận lại gói tương ứng từ môi trường mạng. TCP/IP được thiết kế để hoạt động độc lập với phương pháp truy cập mạng, định dạng frame, và loại môi trường truyền dẫn. |

***

### Đặc điểm chính

Với TCP/IP, mọi ứng dụng có thể truyền và trao đổi dữ liệu qua bất kỳ mạng nào, không quan trọng máy nhận ở đâu.

* **IP** đảm bảo rằng gói dữ liệu đến được đích.
* **TCP** kiểm soát việc truyền dữ liệu và đảm bảo kết nối giữa luồng dữ liệu và ứng dụng.

Điểm khác biệt chính giữa TCP/IP và OSI là số lượng tầng: trong TCP/IP một số tầng đã được gộp lại thay vì tách riêng như trong OSI.

So sánh nhanh:

* **OSI** có 7 tầng: Application, Presentation, Session, Transport, Network, Data-Link, Physical.
* **TCP/IP** có 4 tầng: Application, Transport, Internet, Link.

<figure><img src="../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>

***

### Các nhiệm vụ quan trọng của TCP/IP

| Nhiệm vụ                                        | Giao thức | Mô tả                                                                                                                                                                                                                                                 |
| ----------------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Logical Addressing** (Địa chỉ logic)          | **IP**    | Do có nhiều host trong các mạng khác nhau, cần cấu trúc topology mạng và địa chỉ logic. Trong TCP/IP, IP chịu trách nhiệm địa chỉ logic của mạng và các node. Gói dữ liệu chỉ đến đúng mạng đích. Các phương pháp: network classes, subnetting, CIDR. |
| **Routing** (Định tuyến)                        | **IP**    | Với mỗi gói dữ liệu, tại mỗi node trên đường đi từ người gửi đến người nhận, node kế tiếp sẽ được xác định. Nhờ đó, gói tin được định tuyến đến đích, ngay cả khi vị trí đích không rõ ràng với người gửi.                                            |
| **Error & Control Flow** (Kiểm soát và báo lỗi) | **TCP**   | Người gửi và người nhận duy trì kết nối ảo, thường xuyên trao đổi thông điệp điều khiển để kiểm tra xem kết nối còn tồn tại hay không.                                                                                                                |
| **Application Support** (Hỗ trợ ứng dụng)       | **TCP**   | Các port TCP và UDP tạo thành một lớp trừu tượng phần mềm, giúp phân biệt ứng dụng cụ thể và kênh liên lạc của chúng.                                                                                                                                 |
| **Name Resolution** (Phân giải tên miền)        | **DNS**   | DNS cung cấp khả năng phân giải tên miền đầy đủ (FQDN) sang địa chỉ IP, cho phép ta truy cập host mong muốn bằng tên định danh trên Internet.                                                                                                         |



## IV. Addressing

## Tầng Mạng (Network Layer)

**Tầng mạng (Layer 3)** trong mô hình OSI kiểm soát việc trao đổi các gói dữ liệu, vì các gói này không thể được định tuyến trực tiếp đến máy nhận mà cần phải thông qua các **nút định tuyến (routing nodes)**. Các gói dữ liệu được chuyển từ nút này sang nút khác cho đến khi tới được đích.

Để thực hiện điều này, tầng mạng sẽ:

* Xác định từng nút mạng riêng lẻ,
* Thiết lập và giải phóng các kênh kết nối,
* Quản lý định tuyến và điều khiển luồng dữ liệu (routing and data flow control).

Khi gửi gói tin, địa chỉ sẽ được phân tích, và dữ liệu được định tuyến qua mạng từ nút này sang nút khác. Thông thường, trong các nút mạng, **không có xử lý dữ liệu của các tầng trên L3**. Dựa trên địa chỉ, định tuyến và việc xây dựng bảng định tuyến sẽ được thực hiện.

***

### Tóm gọn lại, tầng mạng chịu trách nhiệm về:

* Địa chỉ logic (Logical Addressing)
* Định tuyến (Routing)

***

### Giao thức trong tầng mạng

Ở mỗi tầng trong mô hình OSI, các giao thức được định nghĩa để làm tập hợp các quy tắc giao tiếp trong tầng đó. Các giao thức này **minh bạch với các giao thức ở tầng trên hoặc dưới**. Một số giao thức có thể đảm nhận chức năng ở nhiều tầng, kéo dài qua hai hoặc nhiều tầng.

Các giao thức được sử dụng phổ biến nhất ở tầng mạng:

* **IPv4 / IPv6**
* **IPsec**
* **ICMP**
* **IGMP**
* **RIP**
* **OSPF**

***

### Chức năng định tuyến

Tầng mạng đảm bảo định tuyến gói tin từ nguồn tới đích, cả **bên trong hoặc bên ngoài một subnet**. Hai subnet này có thể:

* Sử dụng các sơ đồ địa chỉ khác nhau, hoặc
* Có kiểu địa chỉ không tương thích.

Trong cả hai trường hợp, việc truyền dữ liệu đều phải đi qua toàn bộ mạng truyền thông và bao gồm định tuyến giữa các nút mạng.

Vì việc truyền trực tiếp từ máy gửi đến máy nhận không phải lúc nào cũng khả thi (do khác subnet), các gói tin phải được **chuyển tiếp bởi các nút trung gian (router)** trên đường đi.

Các gói tin được chuyển tiếp này sẽ **không đi lên các tầng cao hơn**, mà được gán một **đích trung gian mới** rồi gửi tới nút kế tiếp.



## Địa chỉ IP (IP Addresses)

Mỗi host trong mạng có thể được nhận diện bằng **địa chỉ Media Access Control (MAC)**. Điều này cho phép trao đổi dữ liệu trong một mạng duy nhất. Tuy nhiên, nếu host ở xa nằm trong một mạng khác, chỉ biết địa chỉ MAC là chưa đủ để thiết lập kết nối.

Việc định địa chỉ trên Internet được thực hiện thông qua **địa chỉ IPv4 và/hoặc IPv6**, bao gồm **địa chỉ mạng (network address)** và **địa chỉ host (host address)**.

Dù là một mạng nhỏ (ví dụ: mạng máy tính gia đình) hay toàn bộ Internet, địa chỉ IP luôn đảm bảo việc chuyển dữ liệu tới đúng máy nhận.

Ta có thể hình dung như sau:

* **IPv4 / IPv6**: giống như địa chỉ bưu chính và khu vực của tòa nhà người nhận.
* **MAC**: giống như số tầng và căn hộ chính xác của người nhận.

Một địa chỉ IP có thể nhắm đến nhiều người nhận (broadcast), hoặc một thiết bị có thể phản hồi cho nhiều địa chỉ IP. Tuy nhiên, mỗi địa chỉ IP phải luôn là **duy nhất trong một mạng**.

***

### Cấu trúc IPv4

Phương pháp phổ biến nhất để gán địa chỉ IP là **IPv4**.

* IPv4 gồm một số nhị phân 32 bit.
* Chia thành 4 byte (mỗi byte 8 bit).
* Các nhóm 8 bit (octet) có giá trị từ 0–255.
* Biểu diễn dưới dạng thập phân, phân tách bằng dấu chấm (**dotted-decimal notation**).

Ví dụ:

| Notation | Biểu diễn                               |
| -------- | --------------------------------------- |
| Binary   | 0111 1111.0000 0000.0000 0000.0000 0001 |
| Decimal  | 127.0.0.1                               |

Mỗi giao diện mạng (card mạng, máy in mạng, hoặc router) được gán một địa chỉ IP duy nhất.

Định dạng IPv4 cho phép **4.294.967.296 địa chỉ duy nhất**. Địa chỉ IP được chia thành **phần host** và **phần mạng**.

* Phần host thường được gán bởi router tại gia hoặc quản trị viên.
* Phần mạng được gán bởi quản trị mạng, còn trên Internet là do **IANA** (Internet Assigned Numbers Authority) quản lý.

Trong quá khứ, không gian địa chỉ IPv4 được phân chia thành các lớp A–E.

***

### Các lớp địa chỉ IPv4

| Lớp | Network Address | First Address | Last Address    | Subnetmask    | CIDR      | Subnets   | IPs            |
| --- | --------------- | ------------- | --------------- | ------------- | --------- | --------- | -------------- |
| A   | 1.0.0.0         | 1.0.0.1       | 127.255.255.255 | 255.0.0.0     | /8        | 127       | 16,777,214 + 2 |
| B   | 128.0.0.0       | 128.0.0.1     | 191.255.255.255 | 255.255.0.0   | /16       | 16,384    | 65,534 + 2     |
| C   | 192.0.0.0       | 192.0.0.1     | 223.255.255.255 | 255.255.255.0 | /24       | 2,097,152 | 254 + 2        |
| D   | 224.0.0.0       | 224.0.0.1     | 239.255.255.255 | Multicast     | Multicast | Multicast | Multicast      |
| E   | 240.0.0.0       | 240.0.0.1     | 255.255.255.255 | reserved      | reserved  | reserved  | reserved       |

***

### Subnet Mask

Việc phân tách các lớp thành các mạng nhỏ hơn (**subnetting**) được thực hiện bằng **netmask**. Netmask dài bằng địa chỉ IPv4, chỉ định vị trí bit nào là phần **mạng** và bit nào là phần **host**.

Ví dụ:

* **Subnetmask lớp C**: 255.255.255.0 (hay /24) → chỉ có 8 bit cuối dành cho host.

***

### Địa chỉ mạng và Gateway

Trong mỗi subnet, có **hai địa chỉ đặc biệt** không thể dùng cho host:

* **Địa chỉ mạng (Network Address)**: xác định toàn mạng con.
* **Địa chỉ broadcast**: để gửi dữ liệu đến tất cả host trong subnet.

Ngoài ra, còn có **Default Gateway**, thường là địa chỉ IP của router. Gateway kết nối mạng nội bộ với mạng bên ngoài, quản lý địa chỉ và phương thức truyền. Thông thường, gateway mặc định được gán bằng **địa chỉ đầu tiên hoặc cuối cùng khả dụng trong subnet** (không bắt buộc về mặt kỹ thuật, nhưng đã thành chuẩn thực tế).

***

### Broadcast Address

Địa chỉ broadcast dùng để kết nối tất cả thiết bị trong một mạng.

* Khi broadcast, một thông điệp được gửi tới mọi thành viên trong mạng mà không yêu cầu phản hồi.
* Một host có thể gửi gói tin tới tất cả host khác, đồng thời thông báo địa chỉ IP của nó để các host có thể liên hệ lại.
* Địa chỉ broadcast thường là **địa chỉ IPv4 cuối cùng trong subnet**.

***

### Hệ nhị phân (Binary System)

Hệ nhị phân chỉ sử dụng **2 trạng thái**: 0 và 1 (so với hệ thập phân dùng 0–9).

Địa chỉ IPv4 chia thành 4 **octet**, mỗi octet gồm 8 bit. Mỗi vị trí bit trong một octet có giá trị thập phân tương ứng.

Ví dụ địa chỉ IPv4: **192.168.10.39**

1st Octet (192):

| Giá trị bit | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| ----------- | --- | -- | -- | -- | - | - | - | - |
| Nhị phân    | 1   | 1  | 0  | 0  | 0 | 0 | 0 | 0 |

Cộng lại: 128 + 64 = 192.

Kết quả toàn bộ:

| Octet | Giá trị nhị phân | Giá trị thập phân |
| ----- | ---------------- | ----------------- |
| 1st   | 1100 0000        | 192               |
| 2nd   | 1010 1000        | 168               |
| 3rd   | 0000 1010        | 10                |
| 4th   | 0010 0111        | 39                |

**IPv4 Address: 192.168.10.39**

***

### Subnet Mask tính toán

Ví dụ subnet mask **255.255.255.0**:

| Octet | Giá trị nhị phân | Giá trị thập phân |
| ----- | ---------------- | ----------------- |
| 1st   | 1111 1111        | 255               |
| 2nd   | 1111 1111        | 255               |
| 3rd   | 1111 1111        | 255               |
| 4th   | 0000 0000        | 0                 |

Subnet Mask: **255.255.255.0**

***

### CIDR (Classless Inter-Domain Routing)

CIDR thay thế việc gán cố định giữa địa chỉ IPv4 và lớp mạng (A, B, C, D, E).

* CIDR sử dụng **subnet mask** hoặc hậu tố CIDR (CIDR suffix).
* Hậu tố CIDR chỉ số lượng bit **1** trong subnet mask (tính từ trái qua phải).

Ví dụ:

* IPv4 Address: **192.168.10.39**
* Subnet Mask: **255.255.255.0**
* CIDR Notation: **192.168.10.39/24**

Ký hiệu **/24** nghĩa là có 24 bit đầu tiên trong subnet mask được đặt thành 1.







