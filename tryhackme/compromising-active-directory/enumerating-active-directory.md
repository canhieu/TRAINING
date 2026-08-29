# Enumerating Active Directory

<figure><img src="../../.gitbook/assets/image (893).png" alt=""><figcaption></figcaption></figure>



Why AD Enumeration\



### AD Enumeration

Khi đã có được bộ **credential AD đầu tiên** và có cách để xác thực với chúng trên mạng, một loạt khả năng mới sẽ mở ra. Chúng ta có thể bắt đầu **enumeration** nhiều thông tin khác nhau về cấu trúc và cách triển khai Active Directory bằng quyền truy cập đã xác thực, ngay cả khi đó chỉ là một tài khoản có đặc quyền rất thấp.

Trong một cuộc **red team engagement**, điều này thường dẫn đến việc tìm ra một hình thức **privilege escalation** hoặc **lateral movement** nào đó để có thêm quyền truy cập. Quá trình này tiếp tục lặp lại cho đến khi đạt được đủ đặc quyền để hoàn thành mục tiêu đề ra.

Trong hầu hết các trường hợp, **enumeration** và **exploitation** gắn liền với nhau:

1. Thực hiện enumeration để tìm các điểm yếu và đường tấn công.
2. Khai thác (exploit) một đường tấn công đã phát hiện.
3. Có được quyền truy cập hoặc đặc quyền cao hơn.
4. Tiếp tục enumeration từ vị trí mới này.
5. Lặp lại quá trình cho đến khi đạt được mục tiêu.

Nói cách khác, thay vì:

```
Enumeration -> Exploitation -> Kết thúc
```

thì thực tế thường là:

```
Enumeration
      ↓
Exploitation
      ↓
Quyền truy cập cao hơn
      ↓
Enumeration
      ↓
Exploitation
      ↓
Quyền truy cập cao hơn nữa
      ↓
...

```

Đây là lý do tại sao ngay cả một tài khoản AD có quyền rất thấp cũng có thể rất giá trị. Nó cung cấp khả năng truy cập đã xác thực, cho phép thu thập thêm thông tin về domain, từ đó tìm ra các cơ hội leo thang đặc quyền hoặc di chuyển ngang trong hệ thống.

<figure><img src="../../.gitbook/assets/image (894).png" alt=""><figcaption></figcaption></figure>

lấy cred

<figure><img src="../../.gitbook/assets/image (895).png" alt=""><figcaption></figcaption></figure>

ssh vào user&#x20;

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ ssh za.tryhackme.com\\stacey.baker@thmjmp1.za.tryhackme.com
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
za.tryhackme.com\stacey.baker@thmjmp1.za.tryhackme.com's password: 


Microsoft Windows [Version 10.0.17763.1098]
(c) 2018 Microsoft Corporation. All rights reserved.

za\stacey.baker@THMJMP1 C:\Users\stacey.baker>


```



Credential Injection\



Trước khi đi sâu vào các **đối tượng Active Directory (AD Objects)** và kỹ thuật **enumeration**, trước tiên hãy nói về các phương pháp **credential injection**.

Qua mạng lab **Breaching AD**, bạn đã thấy rằng credential thường có thể được thu thập mà không cần phải xâm nhập một máy đã tham gia domain (domain-joined machine). Tuy nhiên, một số kỹ thuật enumeration cụ thể yêu cầu những điều kiện hoặc cách thiết lập nhất định để hoạt động hiệu quả.



### Windows vs Linux&#xD; &#xD;

> _"If you know the enemy and know yourself, you need not fear the results of a hundred battles. If you know yourself but not the enemy, for every victory gained you will also suffer defeat."_&#x20;
>
>
>
> \- Sun Tzu, Art of War.

Bạn có thể thực hiện rất nhiều hoạt động **enumeration Active Directory** chỉ từ một máy **Kali**. Tuy nhiên, nếu muốn thực hiện enumeration chuyên sâu hoặc thậm chí là khai thác (exploitation), bạn cần hiểu và mô phỏng cách hoạt động của đối tượng mục tiêu. Nói cách khác, bạn cần một máy **Windows**.

Việc này cho phép chúng ta sử dụng nhiều công cụ và cơ chế có sẵn trong Windows để thực hiện enumeration và khai thác. Trong mạng lab này, chúng ta sẽ tìm hiểu một trong những công cụ tích hợp đó, đó là chương trình **`runas.exe`**.

### Runas Explained

Đã bao giờ bạn tìm được **credential AD** nhưng lại không có nơi nào để đăng nhập bằng chúng chưa? **Runas** có thể là câu trả lời bạn đang tìm kiếm!

Trong các bài đánh giá bảo mật (security assessment), bạn thường có quyền truy cập mạng và vừa phát hiện được credential AD, nhưng không có khả năng hoặc quyền hạn để tạo một máy mới tham gia domain. Vì vậy, chúng ta cần một cách để sử dụng những credential đó trên một máy Windows mà mình kiểm soát.

Nếu có credential AD ở dạng:

```
<username>:<password>
example : stacey.baker:Password1
```

chúng ta có thể sử dụng **Runas**, một chương trình hợp lệ được tích hợp sẵn trong Windows, để nạp (inject) credential vào bộ nhớ.

Lệnh Runas thường có dạng:

```
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

ứng dụng cho bài lab :&#x20;

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>runas.exe /netonly /user:za.tryhackme.com\stacey.baker cmd.exe
Enter the password for za.tryhackme.com\stacey.baker:
Attempting to start cmd.exe as user "za.tryhackme.com\stacey.baker" ...

za\stacey.baker@THMJMP1 C:\Users\stacey.baker>
```



#### Giải thích các tham số

**`/netonly`**

Vì máy hiện tại không tham gia domain, chúng ta chỉ muốn nạp credential để sử dụng cho **xác thực qua mạng**, chứ không đăng nhập trực tiếp với Domain Controller.

Điều này có nghĩa:

* Các lệnh chạy cục bộ vẫn sử dụng tài khoản Windows hiện tại.
* Mọi kết nối mạng sẽ sử dụng credential đã chỉ định.

**`/user`**

Chỉ định domain và username.

Nên sử dụng **FQDN (Fully Qualified Domain Name)** thay vì NetBIOS name để tránh các vấn đề phân giải tên.

Ví dụ:

```
/user:za.tryhackme.com\svcadmin
```

thay vì:

```
/user:ZA\svcadmin
```

**`cmd.exe`**

Đây là chương trình sẽ được chạy sau khi credential được nạp.

Có thể thay bằng chương trình khác, nhưng `cmd.exe` thường là lựa chọn an toàn nhất vì từ đó bạn có thể khởi chạy bất kỳ công cụ nào khác với credential đã được inject.

Sau khi chạy lệnh, Windows sẽ yêu cầu nhập mật khẩu.

Lưu ý rằng vì sử dụng tham số **`/netonly`**, Windows **không xác thực mật khẩu ngay với Domain Controller**, nên về mặt kỹ thuật nó sẽ chấp nhận bất kỳ mật khẩu nào.

Tuy nhiên, credential chỉ thực sự được xác minh khi bạn thực hiện một kết nối mạng (ví dụ SMB, LDAP, WinRM, ...). Vì vậy, sau đó cần kiểm tra xem credential đã được nạp đúng hay chưa.

#### Lưu ý khi dùng máy Windows cá nhân

Nếu sử dụng máy Windows của riêng bạn, hãy mở **Command Prompt bằng quyền Administrator** trước.

Điều này sẽ thêm **Administrator Token** vào phiên CMD hiện tại. Khi bạn khởi chạy Runas từ cửa sổ đó:

* Các lệnh cục bộ vẫn có quyền Administrator trên máy của bạn.
* Các kết nối mạng sẽ sử dụng credential AD đã inject.

Điều này **không cấp quyền Administrator trên domain hay trên mạng**, nhưng sẽ giúp các công cụ yêu cầu quyền Admin cục bộ hoạt động bình thường.



### It's Always DNS

> **Lưu ý:** Các bước tiếp theo chỉ cần thực hiện nếu bạn sử dụng máy Windows của riêng mình cho bài thực hành. Tuy nhiên, đây là kiến thức hữu ích nên học vì có thể giúp ích trong các bài tập red team thực tế.

Sau khi nhập mật khẩu, một cửa sổ Command Prompt mới sẽ được mở. Bây giờ chúng ta vẫn cần xác minh rằng credential của mình hoạt động. Cách chắc chắn nhất để làm điều này là liệt kê thư mục **SYSVOL**. Bất kỳ tài khoản AD nào, dù có đặc quyền thấp đến đâu, cũng có thể đọc nội dung của thư mục SYSVOL.

**SYSVOL** là một thư mục tồn tại trên tất cả các Domain Controller. Đây là thư mục chia sẻ dùng để lưu trữ các **Group Policy Object (GPO)**, thông tin liên quan và các script của domain. Nó là một thành phần thiết yếu của Active Directory vì chịu trách nhiệm phân phối các GPO này đến tất cả các máy tính trong domain.

Các máy đã tham gia domain có thể đọc các GPO này và áp dụng những chính sách phù hợp, cho phép thực hiện các thay đổi cấu hình trên toàn domain từ một vị trí tập trung.

Trước khi có thể liệt kê SYSVOL, chúng ta cần cấu hình DNS. Đôi khi bạn sẽ may mắn vì DNS nội bộ được cấu hình tự động thông qua DHCP hoặc kết nối VPN, nhưng không phải lúc nào cũng vậy (như trong mạng TryHackMe này). Vì thế, việc biết cách cấu hình thủ công là rất quan trọng.

Lựa chọn an toàn nhất cho DNS server thường là **Domain Controller**. Sử dụng địa chỉ IP của Domain Controller, chúng ta có thể chạy các lệnh sau trong cửa sổ PowerShell:

```powershell
$dnsip = "<DC IP>"
$index = Get-NetAdapter -Name 'Ethernet' | Select-Object -ExpandProperty 'ifIndex'
Set-DnsClientServerAddress -InterfaceIndex $index -ServerAddresses $dnsip
```

Tất nhiên, `'Ethernet'` sẽ là tên của interface đang kết nối với mạng TryHackMe.

Chúng ta có thể kiểm tra DNS hoạt động hay chưa bằng lệnh:

```
C:\> nslookup za.tryhackme.com
```

Lệnh này sẽ trả về địa chỉ IP của Domain Controller, vì đây là nơi đang host FQDN đó.

Khi DNS đã hoạt động, cuối cùng chúng ta có thể kiểm tra credential của mình. Có thể sử dụng lệnh sau để buộc thực hiện việc liệt kê thư mục SYSVOL thông qua kết nối mạng:

```
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>dir \\za.tryhackme.com\SYSVOL 
 Volume in drive \\za.tryhackme.com\SYSVOL is Windows 
 Volume Serial Number is 1634-22A9                                                 
                                                                                   
 Directory of \\za.tryhackme.com\SYSVOL                                            
                                                                                   
02/24/2022  10:57 PM    <DIR>          .                                           
02/24/2022  10:57 PM    <DIR>          ..                                          
02/24/2022  10:57 PM    <JUNCTION>     za.tryhackme.com [C:\Windows\SYSVOL\domain] 
               0 File(s)              0 bytes                                      
               3 Dir(s)  51,519,635,456 bytes free                                 
                                                                                   
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>       
```

Chúng ta sẽ chưa đi quá sâu vào nội dung của SYSVOL ở thời điểm này, nhưng hãy lưu ý rằng đây cũng là một nơi rất đáng để enumeration, vì có thể tồn tại thêm các credential AD được lưu trữ bên trong.



### IP vs Hostnames&#xD; &#xD;

**Câu hỏi:** Có sự khác biệt nào giữa `dir \\za.tryhackme.com\SYSVOL` và `dir \\<DC IP>\SYSVOL` không? Và tại sao DNS lại quan trọng đến vậy?

<figure><img src="../../.gitbook/assets/image (896).png" alt=""><figcaption></figcaption></figure>

Có sự khác biệt khá lớn, và điều này liên quan đến **phương thức xác thực** được sử dụng.

Khi chúng ta cung cấp **hostname**, quá trình xác thực mạng sẽ ưu tiên sử dụng **Kerberos**. Vì Kerberos sử dụng hostname được nhúng trong ticket, nên nếu chúng ta cung cấp **địa chỉ IP** thay vì hostname, chúng ta có thể ép hệ thống sử dụng **NTLM** để xác thực.

Thoạt nhìn điều này có vẻ không quan trọng ở thời điểm hiện tại, nhưng việc hiểu những khác biệt nhỏ này rất hữu ích vì chúng có thể giúp bạn hoạt động kín đáo hơn trong một cuộc đánh giá Red Team.

Trong một số trường hợp, tổ chức có thể đang giám sát các kỹ thuật như:

* OverPass-the-Hash
* Pass-the-Hash

Khi đó, việc ép hệ thống sử dụng NTLM là một mẹo hữu ích để giảm khả năng bị phát hiện.

### Using Injected Credentials

Bây giờ khi chúng ta đã inject credential AD vào bộ nhớ, phần thú vị mới thực sự bắt đầu.

Với tùy chọn **`/netonly`**, mọi giao tiếp qua mạng sẽ sử dụng credential đã được inject để xác thực. Điều này áp dụng cho tất cả các kết nối mạng được tạo bởi các ứng dụng khởi chạy từ cửa sổ Command Prompt đó.

Đây chính là điểm mạnh của kỹ thuật này.

Bạn đã bao giờ gặp trường hợp một cơ sở dữ liệu **MS SQL** sử dụng **Windows Authentication**, nhưng máy của bạn lại không tham gia domain chưa?

Chỉ cần khởi chạy **MS SQL Management Studio** từ cửa sổ Command Prompt đã được tạo bằng Runas. Mặc dù giao diện vẫn hiển thị username cục bộ của bạn, khi nhấn **Log In**, chương trình sẽ âm thầm sử dụng credential AD đã được inject để xác thực ở phía sau.

Chúng ta thậm chí có thể dùng kỹ thuật này để xác thực với các ứng dụng web sử dụng **NTLM Authentication**.

Chúng ta sẽ sử dụng khả năng này trong phần tiếp theo để thực hiện kỹ thuật **AD Enumeration** đầu tiên.



## Enumeration through Microsoft Management Console

### Microsoft Management Console

lệnh remote desktop :&#x20;

```bash
xfreerdp /v:<IP> /u:<User> /p:<Password>
xfreerdp /v:10.200.71.248 /u:stacey.baker /p:Password1
ping <IP>
```

Trong phần này, chúng ta sẽ tìm hiểu kỹ thuật **enumeration đầu tiên**. Đây cũng là phương pháp duy nhất sử dụng giao diện đồ họa (GUI) cho đến tận bài cuối cùng.

<figure><img src="../../.gitbook/assets/image (897).png" alt=""><figcaption></figcaption></figure>

Chúng ta sẽ sử dụng **Microsoft Management Console (MMC)** cùng với các **AD Snap-In** của **Remote Server Administration Tools (RSAT)**. [https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)

Nếu bạn đang sử dụng máy Windows được cung cấp (**THMJMP1**), các công cụ này đã được cài đặt sẵn. Tuy nhiên, nếu bạn sử dụng máy Windows của riêng mình, hãy thực hiện các bước sau để cài đặt AD Snap-In:

1. Nhấn **Start**
2. Tìm kiếm **"Apps & Features"** và nhấn Enter
3. Chọn **Manage Optional Features**
4. Chọn **Add a feature**
5. Tìm kiếm **"RSAT"**
6. Chọn **"RSAT: Active Directory Domain Services and Lightweight Directory Tools"**
7. Nhấn **Install**

<figure><img src="../../.gitbook/assets/image (898).png" alt=""><figcaption></figcaption></figure>

Bạn có thể khởi chạy **MMC** bằng cách:

1. Nhấn **Start**
2. Tìm **Run**
3. Gõ:

```
mmc
```

và nhấn Enter.

Nếu chỉ chạy MMC theo cách thông thường, nó sẽ không hoạt động vì máy tính của chúng ta **không tham gia domain (domain-joined)**, và tài khoản cục bộ hiện tại không thể được dùng để xác thực với domain.&#x20;



In MMC, we can now attach the AD RSAT Snap-In:

1. Click File -> Add/Remove Snap-in
2. Select and Add all three Active Directory Snap-ins
3. Click through any errors and warnings
4. Right-click on Active Directory Domains and Trusts and select Change Forest
5. Enter _za.tryhackme.com_ as the Root domain and Click OK![](<../../.gitbook/assets/image (901).png>)
6. Right-click on Active Directory Sites and Services and select Change Forest
7. Enter _za.tryhackme.com_ as the Root domain and Click OK
8. Right-click on Active Directory Users and Computers and select Change Domain
9.  Enter _za.tryhackme.com_ as the Domain and Click OK

    <figure><img src="../../.gitbook/assets/image (902).png" alt=""><figcaption></figcaption></figure>
10. Right-click on Active Directory Users and Computers in the left-hand pane<br>
11. Click on View -> Advanced Features

<figure><img src="../../.gitbook/assets/image (899).png" alt=""><figcaption></figcaption></figure>

We can now start enumerating information about the AD structure here.

<figure><img src="../../.gitbook/assets/image (903).png" alt=""><figcaption></figcaption></figure>



### Users and Computers

hãy xem cấu trúc của **Active Directory**.

Trong phần này, chúng ta sẽ tập trung vào **Active Directory Users and Computers**. Hãy mở rộng (expand) snap-in đó, sau đó mở rộng domain **za** để xem cấu trúc **Organisational Unit (OU)** ban đầu:

<figure><img src="../../.gitbook/assets/image (904).png" alt=""><figcaption></figcaption></figure>

Hãy xem thư mục **People**. Tại đây, chúng ta có thể thấy các người dùng được phân chia thành các **OU theo từng phòng ban (department)**. Khi nhấp vào từng OU này, bạn sẽ thấy danh sách các người dùng thuộc phòng ban tương ứng.

<figure><img src="../../.gitbook/assets/image (905).png" alt=""><figcaption></figcaption></figure>

Nhấp vào bất kỳ người dùng nào trong số này sẽ cho phép chúng ta xem tất cả các **properties** và **thuộc tính chi tiết** của họ.Chúng ta cũng có thể xem người dùng đó là thành viên của những **nhóm (groups)** nào:

<figure><img src="../../.gitbook/assets/image (906).png" alt=""><figcaption></figcaption></figure>

Ta cũng có thể sử dụng MMC để tìm hosts trong environment. If we click on either Servers or Workstations, the list of domain-joined machines will be displayed

<figure><img src="../../.gitbook/assets/image (907).png" alt=""><figcaption></figcaption></figure>

Nếu có các quyền phù hợp, chúng ta cũng có thể sử dụng **MMC** để thực hiện trực tiếp các thay đổi trong Active Directory, chẳng hạn như:

* Thay đổi mật khẩu của người dùng.

![](<../../.gitbook/assets/image (908).png>)

* Thêm một tài khoản vào một nhóm cụ thể.

Hãy dành thời gian khám phá MMC để hiểu rõ hơn về cấu trúc của domain Active Directory. Đồng thời, hãy sử dụng tính năng **Search** để tìm kiếm các đối tượng trong AD.

### Ưu điểm

* Giao diện đồ họa (GUI) cung cấp một cách rất trực quan để có cái nhìn tổng thể về môi trường Active Directory.
* Có thể tìm kiếm nhanh nhiều loại đối tượng AD khác nhau.
* Cho phép xem trực tiếp các thuộc tính và thông tin chi tiết của từng đối tượng AD.
* Nếu có đủ quyền, chúng ta có thể chỉnh sửa các đối tượng AD hiện có hoặc tạo mới các đối tượng ngay từ giao diện.

### Nhược điểm

* GUI yêu cầu quyền truy cập **RDP** vào máy đang chạy MMC.
* Mặc dù việc tìm kiếm một đối tượng cụ thể rất nhanh, nhưng không phù hợp để thu thập hàng loạt thuộc tính hoặc thông tin trên toàn bộ Active Directory.

<figure><img src="../../.gitbook/assets/image (910).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (909).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (914).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (913).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (911).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (912).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (915).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (916).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (918).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (917).png" alt=""><figcaption></figcaption></figure>



## Enumeration through Command Prompt

### Command Prompt&#xD; &#xD;

Có những lúc bạn chỉ cần thực hiện một vài truy vấn AD nhanh và đơn giản, và **Command Prompt (CMD)** có thể đáp ứng nhu cầu đó.

CMD là một công cụ đáng tin cậy, đặc biệt hữu ích trong các tình huống như:

* Bạn không có quyền truy cập RDP vào hệ thống.
* Đội phòng thủ đang giám sát việc sử dụng PowerShell.
* Bạn phải thực hiện AD Enumeration thông qua một **Remote Access Trojan (RAT)**.
* Bạn muốn nhúng một vài lệnh AD Enumeration đơn giản vào payload phishing để thu thập thông tin quan trọng phục vụ cho giai đoạn tấn công tiếp theo.

CMD có sẵn một lệnh tích hợp rất hữu ích để thu thập thông tin về Active Directory, đó là lệnh **`net`**.

Lệnh **`net`** là một công cụ tiện lợi dùng để liệt kê thông tin về cả hệ thống cục bộ và môi trường AD. Chúng ta sẽ xem xét một số thông tin thú vị có thể thu thập được từ vị trí hiện tại, tuy nhiên đây không phải là danh sách đầy đủ tất cả khả năng của lệnh này.

> **Lưu ý:** Trong phần thực hành này, bạn bắt buộc phải sử dụng máy **THMJMP1** và sẽ không thể sử dụng máy Windows cá nhân của mình. Lý do cho điều này sẽ được giải thích ở phần nhược điểm.

### Users

We can use the `net` command to list all users in the AD domain by using the `user` sub-option:

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net user  /domain
The request will be processed at a domain controller for domain za.tryhackme.com. 


User accounts for \\THMDC.za.tryhackme.com                                      
                                                                                
------------------------------------------------------------------------------- 
aaron.conway             aaron.hancock            aaron.harris                  
aaron.johnson            aaron.lewis              aaron.moore                   
aaron.patel              aaron.smith              abbie.joyce                   
abbie.robertson          abbie.taylor             abbie.walker                  
abdul.akhtar             abdul.bates              abdul.holt                    
abdul.jones              abdul.wall               abdul.west                    
abdul.wilson             abigail.cox              abigail.cox1                  
victoria.savage          victoria.shaw            victoria.woodward
[....]
vincent.young            wayne.bentley            wayne.harrison
wayne.henderson          wayne.walker             wayne.whitehouse
wendy.carpenter          wendy.evans              wendy.mills
wendy.roberts            wendy.taylor             wendy.whittaker
william.bailey           william.holmes           william.little
william.miah             william.payne            william.williams
yvonne.baker             yvonne.black             yvonne.craig
yvonne.grant             yvonne.johnson           yvonne.smith
zoe.barnes               zoe.ellis                zoe.fleming
zoe.hopkins              zoe.humphreys            zoe.lane
zoe.marshall             zoe.watson
The command completed successfully.
```

Lệnh này sẽ trả về **toàn bộ người dùng trong Active Directory**, rất hữu ích để đánh giá quy mô của domain và chuẩn bị cho các bước tấn công tiếp theo.

Chúng ta cũng có thể sử dụng tùy chọn phụ này để thu thập thông tin chi tiết hơn về một tài khoản người dùng cụ thể:

```bash
net user <username> /domain
```

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net user zoe.marshall /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

User name                    zoe.marshall
Full Name                    Zoe Marshall
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            2/24/2022 11:06:06 PM
Password expires             Never
Password changeable          2/24/2022 11:06:06 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   Never

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users         *Internet Access
The command completed successfully.
```

> **Lưu ý:** Nếu người dùng chỉ là thành viên của một số ít nhóm AD, lệnh này sẽ hiển thị được các nhóm mà họ tham gia. Tuy nhiên, trong thực tế, khi một tài khoản là thành viên của nhiều hơn khoảng mười nhóm, lệnh này thường sẽ không thể liệt kê đầy đủ tất cả các nhóm đó.

### Groups

Ta cũng có thể dùng `net` command để enumerate groups của domain bằng cách sử dụng `group` sub-option :

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net group /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

Group Accounts for \\THMDC.za.tryhackme.com

-------------------------------------------------------------------------------
*Cloneable Domain Controllers
*DnsUpdateProxy
*Domain Admins
*Domain Computers
*Domain Controllers
*Domain Guests
*Domain Users
*Enterprise Admins
*Enterprise Key Admins
*Enterprise Read-only Domain Controllers
*Group Policy Creator Owners
*HR Share RW
*Internet Access
*Key Admins
*Protected Users
*Read-only Domain Controllers
*Schema Admins
*Server Admins
*Tier 0 Admins
*Tier 1 Admins
*Tier 2 Admins
The command completed successfully.
```

Thông tin này có thể giúp chúng ta xác định những nhóm cụ thể đáng quan tâm để phục vụ cho việc đạt được mục tiêu cuối cùng của cuộc tấn công.

Chúng ta cũng có thể thu thập thêm thông tin chi tiết, chẳng hạn như danh sách thành viên của một nhóm, bằng cách chỉ định tên nhóm trong cùng lệnh:

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net group "Tier 1 Admins" /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

Group name     Tier 1 Admins
Comment

Members

-------------------------------------------------------------------------------
t1_arthur.tyler          t1_gary.moss             t1_henry.miller
t1_jill.wallis           t1_joel.stephenson       t1_marian.yates
t1_rosie.bryant
The command completed successfully.
```



### Password Policy&#xD; &#xD;

We can use the `net` command to enumerate the password policy of the domain by using the `accounts` sub-option:

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net accounts /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          Unlimited
Minimum password length:                              0
Length of password history maintained:                None
Lockout threshold:                                    Never
Lockout duration (minutes):                           30
Lockout observation window (minutes):                 30
Computer role:                                        PRIMARY
The command completed successfully.


za\stacey.baker@THMJMP1 C:\Users\stacey.baker>
```

Lệnh này sẽ cung cấp cho chúng ta những thông tin hữu ích như:

* **Số lượng mật khẩu cũ được lưu trong lịch sử mật khẩu (Password History)**: tức là người dùng phải sử dụng bao nhiêu mật khẩu mới khác nhau trước khi được phép dùng lại một mật khẩu cũ.
* **Ngưỡng khóa tài khoản (Lockout Threshold)** khi nhập sai mật khẩu và thời gian tài khoản sẽ bị khóa.
* **Độ dài tối thiểu của mật khẩu (Minimum Password Length)**.
* **Tuổi thọ tối đa của mật khẩu (Maximum Password Age)**, cho biết liệu mật khẩu có phải được thay đổi định kỳ hay không.

Những thông tin này có thể rất hữu ích nếu chúng ta muốn thực hiện thêm các cuộc tấn công **Password Spraying** đối với các tài khoản người dùng đã được liệt kê trước đó.

Cụ thể, chúng giúp chúng ta:

* Đoán tốt hơn những mật khẩu phổ biến có khả năng được sử dụng.
* Xác định số lần thử mật khẩu có thể thực hiện trước khi có nguy cơ làm khóa tài khoản.

Tuy nhiên, cần lưu ý rằng nếu thực hiện một cuộc tấn công Password Spraying một cách "mù" (blind password spraying), chúng ta vẫn có thể làm khóa tài khoản. Lý do là chúng ta không biết trước mỗi tài khoản đã còn bao nhiêu lần nhập sai trước khi chạm ngưỡng khóa.

Bạn có thể tự khám phá thêm các tùy chọn khác của lệnh **`net`**. Hãy thử sử dụng các lệnh này để thu thập thông tin về những người dùng và nhóm khác nhau trong Active Directory.

### Ưu điểm

* Không cần cài đặt thêm bất kỳ công cụ bên ngoài nào.
* Các lệnh đơn giản này thường không bị đội Blue Team giám sát chặt chẽ.
* Không cần giao diện đồ họa (GUI) để thực hiện enumeration.
* VBScript và nhiều ngôn ngữ macro thường được sử dụng trong payload phishing hỗ trợ các lệnh này sẵn, cho phép thu thập thông tin ban đầu về AD trước khi triển khai các payload phức tạp hơn.

### Nhược điểm

* Các lệnh `net` phải được thực thi trên một **máy đã tham gia domain (domain-joined machine)**. Nếu máy không tham gia domain, lệnh sẽ mặc định làm việc với **WORKGROUP** thay vì Active Directory.
* Các lệnh `net` không phải lúc nào cũng hiển thị đầy đủ thông tin. Ví dụ, nếu một người dùng là thành viên của hơn mười nhóm, đầu ra của lệnh có thể không liệt kê hết tất cả các nhóm đó.



### QUESTION



```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net user aaron.harris /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

User name                    aaron.harris
Full Name                    Aaron Harris
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            2/24/2022 11:05:11 PM
Password expires             Never
Password changeable          2/24/2022 11:05:11 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   Never

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users         *Internet Access
The command completed successfully.

```

<figure><img src="../../.gitbook/assets/image (919).png" alt=""><figcaption></figcaption></figure>



```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net user guest /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

User name                    Guest
Full Name
Comment                      Built-in account for guest access to the computer/domain
User's comment
Country/region code          000 (System Default)
Account active               No

```



<figure><img src="../../.gitbook/assets/image (920).png" alt=""><figcaption></figcaption></figure>

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net group "Tier 1 Admins" /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

Group name     Tier 1 Admins
Comment

Members

-------------------------------------------------------------------------------
t1_arthur.tyler          t1_gary.moss             t1_henry.miller
t1_jill.wallis           t1_joel.stephenson       t1_marian.yates
t1_rosie.bryant
The command completed successfully.


za\stacey.baker@THMJMP1 C:\Users\stacey.baker>
```



<figure><img src="../../.gitbook/assets/image (921).png" alt=""><figcaption></figcaption></figure>

```bash
za\stacey.baker@THMJMP1 C:\Users\stacey.baker>net accounts /domain
The request will be processed at a domain controller for domain za.tryhackme.com.

Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          Unlimited
Minimum password length:                              0
Length of password history maintained:                None
Lockout threshold:                                    Never
Lockout duration (minutes):                           30
Lockout observation window (minutes):                 30
Computer role:                                        PRIMARY
The command completed successfully.
```



Enumeration through PowerShell\



### PowerShell

**PowerShell** là phiên bản nâng cấp của **Command Prompt**. Microsoft phát hành PowerShell lần đầu tiên vào năm 2006.

Mặc dù PowerShell có đầy đủ các chức năng cơ bản mà Command Prompt cung cấp, nó còn hỗ trợ **cmdlet** (đọc là _command-let_), là các lớp .NET được thiết kế để thực hiện những chức năng cụ thể.

Mặc dù chúng ta có thể tự viết cmdlet riêng, giống như cách các tác giả của **PowerView** đã thực hiện, nhưng chỉ với các cmdlet có sẵn, chúng ta đã có thể thực hiện rất nhiều hoạt động hữu ích.

#### Import PowerView

```bash
powershell -ep bypass
```

```bash
. .\PowerView.ps1
```

hoặc

```bash
Import-Module .\PowerView.ps1
```



Kiểm tra đã load thành công:

```bash
Get-Domain
```

Do đã cài đặt bộ công cụ **AD-RSAT** ở Bài 3, các cmdlet liên quan đến Active Directory cũng đã được cài đặt tự động. Có hơn **50 cmdlet** được cài đặt sẵn.

Chúng ta sẽ tìm hiểu một số cmdlet trong số đó, nhưng bạn có thể tham khảo danh sách đầy đủ các cmdlet để xem toàn bộ những gì có sẵn.

Sử dụng phiên SSH hiện tại, chúng ta có thể chuyển sang môi trường PowerShell bằng lệnh:

```bash
powershell
```

### Users

We can use the `Get-ADUser` cmdlet to enumerate AD users:

```bash
PS C:\Users\stacey.baker> Get-ADUser -Identity gordon.stevens -Server za.tryhackme.com -Properties *


AccountExpirationDate                :
accountExpires                       : 9223372036854775807
AccountLockoutTime                   :
AccountNotDelegated                  : False
AllowReversiblePasswordEncryption    : False
AuthenticationPolicy                 : {}
AuthenticationPolicySilo             : {}
BadLogonCount                        : 2
badPasswordTime                      : 134262817798268760
badPwdCount                          : 2
CannotChangePassword                 : False
CanonicalName                        : za.tryhackme.com/People/Consulting/gordon.stevens
Certificates                         : {}
City                                 :
CN                                   : gordon.stevens
codePage                             : 0
Company                              :
CompoundIdentitySupported            : {}
Country                              :
countryCode                          : 0
Created                              : 2/24/2022 10:06:44 PM
createTimeStamp                      : 2/24/2022 10:06:44 PM
Deleted                              :
Department                           : Consulting
Description                          :
DisplayName                          : Gordon Stevens
DistinguishedName                    : CN=gordon.stevens,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com
Division                             :
DoesNotRequirePreAuth                : False
dSCorePropagationData                : {1/1/1601 12:00:00 AM}
EmailAddress                         :
EmployeeID                           :
EmployeeNumber                       :
Enabled                              : True
Fax                                  :
GivenName                            : Gordon
HomeDirectory                        :
HomedirRequired                      : False
HomeDrive                            :
HomePage                             :
HomePhone                            :
Initials                             :
instanceType                         : 4 
isDeleted                            :
KerberosEncryptionType               : {}
LastBadPasswordAttempt               : 6/18/2026 7:42:59 PM
LastKnownParent                      :
lastLogoff                           : 0
lastLogon                            : 132908987618422496
LastLogonDate                        : 4/29/2022 11:13:07 PM
lastLogonTimestamp                   : 132957439878817675
LockedOut                            : False
logonCount                           : 4
LogonWorkstations                    :
Manager                              :
MemberOf                             : {CN=Internet Access,OU=Groups,DC=za,DC=tryhackme,DC=com}
MNSLogonAccount                      : False
MobilePhone                          :
Modified                             : 4/29/2022 11:13:07 PM
modifyTimeStamp                      : 4/29/2022 11:13:07 PM
msDS-User-Account-Control-Computed   : 0
Name                                 : gordon.stevens
nTSecurityDescriptor                 : System.DirectoryServices.ActiveDirectorySecurity
ObjectCategory                       : CN=Person,CN=Schema,CN=Configuration,DC=za,DC=tryhackme,DC=com
ObjectClass                          : user
ObjectGUID                           : 48ddd5f1-37ae-4040-a281-47dd58313fcb
objectSid                            : S-1-5-21-3330634377-1326264276-632209373-3058
Office                               :
OfficePhone                          :
Organization                         :
OtherName                            :
PasswordExpired                      : False
PasswordLastSet                      : 2/24/2022 10:06:44 PM
PasswordNeverExpires                 : False
PasswordNotRequired                  : False
POBox                                :
PostalCode                           :
PrimaryGroup                         : CN=Domain Users,CN=Users,DC=za,DC=tryhackme,DC=com
primaryGroupID                       : 513
PrincipalsAllowedToDelegateToAccount : {}
ProfilePath                          :
ProtectedFromAccidentalDeletion      : False
pwdLastSet                           : 132902140043901058
SamAccountName                       : gordon.stevens
sAMAccountType                       : 805306368
ScriptPath                           :
sDRightsEffective                    : 0
ServicePrincipalNames                : {}
SID                                  : S-1-5-21-3330634377-1326264276-632209373-3058
SIDHistory                           : {}
SmartcardLogonRequired               : False
sn                                   : Stevens
State                                :
StreetAddress                        :
Surname                              : Stevens
Title                                : Mid-level
TrustedForDelegation                 : False
TrustedToAuthForDelegation           : False
UseDESKeyOnly                        : False
userAccountControl                   : 512
userCertificate                      : {}
UserPrincipalName                    :
uSNChanged                           : 103860
uSNCreated                           : 30825
whenChanged                          : 4/29/2022 11:13:07 PM
whenCreated                          : 2/24/2022 10:06:44 PM
```

Các tham số này được sử dụng như sau:

* **`-Identity`** – Chỉ định tên tài khoản (account) mà chúng ta muốn thu thập thông tin.
* **`-Properties`** – Chỉ định những thuộc tính (properties) nào của tài khoản sẽ được hiển thị. Sử dụng ký tự **`*`** để hiển thị toàn bộ các thuộc tính có sẵn.
* **`-Server`** – Vì máy của chúng ta không tham gia domain (**not domain-joined**), nên cần sử dụng tham số này để chỉ định Domain Controller mà cmdlet sẽ kết nối tới.

Đối với hầu hết các cmdlet này, chúng ta cũng có thể sử dụng tham số **`-Filter`** để kiểm soát quá trình enumeration một cách chi tiết hơn. Ngoài ra, có thể kết hợp với cmdlet **`Format-Table`** để hiển thị kết quả dưới dạng bảng gọn gàng và dễ đọc hơn, ví dụ như sau:

```bash
PS C:\Users\stacey.baker> Get-ADUser -Filter 'Name -like "*stevens"' -Server za.tryhackme.com | Format-Table Name,SamAccountName -A

Name             SamAccountName   
----             --------------
chloe.stevens    chloe.stevens
samantha.stevens samantha.stevens
mohammed.stevens mohammed.stevens
jacob.stevens    jacob.stevens
timothy.stevens  timothy.stevens
trevor.stevens   trevor.stevens
owen.stevens     owen.stevens
jane.stevens     jane.stevens
janice.stevens   janice.stevens
gordon.stevens   gordon.stevens
```

### Groups

We can use the `Get-ADGroup` cmdlet to enumerate AD groups:

```bash
PS C:\Users\stacey.baker> Get-ADGroup -Identity Administrators -Server za.tryhackme.com


DistinguishedName : CN=Administrators,CN=Builtin,DC=za,DC=tryhackme,DC=com
GroupCategory     : Security
GroupScope        : DomainLocal
Name              : Administrators
ObjectClass       : group
ObjectGUID        : f4d1cbcd-4a6f-4531-8550-0394c3273c4f
SamAccountName    : Administrators
SID               : S-1-5-32-544
```

```
CN=Administrators
```

→ Tên object.

```
CN=Builtin
```

→ Nằm trong container Builtin.

```
DC=za,DC=tryhackme,DC=com
```

→ Domain:

```
za.tryhackme.com
```

SID = Security Identifier.

```
S-1-5-32-544
```

Trong đó:

```
S
```

\= SID

```
1
```

\= Revision

```
5
```

\= NT Authority

```
32
```

\= Builtin Domain

```
544
```

\= RID của nhóm Administrators

#### Một số RID nổi tiếng

| RID | Group/User        |
| --- | ----------------- |
| 500 | Administrator     |
| 501 | Guest             |
| 512 | Domain Admins     |
| 513 | Domain Users      |
| 514 | Domain Guests     |
| 518 | Schema Admins     |
| 519 | Enterprise Admins |
| 544 | Administrators    |



### AD Objects

Chúng ta cũng có thể thực hiện các truy vấn tổng quát hơn đối với mọi đối tượng trong Active Directory bằng cmdlet **`Get-ADObject`**.

Ví dụ, nếu muốn tìm tất cả các đối tượng AD đã được thay đổi sau một ngày cụ thể, chúng ta có thể sử dụng lệnh như sau:

```bash
PS C:\> $ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)
PS C:\> Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -includeDeletedObjects -Server za.tryhackme.com

Deleted           :
DistinguishedName : DC=za,DC=tryhackme,DC=com
Name              : za
ObjectClass       : domainDNS
ObjectGUID        : 518ee1e7-f427-4e91-a081-bb75e655ce7a

Deleted           :
DistinguishedName : CN=Administrator,CN=Users,DC=za,DC=tryhackme,DC=com
Name              : Administrator
ObjectClass       : user
ObjectGUID        : b10fe384-bcce-450b-85c8-218e3c79b30f
```

Nếu muốn thực hiện một cuộc tấn công **Password Spraying** mà không làm khóa tài khoản người dùng, chúng ta có thể sử dụng phương pháp này để liệt kê các tài khoản có giá trị **`badPwdCount`** lớn hơn 0. Sau đó, loại bỏ các tài khoản này khỏi danh sách mục tiêu của cuộc tấn công.

`badPwdCount` là thuộc tính ghi lại số lần đăng nhập thất bại gần đây của một tài khoản. Nếu giá trị này đã lớn hơn 0, nghĩa là tài khoản đó đã có một hoặc nhiều lần nhập sai mật khẩu. Việc tiếp tục thử mật khẩu trên những tài khoản này có thể khiến chúng chạm ngưỡng khóa tài khoản (lockout threshold).

Bằng cách xác định và tránh các tài khoản có `badPwdCount > 0`, chúng ta có thể giảm nguy cơ vô tình làm khóa tài khoản trong quá trình Password Spraying.&#x20;

```bash
PS C:\Users\stacey.baker> Get-ADObject -Filter 'badPwdCount -gt 0' -Server za.tryhackme.com

DistinguishedName                                                        Name              ObjectClass ObjectGUID                           
-----------------                                                        ----              ----------- ----------
CN=Administrator,CN=Users,DC=za,DC=tryhackme,DC=com                      Administrator     user        b10fe384-bcce-450b-85c8-218e3c79b30f 
CN=henry.taylor,OU=IT,OU=People,DC=za,DC=tryhackme,DC=com                henry.taylor      user        154e4541-219e-4fa9-a5bf-ec5a367c5e21
CN=frank.fletcher,OU=IT,OU=People,DC=za,DC=tryhackme,DC=com              frank.fletcher    user        3dd92645-4b2d-4ba0-957c-9f6c20421d54
CN=olivia.morgan,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com      olivia.morgan     user        caa23b31-7706-43cf-b351-cfa1022c6b76
CN=simon.griffiths,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com     simon.griffiths   user        90d6a907-0c0e-4f0c-8ad5-99b43f14db64
CN=henry.black,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com        henry.black       user        379df099-f89b-47fa-886d-ae915e2f8d32
CN=adrian.wilson,OU=Marketing,OU=People,DC=za,DC=tryhackme,DC=com        adrian.wilson     user        013949ce-b679-4d7c-afa4-b6d46418e7a5
CN=mark.oconnor,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com       mark.oconnor      user        e0bb6195-9f2e-4de1-83a5-0f9613a28e8f
CN=dawn.hughes,OU=Finance,OU=People,DC=za,DC=tryhackme,DC=com            dawn.hughes       user        fed968f3-3e5e-4d36-b66a-289ddb6e8db2
CN=joanne.davies,OU=Marketing,OU=People,DC=za,DC=tryhackme,DC=com        joanne.davies     user        81b8d2ab-d3e1-4316-8115-9d305a0824b8
CN=alan.jones,OU=Human Resources,OU=People,DC=za,DC=tryhackme,DC=com     alan.jones        user        88922cf5-828b-48f4-ab30-86d37381233c
CN=maria.sheppard,OU=Human Resources,OU=People,DC=za,DC=tryhackme,DC=com maria.sheppard    user        edeffae5-eb5c-4c4a-8ba1-64e750e84fbe
CN=sophie.blackburn,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com    sophie.blackburn  user        e2854343-659c-4b90-94ac-111af7c60ce3
CN=gordon.stevens,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com      gordon.stevens    user        48ddd5f1-37ae-4040-a281-47dd58313fcb
CN=dominic.elliott,OU=Finance,OU=People,DC=za,DC=tryhackme,DC=com        dominic.elliott   user        2a5eabcc-0bff-4341-a2ce-f14fc1621894
CN=louise.talbot,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com       louise.talbot     user        b5fe09ec-935d-4158-8413-3b596da9e11c
CN=jennifer.wood,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com      jennifer.wood     user        90d6e815-5260-4a26-b5c3-b3fb6a28f192
CN=frances.chapman,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com    frances.chapman   user        26616091-bb69-4182-99e7-41d61e578034
CN=dawn.turner,OU=Finance,OU=People,DC=za,DC=tryhackme,DC=com            dawn.turner       user        178cb599-6a57-41cb-94b6-30415f04a008
CN=samantha.thompson,OU=Engineering,OU=People,DC=za,DC=tryhackme,DC=com  samantha.thompson user        f78decbb-6ec8-40bb-9190-af2193a23ee5
CN=anthony.reynolds,OU=Marketing,OU=People,DC=za,DC=tryhackme,DC=com     anthony.reynolds  user        ab44469f-8752-4bb7-bd36-10e6705028e4
```

Lệnh này sẽ chỉ trả về kết quả nếu có người dùng nào đó trong mạng đã nhập sai mật khẩu một vài lần.

### Domains

We can use `Get-ADDomain` to retrieve additional information about the specific domain:

```bash
PS C:\Users\stacey.baker> Get-ADDomain -Server za.tryhackme.com

AllowedDNSSuffixes                 : {}
ChildDomains                       : {}
ComputersContainer                 : CN=Computers,DC=za,DC=tryhackme,DC=com
DeletedObjectsContainer            : CN=Deleted Objects,DC=za,DC=tryhackme,DC=com
DistinguishedName                  : DC=za,DC=tryhackme,DC=com
DNSRoot                            : za.tryhackme.com
DomainControllersContainer         : OU=Domain Controllers,DC=za,DC=tryhackme,DC=com
DomainMode                         : Windows2012R2Domain
DomainSID                          : S-1-5-21-3330634377-1326264276-632209373
ForeignSecurityPrincipalsContainer : CN=ForeignSecurityPrincipals,DC=za,DC=tryhackme,DC=com
Forest                             : za.tryhackme.com
InfrastructureMaster               : THMDC.za.tryhackme.com
LastLogonReplicationInterval       :
LinkedGroupPolicyObjects           : {CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=za,DC=tryhackme,DC=com}
LostAndFoundContainer              : CN=LostAndFound,DC=za,DC=tryhackme,DC=com
ManagedBy                          :
Name                               : za
NetBIOSName                        : ZA
ObjectClass                        : domainDNS
ObjectGUID                         : 518ee1e7-f427-4e91-a081-bb75e655ce7a
ParentDomain                       :
PDCEmulator                        : THMDC.za.tryhackme.com
PublicKeyRequiredPasswordRolling   :
QuotasContainer                    : CN=NTDS Quotas,DC=za,DC=tryhackme,DC=com
ReadOnlyReplicaDirectoryServers    : {}
ReplicaDirectoryServers            : {THMDC.za.tryhackme.com}
RIDMaster                          : THMDC.za.tryhackme.com
SubordinateReferences              : {DC=ForestDnsZones,DC=za,DC=tryhackme,DC=com, DC=DomainDnsZones,DC=za,DC=tryhackme,DC=com, CN=Configuration,DC=za,DC=tryhackme,DC=com}  
SystemsContainer                   : CN=System,DC=za,DC=tryhackme,DC=com
UsersContainer                     : CN=Users,DC=za,DC=tryhackme,DC=com
```

### Altering AD Objects&#xD;

Điều tuyệt vời của các **AD-RSAT cmdlet** là một số cmdlet còn cho phép bạn tạo mới hoặc chỉnh sửa các đối tượng Active Directory hiện có.

Tuy nhiên, trong mạng lab này, trọng tâm của chúng ta là **enumeration**. Việc tạo mới hoặc thay đổi các đối tượng AD được xem là **AD Exploitation**, nội dung này sẽ được đề cập ở phần sau của module Active Directory.

Dù vậy, chúng ta sẽ xem một ví dụ về việc thay đổi mật khẩu của tài khoản AD bằng cách sử dụng cmdlet **`Set-ADAccountPassword`**:

```
PS C:\> Set-ADAccountPassword -Identity gordon.stevens -Server za.tryhackme.com -OldPassword (ConvertTo-SecureString -AsPlaintext "old" -force) -NewPassword (ConvertTo-SecureString -AsPlainText "new" -Force)
```

Hãy nhớ thay đổi giá trị **Identity** và mật khẩu tương ứng với tài khoản được cấp cho bạn để thực hiện enumeration trên trang phân phối ở Bài 1.

### Ưu điểm

* Các cmdlet PowerShell có thể thu thập được nhiều thông tin hơn đáng kể so với các lệnh `net` trong Command Prompt.
* Chúng ta có thể chỉ định trực tiếp server và domain cần truy vấn, cho phép sử dụng cùng với `runas` trên một máy không tham gia domain.
* Có thể tự viết các cmdlet riêng để thu thập những thông tin cụ thể theo nhu cầu.
* Các cmdlet AD-RSAT cho phép chỉnh sửa trực tiếp các đối tượng AD, chẳng hạn như đặt lại mật khẩu hoặc thêm người dùng vào một nhóm cụ thể.

### Nhược điểm

* PowerShell thường bị đội Blue Team giám sát chặt chẽ hơn so với Command Prompt.
* Chúng ta phải cài đặt bộ công cụ **AD-RSAT** hoặc sử dụng các script PowerShell khác, vốn có thể dễ bị phát hiện hơn trong quá trình enumeration.



### QUESTION

<figure><img src="../../.gitbook/assets/image (922).png" alt=""><figcaption></figcaption></figure>

```bash
PS C:\Users\stacey.baker> Get-ADUser -Identity beth.nolan -Server za.tryhackme.com -Property * | ft Name,Title

Name       Title  
----       -----
beth.nolan Senior
```



<figure><img src="../../.gitbook/assets/image (924).png" alt=""><figcaption></figcaption></figure>

```bash
PS C:\Users\stacey.baker> Get-ADUser -Identity annette.manning -Server za.tryhackme.com -Properties * | ft  DistinguishedName

DistinguishedName                                                   
-----------------
CN=annette.manning,OU=Marketing,OU=People,DC=za,DC=tryhackme,DC=com
```



<figure><img src="../../.gitbook/assets/image (925).png" alt=""><figcaption></figcaption></figure>

```bash
PS C:\Users\stacey.baker> Get-ADGroup -Identity 'Tier 2 Admins'  -Server za.tryhackme.com -Properties * | ft Name,whenCreated

Name          whenCreated           
----          -----------
Tier 2 Admins 2/24/2022 10:04:41 PM
```

<figure><img src="../../.gitbook/assets/image (926).png" alt=""><figcaption></figcaption></figure>

```bash
PS C:\Users\stacey.baker> Get-ADGroup -Identity 'Enterprise Admins'  -Server za.tryhackme.com -Properties * | ft SID

SID                                          
---
S-1-5-21-3330634377-1326264276-632209373-519
```



<figure><img src="../../.gitbook/assets/image (927).png" alt=""><figcaption></figcaption></figure>

```bash
PS C:\Users\stacey.baker> Get-ADDomain -Server za.tryhackme.com

AllowedDNSSuffixes                 : {}
ChildDomains                       : {}
ComputersContainer                 : CN=Computers,DC=za,DC=tryhackme,DC=com
DeletedObjectsContainer            : CN=Deleted Objects,DC=za,DC=tryhackme,DC=com
```



## Enumeration through Bloodhound

{% file src="../../.gitbook/assets/bh-session-inject-1654672411088.zip" %}

Cuối cùng, chúng ta sẽ tìm hiểu cách thực hiện **AD Enumeration** bằng **BloodHound**.

**BloodHound** là công cụ AD Enumeration mạnh mẽ nhất hiện nay. Khi được phát hành vào năm **2016**, nó đã thay đổi hoàn toàn cách thức thực hiện AD Enumeration trong môi trường Active Directory.

### Bloodhound History

Trong một khoảng thời gian dài, các **red teamer** (và không may là cả các **attacker**) đã chiếm ưu thế. Thậm chí đến mức **Microsoft** đã tích hợp phiên bản BloodHound của riêng họ vào giải pháp **Advanced Threat Protection**.

Tất cả đều xoay quanh câu nói sau:\\

> _"Defenders think in lists, Attackers think in graphs." - Unknown_

BloodHound cho phép kẻ tấn công (và hiện nay cả đội phòng thủ) **trực quan hóa môi trường Active Directory dưới dạng đồ thị (graph)** với các nút (node) được liên kết với nhau. Mỗi liên kết là một con đường tiềm năng có thể bị khai thác để đạt được mục tiêu. Trong khi đó, phía phòng thủ thường chỉ làm việc với các danh sách, chẳng hạn như danh sách Domain Admin hoặc danh sách tất cả các máy chủ trong môi trường.

Cách tư duy dựa trên đồ thị này đã mở ra một thế giới mới cho kẻ tấn công. Nó cho phép thực hiện các cuộc tấn công theo hai giai đoạn.

Ở giai đoạn đầu, kẻ tấn công tiến hành các chiến dịch phishing để có được điểm truy cập ban đầu và thu thập thông tin từ Active Directory. Payload ban đầu này thường rất dễ bị phát hiện và thường bị đội Blue Team phát hiện, ngăn chặn trước khi kẻ tấn công kịp làm gì ngoài việc lấy dữ liệu đã thu thập được.

Tuy nhiên, kẻ tấn công có thể sử dụng dữ liệu đó ngoại tuyến (offline) để xây dựng một đường tấn công dưới dạng đồ thị, thể hiện chính xác từng bước và từng điểm trung gian cần đi qua để đạt được mục tiêu.

Sau đó, trong chiến dịch phishing thứ hai, với thông tin đã có, kẻ tấn công thường có thể đạt được mục tiêu chỉ trong vài phút sau khi xâm nhập thành công. Nhiều trường hợp còn nhanh hơn thời gian để đội Blue Team nhận được cảnh báo đầu tiên.

Đó chính là sức mạnh của việc tư duy bằng đồ thị. Đây cũng là lý do ngày càng nhiều đội Blue Team bắt đầu sử dụng các công cụ tương tự để hiểu rõ hơn về trạng thái bảo mật của môi trường của họ.



### Sharphound

Bạn sẽ thường nghe mọi người nhắc đến **SharpHound** và **BloodHound** như thể chúng là một. Tuy nhiên, chúng không giống nhau.

**SharpHound** là công cụ thu thập dữ liệu (enumeration tool) của BloodHound. Nó được sử dụng để thu thập thông tin từ Active Directory, sau đó dữ liệu này sẽ được hiển thị trực quan trong BloodHound.

**BloodHound** là giao diện đồ họa (GUI) dùng để hiển thị các đồ thị tấn công Active Directory. Vì vậy, trước tiên chúng ta cần học cách sử dụng SharpHound để thu thập dữ liệu AD trước khi có thể xem kết quả bằng BloodHound.

Có ba trình thu thập dữ liệu (collector) khác nhau của SharpHound:

* **SharpHound.ps1** – Script PowerShell dùng để chạy SharpHound. Tuy nhiên, các phiên bản SharpHound mới nhất đã ngừng phát hành bản PowerShell này. Phiên bản này hữu ích khi sử dụng cùng RAT vì script có thể được nạp trực tiếp vào bộ nhớ, giúp tránh bị phát hiện bởi các giải pháp antivirus quét trên đĩa.
* **SharpHound.exe** – Phiên bản thực thi Windows của SharpHound.
* **AzureHound.ps1** – Script PowerShell dùng để thu thập dữ liệu từ môi trường Azure (dịch vụ điện toán đám mây của Microsoft). BloodHound có thể nhập dữ liệu từ Azure để tìm các đường tấn công liên quan đến cấu hình Identity and Access Management (IAM) của Azure.

> **Lưu ý:** Phiên bản BloodHound và SharpHound nên tương thích với nhau để đạt kết quả tốt nhất. Thông thường, khi BloodHound được cập nhật, dữ liệu thu thập bởi các phiên bản SharpHound cũ có thể không còn nhập được nữa. Mạng lab này được xây dựng bằng **BloodHound v4.1.0**, vì vậy hãy sử dụng phiên bản này với dữ liệu SharpHound tương ứng.

Khi sử dụng các collector này trong một cuộc đánh giá bảo mật, khả năng cao các file sẽ bị nhận diện là mã độc và tạo cảnh báo cho Blue Team. Đây là lúc máy Windows không tham gia domain phát huy tác dụng.

Chúng ta có thể sử dụng lệnh **runas** để inject credential AD và chỉ định Domain Controller cho SharpHound. Vì chúng ta kiểm soát máy Windows này, chúng ta có thể tắt antivirus hoặc tạo ngoại lệ cho các file/thư mục cụ thể. Việc này đã được cấu hình sẵn trên máy **THMJMP1**.

Bạn có thể tìm các file SharpHound trong thư mục:

```
C:\Tools\
```

```bash
PS C:\Users\stacey.baker> dir C:\Tools\


    Directory: C:\Tools


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         6/7/2026   9:14 AM         120776 20260607091428_BloodHound.zip
-a----        6/12/2026   4:57 PM         121608 20260612165755_BloodHound.zip
-a----        6/12/2026   4:59 PM         122180 20260612165937_BloodHound.zip
-a----        6/13/2026   2:17 PM         121513 20260613141702_BloodHound.zip
-a----        6/18/2026   5:35 AM         126426 20260618053506_BloodHound.zip
-a----         6/5/2026   1:59 PM         122268 2BloodHound.zip
-a----         6/5/2026   8:55 AM         121163 BloodHound.zip
-a----        6/12/2026   4:27 PM         770279 PowerView.ps1
-a----        3/16/2022   5:19 PM         906752 SharpHound.exe
-a----        6/12/2026   4:40 PM        5747296 user_enum.txt
-a----         6/5/2026   1:59 PM         359470 YzE4MDdkYjAtYjc2MC00OTYyLTk1YTEtYjI0NjhiZmRiOWY1.bin
```



Trong bài này, chúng ta sẽ sử dụng **SharpHound.exe**, nhưng bạn có thể tự thử nghiệm với hai phiên bản còn lại. <[https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound-all-flags.html](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound-all-flags.html)>

Chúng ta sẽ chạy SharpHound như sau:

```
SharpHound.exe --CollectionMethods <Methods> --Domain za.tryhackme.com --ExcludeDCs
```

#### Giải thích tham số

* **CollectionMethods** – Xác định loại dữ liệu mà SharpHound sẽ thu thập. Các tùy chọn phổ biến nhất là `Default` hoặc `All`. Ngoài ra, SharpHound có cơ chế cache dữ liệu, nên sau lần chạy đầu tiên, bạn có thể chỉ dùng phương thức `Session` để thu thập các phiên đăng nhập mới và tăng tốc quá trình.
* **Domain** – Chỉ định domain cần thu thập dữ liệu. Trong một số trường hợp, bạn có thể muốn thu thập dữ liệu từ domain cha hoặc một domain khác có quan hệ trust với domain hiện tại. Tham số này cho phép bạn chỉ định domain cần enumerate.
* **ExcludeDCs** – Yêu cầu SharpHound không tương tác với các Domain Controller, giúp giảm khả năng việc chạy SharpHound tạo ra cảnh báo cho hệ thống giám sát.



Using your SSH PowerShell session from the previous task, copy the Sharphound binary to your AD user's Documents directory:

```bash
PS C:\Users\stacey.baker> copy C:\Tools\Sharphound.exe ~\Documents\
PS C:\Users\stacey.baker> cd ~\Documents\
PS C:\Users\stacey.baker\Documents>  
```





```bash
PS C:\Users\stacey.baker\Documents> ./SharpHound.exe --CollectionMethods All --Domain za.tryhackme.com --ExcludeDCs
2026-06-19T16:20:37.9489154+01:00|INFORMATION|Resolved Collection Methods: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCO
M, SPNTargets, PSRemote
2026-06-19T16:20:37.9645282+01:00|INFORMATION|Initializing SharpHound at 4:20 PM on 6/19/2026
2026-06-19T16:20:38.2457595+01:00|INFORMATION|Flags: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemot
e
2026-06-19T16:20:38.4646354+01:00|INFORMATION|Beginning LDAP search for za.tryhackme.com
2026-06-19T16:21:08.5613065+01:00|INFORMATION|Status: 0 objects finished (+0 0)/s -- Using 49 MB RAM
2026-06-19T16:21:26.6152888+01:00|INFORMATION|Producer has finished, closing LDAP channel
2026-06-19T16:21:26.8184177+01:00|INFORMATION|LDAP channel closed, waiting for consumers
2026-06-19T16:21:26.9434083+01:00|INFORMATION|Consumers finished, closing output channel
2026-06-19T16:21:27.0059217+01:00|INFORMATION|Output channel closed, waiting for output task to complete
Closing writers
2026-06-19T16:21:27.4434017+01:00|INFORMATION|Status: 2159 objects finished (+2159 44.97917)/s -- Using 87 MB RAM
2026-06-19T16:21:27.4434017+01:00|INFORMATION|Enumeration finished in 00:00:48.9909630
2026-06-19T16:21:27.7247223+01:00|INFORMATION|SharpHound Enumeration Completed at 4:21 PM on 6/19/2026! Happy Graphing!
PS C:\Users\stacey.baker\Documents> dir


    Directory: C:\Users\stacey.baker\Documents


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/19/2026   4:21 PM         121337 20260619162125_BloodHound.zip
-a----        3/16/2022   5:19 PM         906752 Sharphound.exe
-a----        6/19/2026   4:21 PM         359470 YzE4MDdkYjAtYjc2MC00OTYyLTk1YTEtYjI0NjhiZmRiOWY1.bin
```

Bây giờ chúng ta có thể sử dụng **BloodHound** để nhập (ingest) file **ZIP** này và hiển thị các **đường tấn công (attack paths)** một cách trực quan.

```
SharpHound -> Thu thập dữ liệu AD -> File ZIP -> BloodHound -> Phân tích Attack Paths
```

### Bloodhound

Như đã đề cập trước đó, **BloodHound** là giao diện đồ họa (GUI) cho phép chúng ta nhập dữ liệu được thu thập bởi **SharpHound** và trực quan hóa chúng thành các **đường tấn công (attack paths)**.

BloodHound sử dụng **Neo4j** làm cơ sở dữ liệu backend và hệ thống đồ thị. **Neo4j** là một hệ quản trị cơ sở dữ liệu dạng đồ thị (graph database management system).

Trong các trường hợp khác, hãy đảm bảo rằng **BloodHound** và **Neo4j** đã được cài đặt và cấu hình trên máy tấn công của bạn.

Dù sử dụng cách nào, việc hiểu những gì diễn ra phía sau vẫn rất quan trọng.

Trước khi khởi động BloodHound, chúng ta cần khởi chạy **Neo4j**:

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo neo4j start
Directories in use:
home:         /usr/share/neo4j
config:       /usr/share/neo4j/conf
logs:         /etc/neo4j/logs
plugins:      /usr/share/neo4j/plugins
import:       /usr/share/neo4j/import
data:         /etc/neo4j/data
certificates: /usr/share/neo4j/certificates
licenses:     /usr/share/neo4j/licenses
run:          /var/lib/neo4j/run
Starting Neo4j.
Started neo4j (pid:221115). It is available at http://localhost:7474
There may be a short delay until the server is ready.
```

In another Terminal tab, run `bloodhound --no-sandbox`. This will show you the authentication GUI:

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/39f261aecedccbaf118eb2ee69d55129.png" alt="Bloodhound"></div>

pass : admin/admin&#x20;

The default credentials for the neo4j database will be `neo4j:neo4j`. Use this to authenticate in Bloodhound. To import our results, you will need to recover the ZIP file from the Windows host.

```bash
scp <AD Username>@THMJMP1.za.tryhackme.com:C:/Users/<AD Username>/Documents/<Sharphound ZIP> .
```

```bash
(base) ┌──(kali㉿kali)-[~/abc]
└─$ scp stacey.baker@thmjmp1.za.tryhackme.com:C:/Users/stacey.baker/Documents/20260619162125_BloodHound.zip .
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
stacey.baker@thmjmp1.za.tryhackme.com's password: 
20260619162125_BloodHound.zip                                                                                                              100%  118KB  63.2KB/s   00:01    
                                                                                                                                                                             
(base) ┌──(kali㉿kali)-[~/abc]
```

<p align="center"><br><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/d7bed860790aaca612cc658d19d782ef.png" alt="Bloodhound"><br></p>

Once all JSON files have been imported, we can start using Bloodhound to enumerate attack paths for this specific domain.

### Attack Paths

### &#xD;

<figure><img src="../../.gitbook/assets/image (944).png" alt=""><figcaption></figcaption></figure>



BloodHound có thể hiển thị nhiều **đường tấn công (attack paths)** khác nhau.

Khi nhấn vào biểu tượng **ba gạch** bên cạnh ô **"Search for a node"**, bạn sẽ thấy các tùy chọn có sẵn.

Tab đầu tiên sẽ hiển thị thông tin về các dữ liệu (imports) hiện đang được nhập vào BloodHound.

<figure><img src="../../.gitbook/assets/image (929).png" alt=""><figcaption></figcaption></figure>

<br>

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/5d695d25afebc2b1dfc7cb408704e755.png" alt="Bloodhound"><br></p>

Lưu ý rằng nếu bạn nhập (import) một lần chạy SharpHound mới, các số liệu này sẽ được **cộng dồn** vào dữ liệu hiện có.

Trước tiên, chúng ta sẽ xem phần **Node Info**.

Hãy tìm kiếm tài khoản AD của bạn trong BloodHound. Sau khi tìm thấy, bạn cần **nhấp vào node** để làm mới (refresh) giao diện hiển thị.

Ngoài ra, hãy lưu ý rằng bạn có thể thay đổi cách hiển thị nhãn (label scheme) bằng cách nhấn **Left Ctrl**.

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/a6e1af6f79653eeedb18ac9c3be7a038.png" alt="Bloodhound"><br></p>

Chúng ta có thể thấy BloodHound trả về một lượng lớn thông tin liên quan đến tài khoản người dùng. Mỗi danh mục cung cấp các loại thông tin sau:

* **Overview** – Cung cấp thông tin tổng quan, chẳng hạn như số lượng phiên đăng nhập (active sessions) của tài khoản và liệu tài khoản đó có thể tiếp cận các mục tiêu có giá trị cao (high-value targets) hay không.
* **Node Properties** – Hiển thị các thuộc tính của tài khoản AD, chẳng hạn như tên hiển thị (display name) và chức danh (title).
* **Extra Properties** – Cung cấp thông tin AD chi tiết hơn, chẳng hạn như Distinguished Name (DN) và thời điểm tài khoản được tạo.
* **Group Membership** – Hiển thị các nhóm mà tài khoản là thành viên.
* **Local Admin Rights** – Cung cấp thông tin về các máy đã tham gia domain mà tài khoản này có quyền quản trị viên cục bộ (local administrator).
* **Execution Rights** – Hiển thị các quyền đặc biệt, chẳng hạn như khả năng đăng nhập qua RDP vào một máy.
* **Outbound Control Rights** – Hiển thị các đối tượng AD mà tài khoản này có quyền sửa đổi thuộc tính của chúng.
* **Inbound Control Rights** – Cung cấp thông tin về các đối tượng AD có quyền sửa đổi thuộc tính của chính tài khoản này.

Nếu muốn xem thêm thông tin trong từng danh mục, bạn có thể nhấn vào **con số** bên cạnh truy vấn thông tin tương ứng.Ví dụ, hãy xem **Group Membership** của tài khoản. Khi nhấn vào con số bên cạnh **"First Degree Group Membership"**, chúng ta có thể thấy tài khoản này là thành viên của **hai nhóm**.

<figure><img src="../../.gitbook/assets/image (930).png" alt=""><figcaption></figcaption></figure>

Tiếp theo, chúng ta sẽ tìm hiểu về các **Analysis Queries**. Đây là những truy vấn được các nhà phát triển của **BloodHound** xây dựng sẵn nhằm giúp thu thập và hiển thị những thông tin hữu ích trong môi trường Active Directory.

<figure><img src="../../.gitbook/assets/image (936).png" alt=""><figcaption></figcaption></figure>

Under the Domain Information section, we can run the Find all Domain Admins query. Note that you can press LeftCtrl to change the label display settings.

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/9e4a7afd2acd099df71dc70d9eccf705.png" alt="Bloodhound"><br></p>

Các biểu tượng trong BloodHound được gọi là **node**, còn các đường nối giữa chúng được gọi là **edge**. Hãy cùng phân tích kỹ hơn những gì BloodHound đang hiển thị.

Có một tài khoản người dùng Active Directory với tên **T0\_TINUS.GREEN**, là thành viên của nhóm **Tier 0 ADMINS**. Tuy nhiên, nhóm này lại là một **nested group** bên trong nhóm **DOMAIN ADMINS**. Điều này có nghĩa là tất cả thành viên của **Tier 0 ADMINS** đều gián tiếp có quyền **Domain Admin (DA)**.

Ngoài ra, còn có một tài khoản AD khác là **ADMINISTRATOR**, vốn là thành viên trực tiếp của nhóm **DOMAIN ADMINS**.

Như vậy, trong bề mặt tấn công (attack surface) hiện tại có **hai tài khoản** mà chúng ta có thể nhắm tới nếu muốn đạt được quyền **Domain Admin**:

* **T0\_TINUS.GREEN**
* **ADMINISTRATOR**

Tuy nhiên, vì **ADMINISTRATOR** là tài khoản mặc định (built-in account) của hệ thống, nên thông thường chúng ta sẽ tập trung vào tài khoản **T0\_TINUS.GREEN**, vì đây là tài khoản người dùng thực tế và thường có nhiều khả năng bị khai thác hơn.<br>

Mỗi AD object đã được đề cập trong các nhiệm vụ trước có thể trở thành một **node** trong BloodHound, và mỗi node sẽ có một icon khác nhau thể hiện loại object mà nó đại diện. Nếu chúng ta muốn xây dựng một **attack path**, chúng ta cần xem xét các **edges** khả dụng giữa vị trí hiện tại, các **privileges** mà chúng ta đang có, và nơi chúng ta muốn đạt tới. BloodHound có nhiều loại edges khác nhau có thể được truy cập thông qua biểu tượng **filter**.

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/c21ccdbdd84a6e709d39fdff14764cea.png" alt="Bloodhound"><br></p>

These are also constantly being updated as new attack vectors are discovered. We will be looking at exploiting these different edges in a future network. However, let's look at the most basic attack path using only the default and some special edges. We will run a search in Bloodhound to enumerate the attack path. Press the path icon to allow for path searching.

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/d3fab8519fda4ac61db80c35274c53a1.png" alt="Bloodhound"></div>

Our Start Node would be our AD username, and our End Node will be the Tier 1 ADMINS group since this group has administrative privileges over servers.

<figure><img src="../../.gitbook/assets/image (937).png" alt=""><figcaption></figcaption></figure>

Nếu không có **attack path** khả dụng khi sử dụng các bộ lọc **edge** đã chọn, BloodHound sẽ hiển thị thông báo **"No Results Found"**. Lưu ý rằng điều này cũng có thể xảy ra do sự không khớp giữa **BloodHound/SharpHound**, nghĩa là dữ liệu đã không được **ingest** (thu thập và nạp vào) một cách đúng đắn. Vì vậy, nên sử dụng **BloodHound v4.1.0**.

Tuy nhiên, trong trường hợp của chúng ta, BloodHound đã hiển thị một **attack path**. Nó cho thấy một trong các **T1 ADMIN accounts** đã vi phạm mô hình **tiering model** bằng cách sử dụng thông tin xác thực của họ để xác thực vào **THMJMP1**, đây là một **workstation**. Nó cũng cho thấy rằng bất kỳ người dùng nào thuộc nhóm **DOMAIN USERS**, bao gồm cả tài khoản AD của chúng ta, đều có khả năng **RDP** vào host này.

Chúng ta có thể thực hiện một quy trình như sau để khai thác đường tấn công này:

* Sử dụng thông tin xác thực AD của chúng ta để **RDP** vào **THMJMP1**.
* Tìm kiếm một vector **privilege escalation** trên máy, nhằm đạt được quyền **Administrative access**.
* Khi đã có quyền quản trị, sử dụng các kỹ thuật và công cụ thu thập thông tin xác thực như **Mimikatz**.
* Vì **T1 Admin** có một session đang hoạt động trên **THMJMP1**, việc thu thập thông tin xác thực có thể giúp chúng ta lấy được **NTLM hash** của tài khoản tương ứng.

Đây là một ví dụ tương đối đơn giản. Trong thực tế, các **attack path** thường phức tạp hơn nhiều và yêu cầu nhiều bước để đạt được mục tiêu cuối cùng. Nếu bạn quan tâm đến các kỹ thuật khai thác liên quan đến từng loại **edge**, tài liệu chính thức của BloodHound (liên kết mở trong tab mới) cung cấp một hướng dẫn rất chi tiết. BloodHound là một công cụ **enumeration Active Directory** cực kỳ mạnh mẽ, cung cấp cái nhìn sâu về cấu trúc AD và bề mặt tấn công. Việc dành thời gian thực hành với nó sẽ giúp bạn hiểu rõ hơn các tính năng của công cụ này.

### Session Data Only&#xD; &#xD;

Cấu trúc của **Active Directory (AD)** trong các tổ chức lớn thường không thay đổi thường xuyên. Có thể có một vài nhân viên mới, nhưng cấu trúc tổng thể của các **OU (Organizational Units)**, **Groups**, **Users**, và **permissions** sẽ vẫn giữ nguyên.

Tuy nhiên, thứ thay đổi liên tục là các **active sessions** và các sự kiện **LogOn**. Vì **SharpHound** tạo ra một **snapshot tại một thời điểm (point-in-time snapshot)** của cấu trúc AD, nên dữ liệu về **active session** không phải lúc nào cũng chính xác, do một số user có thể đã đăng xuất khỏi session của họ hoặc các user mới có thể đã tạo session mới. Đây là một điểm rất quan trọng cần lưu ý, và đó là lý do tại sao nên chạy SharpHound theo định kỳ.

Một cách tiếp cận tốt là chạy SharpHound với phương thức thu thập **"All"** ở đầu quá trình đánh giá, sau đó chạy lại ít nhất hai lần mỗi ngày với phương thức thu thập **"Session"**. Điều này sẽ cung cấp dữ liệu session mới và giúp các lần chạy sau nhanh hơn vì không cần enumerate lại toàn bộ cấu trúc AD. Thời điểm tốt nhất để chạy các lần thu thập session này là khoảng **10:00**, khi người dùng vừa bắt đầu làm việc sau khi uống cà phê, và khoảng **14:00**, khi họ quay lại làm việc sau giờ nghỉ trưa nhưng chưa tan ca.

Bạn có thể xóa dữ liệu session cũ (stagnant session data) trong BloodHound tại tab **Database Info** bằng cách nhấn **"Clear Session Information"** trước khi import dữ liệu từ các lần chạy SharpHound mới.

### Benefits

* Cung cấp giao diện **GUI** cho việc enumerate AD
* Có khả năng hiển thị **attack paths** từ dữ liệu AD đã thu thập
* Cung cấp insight sâu hơn về các **AD objects**, những thứ thường cần nhiều truy vấn thủ công mới lấy được

### Drawbacks

* Yêu cầu chạy **SharpHound**, điều này khá “ồn” (noisy) và có thể bị phát hiện bởi các hệ thống **AV hoặc EDR**

<figure><img src="../../.gitbook/assets/image (938).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (939).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (940).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (941).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (942).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (945).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (946).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (947).png" alt=""><figcaption></figcaption></figure>

## Conclusion

Enumerating AD là một nhiệm vụ rất lớn. Việc **AD enumeration** đúng cách là cần thiết để hiểu rõ hơn cấu trúc của domain và xác định các **attack paths** có thể khai thác nhằm thực hiện **privilege escalation** hoặc **lateral movement**.

Additional Enumeration Techniques

Trong network này, chúng ta đã đề cập một số kỹ thuật có thể dùng để enumerate AD. Đây không phải là danh sách đầy đủ. Một số kỹ thuật khác cũng quan trọng bao gồm:

**LDAP enumeration**:\
Bất kỳ cặp **AD credentials** hợp lệ nào cũng có thể **bind** vào giao diện **LDAP** của **Domain Controller**. Điều này cho phép thực hiện các truy vấn **LDAP search** để enumerate thông tin về các **AD objects** trong domain.

**PowerView**:\
**PowerView** là một **recon script** thuộc dự án **PowerSploit**. Mặc dù dự án này không còn được maintain, PowerView vẫn rất hữu ích để thực hiện **semi-manual AD enumeration** khi cần.

**Windows Management Instrumentation (WMI)**:\
**WMI** có thể được dùng để thu thập thông tin từ các **Windows hosts**. Nó có provider tên **root\directory\ldap**, có thể dùng để tương tác với **AD** và thực hiện enumeration thông qua **PowerShell**.

Cũng cần lưu ý rằng phần này tập trung vào việc enumerate toàn bộ cấu trúc của **AD domain**, thay vì chỉ tập trung vào việc tìm các **misconfigurations** hoặc **weaknesses**. Việc enumeration để tìm lỗ hổng như **insecure shares** hoặc các lỗi trong **tiering model** sẽ được đề cập ở các phần sau.

Mitigations

**AD enumeration** rất khó để phòng thủ vì nhiều kỹ thuật giống với **normal network traffic**, khiến việc phân biệt giữa hành vi hợp lệ và hành vi độc hại trở nên khó khăn. Tuy nhiên vẫn có một số cách để phát hiện:

* Phát hiện các **LogOn events** bất thường (ví dụ: **SharpHound** tạo ra rất nhiều LogOn events từ một **single account**)
* Sử dụng **signature-based detection** cho các tool như **SharpHound binaries** và **AD-RSAT tools**
* Giám sát việc sử dụng **Command Prompt** và **PowerShell** để phát hiện hành vi enumeration không hợp lệ

Ngoài ra, **blue team** cũng có thể chủ động sử dụng các kỹ thuật enumeration này để tìm các **misconfigurations** trong cấu trúc AD. Nếu các misconfigurations được khắc phục, ngay cả khi attacker thực hiện enumeration, họ cũng khó tìm được các đường **privilege escalation** hoặc **lateral movement** có thể khai thác.

