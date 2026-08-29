# Leak Force

<figure><img src="../.gitbook/assets/image (633).png" alt=""><figcaption></figcaption></figure>



## ANALYZE

<figure><img src="../.gitbook/assets/image (634).png" alt=""><figcaption></figcaption></figure>

Khi truy cập vào chall thì ta đã được 1 cái hint vô cùng lớn : IDOR

<figure><img src="../.gitbook/assets/image (635).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (636).png" alt=""><figcaption></figcaption></figure>

Ta tìm thấy 1 param khá là đáng ngờ , ta sẽ đặt giả thiết rằng , sẽ thế nào nếu ta thay đổi giá trị của tham số này ?

<figure><img src="../.gitbook/assets/image (637).png" alt=""><figcaption></figcaption></figure>

và BOOM

<figure><img src="../.gitbook/assets/image (638).png" alt=""><figcaption></figcaption></figure>

ta đã có được 1 số thông tin của admin , vậy giờ làm sao để lấy được flag , ta sẽ tiếp tục khám phá các chức năng còn lại .

<figure><img src="../.gitbook/assets/image (639).png" alt=""><figcaption></figcaption></figure>



ta thấy trong chức năng reset mật khẩu dính 1 lỗi vô cùng nghiêm trọng khi chỉ kiểm tra mỗi id để thay đổi mật khẩu , sẽ vô cùng nguy hiểm vì attacker có thể chiếm dụng bất kì tài khoản nào



## EXPLOIT

<figure><img src="../.gitbook/assets/image (640).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (641).png" alt=""><figcaption></figcaption></figure>





