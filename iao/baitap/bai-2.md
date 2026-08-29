# Bài 2

```
1.1 Cài đặt và sử dụng BurpSuite
- Cài đặt và làm quen công cụ burpsuite
- Cài đặt proxy cho trình duyệt
- Cài đặt certificate cho các trình duyệt khác nhau
- Các chức năng cơ bản của burpsuite
1.2. Cấu trúc HTTP Request/Response
- Thành phần của một HTTP Request/HTTP Response
- Chú ý các blank line trong cấu trúc
- Tìm hiểu và demo các HTTP method trên website thực tế
- Các HTTP Header - Tìm hiểu kỹ Host Header
- Tìm hiểu kỹ về cookie và các flag của cookie (Trả lời câu hỏi - vì sao website có thể lưu phiên đăng nhập của người dùng)
- Status code
1.3. Tìm hiểu cấu trúc củiua 1 URL, tham khảo https://www.rfc.editor.org/rfc/rfc1738.html
1.4. Web Functionality - “The web application hacker’s handbook”
1.5. Encoding Schemes - “The web application hacker’s handbook”


Mục tiêu:
- Nắm được cách thức hoạt động của BurpSuite. Biết được các chức năng BurpSuite cung cấp để thực hiện pentest
- Nắm được cách thức hoạt động của một chu trình duyệt web từ người dùng tới server.
- Nắm được cấu trúc và các thành phần của HTTP Request/Response.

Trả lời 1 số câu hỏi:
- Tại sao chúng ta phải dùng BurpSuite để thực hiện pentest web
- Khi chúng ta đánh giá Ứng dụng Web, có nghĩa là chúng ta đánh giá những gì?
- Chu trình xử lý của trình duyệt từ khi nhập địa chỉ website trên thanh  URL tới khi render toàn bộ nội dung website
(Trình bày kỹ từng bước)
```

## 1.1 Cài đặt và sử dụng BurpSuite

<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 094834.png" alt=""><figcaption></figcaption></figure>

Đầu tiên ta sẽ tiến hành truy cập vào `http://burp` để có thể tải CA cert về

<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095225.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095311.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095338.png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095414.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095447.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/Screenshot 2026-08-27 095536.png" alt=""><figcaption></figcaption></figure>

