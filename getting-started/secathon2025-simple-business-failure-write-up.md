# \[SecAthon2025] Simple Business Failure – Write-up

<figure><img src="../.gitbook/assets/image (365).png" alt=""><figcaption></figcaption></figure>

### Thông tin Challenge

* **Thể loại**: Web Exploitation
* **Độ khó**: Medium
* **Loại đề**: Dynamic
* **Số lần submit**: 3
* **Miêu tả**:\
  Truy cập `/login` với tài khoản `admin:admin` để tạo flag. Tuy nhiên, flag không được hiển thị trực tiếp mà chỉ thông báo vị trí lưu trữ trong session. Người chơi được yêu cầu tạo một tài khoản người dùng khác và dùng tài khoản này để đọc flag đã lưu.

***

### Phân Tích Đề Bài

Sau khi đăng nhập bằng tài khoản admin (`admin:admin`), người chơi có thể truy cập endpoint `/admin/flag` để khởi tạo flag.

Tuy nhiên, thay vì trả về nội dung flag, hệ thống chỉ trả thông báo:

```
Flag is stored in Session with key: pxkheosz
```

<figure><img src="../.gitbook/assets/image (366).png" alt=""><figcaption></figcaption></figure>

Điều này cho thấy flag đã được lưu vào session phía server với key là `pxkheosz`. Do đó, mục tiêu là phải tìm cách **đọc nội dung session từ một tài khoản user khác**.

***

### Các Bước Khai Thác

#### 1. Đăng Nhập Tài Khoản Admin

Truy cập `/login`:

```
Username: admin
Password: admin
```

Rồi vào `/admin/flag`, hệ thống phản hồi:

<figure><img src="../.gitbook/assets/image (367).png" alt=""><figcaption></figcaption></figure>

```
Flag is stored in Session with key: pxkheosz
```

***

#### 2. Tạo Tài Khoản Người Dùng Mới

Truy cập `/register` để tạo một tài khoản mới ở trên cùng trình duyệt (vì mã được tạo ra từ cred admin là sẽ dựa theo phiên ) :

```
Username: huhu
Password: 123456
```

Sau đó đăng nhập bằng tài khoản này để truy cập trang người dùng – tại đây có mục cho phép nhập mô tả (`Description`) và cập nhật nội dung.

<figure><img src="../.gitbook/assets/image (368).png" alt=""><figcaption></figcaption></figure>

***

#### 3. Phát Hiện Lỗ Hổng SSTI (Server-Side Template Injection)

Ta thử nghiệm bằng cách nhập:

```
${7*7}
```

Kết quả được render là:

```
49
```

<figure><img src="../.gitbook/assets/image (369).png" alt=""><figcaption></figcaption></figure>

Điều này chứng minh có tồn tại lỗi **SSTI**, sử dụng template engine Java (ví dụ: JSP/Expression Language).

***

#### 4. Đọc Nội Dung Flag Qua SSTI

Biết rằng flag được lưu với key `pxkheosz` trong session, ta dùng payload sau để truy xuất:

```java
${pageContext.session.getAttribute('pxkheosz')}
```

Chèn payload vào ô `Description` và nhấn "Update", ta sẽ thấy nội dung flag được hiển thị ngay trên giao diện.

Vì flag sẽ được lưu dưới dạng : `session.setAttribute("pxkheosz", "flag{xxx}");`

* `${...}`: Cú pháp **Expression Language (EL)** trong Java, dùng để chèn biến vào trang.
* `pageContext`: Đối tượng đại diện cho ngữ cảnh trang JSP hiện tại.
* `session`: Truy cập session hiện tại của người dùng.
* `getAttribute('pxkheosz')`: Lấy giá trị của biến `pxkheosz` đã được lưu trong session (ở đây là flag).

