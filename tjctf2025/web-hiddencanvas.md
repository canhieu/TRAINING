# web-HiddenCanvas

<figure><img src="../.gitbook/assets/image (618).png" alt=""><figcaption></figcaption></figure>

Trang web có chức năng chính là upload ảnh gồm PNG hoặc JPG và giới hạn 5mb

<figure><img src="../.gitbook/assets/image (619).png" alt=""><figcaption></figcaption></figure>

Tôi sử dụng `exiftool tool -ImageDescription="hello" github.png` để chỉnh Image Description có nội dung là hello

<figure><img src="../.gitbook/assets/image (620).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (621).png" alt=""><figcaption></figcaption></figure>

Kết quả là "not valid Base64", tôi thử encoded theo base64 thì nó hiển thị được

<figure><img src="../.gitbook/assets/image (622).png" alt=""><figcaption></figcaption></figure>

Theo như tôi dự đoán thì có vẻ lỗi sẽ liên quan đến server side thay vì client side, tôi thử dùng \{{7\*7\}}

<figure><img src="../.gitbook/assets/image (623).png" alt=""><figcaption></figcaption></figure>

Và nó xuất hiện ssti, tôi sử dụng wappalyzer thì có xuất hiện là website sử dụng python, tôi xài tạm cái payload ssti và encode sang base64

`{{ self.__init__.__globals__.__builtins__.__import__('os').popen('ls').read() }}`

<figure><img src="../.gitbook/assets/image (624).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (625).png" alt=""><figcaption></figcaption></figure>



