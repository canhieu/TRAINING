# LYKN Corp

<figure><img src="../../.gitbook/assets/image (988).png" alt=""><figcaption></figcaption></figure>

## VN version

### Analyze

theo như mô tả thì ta thấy đây là 1 trang nội bộ của 1 công ty , khi truy cập vào trang web thì nó chỉ có duy nhất giao diện login

<figure><img src="../../.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>

Và lúc nào cũng vậy , bước quan trọng nhất là ta phải recon , vậy nên tôi sẽ tiến hành fuzzing directory

```bash
canhieu@DESKTOP-DBGES7N:~$ ffuf -u http://efb1f662-f86f-4822-a2a6-daf607c3f76b.51.79.140.18.nip.io:8080/FUZZ -w wl.txt -mc 200

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://efb1f662-f86f-4822-a2a6-daf607c3f76b.51.79.140.18.nip.io:8080/FUZZ
 :: Wordlist         : FUZZ: /home/canhieu/wl.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

Backup/                 [Status: 200, Size: 269, Words: 62, Lines: 8, Duration: 71ms]
robots.txt              [Status: 200, Size: 32, Words: 3, Lines: 3, Duration: 102ms]
:: Progress: [4842/4842] :: Job [1/1] :: 410 req/sec :: Duration: [0:00:11] :: Errors: 0 :
```

chúng ta tìm được 2 thông tin khá quan trọng là `Backup/` và `robots.txt`

khi truy cập vào `robots.txt` thì ta có được thông tin ẩn về endpoint `/backup`

<figure><img src="../../.gitbook/assets/image (990).png" alt=""><figcaption></figcaption></figure>

Nhưng khi truy cập vào nó thì ta lại bị `nginx` block

<figure><img src="../../.gitbook/assets/image (991).png" alt=""><figcaption></figcaption></figure>

và đáng mừng thay , trong wordlist của tôi lại chứa `Backup/` và khi fuzzing status trả về là 200

Vậy có nghĩa là ta hoàn toàn có thể bypass bằng cách sử dụng Case sensitive : đó là sử dụng `Backup` thay vì dùng `backup`

và trang web này bị listing directory và ta thấy nó chứa 1 file là `credentials.txt`

<figure><img src="../../.gitbook/assets/image (992).png" alt=""><figcaption></figcaption></figure>

```bash
New Employee Credentials
======================
Username: tuan.nguyen
Password: Welcome123!
```

Sau khi chiếm được tài khoản của `tuan.nguyen` , ta đã truy cập được vào web nội bộ của công ty

<figure><img src="../../.gitbook/assets/image (993).png" alt=""><figcaption></figcaption></figure>

đến bước tiếp theo , ta thấy có 1 mail tới từ `minh.le`

Và khi đọc nội dung mail thì ta cũng không thấy gì quá quan trọng để tiếp tục tấn công

<figure><img src="../../.gitbook/assets/image (994).png" alt=""><figcaption></figcaption></figure>

### Hypotheses

Vậy sẽ ra sao nếu ta liệt kê các tài khoản trên hệ thống , rồi tái sử dụng lại mật khẩu default được cung cấp cho nhân viên mới , vì nhiều trường hợp thực tế , nhân viên họ sẽ không muốn thay đổi mật khẩu vì li do tiện và họ lười tạo mật khẩu mới , và đó chính là điểm yếu chí mạng .

cách ở đây chúng ta dùng sẽ là spraying attack

<figure><img src="../../.gitbook/assets/image (995).png" alt=""><figcaption></figcaption></figure>

nhưng trong tài khoản này ta chỉ liệt kê ra được tài khoản duy nhất là `minh.le`

### Test Hypotheses

```
Username: minh.le
Password: Welcome123!
```

BOOMM! ta đã ATO được thành công account của `minh.le`

<figure><img src="../../.gitbook/assets/image (996).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (998).png" alt=""><figcaption></figcaption></figure>

Và ta đã có được tài khoản của admin

```bash
Username: admin
Password: Adm1n_S3cur3_P@ss_2026
```

<figure><img src="../../.gitbook/assets/image (999).png" alt=""><figcaption></figcaption></figure>

### FLAG

```bash
LYKNCTF{3d9476baaace4250b72eb11971bef250}
```

## English version

### Analyze

Based on the description, we can see that this is an internal company website. When accessing the website, it only shows a login interface.

<figure><img src="../../.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>

And as always, the most important step is reconnaissance, so I started by fuzzing directories.

```bash
canhieu@DESKTOP-DBGES7N:~$ ffuf -u http://efb1f662-f86f-4822-a2a6-daf607c3f76b.51.79.140.18.nip.io:8080/FUZZ -w wl.txt -mc 200

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://efb1f662-f86f-4822-a2a6-daf607c3f76b.51.79.140.18.nip.io:8080/FUZZ
 :: Wordlist         : FUZZ: /home/canhieu/wl.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

Backup/                 [Status: 200, Size: 269, Words: 62, Lines: 8, Duration: 71ms]
robots.txt              [Status: 200, Size: 32, Words: 3, Lines: 3, Duration: 102ms]
:: Progress: [4842/4842] :: Job [1/1] :: 410 req/sec :: Duration: [0:00:11] :: Errors: 0 :
```

We found two very important pieces of information: `Backup/` and `robots.txt`.

When accessing `robots.txt`, we discover a hidden endpoint: `/backup`.

<figure><img src="../../.gitbook/assets/image (990).png" alt=""><figcaption></figcaption></figure>

But when we try to access it, it gets blocked by `nginx`.

<figure><img src="../../.gitbook/assets/image (991).png" alt=""><figcaption></figcaption></figure>

Fortunately, my wordlist contained `Backup/`, and the fuzzing result returned status `200`.

That means we can fully bypass the restriction by abusing case sensitivity: using `Backup` instead of `backup`.

This website also has directory listing enabled, and we can see it contains a file named `credentials.txt`.

<figure><img src="../../.gitbook/assets/image (992).png" alt=""><figcaption></figcaption></figure>

```bash
New Employee Credentials
======================
Username: tuan.nguyen
Password: Welcome123!
```

After compromising the account `tuan.nguyen`, we were able to access the company’s internal web application.

<figure><img src="../../.gitbook/assets/image (993).png" alt=""><figcaption></figcaption></figure>

In the next step, we noticed an email from `minh.le`.

But after reading the email content, we did not find anything particularly important to continue the attack.

<figure><img src="../../.gitbook/assets/image (994).png" alt=""><figcaption></figcaption></figure>

### Hypotheses

So what if we enumerate the accounts in the system, then reuse the default password given to new employees? In many real-world cases, employees do not want to change their password because it is convenient, and they are too lazy to create a new one. That becomes a critical weakness.

The method used here is a spraying attack.

<figure><img src="../../.gitbook/assets/image (995).png" alt=""><figcaption></figcaption></figure>

But from this account, we were only able to enumerate a single account: `minh.le`.

### Test Hypotheses

```bash
Username: minh.le
Password: Welcome123!
```

BOOMM! We successfully performed ATO on the account `minh.le`.

<figure><img src="../../.gitbook/assets/image (996).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (998).png" alt=""><figcaption></figcaption></figure>

And we obtained the admin account credentials.

```bash
Username: admin
Password: Adm1n_S3cur3_P@ss_2026
```

<figure><img src="../../.gitbook/assets/image (999).png" alt=""><figcaption></figcaption></figure>

### FLAG

```bash
LYKNCTF{3d9476baaace4250b72eb11971bef250}
```
