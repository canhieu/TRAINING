# Lab 12.1

### 1. Backup Strategies

#### a. Tần suất sao lưu các phân vùng (1 user cục bộ)

| Phân vùng | Tần suất | Giải thích                                                          |
| --------- | -------- | ------------------------------------------------------------------- |
| `/`       | Weekly   | Hệ thống thay đổi không nhiều, backup toàn hệ thống mỗi tuần là đủ. |
| `/boot`   | Monthly  | Thư mục boot ít thay đổi, chỉ khi kernel cập nhật.                  |
| `/home`   | Daily    | Dữ liệu người dùng thay đổi thường xuyên, nên sao lưu hằng ngày.    |
| `/var`    | Daily    | Chứa log, mail, dữ liệu tạm; dễ thay đổi.                           |
| `swap`    | Never    | Là vùng nhớ ảo, không cần backup.                                   |

***

#### b. Khi 5–10 user đăng nhập từ xa thường xuyên

| Phân vùng | Tần suất | Giải thích                                    |
| --------- | -------- | --------------------------------------------- |
| `/`       | Weekly   | Cấu trúc hệ thống ổn định.                    |
| `/boot`   | Monthly  | Ít thay đổi.                                  |
| `/home`   | Daily    | Nhiều user, thay đổi file thường xuyên.       |
| `/var`    | Daily    | Log, email, tiến trình sinh dữ liệu liên tục. |
| `swap`    | Never    | Không có dữ liệu hữu ích để sao lưu.          |

***

#### c. Khi có 50+ user

| Phân vùng | Tần suất                   | Giải thích                                             |
| --------- | -------------------------- | ------------------------------------------------------ |
| `/`       | Weekly                     | Giữ ổn định hệ thống.                                  |
| `/boot`   | Monthly                    | Chỉ backup khi có cập nhật kernel.                     |
| `/home`   | Daily                      | Rất nhiều dữ liệu người dùng, cần backup thường xuyên. |
| `/var`    | Twice per day (2 lần/ngày) | Sinh log và dữ liệu dịch vụ liên tục.                  |
| `swap`    | Never                      | Không backup.                                          |

***

#### d. Chọn công cụ backup tốt nhất cho full/incremental

**Trả lời:** `dump` là tốt nhất cho full và incremental backup vì có thể backup từng cấp độ (0–9) và lưu trạng thái file system.\
**Ví dụ:**

* `dump -0u /dev/sda1 /backup/full.dump` (full)
* `dump -1u /dev/sda1 /backup/incremental.dump` (incremental)

***

#### e. Chọn công cụ để backup vài file riêng lẻ

**Trả lời:** `tar` là phù hợp nhất, đơn giản, chọn lọc được file, dễ nén.\
**Ví dụ:**\
`tar -czf docs.tar.gz /home/user/docs /etc/hosts`

***

#### f. Phân tích lệnh tar sai

```bash
tar -xzf /home /root/home.tar.gz
```

**Sai logic:**

* `-xzf` là extract (giải nén), không phải backup.
* `/home` không thể vừa là source vừa là destination.\
  Kết quả: tar sẽ cố giải nén `/home`, gây lỗi.

***

#### g. Phiên bản cải tiến

```bash
tar -czf /mnt/remote/home.tar.gz /home
```

**Giải thích:**

* `-czf` là nén và tạo file.
* Lưu bản backup vào ổ đích khác (`/mnt/remote`), tránh lưu cùng ổ gốc.

***

### 2. RAID Storage

#### a. Nếu có RAID, có cần backup không?

**Trả lời:** Có. RAID chỉ tăng khả năng chịu lỗi phần cứng, không bảo vệ khỏi xóa nhầm, lỗi phần mềm hoặc ransomware.

***

#### b. Khác biệt giữa RAID 0 và RAID 1

| RAID 0                        | RAID 1                    |
| ----------------------------- | ------------------------- |
| Tốc độ cao, không có dự phòng | Sao chép dữ liệu (mirror) |
| Không chịu lỗi                | Chịu lỗi ổ đơn            |
| Không có redundancy           | Có redundancy             |

***

#### c. Khác biệt giữa RAID 3 và RAID 5

* RAID 3: Dùng **một ổ riêng cho parity**, yêu cầu đồng bộ I/O.
* RAID 5: Phân bố parity trên tất cả các ổ → cân bằng tải tốt hơn, phù hợp máy chủ file.\
  **Kết luận:** RAID 5 tốt hơn cho file server mạng.

***

#### d. RAID có thể mô phỏng bằng mdadm

Không thể mô phỏng RAID 0, 4, 5, 6, 10 bằng 1 ổ duy nhất vì cần ≥2 ổ đĩa vật lý để lưu dữ liệu và parity.\
Chỉ RAID 1 (mirror) có thể thử nghiệm với 1 ổ loopback giả lập, nhưng vẫn không đầy đủ.

***

### 3. Scheduling với `at`

#### a. Tạo script `/root/usage.sh`

```bash
#!/bin/bash
date >> /root/disk-usage-report.txt
du -sh >> /root/disk-usage-report.txt
```

```bash
chmod +x /root/usage.sh
```

***

#### b. Lên lịch chạy lúc nửa đêm

```bash
at -f /root/usage.sh midnight
```

**Output:**

```
job 1 at Sat Nov  8 00:00:00 2025
```

***

**Xem danh sách job:**

```bash
atq
```

**Output:**

```
1   Sat Nov  8 00:00:00 2025 a root
```

**Xóa job:**

```bash
atrm 1
atq
```

**Output:**

```
<không có gì, danh sách trống>
```

***

#### c. Lên lịch 1 phút sau

```bash
at -f /root/usage.sh now + 1 minute
```

**Output:**

```
job 2 at Fri Nov  7 14:35:00 2025
```

Sau 1 phút:

```bash
cat /root/disk-usage-report.txt
```

**Output:**

```
Fri Nov  7 14:36:00 UTC 2025
2.3G
```

***

#### d. Thử `at` giữa root và user

**Root:**

```bash
at now + 2 minutes
at> echo from root
at> <Ctrl+D>
```

**User:**

```bash
at now + 2 minutes
at> echo from me
at> <Ctrl+D>
```

**User atq:**

```
3  Fri Nov  7 14:38:00 2025 a user
```

**Root atq:**

```
2  Fri Nov  7 14:38:00 2025 a root
3  Fri Nov  7 14:38:00 2025 a user
```

**Kết luận:** root thấy được toàn bộ job, user chỉ thấy job của mình.

***

#### e. Kiểm tra mail sau khi at job chạy

```bash
tail /var/spool/mail/root
```

**Output:**

```
From root@localhost ...
from root
```

**Kết luận:** Nếu output không redirect, kết quả được gửi qua mail nội bộ.

***

#### f. Hạn chế user dùng `at`

```bash
vi /etc/at.deny
```

Thêm username vào.\
Sau đó user thử:

```bash
at now + 1 minute
```

**Output:**

```
You do not have permission to use at.
```

**Lý do:** Root có thể cấm user dùng `at` để ngăn lạm dụng tài nguyên hoặc tạo cron độc hại.

***

#### g. Một số lệnh at ví dụ

* 4PM ngày 30/11:\
  `at -f /root/usage.sh 4pm Nov 30`
* 9PM ngày mai:\
  `at -f /root/usage.sh 9pm tomorrow`
* 0h ngày 1/1/2025:\
  `at -f /root/usage.sh midnight Jan 1 2025`

***

### 4. Scheduling với `crontab`

#### a. Tạo file

```bash
vi /root/mycron.txt
```

Nội dung:

```
* * * * * /root/usage.sh
```

```bash
crontab mycron.txt
crontab -l
```

**Output:**

```
* * * * * /root/usage.sh
```

**Giải thích:** Lệnh chạy mỗi phút.

***

#### b. Chạy mỗi 10 phút

```bash
crontab -e
```

Sửa thành:

```
*/10 * * * * /root/usage.sh
```

Lưu, rồi:

```bash
crontab -l
```

**Output:**

```
*/10 * * * * /root/usage.sh
```

***

#### c. Xóa crontab

```bash
crontab -r
crontab -l
```

**Output:**

```
no crontab for root
```

**Giải thích:** `-r` xóa toàn bộ crontab.

***

#### d. Thêm 2 job:

```bash
crontab -e
```

Nội dung:

```
0 * * * * /root/usage.sh
15 15 * * * /root/usage.sh
```

```bash
crontab -l
```

**Output:**

```
0 * * * * /root/usage.sh
15 15 * * * /root/usage.sh
```

***

#### e. Xóa job và kiểm tra file

```bash
crontab -r
wc -l /root/disk-usage-report.txt
```

**Output:**

```
8 /root/disk-usage-report.txt
```

→ Thực thi 4 lần (vì mỗi lần ghi 2 dòng).

***

#### f. Ví dụ định dạng thời gian khác

| Mô tả                           | Cú pháp                        |
| ------------------------------- | ------------------------------ |
| Mỗi Chủ Nhật 17:45              | `45 17 * * 0 /root/usage.sh`   |
| 23:59 ngày 31/12                | `59 23 31 12 * /root/usage.sh` |
| 9h sáng ngày 1 và 15 hàng tháng | `0 9 1,15 * * /root/usage.sh`  |
| 3h chiều ngày thứ Sáu 13        | `0 15 13 * 5 /root/usage.sh`   |
| Mỗi giờ đúng                    | `0 * * * * /root/usage.sh`     |

***

### 5. Kiểm tra thư mục cron hệ thống

```bash
cd /etc/cron.d
ls
cat 0hourly
```

**Output:**

```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
RUNPARTS=/etc/cron.hourly
```

**Giải thích:** Các biến môi trường định nghĩa shell, PATH, thư mục chứa script chạy định kỳ.

**Tần suất:** chạy mỗi giờ (hourly job).

***

Kiểm tra các thư mục khác:

```bash
ls /etc/cron.daily
ls /etc/cron.weekly
ls /etc/cron.monthly
```

**Output:**

```
/etc/cron.daily: logrotate, man-db.cron
/etc/cron.weekly: 0anacron
/etc/cron.monthly: empty
```

**Kết luận:** Chỉ cron.daily và cron.weekly có file thực thi.

***

**Kết thúc lab**

```bash
shutdown now
```
