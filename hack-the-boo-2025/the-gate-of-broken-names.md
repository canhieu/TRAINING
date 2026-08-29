# The Gate of Broken Names

<figure><img src="../.gitbook/assets/image (642).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

<figure><img src="../.gitbook/assets/image (643).png" alt=""><figcaption></figcaption></figure>

đầu tiên khi truy cập vào , ta thấy đây là 1 trang web dạng blog , và có tính năng đăng bài , tạo tài khoản và đăng nhập .



ở giao diện người dùng có các chức năng như : đăng bài , chọn chế độ public or private , chế độ xem bài đăng&#x20;

<figure><img src="../.gitbook/assets/image (644).png" alt=""><figcaption></figcaption></figure>

Tôi sẽ tiến hành test thử bằng cách đăng 1 bài viết bất kì&#x20;

<figure><img src="../.gitbook/assets/image (645).png" alt=""><figcaption></figcaption></figure>



và khi check bên burpsuite , tôi nhận thấy có 1 api dùng để xem các bài đăng  theo dạng :&#x20;

```
/api/notes/{id}
```

<figure><img src="../.gitbook/assets/image (646).png" alt=""><figcaption></figcaption></figure>

và tôi sẽ ra sao nếu tôi thay đổi trường id thành 1 số khác ...



Và BOOM! , ta có thể chắc chắn rằng trang web này đã bị dính lỗ hổng IDOR

<figure><img src="../.gitbook/assets/image (647).png" alt=""><figcaption></figcaption></figure>



## EXPLOIT

Ta sẽ tiến hành conf và brute để tìm flag thôi&#x20;

<figure><img src="../.gitbook/assets/image (650).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (649).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (651).png" alt=""><figcaption></figcaption></figure>



## FLAG

```
HTB{br0k3n_n4m3s_r3v3rs3d_4nd_r3st0r3d_2dde1ba3a5ee8a682341ccde99da9303}
```



