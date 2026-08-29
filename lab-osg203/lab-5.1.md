# Lab 5.1

## Step 1 — Tải file

<figure><img src="../.gitbook/assets/image (258).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (260).png" alt=""><figcaption></figcaption></figure>

## Step 2 — `equals.txt`

<figure><img src="../.gitbook/assets/image (259).png" alt=""><figcaption></figcaption></figure>

## Step 3 — `names.txt`

1. Có ký hiệu căn hộ (Apt hoặc `#`)

```bash
egrep -n '(Apt\.?|#)\s*[A-Za-z0-9]+' names.txt
```

2. Tên đầu ≤ 4 chữ cái

```bash
egrep -n '^[A-Za-z]{1,4}\b' names.txt
```

3. Có **chữ cái viết tắt** tên đệm (middle initial)

```bash
egrep -n '^[A-Za-z]+ +[A-Z]\.' names.txt
```

4. Địa chỉ nằm trên **đường** (ký hiệu “St” hoặc “St.” — tránh khớp “1st”)

```bash
egrep -n '\b[A-Za-z]+ +St\.?\b' names.txt
```

5. Thành phố bắt đầu bằng L, M, hoặc N (ngay sau dấu phẩy của city)

```bash
egrep -n ',\s+[LMN][A-Za-z]+' names.txt
```

6. **Không có ZIP code** (không kết thúc bằng số)

```bash
egrep -n -v '[0-9]+$' names.txt
```

7. Chứa a, b, c theo thứ tự (không phân biệt hoa/thường, có thể xen kẽ ký tự khác)

```bash
egrep -ni 'a.*b.*c' names.txt
```

8. Số nhà trong địa chỉ **≤ 2 chữ số** (kể cả không có số)

```bash
# Loại bỏ dòng có chuỗi 3+ chữ số trong phần địa chỉ:
egrep -n -v '(^|[ ,])\d{3,}\b' names.txt
```

9. Thành phố (nếu có) dài **≥ 8** chữ cái

```bash
egrep -n ',\s+[A-Za-z]{8,}\b' names.txt
```

10. Thành phố nhiều **từ** (multi-word)

```bash
egrep -n ',\s+[A-Za-z]+(\s+[A-Za-z]+)+' names.txt
```

11. **Số nhà** có **≥ 4 chữ số** (tránh khớp ZIP bằng cách bám theo phần sau dấu phẩy của địa chỉ)

```bash
egrep -n ',\s+[0-9]{4,}\b' names.txt
```

```bash
[canhieu@HieuCQHE200637 ~]$ egrep -n '(Apt\.?|#)\s*[A-Za-z0-9]+' names.txt
3:George Duke, 1234 Inca Roads Apt 6, San Diego, CA 93241
5:Moon Zappa, 6 Gag Lane #3E, San Fernando, CA 91340
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
[canhieu@HieuCQHE200637 ~]$ egrep -n '^[A-Za-z]{1,4}\b' names.txt
4:Ian I. Underwood, 61 Brown St, Chicago, IL 63112
5:Moon Zappa, 6 Gag Lane #3E, San Fernando, CA 91340
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
9:Ruth Underwood, 16161 Mallet St, Mariemont, OH  42522
[canhieu@HieuCQHE200637 ~]$ egrep -n '^[A-Za-z]+ +[A-Z]\.' names.txt
1:Bianca K. Jones, 612 State Ave, Louisville, KY 40568
4:Ian I. Underwood, 61 Brown St, Chicago, IL 63112
8:Pamela C. Barnes, 12 Caster Ln, Los Angeles, CA 90221
11:Terry T. Bozzio, 86 Meadowland Dr Apartment 2E, New York, NY 06386
[canhieu@HieuCQHE200637 ~]$ egrep -n '\b[A-Za-z]+ +St\.?\b' names.txt
4:Ian I. Underwood, 61 Brown St, Chicago, IL 63112
9:Ruth Underwood, 16161 Mallet St, Mariemont, OH  42522
[canhieu@HieuCQHE200637 ~]$ egrep -n -v '[0-9]+$' names.txt
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
12:Thana Harris, 1234 Inca Roads, KY
[canhieu@HieuCQHE200637 ~]$ egrep -ni 'a.*b.*c' names.txt
2:Frank Zappa, 101 Hot Rats Blvd, Los Angeles, CA 90125
4:Ian I. Underwood, 61 Brown St, Chicago, IL 63112
8:Pamela C. Barnes, 12 Caster Ln, Los Angeles, CA 90221
10:Suzie Creamcheese, Lark Towne Ave, San Obispo, CA 91100
[canhieu@HieuCQHE200637 ~]$ # Loại bỏ dòng có chuỗi 3+ chữ số trong phần địa chỉ:
egrep -n -v '(^|[ ,])\d{3,}\b' names.txt
1:Bianca K. Jones, 612 State Ave, Louisville, KY 40568
2:Frank Zappa, 101 Hot Rats Blvd, Los Angeles, CA 90125
3:George Duke, 1234 Inca Roads Apt 6, San Diego, CA 93241
4:Ian I. Underwood, 61 Brown St, Chicago, IL 63112
5:Moon Zappa, 6 Gag Lane #3E, San Fernando, CA 91340
6:Steve Vai, 21 Flex Ct, Mason, OH 45301
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
8:Pamela C. Barnes, 12 Caster Ln, Los Angeles, CA 90221
9:Ruth Underwood, 16161 Mallet St, Mariemont, OH  42522
10:Suzie Creamcheese, Lark Towne Ave, San Obispo, CA 91100
11:Terry T. Bozzio, 86 Meadowland Dr Apartment 2E, New York, NY 06386
12:Thana Harris, 1234 Inca Roads, KY
[canhieu@HieuCQHE200637 ~]$ egrep -n ',\s+[A-Za-z]{8,}\b' names.txt
1:Bianca K. Jones, 612 State Ave, Louisville, KY 40568
9:Ruth Underwood, 16161 Mallet St, Mariemont, OH  42522
[canhieu@HieuCQHE200637 ~]$ egrep -n ',\s+[A-Za-z]+(\s+[A-Za-z]+)+' names.txt
2:Frank Zappa, 101 Hot Rats Blvd, Los Angeles, CA 90125
3:George Duke, 1234 Inca Roads Apt 6, San Diego, CA 93241
5:Moon Zappa, 6 Gag Lane #3E, San Fernando, CA 91340
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
8:Pamela C. Barnes, 12 Caster Ln, Los Angeles, CA 90221
10:Suzie Creamcheese, Lark Towne Ave, San Obispo, CA 91100
11:Terry T. Bozzio, 86 Meadowland Dr Apartment 2E, New York, NY 06386
[canhieu@HieuCQHE200637 ~]$ egrep -n ',\s+[0-9]{4,}\b' names.txt
3:George Duke, 1234 Inca Roads Apt 6, San Diego, CA 93241
7:Mike Keneally, 2242 Sluggo Dr #6, Orange County, CA
9:Ruth Underwood, 16161 Mallet St, Mariemont, OH  42522
12:Thana Harris, 1234 Inca Roads, KY
[canhieu@HieuCQHE200637 ~]$
```



## Step 4 — `sentences.txt`

1. Có **hai nguyên âm liền nhau** trong một từ

```bash
egrep -n '\b\w*[AEIOUaeiou]{2}\w*\b' sentences.txt
```

2. Có **ít nhất 3 từ**

```bash
egrep -n '(\b[[:alpha:]]+\b.*){3,}' sentences.txt
```

3. Có **chính xác 4 từ** (cho phép dấu câu đơn cuối dòng)

```bash
egrep -n '^[[:space:]]*(\b[[:alpha:]]+\b[[:space:]]+){3}\b[[:alpha:]]+\b[[:space:]]*[.?!"]?$' sentences.txt
```

4. Có **dấu câu** xuất hiện **trước ký tự cuối dòng** (có thể vẫn kết thúc bằng dấu câu)

```bash
egrep -n '.*[[:punct:]].+.$' sentences.txt
```

<figure><img src="../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>



## Step 5 — Piping vào `egrep`

1. **Số** file **character device** trong `/dev` (bắt đầu bằng `c`)

```bash
ls -l /dev 2>/dev/null | egrep '^c' | wc -l
```

2. Thiết bị trong `/dev` **ghi được cho group** nhưng **không đọc được cho group**

```bash
# Quyền: vị trí 5-7 là nhóm; cần “-w-” hoặc “-wx”, nói chung là có 'w' nhưng không có 'r'
ls -l /dev 2>/dev/null | egrep '^.[rwx-]{3}-w[-x-]'
```

3. File trong `~Student/DUMMY-DIRECTORY` **group-writable**

```bash
ls -l ~Student/DUMMY-DIRECTORY 2>/dev/null | egrep '^.[rwx-]{3}.[w][rwx-]'
```

4. Tên trong `/proc` là **đúng 4 chữ số** (không tính 5 chữ số); **trả về số lượng**

```bash
ls -d /proc/[0-9]* 2>/dev/null | egrep '/proc/[0-9]{4}$' | wc -l
```

5. Tiến trình đang chạy có **TIME > 0:00**

```bash
ps aux | egrep -v '^\s*USER' | egrep -v ' 0:00 '
```

6. Tiến trình có **STAT bắt đầu bằng S** nhưng **không** phải `Ss`, `Sl`, `S<`, …

```bash
ps -eo stat,pid,user,cmd | egrep -E '^S($|[^sl<])'
```

7. Tiến trình có **owner không phải root cũng không phải bạn**

```bash
ps -eo user,pid,cmd | egrep -v '^\s*USER' | egrep -v "^(root|$(whoami))\b"
```



```bash
[canhieu@HieuCQHE200637 ~]$ ls -l /dev 2>/dev/null | egrep '^c' | wc -l
131
[canhieu@HieuCQHE200637 ~]$ # Quyền: vị trí 5-7 là nhóm; cần “-w-” hoặc “-wx”, nói chung là có 'w' nhưng không có 'r'
ls -l /dev 2>/dev/null | egrep '^.[rwx-]{3}-w[-x-]'
crw--w----. 1 root    tty       5,   1 Sep 29 15:46 console
crw--w----. 1 root    tty       4,   0 Sep 29 15:46 tty0
crw--w----. 1 root    tty       4,   1 Sep 29 15:46 tty1
crw--w----. 1 root    tty       4,  10 Sep 29 15:46 tty10
crw--w----. 1 root    tty       4,  11 Sep 29 15:46 tty11
crw--w----. 1 root    tty       4,  12 Sep 29 15:46 tty12
crw--w----. 1 root    tty       4,  13 Sep 29 15:46 tty13
crw--w----. 1 root    tty       4,  14 Sep 29 15:46 tty14
crw--w----. 1 root    tty       4,  15 Sep 29 15:46 tty15
crw--w----. 1 root    tty       4,  16 Sep 29 15:46 tty16
crw--w----. 1 root    tty       4,  17 Sep 29 15:46 tty17
crw--w----. 1 root    tty       4,  18 Sep 29 15:46 tty18
crw--w----. 1 root    tty       4,  19 Sep 29 15:46 tty19
crw--w----. 1 canhieu tty       4,   2 Sep 29 15:46 tty2
crw--w----. 1 root    tty       4,  20 Sep 29 15:46 tty20
crw--w----. 1 root    tty       4,  21 Sep 29 15:46 tty21
crw--w----. 1 root    tty       4,  22 Sep 29 15:46 tty22
crw--w----. 1 root    tty       4,  23 Sep 29 15:46 tty23
crw--w----. 1 root    tty       4,  24 Sep 29 15:46 tty24
crw--w----. 1 root    tty       4,  25 Sep 29 15:46 tty25
crw--w----. 1 root    tty       4,  26 Sep 29 15:46 tty26
crw--w----. 1 root    tty       4,  27 Sep 29 15:46 tty27
crw--w----. 1 root    tty       4,  28 Sep 29 15:46 tty28
crw--w----. 1 root    tty       4,  29 Sep 29 15:46 tty29
crw--w----. 1 root    tty       4,   3 Sep 29 15:46 tty3
crw--w----. 1 root    tty       4,  30 Sep 29 15:46 tty30
crw--w----. 1 root    tty       4,  31 Sep 29 15:46 tty31
crw--w----. 1 root    tty       4,  32 Sep 29 15:46 tty32
crw--w----. 1 root    tty       4,  33 Sep 29 15:46 tty33
crw--w----. 1 root    tty       4,  34 Sep 29 15:46 tty34
crw--w----. 1 root    tty       4,  35 Sep 29 15:46 tty35
crw--w----. 1 root    tty       4,  36 Sep 29 15:46 tty36
crw--w----. 1 root    tty       4,  37 Sep 29 15:46 tty37
crw--w----. 1 root    tty       4,  38 Sep 29 15:46 tty38
crw--w----. 1 root    tty       4,  39 Sep 29 15:46 tty39
crw--w----. 1 root    tty       4,   4 Sep 29 15:46 tty4
crw--w----. 1 root    tty       4,  40 Sep 29 15:46 tty40
crw--w----. 1 root    tty       4,  41 Sep 29 15:46 tty41
crw--w----. 1 root    tty       4,  42 Sep 29 15:46 tty42
crw--w----. 1 root    tty       4,  43 Sep 29 15:46 tty43
crw--w----. 1 root    tty       4,  44 Sep 29 15:46 tty44
crw--w----. 1 root    tty       4,  45 Sep 29 15:46 tty45
crw--w----. 1 root    tty       4,  46 Sep 29 15:46 tty46
crw--w----. 1 root    tty       4,  47 Sep 29 15:46 tty47
crw--w----. 1 root    tty       4,  48 Sep 29 15:46 tty48
crw--w----. 1 root    tty       4,  49 Sep 29 15:46 tty49
crw--w----. 1 root    tty       4,   5 Sep 29 15:46 tty5
crw--w----. 1 root    tty       4,  50 Sep 29 15:46 tty50
crw--w----. 1 root    tty       4,  51 Sep 29 15:46 tty51
crw--w----. 1 root    tty       4,  52 Sep 29 15:46 tty52
crw--w----. 1 root    tty       4,  53 Sep 29 15:46 tty53
crw--w----. 1 root    tty       4,  54 Sep 29 15:46 tty54
crw--w----. 1 root    tty       4,  55 Sep 29 15:46 tty55
crw--w----. 1 root    tty       4,  56 Sep 29 15:46 tty56
crw--w----. 1 root    tty       4,  57 Sep 29 15:46 tty57
crw--w----. 1 root    tty       4,  58 Sep 29 15:46 tty58
crw--w----. 1 root    tty       4,  59 Sep 29 15:46 tty59
crw--w----. 1 root    tty       4,   6 Sep 29 15:46 tty6
crw--w----. 1 root    tty       4,  60 Sep 29 15:46 tty60
crw--w----. 1 root    tty       4,  61 Sep 29 15:46 tty61
crw--w----. 1 root    tty       4,  62 Sep 29 15:46 tty62
crw--w----. 1 root    tty       4,  63 Sep 29 15:46 tty63
crw--w----. 1 root    tty       4,   7 Sep 29 15:46 tty7
crw--w----. 1 root    tty       4,   8 Sep 29 15:46 tty8
crw--w----. 1 root    tty       4,   9 Sep 29 15:46 tty9
[canhieu@HieuCQHE200637 ~]$ ls -l ~Student/DUMMY-DIRECTORY 2>/dev/null | egrep '^.[rwx-]{3}.[w][rwx-]'
[canhieu@HieuCQHE200637 ~]$ ls -d /proc/[0-9]* 2>/dev/null | egrep '/proc/[0-9]{4}$' | wc -l
87
[canhieu@HieuCQHE200637 ~]$ ps aux | egrep -v '^\s*USER' | egrep -v ' 0:00 '
root           1  0.0  0.4 109196 17368 ?        Ss   15:45   0:01 /usr/lib/systemd/systemd rhgb --switched-root --system --deserialize 31
root          33  0.0  0.0      0     0 ?        SN   15:45   0:01 [khugepaged]
polkitd      756  0.0  0.6 2580288 26552 ?       Ssl  15:46   0:01 /usr/lib/polkit-1/polkitd --no-debug
root         945  0.0  0.7 484492 30336 ?        Ssl  15:46   0:01 /usr/bin/python3 -Es /usr/sbin/tuned -l -P
canhieu     1967  0.1  6.8 3487536 291188 ?      Ssl  15:46   0:04 /usr/bin/gnome-shell
canhieu     2161  0.0  2.5 986116 109612 ?       Sl   15:46   0:02 /usr/bin/gnome-software --gapplication-service
[canhieu@HieuCQHE200637 ~]$ ps -eo stat,pid,user,cmd | egrep -E '^S($|[^sl<])'
STAT     PID USER     CMD
S          2 root     [kthreadd]
S          3 root     [pool_workqueue_]
S         16 root     [ksoftirqd/0]
S         18 root     [rcu_exp_par_gp_]
S         19 root     [rcu_exp_gp_kthr]
S         20 root     [migration/0]
S         21 root     [idle_inject/0]
S         23 root     [cpuhp/0]
S         25 root     [kdevtmpfs]
S         27 root     [kauditd]
S         28 root     [khungtaskd]
S         29 root     [oom_reaper]
S         31 root     [kcompactd0]
SN        32 root     [ksmd]
SN        33 root     [khugepaged]
S         37 root     [irq/9-acpi]
S         42 root     [watchdogd]
S         46 root     [kswapd0]
S        390 root     [scsi_eh_0]
S        392 root     [scsi_eh_1]
S        393 root     [scsi_eh_2]
S        396 root     [scsi_eh_3]
S        422 root     [irq/18-vmwgfx]
S        523 root     [xfsaild/dm-0]
S        710 root     [xfsaild/sda1]
S        751 dbus     dbus-broker --log 4 --controller 9 --machine-id c7efbeb3957747f8ad00dff0e2670553 --max-bytes 536870912 --max-fds 4096 --max-matches 131072 --audit
SNsl     758 rtkit    /usr/libexec/rtkit-daemon
S        775 chrony   /usr/sbin/chronyd -F 2
S        783 avahi    avahi-daemon: chroot helper
SNs      792 root     /usr/sbin/alsactl -s -n 19 -c -E ALSA_CONFIG_PATH=/etc/alsa/alsactl.conf --initfile=/lib/alsa/init/00main rdaemon
S       1886 canhieu  (sd-pam)
S       1904 canhieu  dbus-broker --log 4 --controller 9 --machine-id c7efbeb3957747f8ad00dff0e2670553 --max-bytes 100000000000000 --max-fds 25000000000000 --max-matches 5000000000
S       1989 canhieu  /usr/bin/dbus-broker-launch --config-file=/usr/share/defaults/at-spi2/accessibility.conf --scope user
S       1995 canhieu  dbus-broker --log 4 --controller 9 --machine-id c7efbeb3957747f8ad00dff0e2670553 --max-bytes 100000000000000 --max-fds 6400000 --max-matches 5000000000
S       2612 canhieu  sshd: canhieu@pts/1
[canhieu@HieuCQHE200637 ~]$ ps -eo user,pid,cmd | egrep -v '^\s*USER' | egrep -v "^(root|$(whoami))\b"
rpc          717 /usr/bin/rpcbind -w -f
dbus         749 /usr/bin/dbus-broker-launch --scope system --audit
avahi        750 avahi-daemon: running [HieuCQHE200637.local]
dbus         751 dbus-broker --log 4 --controller 9 --machine-id c7efbeb3957747f8ad00dff0e2670553 --max-bytes 536870912 --max-fds 4096 --max-matches 131072 --audit
libstor+     754 /usr/bin/lsmd -d
polkitd      756 /usr/lib/polkit-1/polkitd --no-debug
rtkit        758 /usr/libexec/rtkit-daemon
chrony       775 /usr/sbin/chronyd -F 2
avahi        783 avahi-daemon: chroot helper
colord      1789 /usr/libexec/colord
```









