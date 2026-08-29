# Báo cáo IAO nhóm số 2 - IA2006

## SILDE :&#x20;

[https://www.canva.com/design/DAHEBQTd4M4/txnEOhX0BS\_tphrbQ23Orw/edit?utm\_content=DAHEBQTd4M4\&utm\_campaign=designshare\&utm\_medium=link2\&utm\_source=sharebutton](https://www.canva.com/design/DAHEBQTd4M4/txnEOhX0BS_tphrbQ23Orw/edit?utm_content=DAHEBQTd4M4\&utm_campaign=designshare\&utm_medium=link2\&utm_source=sharebutton)

## ANALYZE

Khi truy cập vào trang web ,thì ta thấy đây là 1 trang landing page chứa các bức ảnh và có chức năng duy nhất là view ảnh

<figure><img src="../.gitbook/assets/image (684).png" alt=""><figcaption></figcaption></figure>

Khi ta bấm vào chức năng view ảnh , thì ta phát hiện 1 param là `?file_name=`

<figure><img src="../.gitbook/assets/image (685).png" alt=""><figcaption></figcaption></figure>

Bây giờ ta sẽ đặt giả thuyết là : sẽ ra sao nếu ta thay đổi giá trị đầu vào của param thành path của 1 file tệp khác ?

Và Boom , ta đã có thể đọc được file /etc/passwd một cách trái phép

<figure><img src="../.gitbook/assets/image (686).png" alt=""><figcaption></figcaption></figure>

### RootCause

Ta sẽ tiến hành đọc file `loadImage.php` thông qua document root `var/www/html`

<figure><img src="../.gitbook/assets/image (687).png" alt=""><figcaption></figcaption></figure>



Ta nhận thấy được là biến `$file_name` được truyền vào thông qua param là `file_name`&#x20;

và nó được truyền thẳng vào hàm `readfile($file_path);`&#x20;

và đây là 1 unsafe method , hàm này giúp chúng ta có thể đọc nội dung trong file&#x20;

[https://www.php.net/manual/en/function.readfile.php](https://www.php.net/manual/en/function.readfile.php)

```php
<?php 
$file_name = $_GET['file_name'];
$file_path = '/var/www/html/images/' . $file_name; //untrusted data
if (file_exists($file_path)) {
    header('Content-Type: image/png');
    readfile($file_path); //unsafe method  =>  unsafe method + untrusted data = vulnerability
}
else { // Image file not found
    echo " 404 Not Found";
}

```

## CVSS

* Attack Vector

VÌ trường hợp này là giả lập cho việc tấn công từ bên ngoài Internet nên sẽ là : Network

<figure><img src="../.gitbook/assets/image (688).png" alt=""><figcaption></figcaption></figure>

* Attack Complexity

Vì là lỗ hổng này được khai thác 1 cách dễ dàng mà không phải bypass filter gì cả&#x20;

<figure><img src="../.gitbook/assets/image (689).png" alt=""><figcaption></figcaption></figure>

Nên là sẽ xét ở mức : Low

<figure><img src="../.gitbook/assets/image (690).png" alt=""><figcaption></figcaption></figure>

* Privileges Required

Trang web này không yêu cầu đăng nhập vì là landing page nên có thể truy cập được vào chức năng ngay khi truy cập vào trang web

Nên sẽ xếp vào mức: None

<figure><img src="../.gitbook/assets/image (691).png" alt=""><figcaption></figcaption></figure>

* User Interaction

Chức năng này là attacker sẽ chủ động và không cần đến tương tác người dùng

<figure><img src="../.gitbook/assets/image (692).png" alt=""><figcaption></figcaption></figure>

⇒ None



* Scope

Vì là không tấn công ngang hàng được nên scope sẽ xếp vào Unchaged

<figure><img src="../.gitbook/assets/image (693).png" alt=""><figcaption></figcaption></figure>



* Confidentiality

<figure><img src="../.gitbook/assets/image (694).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (695).png" alt=""><figcaption></figcaption></figure>

Dựa theo 2 ảnh trên , ta có thể thấy rõ là chỉ có thể đọc được 1 số file tệp nhất định , khi truy cập vào những file cần quyền root sẽ bị chặn truy cập

⇒ Ta sẽ xếp nó ở múc Low

<figure><img src="../.gitbook/assets/image (696).png" alt=""><figcaption></figcaption></figure>

* Integrity

Vì là trang web này sử dụng hàm `readfile`  nên không có quyền ghi và sửa nên ⇒ None

<figure><img src="../.gitbook/assets/image (697).png" alt=""><figcaption></figcaption></figure>

* Availability

Vì là trang web này sử dụng hàm `readfile`  nên không có quyền ghi và sửa nên ⇒ None

<figure><img src="../.gitbook/assets/image (698).png" alt=""><figcaption></figcaption></figure>

## Kết quả CVSS

<figure><img src="../.gitbook/assets/image (699).png" alt=""><figcaption></figcaption></figure>
