---
hidden: true
---

# note NMAP

## 1) Định nghĩa ngắn

Nmap là một bộ công cụ mã nguồn mở để: discovery (phát hiện host), port scanning (phát hiện port mở), service/version detection (xác định dịch vụ và phiên bản), OS detection (nhận dạng hệ điều hành), và scripting (NSE — Nmap Scripting Engine) để tự động hoá kiểm thử.

***

## 2) Cơ chế hoạt động (high-level)

1. **Host discovery**: xác định host còn sống (ping/ARP/ICMP/TCP probes).
2. **Port scanning**: gửi gói TCP/UDP/… tới port mục tiêu, dựa vào phản hồi (SYN/ACK/RST/ICMP) để quyết định trạng thái port (open/closed/filtered).
3. **Service detection**: kết nối/trao đổi banner hoặc probe đặc thù để nhận service và phiên bản.
4. **OS fingerprinting**: phân tích các trường header TCP/IP (TTL, window size, options, TCP quirks) so sánh với database fingerprint.
5. **NSE (scripts)**: chạy script Lua để kiểm tra lỗ hổng, brute-force, thu thập thông tin nâng cao.
6. **Timing & evasion**: Nmap có profile timing (-T0..-T5) và nhiều tùy chọn tránh phát hiện như fragment, decoy, spoofing, idle scan.

***

## 3) Các chế độ/quy tắc quét chính (những cần biết)

* **TCP SYN scan (-sS)** — “half-open” scan, gửi SYN, nếu nhận SYN/ACK => port open (gửi RST để không hoàn tất handshake). Nhanh & phổ biến.
* **TCP Connect scan (-sT)** — dùng hệ điều hành để mở kết nối TCP đầy đủ (handshake). Dùng khi không có privileges raw-sockets.
* **TCP ACK scan (-sA)** — xác định rule/filtered (dùng để map stateful firewall).
* **TCP FIN / NULL / Xmas scans (-sF, -sN, -sX)** — gửi gói không chuẩn để né một số firewall/IDS; dựa trên RFC behavior để phát hiện closed vs open.
* **UDP scan (-sU)** — gửi UDP probe; chậm và khó chịu do thiếu phản hồi (ICMP unreachable vs no-response).
* **IP Protocol scan (-sO)** — thử xác định các protocol IP (icmp, gre, esp...).
* **SCTP INIT scan (-sY) / SCTP COOKIE-ECHO (-sZ)** — cho SCTP.
* **Ping Scan (-sn)** — chỉ phát hiện host online, không quét port.
* **Version detection (-sV)** — thử probe service để lấy banner/version.
* **OS detection (-O)** — fingerprint OS.
* **Nmap Scripting Engine (--script \<name>)** — chạy script để kiểm tra CVE, brute-force, enumerate.
* **Idle scan (-sI)** — scan gián tiếp qua “zombie” để che giấu nguồn — kỹ thuật nâng cao.
* **Fragmentation (-f)**, **decoy (-D)**, **source port spoofing (--source-port)** — các kỹ thuật né detection/evade.

***

## 4) Các option quan trọng & ví dụ (giải thích từng dòng)

Mỗi ví dụ kèm giải thích từng tùy chọn.

1. Quét nhanh SYN cho một host, tất cả port TCP:

```
nmap -sS -p- 10.0.0.5
```

* `nmap` — chương trình.
* `-sS` — SYN scan (half-open).
* `-p-` — quét tất cả port 1–65535.
* `10.0.0.5` — mục tiêu.

2. Quét UDP + TCP (phổ biến cho kiểm thử):

```
nmap -sS -sU -p T:1-1000,U:1-200 10.0.0.5
```

* `-sU` — UDP scan.
* `-p T:1-1000,U:1-200` — chỉ định phạm vi port TCP/UDP.

3. Quét với phát hiện dịch vụ & OS, lưu output verbose:

```
nmap -sS -sV -O -A -v -oA scan1 10.0.0.5
```

* `-sV` — service/version detection.
* `-O` — OS detection.
* `-A` — bật nhiều tính năng (OS detection, version detection, script, traceroute).
* `-v` — verbose.
* `-oA scan1` — lưu output ở ba định dạng: .nmap, .xml, .gnmap.

4. Sử dụng NSE script cụ thể (ví dụ: kiểm tra SSL):

```
nmap -sV --script ssl-enum-ciphers -p 443 example.com
```

* `--script ssl-enum-ciphers` — chạy script liệt kê cipher TLS/SSL.
* `-p 443` — chỉ port 443.

5. Thời gian và né detection (chậm & frag + decoy):

```
nmap -sS -T2 -f -D RND:3 10.0.0.5
```

* `-T2` — timing polite (chậm, tránh gây tải).
* `-f` — fragment IP packets (một số firewall khó ghép lại).
* `-D RND:3` — dùng 3 decoy (random) để che địa chỉ nguồn.

6. Idle scan (phức tạp, che giấu nguồn):

```
nmap -sI zombie_ip target_ip
```

* `-sI zombie_ip` — dùng zombie (host có predictable IPID) để quét target. Rất khó thực hiện và dễ gây ảnh hưởng.

7. Chỉ ping discover (không quét port):

```
nmap -sn 10.0.0.0/24
```

* `-sn` — ping scan; chỉ tìm host sống.

8. Xuất CSV/grepable:

```
nmap -oG results.gnmap 10.0.0.0/24
```

* `-oG` — xuất dạng grepable.

***

## 5) Detection / IOC (làm thế nào defender phát hiện Nmap)

* **Traffic patterns**: nhiều SYN đến nhiều port trong thời gian ngắn; SYN floods; nhiều probe UDP/ICMP.
* **TTL / TCP option signatures**: fingerprinting có thể lộ Nmap version (một số phiên bản có đặc trưng).
* **IDS/IPS rules**: Snort/Suricata có signature cho SYN scan, NULL/FIN/XMAS, UDP scan, port sweep.
* **Firewall logs**: nhiều kết nối bị block từ cùng nguồn.
* **Rate anomalies**: spike trong connection attempts từ 1 IP.
* **Unexpected traceroutes/ARP**: ARP sweeps trong LAN.

**Ví dụ IOC**:

* Nguồn gửi >100 SYN trong 1 phút đến distinct ports trên 1 host.
* Repeated ICMP echo with varying packet sizes from same IP.
* Multiple TCP packets with unusual flag combinations (XMAS: FIN+URG+PSH).

***

## 6) Mitigations & hardening (phòng chống quét/giảm rủi ro)

1. **Network segmentation** — giới hạn phạm vi quét; minimal exposure.
2. **Firewall policy & rate-limiting** — block/limit connection attempts per source.
3. **Harden services / đóng port không cần thiết** — chỉ mở service cần thiết.
4. **Disable/obfuscate banners** — giảm thông tin trả về cho version detection.
5. **IDS/IPS & honeypots** — phát hiện quét & dụ tấn công.
6. **Egress filtering** — chặn các máy nội bộ quét ra ngoài.
7. **Monitoring & alerts** — cảnh báo khi xuất hiện pattern quét.
8. **Patch management** — fix vuln được phát hiện sau quét.

***

## 7) Cách dùng output Nmap để tiếp hành pentest (cẩn trọng & hợp pháp)

* Dùng kết quả để chọn mục tiêu cho dịch vụ version-specific (vd. tìm version vulnerable).
* Chạy NSE scripts chỉ sau khi được phép.
* Ghi lại output (XML/.nmap) để audit.
* Luôn snapshot VM hoặc test trên môi trường isolated trước khi thực nghiệm khai thác.

***

## 8) Lab practice (3 mức): mục tiêu, setup, bước, criteria & rủi ro/biện pháp an toàn

**Quy tắc chung trước khi làm lab**: tạo 2 VM trong mạng riêng (NAT hoặc host-only), snapshot trước mỗi bài; không quét host ngoài mạng lab; bật Wireshark trên máy defender để quan sát; bật IDS (Snort/Suricata) nếu cần.

### A — Cơ bản

* **Mục tiêu**: Quét port & xác định dịch vụ trên VM mục tiêu.
* **Setup**: Kali VM (attacker) + Ubuntu VM (target) trên network host-only. Snapshot cả 2.
* **Steps**:
  1. `nmap -sn 192.168.56.0/24` — tìm host sống.
  2. `nmap -sS -p1-1024 192.168.56.101` — SYN scan các well-known ports.
  3. `nmap -sV -p22,80,443 192.168.56.101` — lấy banner/version.
* **Criteria**: xác định được list port mở; xác thực service/version.
* **Rủi ro/controls**: snapshot VM; tắt network bridging để tránh lan ra ngoài.

### B — Trung cấp

* **Mục tiêu**: OS fingerprinting + sử dụng NSE để kiểm tra SSL/TLS.
* **Setup**: Thêm một IDS (Snort) VM; web server chạy trên target (apache/nginx) có TLS cấu hình yếu.
* **Steps**:
  1. `nmap -sS -O -p80,443 192.168.56.101 -oA mid_scan` — port + OS.
  2. `nmap --script ssl-enum-ciphers -p443 192.168.56.101` — kiểm tra cipher suites.
  3. Giải thích output và so sánh với CVE/OWASP recommendations.
* **Criteria**: phát hiện OS chính xác trong tập hợp, liệt kê cipher yếu.
* **Rủi ro/controls**: chạy script chỉ trên lab; theo dõi IDS logs.

### C — Nâng cao

* **Mục tiêu**: Evasion & stealth techniques (idle scan, fragmentation, decoy) + parse output tự động.
* **Setup**: Kali (attacker), Ubuntu (target), zombie VM (được cấu hình IPID predictable), IDS. Isolate mạng.
* **Steps**:
  1. `nmap -sI zombie_ip -p80 target_ip` — idle scan. Giải thích limitations (cần zombie phù hợp).
  2. `nmap -sS -f -D decoy1,decoy2,target 192.168.56.101` — fragment + decoy.
  3. `nmap -sV --script=http-vuln* -oX advanced.xml target_ip` — chạy các NSE check vuln cụ thể, lưu xml.
  4. Viết script nhỏ parse XML để extract CVE-related service versions.
* **Criteria**: thực hiện idle scan thành công (phát hiện port open) và so sánh logs IDS (xem độ “stealth”).
* **Rủi ro/controls**: cực kỳ nhạy cảm — làm chỉ trên lab; snapshot; kiểm tra tác động hệ thống.
