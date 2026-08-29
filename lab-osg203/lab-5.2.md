# Lab 5.2

Tải file&#x20;

<figure><img src="../.gitbook/assets/image (585).png" alt=""><figcaption></figcaption></figure>

## Step 1 — sed + regex

**a–c)**

```bash
sed 's/,//' sales.txt
```

**d) Giải thích:** `s/,//` là thay thế **một** dấu phẩy đầu tiên tìm thấy trên **mỗi dòng** bằng rỗng → nên chỉ mất 1 dấu phẩy/ dòng.

**e) Bỏ mọi dấu phẩy:** thêm cờ `g` (global)

```bash
sed 's/,//g' sales.txt
```

**f) Xóa tháng (Jan|Feb|Mar|Apr):**

* GNU sed (dùng `-E` cho ERE)

```bash
sed -E 's/(Jan|Feb|Mar|Apr)//' sales.txt
```

* Hoặc không `-E` (BRE):

```bash
sed 's/\(Jan\|Feb\|Mar\|Apr\)//' sales.txt
```

**g) Vì sao không cần `g`?** Mỗi dòng chỉ có **một** tháng, nên thay thế lần đầu là đủ.

**h) Thêm `$` trước giá trị Sales (số đầu dòng):**

```bash
sed 's/^[0-9]\+/\$&/' sales.txt
```

Giải thích: `^[0-9]\+` bắt **chuỗi số đầu dòng**; `&` là phần khớp, `\$&` chèn `$` trước nó.

**i) Khi Commission đứng trước Sales thì lệnh trên sai vì** nó luôn lấy **số đầu dòng** (khi đó là commission), không phải Sales.

**j) Chèn dấu phẩy ngăn cách hàng nghìn trong Sales (4–5 chữ số):**

```bash
# Dùng ERE cho gọn
sed -E 's/^([0-9]{1,2})([0-9]{3})\t/\1,\2\t/' sales.txt
```

Giải thích: chia Sales 4–5 chữ số thành 1–2 chữ số + 3 chữ số, chèn dấu phẩy trước 3 chữ số cuối, giữ lại ký tự tab sau trường.

**k) Nếu Sales chỉ 3 chữ số?** Regex trên **không khớp**, nên **không chèn** dấu phẩy (không thay đổi).

**l) Nếu Sales 7 chữ số?** Chỉ chèn **một** dấu phẩy (ví dụ `1234567` → `1234,567`), **không đủ** chuẩn `1,234,567`.

**m) Chuyển các bang cuối dòng (chỉ chữ hoa/ dấu phẩy/ khoảng trắng) thành chữ thường:**

```bash
# GNU sed với ERE:
sed -E 's/[A-Z, ]+$/\L&/' sales.txt
```

Nếu **không dùng `$` chặn cuối dòng**, regex có thể khớp các đoạn chữ hoa khác trong dòng (ví dụ tên viết hoa) và biến chúng thành chữ thường — sai ý.

**n) Đổi định dạng Commission từ `.##` thành `##%` (cách NGHĨ sai thường gặp):**

```bash
sed 's/\./&%/' sales.txt
```

**o) Vì sao sai?** Lệnh trên chỉ thêm `%` **ngay sau dấu chấm đầu tiên** của dòng (có thể không phải chấm của Commission) và **không loại bỏ dấu chấm** trước hai chữ số.

**p) Cách đúng với nhóm bắt (placeholders):** (chỉ đổi chấm **giữa hai tab** của cột commission)

```bash
# BRE (tương thích rộng, dùng \( \) và \{ \})
sed 's/\t\.\([0-9][0-9]\)\t/\t\1%\t/' sales.txt
```

Giải thích: Bắt đúng mẫu `\t.(dd)\t` ở cột commission, thay bằng `\t(dd)%\t`, **loại bỏ dấu chấm**.

> (Nếu bạn muốn mẫu “tổng quát” hơn mà không dựa vào tab, có thể dùng:\
> `sed -E 's/(^|[[:space:]])\.([0-9]{2})([[:space:]]|,|$)/\1\2%\3/' sales.txt`)

```bash
[canhieu@HieuCQHE200637 ~]$ sed 's/,//' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .10             NJ PA
Jan     Cameron         8781    .15             OH KY, IN
Jan     Smith           9814    .12             NY VA, WV, DC
Feb     Barber          19711   .10             NJ OH, KY, IN
Feb     Dean            15831   .16             PA VA, WV, DC
Feb     Smith           7601    .12             NY MA
Mar     Barber          15877   .10             NJ OH, KY, NY
Mar     Smith           20857   .16             PA VA, WV, DC, MA, IN
Apr     Cameron         14863   .15             NJ OH, PA, VA, DC
Apr     Dean            9823    .16             IN MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ sed 's/,//g' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .10             NJ PA
Jan     Cameron         8781    .15             OH KY IN
Jan     Smith           9814    .12             NY VA WV DC
Feb     Barber          19711   .10             NJ OH KY IN
Feb     Dean            15831   .16             PA VA WV DC
Feb     Smith           7601    .12             NY MA
Mar     Barber          15877   .10             NJ OH KY NY
Mar     Smith           20857   .16             PA VA WV DC MA IN
Apr     Cameron         14863   .15             NJ OH PA VA DC
Apr     Dean            9823    .16             IN MA WV KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ sed -E 's/(Jan|Feb|Mar|Apr)//' sales.txt
Month   Salesman        Sales   Commission      Region
        Barber          10381   .10             NJ, PA
        Cameron         8781    .15             OH, KY, IN
        Smith           9814    .12             NY, VA, WV, DC
        Barber          19711   .10             NJ, OH, KY, IN
        Dean            15831   .16             PA, VA, WV, DC
        Smith           7601    .12             NY, MA
        Barber          15877   .10             NJ, OH, KY, NY
        Smith           20857   .16             PA, VA, WV, DC, MA, IN
        Cameron         14863   .15             NJ, OH, PA, VA, DC
        Dean            9823    .16             IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ sed 's/\(Jan\|Feb\|Mar\|Apr\)//' sales.txt
Month   Salesman        Sales   Commission      Region
        Barber          10381   .10             NJ, PA
        Cameron         8781    .15             OH, KY, IN
        Smith           9814    .12             NY, VA, WV, DC
        Barber          19711   .10             NJ, OH, KY, IN
        Dean            15831   .16             PA, VA, WV, DC
        Smith           7601    .12             NY, MA
        Barber          15877   .10             NJ, OH, KY, NY
        Smith           20857   .16             PA, VA, WV, DC, MA, IN
        Cameron         14863   .15             NJ, OH, PA, VA, DC
        Dean            9823    .16             IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ sed 's/^[0-9]\+/\$&/' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .10             NJ, PA
Jan     Cameron         8781    .15             OH, KY, IN
Jan     Smith           9814    .12             NY, VA, WV, DC
Feb     Barber          19711   .10             NJ, OH, KY, IN
Feb     Dean            15831   .16             PA, VA, WV, DC
Feb     Smith           7601    .12             NY, MA
Mar     Barber          15877   .10             NJ, OH, KY, NY
Mar     Smith           20857   .16             PA, VA, WV, DC, MA, IN
Apr     Cameron         14863   .15             NJ, OH, PA, VA, DC
Apr     Dean            9823    .16             IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ # Dùng ERE cho gọn
sed -E 's/^([0-9]{1,2})([0-9]{3})\t/\1,\2\t/' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .10             NJ, PA
Jan     Cameron         8781    .15             OH, KY, IN
Jan     Smith           9814    .12             NY, VA, WV, DC
Feb     Barber          19711   .10             NJ, OH, KY, IN
Feb     Dean            15831   .16             PA, VA, WV, DC
Feb     Smith           7601    .12             NY, MA
Mar     Barber          15877   .10             NJ, OH, KY, NY
Mar     Smith           20857   .16             PA, VA, WV, DC, MA, IN
Apr     Cameron         14863   .15             NJ, OH, PA, VA, DC
Apr     Dean            9823    .16             IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ # GNU sed với ERE:
sed -E 's/[A-Z, ]+$/\L&/' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .10             nj, pa
Jan     Cameron         8781    .15             oh, ky, in
Jan     Smith           9814    .12             ny, va, wv, dc
Feb     Barber          19711   .10             nj, oh, ky, in
Feb     Dean            15831   .16             pa, va, wv, dc
Feb     Smith           7601    .12             ny, ma
Mar     Barber          15877   .10             nj, oh, ky, ny
Mar     Smith           20857   .16             pa, va, wv, dc, ma, in
Apr     Cameron         14863   .15             nj, oh, pa, va, dc
Apr     Dean            9823    .16             in, ma, wv, ky[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ sed 's/\./&%/' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   .%10            NJ, PA
Jan     Cameron         8781    .%15            OH, KY, IN
Jan     Smith           9814    .%12            NY, VA, WV, DC
Feb     Barber          19711   .%10            NJ, OH, KY, IN
Feb     Dean            15831   .%16            PA, VA, WV, DC
Feb     Smith           7601    .%12            NY, MA
Mar     Barber          15877   .%10            NJ, OH, KY, NY
Mar     Smith           20857   .%16            PA, VA, WV, DC, MA, IN
Apr     Cameron         14863   .%15            NJ, OH, PA, VA, DC
Apr     Dean            9823    .%16            IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
[canhieu@HieuCQHE200637 ~]$ # BRE (tương thích rộng, dùng \( \) và \{ \})
sed 's/\t\.\([0-9][0-9]\)\t/\t\1%\t/' sales.txt
Month   Salesman        Sales   Commission      Region
Jan     Barber          10381   10%             NJ, PA
Jan     Cameron         8781    15%             OH, KY, IN
Jan     Smith           9814    12%             NY, VA, WV, DC
Feb     Barber          19711   10%             NJ, OH, KY, IN
Feb     Dean            15831   16%             PA, VA, WV, DC
Feb     Smith           7601    12%             NY, MA
Mar     Barber          15877   10%             NJ, OH, KY, NY
Mar     Smith           20857   16%             PA, VA, WV, DC, MA, IN
Apr     Cameron         14863   15%             NJ, OH, PA, VA, DC
Apr     Dean            9823    16%             IN, MA, WV, KY[canhieu@HieuCQHE200637 ~]$
```



## Step 2 — awk

**a)** Tính monthly earnings = `Sales * Commission`, in `Name Month Amount`:

```bash
awk '{print $2, $1, $3*$4}' sales.txt
```

**b) Vấn đề:**

* In luôn **header** dòng đầu.
* Số tiền **chưa có `$`** và **không định dạng 2 chữ số thập phân**.

**c) Bỏ header, thêm `$`, định dạng 2 số lẻ:**

```bash
awk '/[0-9]/ { printf "%s %s $%.2f\n", $2, $1, $3*$4 }' sales.txt
```

(Mẫu `/[0-9]/` lọc các dòng dữ liệu; `printf` định dạng tiền tệ.)

**d)** Chỉ các dòng có làm việc tại **OH** và **bonus 5%**:

```bash
awk '/(^|,)[[:space:]]*OH([[:space:]]*,|$)/ { printf "%s %s $%.2f\n", $2, $1, ($3*$4)*1.05 }' sales.txt
```

Nếu in **tất cả** nhưng **chỉ cộng bonus khi có OH**:

```bash
awk '{
  amt = $3*$4
  if ($0 ~ /(^|,)[[:space:]]*OH([[:space:]]*,|$)/) amt *= 1.05
  printf "%s %s $%.2f\n", $2, $1, amt
}' sales.txt
```

**e)** Nếu làm việc ở **≥4 bang** thì +10% (dùng `NF`):

```bash
awk '{
  amt = $3*$4
  if (NF >= 8) amt *= 1.10   # 4 trường đầu + >=4 bang → NF>=8
  printf "%s %s $%.2f\n", $2, $1, amt
}' sales.txt
```

(Phương án khác: `if (gsub(/,/, x) >= 3) ...` vì ≥3 dấu phẩy nghĩa là ≥4 bang.)

**f)** Tổng và trung bình thu nhập của **tất cả salesman trong tháng Jan**:

```bash
awk '$1=="Jan" { sum += $3*$4; n++ }
     END { printf "Jan Total: $%.2f\nJan Average: $%.2f\n", sum, (n?sum/n:0) }' sales.txt
```

Cho **Dec** (không có dữ liệu → tổng 0, trung bình 0, tránh chia cho 0):

```bash
awk '$1=="Dec" { sum += $3*$4; n++ }
     END {
       printf "Dec Total: $%.2f\n", sum
       if (n) printf "Dec Average: $%.2f\n", sum/n; else print "Dec Average: $0.00"
     }' sales.txt
```

**g)** Đếm số **regular files** trong `/etc`:

```bash
ls -l /etc | awk '/^-/ {count++} END {print count}'
```

**h)** In **tên file** và cuối cùng là **tổng đếm**:

```bash
ls -l /etc | awk '/^-/ {print $9; count++} END {print count}'
```

(_Lưu ý:_ nếu tên file có khoảng trắng, `$9` không còn đủ; khi cần bền vững hơn: `sub(/^.*: /,"");` trên dòng từ `ls -l` không đơn giản. Với `ls -1` thì dễ: `ls -1 /etc | awk '{print; count++} END {print count}'`.)

**i)** In các mục trong `/etc` có **hard link > 1** (`$2` là link count):

```bash
ls -l /etc | awk '$2 > 1 {print}'
```

**j)** Tổng và trung bình **kích thước file thường** trong `/etc` (`$5` là size):

```bash
ls -l /etc | awk '/^-/ {sum += $5; n++} END {print "Total:", sum, "Average:", (n?sum/n:0)}'
```

**k)** Liệt kê các mục trong `/dev` mà **owner** khác **group** (`$3` vs `$4`):

```bash
ls -l /dev | awk '$3 != $4 {print}'
```

**l)** Tên thiết bị trong `/dev` chứa **sda** hoặc **lp**:

```bash
ls -l /dev | awk '$9 ~ /(sda|lp)/ {print $9}'
```

**m)** Tên thiết bị **không kết thúc bằng chữ số**:

```bash
ls -l /dev | awk '$9 !~ /[0-9]$/ {print $9}'
```

**n)** Tên file trong `/bin` có **tháng KHÔNG phải May**:

```bash
ls -l /bin | awk '$6 != "May" {print $9}'
```

**o)** Tên file trong `/bin` có **setuid (user s) hoặc setgid (group s)**:

```bash
ls -l /bin | awk 'substr($1,4,1) ~ /s/ || substr($1,7,1) ~ /s/ {print $9}'
```

```bash
[canhieu@HieuCQHE200637 ~]$ awk '{print $2, $1, $3*$4}' sales.txt
Salesman Month 0
Barber Jan 1038.1
Cameron Jan 1317.15
Smith Jan 1177.68
Barber Feb 1971.1
Dean Feb 2532.96
Smith Feb 912.12
Barber Mar 1587.7
Smith Mar 3337.12
Cameron Apr 2229.45
Dean Apr 1571.68
[canhieu@HieuCQHE200637 ~]$ awk '/[0-9]/ { printf "%s %s $%.2f\n", $2, $1, $3*$4 }' sales.txt
Barber Jan $1038.10
Cameron Jan $1317.15
Smith Jan $1177.68
Barber Feb $1971.10
Dean Feb $2532.96
Smith Feb $912.12
Barber Mar $1587.70
Smith Mar $3337.12
Cameron Apr $2229.45
Dean Apr $1571.68
[canhieu@HieuCQHE200637 ~]$ awk '/(^|,)[[:space:]]*OH([[:space:]]*,|$)/ { printf "%s %s $%.2f\n", $2, $1, ($3*$4)*1.05 }' sales.txt
Barber Feb $2069.66
Barber Mar $1667.09
Cameron Apr $2340.92
[canhieu@HieuCQHE200637 ~]$ awk '{
  amt = $3*$4
  if ($0 ~ /(^|,)[[:space:]]*OH([[:space:]]*,|$)/) amt *= 1.05
  printf "%s %s $%.2f\n", $2, $1, amt
}' sales.txt
Salesman Month $0.00
Barber Jan $1038.10
Cameron Jan $1317.15
Smith Jan $1177.68
Barber Feb $2069.66
Dean Feb $2532.96
Smith Feb $912.12
Barber Mar $1667.09
Smith Mar $3337.12
Cameron Apr $2340.92
Dean Apr $1571.68
[canhieu@HieuCQHE200637 ~]$ awk '{
  amt = $3*$4
  if (NF >= 8) amt *= 1.10   # 4 trường đầu + >=4 bang → NF>=8
  printf "%s %s $%.2f\n", $2, $1, amt
}' sales.txt
Salesman Month $0.00
Barber Jan $1038.10
Cameron Jan $1317.15
Smith Jan $1295.45
Barber Feb $2168.21
Dean Feb $2786.26
Smith Feb $912.12
Barber Mar $1746.47
Smith Mar $3670.83
Cameron Apr $2452.39
Dean Apr $1728.85
[canhieu@HieuCQHE200637 ~]$ awk '$1=="Jan" { sum += $3*$4; n++ }
     END { printf "Jan Total: $%.2f\nJan Average: $%.2f\n", sum, (n?sum/n:0) }' sales.txt
Jan Total: $3532.93
Jan Average: $1177.64
[canhieu@HieuCQHE200637 ~]$ awk '$1=="Dec" { sum += $3*$4; n++ }
     END {
       printf "Dec Total: $%.2f\n", sum
       if (n) printf "Dec Average: $%.2f\n", sum/n; else print "Dec Average: $0.00"
     }' sales.txt
Dec Total: $0.00
Dec Average: $0.00
[canhieu@HieuCQHE200637 ~]$ ls -l /etc | awk '/^-/ {count++} END {print count}'
104
[canhieu@HieuCQHE200637 ~]$ ls -l /etc | awk '/^-/ {print $9; count++} END {print count}'
adjtime
aliases
anacrontab
anthy-unicode.conf
appstream.conf
asound.conf
at.deny
bashrc
bindresvport.blacklist
brlapi.key
brltty.conf
centos-release
chrony.conf
chrony.keys
cron.deny
crontab
crypttab
csh.cshrc
csh.login
DIR_COLORS
DIR_COLORS.lightbgcolor
dnsmasq.conf
dracut.conf
enscript.cfg
environment
ethertypes
exports
filesystems
fprintd.conf
fstab
fuse.conf
gdbinit
GREP_COLORS
group
group-
gshadow
gshadow-
host.conf
hostname
hosts
idmapd.conf
inittab
inputrc
issue
issue.net
kdump.conf
krb5.conf
ld.so.cache
ld.so.conf
libaudit.conf
libuser.conf
locale.conf
login.defs
logrotate.conf
machine-id
magic
mailcap
makedumpfile.conf.sample
man_db.conf
mime.types
mke2fs.conf
motd
nanorc
netconfig
networks
nfs.conf
nfsmount.conf
nsswitch.conf.bak
papersize
passwd
passwd-
pbm2ppa.conf
pinforc
pnm2ppa.conf
printcap
profile
protocols
request-key.conf
resolv.conf
rpc
rsyncd.conf
rsyslog.conf
services
sestatus.conf
shadow
shadow-
shells
subgid
subgid-
subuid
subuid-
sudo.conf
sudoers
sudo-ldap.conf
sysctl.conf
system-release-cpe
trusted-key.key
updatedb.conf
usb_modeswitch.conf
vconsole.conf
vimrc
virc
wgetrc
xattr.conf
104
[canhieu@HieuCQHE200637 ~]$ ls -l /etc | awk '$2 > 1 {print}'
total 1348
drwxr-xr-x.  3 root root        28 Sep  8 21:30 accountsservice
drwxr-xr-x.  3 root root        65 Sep  8 21:31 alsa
drwxr-xr-x.  2 root root      4096 Sep 25 14:38 alternatives
drwxr-xr-x.  4 root root      4096 Sep 25 14:38 asciidoc
drwxr-x---.  4 root root       100 Sep  8 21:30 audit
drwxr-xr-x.  3 root root      4096 Sep  8 21:36 authselect
drwxr-xr-x.  4 root root        71 Sep  8 21:30 avahi
drwxr-xr-x.  2 root root       124 Sep  8 21:31 bash_completion.d
drwxr-xr-x.  2 root root         6 Aug 15 20:14 binfmt.d
dr-xr-xr-x.  2 root root        23 Sep  8 21:30 bluetooth
drwxr-xr-x.  7 root root        84 Sep  8 21:31 brltty
drwxr-xr-x.  3 root root        36 Sep  8 21:31 chromium
drwxr-xr-x.  2 root root        26 Sep  8 21:30 cifs-utils
drwxr-xr-x.  4 root root        66 Sep  8 21:30 cockpit
drwxr-xr-x.  2 root root        21 Sep  8 21:30 cron.d
drwxr-xr-x.  2 root root         6 Aug 10  2021 cron.daily
drwxr-xr-x.  2 root root        22 Aug 10  2021 cron.hourly
drwxr-xr-x.  2 root root         6 Aug 10  2021 cron.monthly
drwxr-xr-x.  2 root root         6 Aug 10  2021 cron.weekly
drwxr-xr-x.  6 root root        81 Sep  8 21:29 crypto-policies
drwxr-xr-x.  4 root lp        4096 Sep 29 16:45 cups
drwxr-xr-x.  2 root root        34 Sep  8 21:31 cupshelpers
drwxr-xr-x.  4 root root        78 Sep  8 21:30 dbus-1
drwxr-xr-x.  4 root root        31 Sep  8 21:30 dconf
drwxr-xr-x.  2 root root        52 Sep  8 21:30 debuginfod
drwxr-xr-x.  2 root root        33 Sep  8 21:36 default
drwxr-xr-x.  2 root root        40 Sep  8 21:31 depmod.d
drwxr-xr-x.  3 root root        24 Sep  8 21:31 dhcp
drwxr-xr-x.  9 root root       163 Sep  8 21:29 dnf
drwxr-xr-x.  2 root dnsmasq      6 Aug  7 21:40 dnsmasq.d
drwxr-xr-x.  2 root root         6 Aug 18 21:11 dracut.conf.d
drwxr-xr-x.  3 root root        37 Sep  8 21:29 egl
drwxr-xr-x.  2 root root         6 Jul  9 04:36 exports.d
drwxr-xr-x.  3 root root        18 Sep  8 21:31 firefox
drwxr-x---.  8 root root       149 Sep  8 21:31 firewalld
drwxr-xr-x.  3 root root        23 Sep  8 21:31 flatpak
drwxr-xr-x.  3 root root        38 Sep  8 21:30 fonts
drwxr-xr-x.  2 root root        28 Sep  8 21:31 foomatic
drwxr-xr-x.  4 root root        64 Sep  8 21:31 fwupd
drwxr-xr-x.  2 root root         6 Aug  1  2024 gcrypt
drwxr-xr-x.  2 root root         6 Jun  2 23:42 gdbinit.d
drwxr-xr-x.  6 root root       107 Sep  8 21:31 gdm
drwxr-xr-x.  2 root root        26 Sep  8 21:30 geoclue
drwxr-xr-x.  3 root root        26 Sep  8 21:29 glvnd
drwxr-xr-x.  2 root root         6 Apr 26  2023 gnupg
drwxr-xr-x.  4 root root        40 Sep  8 21:29 groff
drwx------.  2 root root      4096 Sep  8 21:32 grub.d
drwxr-xr-x.  3 root root        20 Sep  8 21:29 gss
drwxr-xr-x.  2 root root        79 Sep  8 21:31 gssproxy
drwxr-xr-x.  2 root root        28 Sep  8 21:30 highlight
drwxr-xr-x.  2 root root        24 Sep  8 21:29 hp
drwxr-xr-x.  2 root root        20 Sep  8 21:29 iproute2
drwxr-xr-x.  2 root root        25 Sep  8 21:30 iscsi
drwxr-xr-x.  2 root root        27 Sep  8 21:30 issue.d
drwxr-xr-x.  4 root root        48 Sep 25 14:36 java
drwxr-xr-x.  2 root root         6 Dec 24  2024 jvm
drwxr-xr-x.  2 root root         6 Dec 24  2024 jvm-common
drwxr-xr-x.  4 root root        33 Sep  8 21:31 kdump
drwxr-xr-x.  3 root root        38 Sep  8 21:36 kernel
drwxr-xr-x.  3 root root        17 Sep  8 21:30 keys
drwxr-xr-x.  2 root root         6 Oct 17  2022 keyutils
drwxr-xr-x.  2 root root        83 Sep  8 21:31 krb5.conf.d
drwxr-xr-x.  2 root root        66 Sep 25 14:36 ld.so.conf.d
drwxr-xr-x.  3 root root        20 Sep  8 21:30 libblockdev
drwxr-xr-x.  2 root root      4096 Sep  8 21:30 libibverbs.d
drwxr-xr-x.  2 root root        35 Sep  8 21:29 libnl
drwxr-xr-x.  2 root root         6 Aug 10  2021 libpaper.d
drwxr-xr-x.  6 root root      4096 Sep  8 21:30 libreport
drwxr-xr-x.  2 root root        62 Sep  8 21:29 libssh
drwxr-xr-x.  2 root root      4096 Sep  8 21:31 logrotate.d
drwxr-xr-x.  3 root root        43 Sep  8 21:31 lsm
drwxr-xr-x.  7 root root       115 Sep  8 21:30 lvm
drwxr-xr-x.  3 root root        41 Sep  8 21:31 mcelog
drwxr-xr-x.  3 root root        32 Sep  8 21:31 microcode_ctl
drwxr-xr-x.  2 root root        72 Sep  8 21:31 modprobe.d
drwxr-xr-x.  2 root root         6 Aug 15 20:14 modules-load.d
drwxr-xr-x.  2 root root        21 Sep  8 21:30 motd.d
drwxr-xr-x.  2 root root         6 Jul 15 07:00 multipath
drwxr-xr-x.  7 root root       134 Sep  8 21:30 NetworkManager
drwx------.  3 root root        66 Sep  8 21:30 nftables
drwxr-xr-x.  2 root root        57 Sep  8 21:31 nvme
drwxr-xr-x.  3 root root        36 Sep  8 21:29 openldap
drwxr-xr-x.  3 root root        20 Sep  8 21:31 opt
drwxr-xr-x.  3 root root        23 Sep  8 21:30 ostree
drwxr-xr-x.  2 root root        76 Sep  8 21:31 PackageKit
drwxr-xr-x.  2 root root      4096 Sep  8 21:36 pam.d
drwxr-xr-x.  2 root root        33 Sep 25 14:38 pesign
drwxr-xr-x.  3 root root        21 Sep  8 21:29 pkcs11
drwxr-xr-x.  3 root root        27 Sep  8 21:30 pkgconfig
drwxr-xr-x. 12 root root       159 Sep 25 14:38 pki
drwxr-xr-x.  2 root root        28 Sep  8 21:31 plymouth
drwxr-xr-x.  5 root root        52 Sep  8 21:29 pm
drwxr-xr-x.  5 root root        72 Sep  8 21:30 polkit-1
drwxr-xr-x.  2 root root        25 Sep 25 14:38 popt.d
drwxr-xr-x.  2 root root      4096 Sep  8 21:31 profile.d
drwxr-xr-x.  2 root root        25 Sep  8 21:30 pulse
drwxr-xr-x.  3 root root        50 Sep  8 21:31 qemu-ga
drwxr-xr-x.  3 root root        36 Sep  8 21:30 rc.d
drwxr-xr-x.  2 root root        77 Sep  8 21:31 request-key.d
drwxr-xr-x.  2 root root         6 Aug 21 15:26 rpm
drwxr-xr-x.  2 root root         6 Jul 31 17:45 rsyslog.d
drwxr-xr-x.  2 root root        51 Sep  8 21:30 rwtab.d
drwxr-xr-x.  2 root root        61 Sep  8 21:30 samba
drwxr-xr-x.  3 root root      4096 Sep  8 21:31 sane.d
drwxr-xr-x.  2 root root         6 Sep 13  2022 sasl2
drwxr-xr-x.  7 root root      4096 Sep  8 21:30 security
drwxr-xr-x.  3 root root        57 Sep  8 21:30 selinux
drwxr-xr-x.  2 root root        33 Sep  8 21:30 setroubleshoot
drwxr-xr-x.  3 root root      4096 Sep 25 14:36 sgml
drwxr-xr-x.  3 root root        78 Sep  8 21:29 skel
drwxr-xr-x.  3 root root        74 Sep  8 21:31 smartmontools
drwxr-xr-x.  6 root root        86 Sep  8 21:30 sos
drwxr-xr-x.  4 root root        56 Sep  8 21:30 speech-dispatcher
drwxr-xr-x.  4 root root      4096 Sep  8 21:40 ssh
drwxr-xr-x.  2 root root        77 Sep  8 21:29 ssl
drwx------.  4 sssd sssd        31 Sep  8 21:30 sssd
drwxr-xr-x.  2 root root         6 Jun 25  2024 statetab.d
drwxr-x---.  2 root root         6 Jun 30 19:10 sudoers.d
drwxr-xr-x.  3 root root      4096 Sep  8 21:36 sysconfig
drwxr-xr-x.  2 root root        28 Sep  8 21:30 sysctl.d
drwxr-xr-x.  4 root root       166 Sep  8 21:30 systemd
drwxr-xr-x.  2 root root         6 Jul  1 16:22 terminfo
drwxr-xr-x.  2 root root        40 Sep  8 21:31 thermald
drwxr-xr-x.  2 root root        22 Aug 15 20:14 tmpfiles.d
drwxr-xr-x.  3 root root        51 Sep  8 21:30 tpm2-tss
drwxr-xr-x.  3 root root       176 Sep  8 21:30 tuned
drwxr-xr-x.  4 root root        68 Sep  8 21:40 udev
drwxr-xr-x.  2 root root        60 Sep  8 21:31 udisks2
drwxr-xr-x.  2 root root        25 Sep  8 21:30 UPower
drwxr-xr-x.  4 root root      4096 Sep  8 21:31 vmware-tools
drwxr-xr-x.  5 root root        67 Sep  8 21:30 vulkan
drwxr-xr-x.  6 root root        81 Sep  8 21:30 wireplumber
drwxr-xr-x.  2 root root        33 Sep  8 21:31 wpa_supplicant
drwxr-xr-x.  7 root root       121 Sep  8 21:31 X11
drwxr-xr-x.  8 root root       159 Sep  8 21:30 xdg
drwxr-xr-x.  3 root root        36 Sep  8 21:30 xml
drwxr-xr-x.  2 root root        57 Sep  8 21:31 yum
drwxr-xr-x.  2 root root        51 Jul 11 01:06 yum.repos.d
[canhieu@HieuCQHE200637 ~]$ ls -l /etc | awk '/^-/ {sum += $5; n++} END {print "Total:", sum, "Average:", (n?sum/n:0)}'
Total: 1011487 Average: 9725.84
[canhieu@HieuCQHE200637 ~]$ ls -l /dev | awk '$3 != $4 {print}'
crw--w----. 1 root    tty       5,   1 Sep 29 15:46 console
brw-rw----. 1 root    disk    253,   0 Sep 29 15:46 dm-0
brw-rw----. 1 root    disk    253,   1 Sep 29 15:46 dm-1
crw-rw----. 1 root    video    29,   0 Sep 29 15:46 fb0
crw-rw----. 1 root    disk     10, 237 Sep 29 15:46 loop-control
crw-rw----. 1 root    lp        6,   0 Sep 29 15:46 lp0
crw-rw----. 1 root    lp        6,   1 Sep 29 15:46 lp1
crw-rw----. 1 root    lp        6,   2 Sep 29 15:46 lp2
crw-rw----. 1 root    lp        6,   3 Sep 29 15:46 lp3
crw-r-----. 1 root    kmem      1,   1 Sep 29 15:46 mem
crw-r-----. 1 root    kmem      1,   4 Sep 29 15:46 port
crw-rw-rw-. 1 root    tty       5,   2 Sep 29 17:08 ptmx
brw-rw----. 1 root    disk      8,   0 Sep 29 15:46 sda
brw-rw----. 1 root    disk      8,   1 Sep 29 15:46 sda1
brw-rw----. 1 root    disk      8,   2 Sep 29 15:46 sda2
brw-rw----. 1 root    disk      8,  16 Sep 29 15:46 sdb
crw-rw----+ 1 root    cdrom    21,   0 Sep 29 15:46 sg0
crw-rw----. 1 root    disk     21,   1 Sep 29 15:46 sg1
crw-rw----. 1 root    disk     21,   2 Sep 29 15:46 sg2
brw-rw----+ 1 root    cdrom    11,   0 Sep 29 15:46 sr0
crw-rw-rw-. 1 root    tty       5,   0 Sep 29 16:14 tty
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
crw-rw----. 1 root    dialout   4,  64 Sep 29 15:46 ttyS0
crw-rw----. 1 root    dialout   4,  65 Sep 29 15:46 ttyS1
crw-rw----. 1 root    dialout   4,  66 Sep 29 15:46 ttyS2
crw-rw----. 1 root    dialout   4,  67 Sep 29 15:46 ttyS3
crw-rw----. 1 root    kvm      10, 125 Sep 29 15:46 udmabuf
crw-rw----. 1 root    tty       7,   0 Sep 29 15:46 vcs
crw-rw----. 1 root    tty       7,   1 Sep 29 15:46 vcs1
crw-rw----. 1 root    tty       7,   2 Sep 29 15:46 vcs2
crw-rw----. 1 root    tty       7,   3 Sep 29 15:46 vcs3
crw-rw----. 1 root    tty       7,   4 Sep 29 15:46 vcs4
crw-rw----. 1 root    tty       7,   5 Sep 29 15:46 vcs5
crw-rw----. 1 root    tty       7,   6 Sep 29 15:46 vcs6
crw-rw----. 1 root    tty       7, 128 Sep 29 15:46 vcsa
crw-rw----. 1 root    tty       7, 129 Sep 29 15:46 vcsa1
crw-rw----. 1 root    tty       7, 130 Sep 29 15:46 vcsa2
crw-rw----. 1 root    tty       7, 131 Sep 29 15:46 vcsa3
crw-rw----. 1 root    tty       7, 132 Sep 29 15:46 vcsa4
crw-rw----. 1 root    tty       7, 133 Sep 29 15:46 vcsa5
crw-rw----. 1 root    tty       7, 134 Sep 29 15:46 vcsa6
crw-rw----. 1 root    tty       7,  64 Sep 29 15:46 vcsu
crw-rw----. 1 root    tty       7,  65 Sep 29 15:46 vcsu1
crw-rw----. 1 root    tty       7,  66 Sep 29 15:46 vcsu2
crw-rw----. 1 root    tty       7,  67 Sep 29 15:46 vcsu3
crw-rw----. 1 root    tty       7,  68 Sep 29 15:46 vcsu4
crw-rw----. 1 root    tty       7,  69 Sep 29 15:46 vcsu5
crw-rw----. 1 root    tty       7,  70 Sep 29 15:46 vcsu6
crw-rw-rw-. 1 root    kvm      10, 238 Sep 29 15:46 vhost-net
crw-rw-rw-. 1 root    kvm      10, 241 Sep 29 15:46 vhost-vsock
[canhieu@HieuCQHE200637 ~]$ ls -l /dev | awk '$9 ~ /(sda|lp)/ {print $9}'
[canhieu@HieuCQHE200637 ~]$ ls -l /dev | awk '$9 !~ /[0-9]$/ {print $9}'

block
bsg
bus
cdrom
char
core
cpu
cs_vbox
disk
dma_heap
dri
fd
hugepages
initctl
input
log
mapper
mqueue
net
pts
rtc
shm
snd
stderr
stdin
stdout
vfio
[canhieu@HieuCQHE200637 ~]$ ls -l /bin | awk '$6 != "May" {print $9}'
/bin
[canhieu@HieuCQHE200637 ~]$ ls -l /bin | awk 'substr($1,4,1) ~ /s/ || substr($1,7,1) ~ /s/ {print $9}'
[canhieu@HieuCQHE200637 ~]$
```

