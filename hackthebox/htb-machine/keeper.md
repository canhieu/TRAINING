# Keeper

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>



## RECON

### Scan port

<figure><img src="../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

sau khi scan 65k port thì tôi nhận thấy là đang có 2 port đang mở chính là 22 và 80

```bash
nmap -sC -sV -p22,80 10.129.229.41
```

```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: nginx/1.18.0 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   99.86 ms  10.10.14.1
2   100.42 ms 10.129.229.41

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

```

### port 80

Khi truy cập vào web service ở  port 80 thì ta nhận được 1 yêu cầu redirect sang một domain là&#x20;

[tickets.keeper.htb/rt/](http://tickets.keeper.htb/rt/)

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

khi truy cập vào domain đó , thì ta dễ dàng nhận thấy đây là cms request tracker ,ta sẽ thử tiến hành tìm default credential để test thử

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

```
root/password
```

ở tại repo này ta kiếm dc tài khoản mặc định : [https://github.com/bestpractical/rt](https://github.com/bestpractical/rt)

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Khi truy cập vào thì ta thấy rằng là cms này đang có 2 user , 1 tài khoản là mặc định , cái còn lại là của `lnorgaard`

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

thì ta biết được mật khẩu của `lnorgaard`  là `Welcome2023!`&#x20;



### Lateral Movement

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>



```bash
lnorgaard@keeper:~$ cat user.txt
5951c5407c032738b2145842b01e2fe1
```



### Privilege escalation

```bash
lnorgaard@keeper:~$ ls
RT30000.zip  user.txt
lnorgaard@keeper:~$ unzip RT30000.zip 
Archive:  RT30000.zip
  inflating: KeePassDumpFull.dmp     
 extracting: passcodes.kdbx          
```

Và tôi thấy , ko thể đọc 2 file đó theo cách thông thường nên tôi đã đi search&#x20;

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://github.com/vdohney/keepass-password-dumper" %}



<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

passwd : `rødgrød med fløde`

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

ở dòng đầu tiên của note ta chú ý đến `PuTTY-User-Key-File-3: ssh-rsa`

vậy nên ta sẽ sử dụng đến công cụ [https://puttygen.com/](https://puttygen.com/)

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

PuTTYgen là một công cụ tạo khóa dùng để sinh cặp khóa công khai và khóa riêng (public key và private key). PuTTYgen hỗ trợ nhiều thuật toán mật mã khóa công khai, bao gồm cả thuật toán RSA algorithm (Rivest–Shamir–Adleman).

Trong trường hợp này, ta có thể dùng PuTTYgen để tạo SSH private key cho user root.

Ở đây sử dụng các tùy chọn sau:

* `-O` để chỉ định định dạng đầu ra, ở đây là private key theo chuẩn OpenSSH
* `-o` để chỉ định tên file đầu ra

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

