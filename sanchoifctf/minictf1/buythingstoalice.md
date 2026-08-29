# BuyThingsToAlice

## ANALYZE

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

Mô tả qua thì đây là 1 trang web mua sắm và sau khi ta mua xong 1 món hàng bất kì thì sẽ được đổi lại điểm thưởng , và ta cần tìm cách để kiếm được 1 lượng điểm thưởng nhất định để có thể đổi được flag



Nghe qua thì ta có thể phỏng đoán đây là dạng Business Logic có thể tham khảo trên portswigger .

{% embed url="https://portswigger.net/web-security/logic-flaws" %}



Ta sẽ tiến hành trải nghiệm trang web như một người dùng thật

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

Xem qua thì ta có 1 vài chức năng như :  mua hàng , check order (có thêm chức năng refund) , đổi điểm, điểm danh hàng ngày



<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>



### giả thiết

* giả thiết 1 : Thường những trang bán hàng như này thì có thể xảy ra 1 số lỗi như ko check quantity chẳng hạn , khiến cho người dùng có thể đặt quatity âm để khiến cho giá sản phẩm về âm rồi mua những món hàng khác để cho giá trị đơn giá về dương với số tiền nhỏ hơn như là mua 1000 sản phảm giá 100 đô nhưng chỉ mất 1 đô .<[https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-low-level](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-low-level)>
* giả thiết 2 : đôi khi chức năng refund kia bên phía server-side không check điểm thưởng , kiểu nếu người dùng refund thì phải trừ tiền và trừ cả điểm thưởng từ sản phẩm đó chẳng hạn nếu thiếu bước kiểm tra thì có thể chỉ hoàn tiền và điểm thưởng còn nguyên .
* giả thiết 3 : trang web có thể bị race condition có thể hình dung là chỉ với a tiền thì người dùng có thể gọi mua 1000 sản phẩm và nó xử lý song song với nhau mà tất cả đều giao dịch thành công , đáng ra phải mất 1000a tiền thì người dùng chỉ mất 1a tiền

### thử nghiệm

* Ta sẽ tiến hành kiểm tra giả thiết số 1 bằng cách thay đổi giá trị của quantity về âm , dựa theo poc bên dưới thì ta có thể confirm rằng giả thiết 1 đã sai

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>



* Ta sẽ chuyển qua giả thiết số 2 , ta sẽ tiến hành sử dụng tính năng refund để check xem liệu ta có bị mất điểm thưởng hay không , và thật bất ngờ , ta không hề mất điểm thưởng ⇒ đây chính là 1 lỗ hổng kinh tế có thể giúp user kiếm dc vô hạn tiền có thể tham khảo qua lab bên dưới :&#x20;

<[https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-infinite-money](https://portswigger.net/web-security/logic-flaws/examples/lab-logic-flaws-infinite-money)>

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

⇒ CF rằng giả thiết số 2 đã đúng và mục tiêu còn lại là kiếm đủ 1000 điểm để đổi lấy flag

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>



* ở giả thiết số 3 thì ta sẽ thử kiểm chứng&#x20;

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

⇒ CF là ko có Race condition

## EXPLOIT

Ta sẽ tiến hành hàng loạt giao dịch mua rồi thực hiện refund để kiếm điểm

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (736).png" alt=""><figcaption></figcaption></figure>



## FLAG

```
SecAthon2026{Hope_you_already_have_fun}
```
