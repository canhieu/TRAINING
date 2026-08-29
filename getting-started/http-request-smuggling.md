# HTTP Request Smuggling

## MOOC 1. HTTP Request Smuggling

### Task 1&#xD; Introduction

<figure><img src="../.gitbook/assets/image (489).png" alt=""><figcaption></figcaption></figure>

**HTTP Request Smuggling** là một lỗ hổng bảo mật xuất hiện khi **các thành phần trong hạ tầng web không thống nhất trong cách hiểu ranh giới của một yêu cầu HTTP**. Các thành phần này có thể bao gồm:

* Proxy
* Load balancer
* Web server

#### Ví dụ minh họa

Hãy hình dung một nhà ga nơi hành khách phải qua nhiều trạm kiểm tra vé. Nếu mỗi trạm lại có tiêu chí đánh giá vé khác nhau, hành khách có thể **lợi dụng sự không nhất quán đó để đi qua** dù không có vé hợp lệ.

Tương tự trong môi trường web, nếu proxy và server **hiểu khác nhau về điểm kết thúc của một yêu cầu HTTP** (thông qua các header như `Content-Length` và `Transfer-Encoding`), thì một yêu cầu giả mạo có thể được **chèn lén vào giữa**, dẫn đến việc **các yêu cầu bị trộn lẫn hoặc xử lý sai**.

***

#### Quá trình tấn công HTTP Request Smuggling

**Cơ chế keep-alive và HTTP pipelining** trong HTTP/1.1 cho phép gửi **nhiều yêu cầu trên cùng một kết nối TCP**. Đây là điều kiện quan trọng để kẻ tấn công thực hiện kỹ thuật smuggling.

**Một số yếu tố kỹ thuật cần chú ý**

* Trong quá trình tính toán kích thước nội dung theo `Content-Length (CL)` hoặc `Transfer-Encoding (TE)`, cần **cẩn trọng với các ký tự định dạng** như:
  * `\r` (carriage return)
  * `\n` (newline)

Những ký tự này không chỉ đóng vai trò định dạng trong HTTP mà còn ảnh hưởng đến **kết quả tính toán độ dài nội dung**, dễ dẫn đến sai lệch nếu bỏ qua.

**Vấn đề khi sử dụng công cụ kiểm thử**

Một số công cụ kiểm thử có thể **tự động điều chỉnh hoặc ghi đè giá trị `Content-Length`**, điều này có thể khiến **payload bị thay đổi**, từ đó **làm sai lệch kết quả kiểm tra**.

**Rủi ro khi kiểm tra trên hệ thống thật**

Việc kiểm tra lỗ hổng này trên hệ thống sản xuất có thể gây ra hậu quả nghiêm trọng:

* **Đầu độc bộ nhớ đệm (cache poisoning)**.
* **Ảnh hưởng đến yêu cầu của người dùng khác**.
* **Làm lệch hoàn toàn quy trình xử lý phía server (pipeline desynchronization)**.

Do đó, cần **hết sức thận trọng** khi thực hiện kiểm thử, chỉ nên tiến hành trên **môi trường kiểm soát**.

***

#### Mục tiêu của tài liệu

* Hiểu rõ HTTP Request Smuggling là gì và tác động của nó.
* Biết cách **phát hiện lỗ hổng** này trong các ứng dụng web.
* Có thể **khai thác lỗ hổng trong môi trường kiểm soát**, không ảnh hưởng đến người dùng khác.
* Áp dụng các **biện pháp giảm thiểu và phòng chống** nguy cơ tấn công.

***

#### Yêu cầu kiến thức trước khi học

Trước khi học về HTTP Request Smuggling, bạn cần có:

* **Hiểu biết cơ bản về giao thức HTTP/1.1** và các HTTP headers.
* Kiến thức nền tảng về **hạ tầng hệ thống web**, đặc biệt là proxy, load balancer và web server.
* **Kinh nghiệm sử dụng công cụ proxy như Burp Suite** để kiểm tra, sửa đổi và quan sát các yêu cầu HTTP.

***

#### Tầm quan trọng của việc hiểu HTTP Request Smuggling

1.  **Tránh né cơ chế phòng thủ**

    Các yêu cầu được smuggle có thể **vượt qua tường lửa ứng dụng web (WAF)**, dẫn đến việc **truy cập trái phép hoặc rò rỉ dữ liệu**.
2.  **Tấn công thông qua cache poisoning**

    Tin tặc có thể **đầu độc bộ nhớ đệm (cache)** bằng cách đưa nội dung độc hại vào, từ đó **người dùng khác sẽ nhận dữ liệu sai lệch hoặc độc hại**.
3.  **Kết hợp với các lỗ hổng khác**

    Yêu cầu giả mạo có thể được **chuỗi hóa với các lỗ hổng khác**, làm tăng mức độ nghiêm trọng của cuộc tấn công.
4.  **Khó phát hiện**

    Do phụ thuộc vào **cách các hệ thống khác nhau xử lý yêu cầu**, nên lỗ hổng này thường **không được phát hiện bởi các cơ chế bảo mật thông thường**, dẫn đến **rủi ro nghiêm trọng nếu không kiểm soát tốt**.



### Task 2  Modern Infrastructure

#### Các thành phần trong ứng dụng web hiện đại

Ngày nay, **ứng dụng web không còn là các hệ thống đơn khối (monolithic)** đơn giản như trước. Thay vào đó, chúng được cấu thành từ **nhiều thành phần riêng biệt** làm việc phối hợp với nhau để cung cấp chức năng, hiệu năng, và độ tin cậy cao hơn.

Dưới đây là các thành phần phổ biến trong một ứng dụng web hiện đại:

***

#### Front-end Server (Máy chủ giao tiếp người dùng)

* Thường đóng vai trò là **reverse proxy** hoặc **load balancer**.
* Nhiệm vụ chính: **tiếp nhận yêu cầu từ client** (trình duyệt, ứng dụng) và **chuyển tiếp đến back-end** phù hợp.
* Đây là nơi đầu tiên xử lý các yêu cầu HTTP đến hệ thống.

***

#### Back-end Server (Máy chủ xử lý phía sau)

* Chịu trách nhiệm **xử lý logic nghiệp vụ**, **giao tiếp với cơ sở dữ liệu**, và **trả dữ liệu về front-end**.
* Được xây dựng bằng các ngôn ngữ phổ biến như:
  * **PHP**, **Python**, **JavaScript**
* Sử dụng các framework hiện đại như:
  * **Laravel** (PHP), **Django** (Python), **Node.js** (JavaScript)

***

#### Databases (Cơ sở dữ liệu)

* Là nơi lưu trữ **dữ liệu lâu dài** của ứng dụng.
* Một số hệ quản trị cơ sở dữ liệu phổ biến:
  * **MySQL**, **PostgreSQL**: là **các hệ quản trị cơ sở dữ liệu quan hệ (Relational Databases)**.
  * **MongoDB**, **Redis**: là **các hệ quản trị cơ sở dữ liệu phi quan hệ (NoSQL Databases)**.

***

#### APIs (Giao diện lập trình ứng dụng)

* Là **cầu nối giao tiếp giữa front-end và back-end**, hoặc giữa các dịch vụ khác nhau.
* Cho phép:
  * Front-end truy vấn dữ liệu từ back-end.
  * Kết nối với các dịch vụ bên ngoài như thanh toán, email, phân tích dữ liệu.

***

#### Microservices (Dịch vụ vi mô)

* Là kiến trúc thay thế cho **monolithic** (toàn bộ ứng dụng gói gọn trong một khối duy nhất).
* Ứng dụng được chia thành **nhiều dịch vụ nhỏ độc lập**, mỗi dịch vụ đảm nhiệm một chức năng riêng biệt.
* Các microservice giao tiếp với nhau thông qua **HTTP/REST** hoặc **gRPC**.
* Ưu điểm:
  * Dễ mở rộng.
  * Triển khai linh hoạt.
  * Tách biệt lỗi tốt hơn.

***

#### Load Balancers và Reverse Proxies

**Load Balancers (Bộ cân bằng tải)**

* **Phân phối lưu lượng truy cập đến nhiều máy chủ khác nhau**, tránh tình trạng quá tải một server.
* Giúp:
  * **Tăng tính sẵn sàng** (high availability)
  * **Tăng độ tin cậy** (reliability)
* Thường đi kèm với reverse proxy.
* Ví dụ:
  * **AWS Elastic Load Balancing**
  * **HAProxy**
  * **F5 BIG-IP**

**Reverse Proxies (Proxy ngược)**

* Là **trạm trung gian giữa client và các web server phía sau**.
* Chức năng:
  * **Chuyển tiếp yêu cầu** đến server thích hợp.
  * **Ẩn danh back-end**, cung cấp một điểm truy cập duy nhất.
* Có thể kiêm luôn chức năng cân bằng tải.
* Ví dụ:
  * **NGINX**
  * **Apache (mod\_proxy)**
  * **Varnish**

<figure><img src="../.gitbook/assets/image (490).png" alt=""><figcaption></figcaption></figure>



#### Vai trò của Caching trong Hạ tầng Web

**Caching** là kỹ thuật lưu trữ tạm thời dữ liệu đã được truy xuất hoặc tính toán trước đó, nhằm:

* **Giảm thời gian xử lý** cho các yêu cầu sau.
* **Giảm tải cho máy chủ và cơ sở dữ liệu**.
* **Tăng tốc độ phản hồi cho người dùng**.

Dưới đây là các hình thức caching phổ biến trong hệ thống web.

***

#### Content Caching (Bộ đệm nội dung tĩnh)

* **Nội dung tĩnh** như hình ảnh, tệp CSS, JS thường **không thay đổi thường xuyên**.
* Caching giúp:
  * **Tránh việc tải lại dữ liệu giống nhau nhiều lần**.
  * **Giảm lưu lượng và gánh nặng xử lý cho web server**.
  * **Tăng tốc độ hiển thị giao diện cho người dùng cuối**.

***

#### Database Query Caching (Bộ đệm truy vấn cơ sở dữ liệu)

* Các **truy vấn thường xuyên (frequent queries)** sẽ được lưu tạm.
* Khi truy vấn giống nhau được gửi lại:
  * Hệ thống **không cần thực hiện tính toán lại từ đầu**.
  * Kết quả có thể được trả ngay từ bộ nhớ đệm.
* Điều này giúp **tối ưu hiệu năng và giảm độ trễ của hệ thống**.

***

#### Full-page Caching (Bộ đệm toàn bộ trang)

* **Toàn bộ nội dung HTML của một trang web** có thể được lưu lại dưới dạng cache.
* Lợi ích:
  * **Không cần tái tạo trang** mỗi lần người dùng truy cập.
  * **Đặc biệt hiệu quả với website có lưu lượng lớn** và nội dung không thay đổi liên tục.

***

#### Edge Caching / CDN (Bộ đệm tại biên mạng)

* **CDN (Content Delivery Network)** là hệ thống máy chủ phân tán khắp thế giới.
* **Lưu trữ bản sao nội dung tại các vị trí địa lý gần người dùng**.
* Giúp:
  * **Giảm độ trễ truy cập (latency)**.
  * **Tăng tốc độ tải trang** bất kể vị trí địa lý của người dùng.
* Áp dụng hiệu quả cho video, hình ảnh, và tệp tĩnh.

***

#### API Caching (Bộ đệm phản hồi API)

* Các API thường phục vụ các yêu cầu giống nhau lặp đi lặp lại.
* Thay vì mỗi lần đều gọi đến back-end:
  * **Kết quả từ lần gọi trước có thể được tái sử dụng**.
* Lợi ích:
  * **Giảm số lần xử lý từ phía back-end**.
  * **Tăng hiệu suất và tiết kiệm tài nguyên tính toán**.



<figure><img src="../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>

### Task 3&#xD; Behind the Scenes

#### Hiểu về cấu trúc HTTP Request

Mỗi HTTP request bao gồm hai phần chính: **header** và **body**.

<figure><img src="../.gitbook/assets/image (492).png" alt=""><figcaption></figcaption></figure>

***

#### Cấu trúc HTTP

**Request Line (Dòng yêu cầu):**\
Dòng đầu tiên của request `POST /admin/login HTTP/1.1` chính là request line. Nó bao gồm ít nhất ba thành phần:

1. **Method (Phương thức):** trong ví dụ này là `"POST"`. Method là một lệnh một từ, cho server biết phải làm gì với tài nguyên.
2. **Path (Đường dẫn):** đây là phần path trong URL của request. Path xác định tài nguyên trên server, trong trường hợp này là `"/admin/login"`.
3. **HTTP Version (Phiên bản HTTP):** con số phiên bản HTTP cho thấy client đang tuân theo chuẩn HTTP nào. Lưu ý rằng **HTTP/2** và **HTTP/1.1** có cấu trúc khác nhau.

**Request Headers (Các header của request):**\
Phần này chứa **metadata** về request, ví dụ như loại nội dung được gửi, định dạng phản hồi mong muốn, và token xác thực. Nó giống như phần “phong bì” của một lá thư, cung cấp thông tin về người gửi, người nhận, và tính chất nội dung bên trong.

**Message Body (Thân thông điệp):**\
Đây là **nội dung thực sự** của request. Body có thể trống trong một yêu cầu **GET**, nhưng với **POST** thì nó có thể chứa dữ liệu biểu mẫu (form data), payload JSON, hoặc file upload.

***

#### Header Content-Length

Header **Content-Length** chỉ định **kích thước body của request hoặc response tính theo byte**. Nó cho server biết phải nhận bao nhiêu dữ liệu để đảm bảo toàn bộ nội dung được nhận đủ.

**Ví dụ Content-Length Request:**

```
POST /submit HTTP/1.1
Host: good.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 14
    
q=smuggledData
```

Điều này có nghĩa là body của request hoặc response chứa **14 byte dữ liệu**.

***

#### Header Transfer-Encoding

Header **Transfer-Encoding** được sử dụng để xác định hình thức mã hóa được áp dụng cho body của HTTP request hoặc response.

* Giá trị thường gặp là `"chunked"`, nghĩa là message body được chia thành nhiều khối (chunk), mỗi khối được ghi trước bởi kích thước của nó ở dạng **hexadecimal (hệ 16)**.
* Các giá trị khác có thể gồm `"compress"`, `"deflate"`, và `"gzip"`, tương ứng với các kiểu nén khác nhau.

**Ví dụ Transfer-Encoding Request:**

```
POST /submit HTTP/1.1
Host: good.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked
    
b
q=smuggledData 
0
```

Trong ví dụ này:

* `b` (hệ 16, bằng **11** trong hệ 10) xác định kích thước của chunk tiếp theo.
* Chunk `q=smuggledData` là dữ liệu thực tế, theo sau là ký tự xuống dòng.
* Request kết thúc bằng một dòng `"0"`, báo hiệu **kết thúc body**.

Mỗi chunk đều có kích thước ở dạng **hexadecimal**, và chunk cuối cùng có kích thước bằng 0 để đánh dấu hết dữ liệu.

***

#### Ảnh hưởng của Headers đến việc xử lý Request

<figure><img src="../.gitbook/assets/image (493).png" alt=""><figcaption></figcaption></figure>

Headers đóng vai trò quan trọng trong việc hướng dẫn server xử lý request. Chúng:

* Quyết định cách **phân tích (parse) body**.
* Ảnh hưởng đến **cơ chế cache**.
* Có thể tác động đến **xác thực (authentication)**, **chuyển hướng (redirection)**, và các phản hồi khác từ server.

Việc thao túng các header như **Content-Length** và **Transfer-Encoding** có thể tạo ra lỗ hổng. Ví dụ, nếu một proxy server bị nhầm lẫn bởi hai header này, nó có thể **không phân biệt chính xác điểm kết thúc của một request và điểm bắt đầu của request khác**.

***

#### Nguồn gốc của HTTP Request Smuggling

<figure><img src="../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>

HTTP Request Smuggling xảy ra chủ yếu do **sự khác biệt trong cách các server khác nhau (ví dụ: front-end server và back-end server) diễn giải ranh giới của HTTP request**.

Một số tình huống điển hình:

* Khi cả hai header **Content-Length** và **Transfer-Encoding** cùng xuất hiện, có thể xảy ra **mơ hồ**.
* Một số thành phần ưu tiên **Content-Length**, trong khi thành phần khác lại ưu tiên **Transfer-Encoding**.

Điều này có thể dẫn đến tình huống:

* Một thành phần nghĩ rằng request đã kết thúc.
* Thành phần khác lại cho rằng request vẫn còn tiếp tục.

Hậu quả là một request có thể bị **“smuggled” (chèn lén)** vào trong request khác, gây ra hành vi bất ngờ và mở ra lỗ hổng bảo mật.

**Ví dụ:**\
Giả sử front-end server sử dụng header **Content-Length** để xác định điểm kết thúc của request, trong khi back-end server lại dựa vào header **Transfer-Encoding**. Một kẻ tấn công có thể tạo ra một request được thiết kế sao cho:

* Front-end server hiểu nó có một ranh giới.
* Back-end server lại hiểu nó có một ranh giới khác.

Kết quả: **một request được chèn lén vào trong request khác**, gây ra sự sai lệch trong xử lý và có thể bị lợi dụng để tấn công.



### Task 4&#xD; Request Smuggling CL.TE



#### Giới thiệu về kỹ thuật **CL.TE Request Smuggling**

**CL.TE** là viết tắt của **Content-Length/Transfer-Encoding** – tên gọi xuất phát từ hai tiêu đề (header) HTTP liên quan: `Content-Length` và `Transfer-Encoding`.

Trong kỹ thuật CL.TE, kẻ tấn công khai thác **sự khác biệt trong cách các máy chủ (thường là một proxy ở phía trước và một máy chủ back-end ở phía sau)** xử lý hai tiêu đề này. Cụ thể:

* **Proxy (máy chủ phía trước)** sử dụng tiêu đề `Content-Length` để xác định điểm kết thúc của một yêu cầu.
* **Máy chủ back-end** lại dựa vào tiêu đề `Transfer-Encoding`.



<figure><img src="../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

***

#### Cách thức hoạt động của **CL.TE Request Smuggling**

Chính vì sự khác biệt trong cách xử lý này, kẻ tấn công có thể tạo ra các **yêu cầu mơ hồ** – được hiểu khác nhau giữa proxy và máy chủ back-end.

**Ví dụ minh họa:**

Một yêu cầu HTTP được gửi đi có chứa **cả hai tiêu đề** `Content-Length` và `Transfer-Encoding`. Khi đó:

* Proxy đọc `Content-Length` và tin rằng yêu cầu kết thúc tại một vị trí cụ thể nào đó (tương ứng với số byte được chỉ định).
* Máy chủ back-end đọc `Transfer-Encoding: chunked` và hiểu cấu trúc dữ liệu theo kiểu từng khối (chunk), dẫn đến cách xử lý khác.

Kết quả là hai máy chủ **hiểu khác nhau** về nội dung và phạm vi của yêu cầu – tạo điều kiện cho các hành vi không mong muốn xảy ra.

***

#### **Exploiting CL.TE for Request Smuggling**



Kẻ tấn công có thể tận dụng kỹ thuật này bằng cách **tạo một yêu cầu HTTP có cả hai tiêu đề**, sao cho:

* **Proxy và máy chủ back-end hiểu khác nhau** về điểm kết thúc yêu cầu.
* Từ đó, phần dữ liệu phía sau có thể được máy chủ back-end xử lý như **một yêu cầu hoàn toàn mới**.

#### Ví dụ cụ thể:

```
POST /search HTTP/1.1
Host: example.com
Content-Length: 130
Transfer-Encoding: chunked

0

POST /update HTTP/1.1
Host: example.com
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

isadmin=true
```

**Phân tích:**

* Proxy đọc `Content-Length: 130` và hiểu toàn bộ yêu cầu chỉ là một – bao gồm cả phần `isadmin=true`.
* Trong khi đó, máy chủ back-end lại thấy `Transfer-Encoding: chunked` và dòng `0`, cho biết đây là **kết thúc của một chuỗi chunk**.
* Do đó, phần tiếp theo (`POST /update ...`) bị máy chủ back-end hiểu là **một yêu cầu hoàn toàn mới** – dẫn đến việc xử lý tách biệt.

**Hệ quả nguy hiểm**: Nếu `/update` là một API có khả năng cập nhật dữ liệu quan trọng (ví dụ: phân quyền admin), thì kẻ tấn công có thể **lén gửi yêu cầu này** mà không bị phát hiện bởi proxy hay hệ thống ghi log ở phía ngoài.



#### **Incorrect Content-Length**

Khi tạo một request smuggling payload, nếu giá trị trong header `Content-Length` **không bằng độ dài thực tế** của request body, một số vấn đề có thể xảy ra.

<figure><img src="../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

#### Hệ quả của Incorrect Content-Length

Máy chủ có thể chỉ xử lý phần nội dung trong request body tương ứng với giá trị được chỉ định trong `Content-Length`. Điều này có thể khiến phần dữ liệu được smuggle không được xử lý như mong đợi hoặc hoàn toàn bị bỏ qua.

Ví dụ, trong ảnh minh họa, kích thước thực tế của body là 24 byte.

#### Kiểm tra Content-Length hợp lệ

Để xác minh `Content-Length` có chính xác hay không, có thể kiểm tra thư mục `/submissions` để xem liệu toàn bộ phần body có được lưu vào file `.txt` hay không.

Nếu body là:

```
username=test&query=test
```

<figure><img src="../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

Thì tổng độ dài là 24 byte. Do đó, gửi `Content-Length` nhỏ hơn 24 sẽ khiến máy chủ back-end hiểu và xử lý phần body một cách khác.

Ví dụ, nếu đặt `Content-Length: 10`, máy chủ sẽ chỉ đọc 10 byte đầu tiên:

```
username=
```

Phần còn lại (`test&query=test`) bị bỏ qua.

<figure><img src="../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>





#### Task 5&#xD; Request Smuggling TE.CL

#### Introduction to TE.CL Technique

**TE.CL** là viết tắt của **Transfer-Encoding/Content-Length**. Kỹ thuật này là **đối lập với phương pháp CL.TE**. Trong cách tiếp cận TE.CL, sự khác biệt trong cách diễn giải header bị đảo ngược: **máy chủ phía trước (front-end)** sử dụng header `Transfer-Encoding` để xác định điểm kết thúc của một yêu cầu, trong khi **máy chủ phía sau (back-end)** sử dụng header `Content-Length`.

Kỹ thuật TE.CL xuất hiện khi **proxy ưu tiên header `Transfer-Encoding`**, còn máy chủ back-end lại **ưu tiên `Content-Length`**.

<figure><img src="../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

***

#### How TE.CL smuggling works

Ví dụ: Nếu một kẻ tấn công gửi một yêu cầu chứa cả hai header, thì **máy chủ phía trước hoặc proxy** có thể diễn giải yêu cầu dựa trên header `Transfer-Encoding`, trong khi **máy chủ phía sau** lại dựa vào `Content-Length`. Sự khác biệt trong cách diễn giải này khiến hai máy chủ hiểu yêu cầu theo hai cách khác nhau, dẫn đến hành vi bất ngờ hoặc trái với dự kiến.

***

#### Exploiting TE.CL for Request Smuggling

Để khai thác kỹ thuật **TE.CL**, kẻ tấn công tạo ra một yêu cầu được thiết kế đặc biệt chứa cả hai header: `Transfer-Encoding` và `Content-Length`, nhằm tạo ra sự mơ hồ trong cách diễn giải yêu cầu giữa máy chủ phía trước và phía sau.

Ví dụ, kẻ tấn công gửi một yêu cầu như sau:

```
POST / HTTP/1.1
Host: example.com
Content-Length: 4
Transfer-Encoding: chunked

78
POST /update HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

isadmin=true
0
```

Trong payload trên:

* Máy chủ phía trước nhìn thấy header `Transfer-Encoding: chunked` và xử lý yêu cầu theo kiểu chunked.
* Dòng `78` là giá trị hex của 120, nghĩa là 120 byte tiếp theo được xem là phần thân (body) của yêu cầu hiện tại.
* Máy chủ phía trước sẽ xem toàn bộ dữ liệu cho đến khi gặp dòng `0` (ký hiệu kết thúc chunked message) là phần thân của yêu cầu đầu tiên.

Tuy nhiên:

* Máy chủ back-end lại sử dụng `Content-Length: 4` và chỉ xử lý 4 byte đầu tiên của request body.
* Nó **không đọc toàn bộ phần smuggled request `POST /update`**.
* Phần còn lại của yêu cầu, bắt đầu từ `POST /update`, sau đó được máy chủ back-end **diễn giải như một yêu cầu mới, riêng biệt**.

***

#### Tác động của yêu cầu smuggled

Yêu cầu bị smuggle được xử lý bởi máy chủ back-end **như thể đó là một yêu cầu hợp lệ và riêng biệt**. Yêu cầu này bao gồm tham số `isadmin=true`, có thể được sử dụng để:

* Tăng quyền truy cập của kẻ tấn công (privilege escalation).
* Thay đổi dữ liệu trên máy chủ.

Tùy thuộc vào chức năng của ứng dụng, hành vi này có thể dẫn đến việc chiếm quyền điều khiển hoặc thay đổi trái phép nội dung hệ thống.



#### Task 6&#xD; Transfer Encoding Obfuscation

#### Introduction to TE.TE Technique

<figure><img src="../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

**Transfer Encoding Obfuscation**, còn được gọi là **TE.TE**, là viết tắt của **Transfer-Encoding/Transfer-Encoding**. Khác với các phương pháp **CL.TE** hoặc **TE.CL**, kỹ thuật **TE.TE** phát sinh khi **cả máy chủ phía trước (front-end)** và **máy chủ phía sau (back-end)** đều sử dụng header `Transfer-Encoding`.

Trong kỹ thuật TE.TE, kẻ tấn công khai thác sự **không nhất quán trong cách các máy chủ xử lý header `Transfer-Encoding`** trong các yêu cầu HTTP.

Điểm đáng chú ý là **lỗ hổng TE.TE không nhất thiết phải có nhiều header `Transfer-Encoding`**. Thay vào đó, kỹ thuật này thường chỉ sử dụng một **header `Transfer-Encoding` bị sai định dạng**, khiến front-end và back-end server **diễn giải khác nhau**. Trong một số trường hợp:

* Máy chủ phía trước có thể bỏ qua hoặc loại bỏ phần sai định dạng của header và xử lý yêu cầu như bình thường.
* Trong khi đó, máy chủ phía sau lại diễn giải header bị sai định dạng đó theo cách khác, **dẫn đến hiện tượng request smuggling**.

***

#### How TE.TE request smuggling works

Ví dụ: Kẻ tấn công thao túng header `Transfer-Encoding` bằng cách chèn từ “chunked” theo cách bị sai định dạng. Mục tiêu là khai thác cách các máy chủ (front-end và back-end) **ưu tiên sử dụng `Transfer-Encoding` hơn `Content-Length`**.

Bằng cách tạo ra một header `Transfer-Encoding` bị sai định dạng, kẻ tấn công cố gắng khiến **một trong hai máy chủ bỏ qua header `TE`** và sử dụng `CL` thay thế. Từ đó, xảy ra sự khác biệt trong cách xác định ranh giới yêu cầu giữa front-end và back-end.

Tình huống này có thể dẫn đến **CL.TE** hoặc **TE.CL**, tùy thuộc vào máy chủ nào quay về sử dụng `Content-Length`.

***

#### Exploiting TE.TE for Request Smuggling

Để khai thác kỹ thuật **TE.TE**, kẻ tấn công có thể tạo một yêu cầu có chứa nhiều header `Transfer-Encoding` với các loại mã hóa khác nhau. Ví dụ:

```
POST / HTTP/1.1
Host: example.com
Content-length: 4
Transfer-Encoding: chunked
Transfer-Encoding: chunked1

4e
POST /update HTTP/1.1
Host: example.com
Content-length: 15

isadmin=true
0
```

Trong payload trên:

* Máy chủ phía trước gặp hai header `Transfer-Encoding`.
  * Header đầu tiên là chuẩn: `Transfer-Encoding: chunked`.
  * Header thứ hai là không hợp lệ (non-standard): `Transfer-Encoding: chunked1`.
* Tùy theo cấu hình, front-end có thể:
  * Chấp nhận header đầu tiên (`chunked`) và **bỏ qua `chunked1`**, sau đó xử lý toàn bộ yêu cầu cho đến khi gặp `0` như một thông điệp chunked hoàn chỉnh.

Trong khi đó:

* Máy chủ back-end có thể xử lý header `Transfer-Encoding: chunked1` theo cách khác:
  * Có thể **từ chối phần sai định dạng** và xử lý giống front-end.
  * Hoặc có thể **diễn giải yêu cầu khác đi** do có sự hiện diện của header không chuẩn.
* Nếu back-end chỉ xử lý **4 byte đầu tiên** (theo `Content-length: 4`), phần còn lại của yêu cầu, bắt đầu từ `POST /update`, sẽ được xem là **một yêu cầu mới, độc lập**.

***

#### Tác động của yêu cầu smuggled

Yêu cầu bị smuggle chứa tham số `isadmin=true` sẽ được máy chủ back-end xử lý như thể nó là **một yêu cầu hợp lệ, riêng biệt**.

Tùy vào chức năng của endpoint `/update`, điều này có thể dẫn đến:

* Thực hiện hành động trái phép (unauthorized actions).
* Thay đổi dữ liệu không hợp lệ (data modification).

Nếu ứng dụng không kiểm tra chặt chẽ, kẻ tấn công có thể lợi dụng điều này để **nâng quyền truy cập** hoặc **can thiệp vào trạng thái hệ thống** một cách bất hợp pháp.







