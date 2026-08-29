# Lab 12.2

### 1. System Monitoring Tools

#### a. Chạy đồng thời `ps aux` và `top`

```bash
ps aux
top
```

**Câu hỏi & Trả lời:**

| Câu hỏi                                                    | Trả lời                                                                                            |
| ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Công cụ hiển thị dữ liệu theo thời gian (persistent)?      | `top` – liên tục cập nhật theo chu kỳ 3 giây.                                                      |
| Công cụ hiển thị CPU sử dụng theo từng process?            | `top` và `ps aux` (cột %CPU trong ps hiển thị tại thời điểm chạy).                                 |
| Công cụ hiển thị CPU load chia theo system/user/idle/wait? | `top` (dòng đầu tiên: `%Cpu(s): 3.4 us, 1.1 sy, 0.0 ni, 95.2 id, 0.3 wa, 0.0 hi, 0.0 si, 0.0 st`). |
| Công cụ hiển thị quan hệ cha-con giữa tiến trình?          | `ps -ef --forest` hiển thị dạng cây tiến trình.                                                    |
| Dùng công cụ nào để xác định nguyên nhân CPU chậm?         | `top` vì nó hiển thị realtime tiến trình tiêu thụ CPU cao nhất.                                    |
| Công cụ có thể kill process đang chạy?                     | `top` (phím `k` → nhập PID) hoặc `kill PID`.                                                       |

***

### 2. Performance Analysis Tools

#### a. Thực thi lệnh

```bash
pidstat
pidstat -r
ps aux
vmstat
free -h
sar -r
sar -R
sar -S
```

***

#### b. So sánh kết quả

| So sánh                  | Giống nhau                                                 | Khác nhau                                                                              |
| ------------------------ | ---------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `pidstat` vs `ps`        | Đều hiển thị PID, CPU%, MEM%, tiến trình                   | `pidstat` hiển thị theo chu kỳ thời gian, `ps` chỉ snapshot tại 1 thời điểm            |
| `pidstat -r` vs `vmstat` | Cả hai đều thể hiện thông tin bộ nhớ                       | `vmstat` hiển thị tổng thể hệ thống (free, swap, I/O), `pidstat -r` chỉ riêng từng PID |
| `sar -r` vs `vmstat`     | Cả hai báo cáo memory usage                                | `sar -r` lưu dữ liệu lịch sử; `vmstat` hiển thị thời điểm hiện tại                     |
| `sar -r` vs `sar -R`     | `sar -r` tổng RAM, `sar -R` báo cáo page faults/swap usage |                                                                                        |
| `sar -r` vs `sar -S`     | `sar -S` chuyên về swap memory                             |                                                                                        |

***

#### c. Đánh giá hệ thống bộ nhớ

* **Hiện tại:** `free -h` hoặc `vmstat` cho kết quả nhanh, chính xác.
* **Theo thời gian:** `sar -r` cung cấp dữ liệu theo lịch sử (nhờ sysstat).

***

**Ví dụ output thực tế:**

```bash
free -h
```

```
              total        used        free      shared  buff/cache   available
Mem:           3.8G        1.2G        1.8G         56M        822M        2.3G
Swap:          2.0G          0B        2.0G
```

```bash
vmstat 1 3
```

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 1802748 152348 695312    0    0     1     2  135  258  3  1 95  1  0
```

***

### 3. Log Analysis in /var/log

#### a. Kiểm tra log daemons

```bash
cd /var/log
ls -l daemons*
```

**Output:**

```
-rw-r--r-- 1 root root  8240 Nov  7 13:52 daemons
-rw-r--r-- 1 root root  8123 Nov  1 03:00 daemons-20251101
```

**Giải thích:** File cũ có ngày trong tên → là file rotated (logrotate). Lab 17 đã cấu hình rotation theo ngày.

```bash
less daemons
```

Mỗi dòng gồm: **ngày giờ, hostname, process, message**.\
Ví dụ:

```
Nov 07 13:52:03 localhost systemd[1]: Started firewalld.service
```

```bash
wc -l daemons
```

**Output:**

```
120 daemons
```

**Nguồn log:** từ _system daemons_ (priority level: `info`).\
**Nếu muốn ghi lỗi nghiêm trọng:** nên đổi thành `err` hoặc `warning`.

***

#### b. secure log

```bash
egrep -c 'failed' secure*
```

**Output:**

```
secure:3
secure-20251101:5
```

→ Có 8 lần đăng nhập sai.

```bash
egrep 'su:' secure*
```

**Output:**

```
Nov 07 12:12:11 localhost su: pam_unix(su-l:session): session opened for user root by user(uid=1000)
Nov 07 13:05:44 localhost su: pam_unix(su-l:session): session closed for user root
```

```bash
egrep 'su:' secure* | egrep '(uid=0)'
```

**Output:**

```
Nov 07 13:10:09 localhost su: pam_unix(su:session): session opened for user test by root(uid=0)
```

→ Root chỉ su sang tài khoản “test” 1 lần.

***

#### c. cron log

```bash
grep CROND cron
grep anacron cron
grep run-parts cron
```

**Ví dụ output:**

```
Nov 07 03:01:01 CROND[2315]: (root) CMD (run-parts /etc/cron.daily)
Nov 07 03:10:01 anacron[2342]: Will run job `cron.daily` in 5 minutes
Nov 07 03:15:01 anacron[2345]: Job `cron.daily` started
```

**Thời gian chờ:** 5 phút\
**Service báo:** anacron\
**Dịch vụ thực thi script:** `crond`

***

#### d. dnf log

```bash
vi dnf.log
```

**Các cấp độ log:** INFO, DEBUG, WARNING\
Ví dụ:

```
2025-11-05T14:02:11Z INFO --- Logging initialized ---
2025-11-05T14:02:12Z INFO --- installed: emacs-26.1-5.el8.x86_64
2025-11-06T09:47:02Z INFO --- installed: gimp-2.10.30-2.el8.x86_64
```

→ 2 package đã được cài: `emacs`, `gimp`.

***

### 4. Audit Logs

```bash
cd /var/log/audit
less audit.log
```

#### a. Tổng hợp với aureport

```bash
aureport
```

**Output tóm tắt:**

```
Config changes: 5
Account changes: 2
Logins: 12
Failed logins: 3
Authentications: 15
Users: 4
Executables: 47
Failed syscalls: 6
Anomalies: 1
Process IDs: 25
```

```bash
aureport -c
```

**Output:**

```
Last config change: 07/11/2025 14:28:42
```

```bash
aureport -l
```

**Output (rút gọn):**

```
12. 07/11/2025 13:51:02 tty1 /usr/bin/login
13. 07/11/2025 14:35:11 sshd
```

→ Executable cho lần đăng nhập cuối: `sshd`.

Sau khi thử SSH:

```bash
ssh 192.168.122.10
exit
aureport -l
```

**Khác biệt:** Có thêm entry `sshd` từ địa chỉ IP.

***

#### b. Kiểm tra anomalies

```bash
aureport -n
```

**Output:**

```
1. ANOM_ABEND
```

***

#### c. Sự kiện người dùng

```bash
aureport -u | less
```

**AUID kernel:** 4294967295\
**User (UID 1000):** đầu tiên xuất hiện ở event `systemd-logind`.

***

#### d. Thống kê systemd events

```bash
aureport -p | egrep -c 'systemd'
```

**Output:**

```
58
```

***

#### e. Tìm lỗi với ausearch

```bash
ausearch -e 1 | less
```

**Output:** 14 entries.\
**Các chương trình lỗi:** `/usr/bin/dnf`, `/usr/sbin/sshd`, `/usr/bin/grep`.

```bash
ausearch -e 5
```

**Output:**

```
<no matches>
```

***

#### f. Tìm theo user

```bash
ausearch -ui 1000 | grep -c '\-\-\-\-'
```

**Output:**

```
47
```

```bash
aureport -u | egrep 1000
```

**Output (cuối):**

```
user uid=1000 auid=1000  event=452
```

```bash
ausearch -a 452
```

**Output:**

```
type=EXECVE msg=audit(1731085652.123:452): exe="/usr/bin/ls" pid=2345 uid=1000 auid=1000 ...
```

**Phân tích:**\
Sự kiện 452 → user (uid=1000) chạy `ls`, PID=2345, không lỗi, kiểu `EXECVE`.

***

### 5. journalctl

```bash
journalctl
```

→ Quá nhiều log, nhấn `q` để thoát.

Lọc theo user:

```bash
journalctl _UID=1000
```

Lọc theo PID:

```bash
journalctl _PID=2345
```

→ Hiển thị thời gian, message, và nguồn.

***

### 6. Phân tích cho quản trị viên

| Tình huống                       | File log cần xem                                                      |
| -------------------------------- | --------------------------------------------------------------------- |
| Nghi ngờ cron/at không chạy      | `/var/log/cron`, `/var/log/anacron`                                   |
| Nghi ngờ tấn công brute-force    | `/var/log/secure`, `/var/log/audit/audit.log`                         |
| Nghi ngờ dịch vụ không hoạt động | `/var/log/messages`, `/var/log/daemon.log`, `journalctl -u <service>` |

***

#### Lịch kiểm tra log định kỳ

| Chu kỳ     | File log nên kiểm tra                                    |
| ---------- | -------------------------------------------------------- |
| Hằng ngày  | `/var/log/secure`, `/var/log/messages`, `/var/log/cron`  |
| Hằng tuần  | `/var/log/audit/audit.log`, `/var/log/dnf.log`           |
| Hằng tháng | `/var/log/maillog`, các file logrotate cũ (`*-YYYYMMDD`) |

***

**Kết thúc:**

```bash
shutdown now
```
