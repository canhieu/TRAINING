---
hidden: true
---

# SQL Injection Fundamentals

## &#x20;Introduction

### Giới thiệu (Introduction)

* Phần lớn các ứng dụng web hiện đại sử dụng **cấu trúc cơ sở dữ liệu** ở phía back-end.
* Cơ sở dữ liệu được dùng để:
  * Lưu trữ và truy xuất dữ liệu liên quan đến ứng dụng web.
  * Bao gồm: nội dung hiển thị trên web, thông tin người dùng, nội dung do người dùng tạo, v.v.
* Để ứng dụng web có tính động (dynamic), nó cần tương tác với cơ sở dữ liệu theo **thời gian thực**.
* Khi có các yêu cầu HTTP(S) từ phía người dùng:
  * Back-end của ứng dụng sẽ gửi **truy vấn SQL** tới cơ sở dữ liệu.
  * Truy vấn này chứa dữ liệu từ yêu cầu HTTP(S) hoặc dữ liệu liên quan khác để tạo phản hồi.

<figure><img src="../../.gitbook/assets/image (245).png" alt=""><figcaption></figcaption></figure>

**Mô hình kiến trúc ba lớp (Three-Tier Architecture):**

1. **Tầng I (Client – Người dùng):** người dùng tương tác qua ứng dụng khách (trình duyệt hoặc app).
2. **Tầng II (Application Server):** máy chủ ứng dụng xử lý logic.
3. **Tầng III (DBMS):** hệ quản trị cơ sở dữ liệu lưu trữ dữ liệu.

***

### SQL Injection (SQLi) là gì?

* Khi thông tin do người dùng cung cấp được dùng để xây dựng truy vấn SQL:\
  → Kẻ tấn công có thể lợi dụng để thay đổi mục đích ban đầu của truy vấn.\
  → Từ đó, kẻ tấn công có quyền **gửi truy vấn trực tiếp** đến cơ sở dữ liệu.
* Đây gọi là tấn công **SQL Injection (SQLi)**.
* SQLi áp dụng cho **cơ sở dữ liệu quan hệ** (MySQL, PostgreSQL, SQL Server,…).
  * Nếu là cơ sở dữ liệu phi quan hệ (MongoDB, CouchDB), thì gọi là **NoSQL Injection**.
* Trong module này, ta tập trung vào **MySQL** để giới thiệu khái niệm SQLi.

***

### SQL Injection (SQLi) – Chi tiết

* Có nhiều dạng lỗ hổng injection trong web:
  * HTTP Injection.
  * Code Injection.
  * Command Injection.
  * Nhưng phổ biến nhất là **SQL Injection**.
* **SQL Injection xảy ra khi:**
  * Người dùng độc hại nhập dữ liệu nhằm thay đổi truy vấn SQL gốc.
  * Hậu quả: kẻ tấn công có thể thực thi những truy vấn SQL không mong muốn trực tiếp trên DB.
* **Cách thức:**
  1. Kẻ tấn công chèn (inject) đoạn mã SQL.
  2. Phá vỡ logic ban đầu của ứng dụng web.
  3. Truy vấn bị thay đổi hoặc thực thi thêm truy vấn khác.
* **Phương pháp tấn công:**
  * Chèn ký tự `'` (nháy đơn) hoặc `"` (nháy kép) → thoát khỏi giới hạn input bình thường.
  * Tạo thành truy vấn SQL hợp lệ, trong đó gồm:
    * Truy vấn gốc (intended query).
    * Truy vấn mới (malicious query).
  * Kỹ thuật thường dùng:
    * **Stacked queries** (chạy nhiều câu lệnh trong một request).
    * **UNION queries** (kết hợp dữ liệu từ nhiều bảng).
  * Kết quả sau khi khai thác có thể hiển thị ngay trên front-end hoặc được trích xuất gián tiếp.

***

### Tác động & Trường hợp sử dụng (Use Cases and Impact)

* **Ảnh hưởng lớn**, đặc biệt khi:
  * Máy chủ back-end cấu hình lỏng lẻo.
  * Quyền của người dùng trong DB được cấp quá rộng.
* **Hậu quả tiềm ẩn:**
  1. **Rò rỉ thông tin nhạy cảm:**
     * Ví dụ: tài khoản, mật khẩu, thông tin thẻ tín dụng.
     * Dữ liệu bị đánh cắp có thể tái sử dụng cho các mục đích độc hại.
     * Đây là nguyên nhân của nhiều vụ **lộ mật khẩu, rò rỉ dữ liệu** quy mô lớn.
  2. **Vượt qua logic ứng dụng web:**
     * Bỏ qua bước đăng nhập (không cần username/password hợp lệ).
     * Truy cập tính năng dành riêng cho người quản trị (admin panel).
  3. **Kiểm soát máy chủ back-end:**
     * Đọc/ghi file trực tiếp trên máy chủ.
     * Tải backdoor lên server.
     * Cuối cùng, chiếm toàn quyền kiểm soát website.

***

### Phòng tránh (Prevention)

* Nguyên nhân:
  * Ứng dụng web được viết code kém an toàn.
  * Máy chủ DB hoặc phân quyền DB không chặt chẽ.
* Biện pháp:
  1. **Lập trình an toàn:**
     * Kiểm tra và lọc (sanitize) dữ liệu đầu vào từ người dùng.
     * Thực hiện xác thực dữ liệu (validation) nghiêm ngặt.
  2. **Giới hạn quyền trên DB:**
     * Cấp quyền tối thiểu cần thiết cho user kết nối DB.
     * Tránh dùng tài khoản DB có quyền root/admin cho ứng dụng web.



## &#x20;Intro to Databases

### Giới thiệu về Cơ sở dữ liệu (Intro to Databases)

* Trước khi tìm hiểu về SQL Injection, cần nắm rõ về **cơ sở dữ liệu** và **Structured Query Language (SQL)**.
* Ứng dụng web sử dụng cơ sở dữ liệu back-end để lưu trữ nhiều loại nội dung và thông tin, ví dụ:
  * Tài sản cốt lõi của ứng dụng (hình ảnh, file).
  * Nội dung (bài viết, cập nhật).
  * Dữ liệu người dùng (username, password).
* Có nhiều loại cơ sở dữ liệu khác nhau, phù hợp với từng mục đích.
* Trước đây, ứng dụng thường dùng **file-based database** (cơ sở dữ liệu dựa trên file).
  * Hạn chế: tốc độ rất chậm khi dữ liệu tăng.
  * Giải pháp: sử dụng **Database Management Systems (DBMS)**.

***

### Hệ quản trị cơ sở dữ liệu (Database Management Systems – DBMS)

* **DBMS** giúp: tạo, định nghĩa, lưu trữ và quản lý cơ sở dữ liệu.
* Các loại DBMS được phát triển theo thời gian:
  * File-based.
  * Relational DBMS (RDBMS).
  * NoSQL.
  * Graph-based.
  * Key/Value stores.
* **Cách tương tác với DBMS:**
  * Dùng công cụ dòng lệnh (CLI).
  * Giao diện đồ họa (GUI).
  * API (Application Programming Interface).
* **Ứng dụng:**
  * Được dùng rộng rãi trong ngân hàng, tài chính, giáo dục.
  * Mục đích: quản lý khối lượng dữ liệu lớn.
* **Tính năng chính của DBMS:**

| Tính năng                           | Mô tả                                                                                                          |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Concurrency (Đồng thời)**         | Nhiều người dùng có thể truy cập cùng lúc mà không gây hỏng hoặc mất dữ liệu.                                  |
| **Consistency (Nhất quán)**         | Dữ liệu phải luôn hợp lệ và thống nhất trong toàn hệ thống, bất chấp nhiều giao dịch đồng thời.                |
| **Security (Bảo mật)**              | Quản lý quyền truy cập chi tiết (authentication, permission) để ngăn xem/chỉnh sửa trái phép dữ liệu nhạy cảm. |
| **Reliability (Độ tin cậy)**        | Hỗ trợ backup và rollback dữ liệu khi có sự cố hoặc mất mát dữ liệu.                                           |
| **Structured Query Language (SQL)** | Ngôn ngữ đơn giản, trực quan, hỗ trợ các thao tác khác nhau để tương tác với DB.                               |

***

### Kiến trúc (Architecture)

* Kiến trúc **hai tầng (two-tiered architecture)** thường gồm:

<figure><img src="../../.gitbook/assets/image (247).png" alt=""><figcaption></figcaption></figure>

* **Tầng I (Client):** ứng dụng phía người dùng (website, GUI app).
  * Thực hiện các tác vụ: đăng nhập, bình luận,…
  * Gửi dữ liệu sang tầng II bằng API call hoặc request.
* **Tầng II (Middleware/Application Server):**
  * Diễn giải sự kiện từ client.
  * Chuyển thành truy vấn cần thiết cho DBMS.
  * Sử dụng thư viện/driver phù hợp với từng loại DBMS.
* **Tầng III (DBMS):**
  * Nhận truy vấn từ tầng II.
  * Thực hiện các thao tác: thêm, truy xuất, xóa, cập nhật.
  * Trả về dữ liệu hoặc mã lỗi khi truy vấn sai.

**Mô hình ba tầng (Three-tier architecture):**



1. Người dùng tương tác với ứng dụng khách ở **Tầng I**.
2. Ứng dụng khách kết nối tới **Application Server (Tầng II)**.
3. Application Server kết nối tới **DBMS (Tầng III)**.

* Ngoài người dùng, **Database Administrator (DBA)** cũng có thể truy cập trực tiếp DBMS.
* **Triển khai hệ thống:**
  * Có thể host **Application Server** và **DBMS** trên cùng máy.
  * Tuy nhiên, với hệ thống lớn, nhiều người dùng:
    * DBMS thường được tách riêng.
    * Mục tiêu: **tăng hiệu suất và khả năng mở rộng**.



## Types of Databases

### Các loại Cơ sở dữ liệu (Types of Databases)

* Nhìn chung, cơ sở dữ liệu được chia thành **Relational Databases** (quan hệ) và **Non-Relational Databases** (phi quan hệ).
* **Chỉ cơ sở dữ liệu quan hệ** mới sử dụng **SQL**.
* **Cơ sở dữ liệu phi quan hệ** dùng nhiều phương thức giao tiếp khác nhau.

***

### Cơ sở dữ liệu quan hệ (Relational Databases)

* Đây là loại phổ biến nhất.
* Sử dụng **schema** (mẫu lược đồ) để quy định cấu trúc dữ liệu lưu trữ.
* Ví dụ: Một công ty bán sản phẩm sẽ lưu:
  * Thông tin khách hàng.
  * Số lượng sản phẩm đã bán & chi phí.
  * Ai đã mua sản phẩm và dữ liệu thanh toán.
* Việc này thường thực hiện ở **back-end**, không hiển thị rõ ở **front-end**.

#### Bảng và khóa (Tables and Keys)

* **Bảng (Table/Entity):** cấu trúc dữ liệu chính trong RDB.
* Các bảng liên kết với nhau thông qua **khóa (key)**.
* Ví dụ:
  * **Bảng khách hàng (customers):** mỗi khách hàng có một **ID** riêng (gồm tên, địa chỉ, thông tin liên hệ).
  * **Bảng sản phẩm (products):** mỗi sản phẩm có một **ID** riêng.
  * **Bảng đơn hàng (orders):** chỉ cần lưu **ID khách hàng**, **ID sản phẩm** và **số lượng**.

#### RDBMS (Relational Database Management System)

* Để liên kết bảng với nhau, cần đến hệ thống quản trị cơ sở dữ liệu quan hệ (**RDBMS**).
* Ngày nay, nhiều công ty chuyển sang dùng RDBMS vì dễ học, dễ dùng, dễ quản lý.
* Ví dụ về RDBMS: **Microsoft Access, MySQL, SQL Server, Oracle, PostgreSQL**.

#### Ví dụ minh họa

<figure><img src="../../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

* **Bảng users:** gồm các cột: id, username, first\_name, last\_name,…
* **Bảng posts:** gồm các cột: id, user\_id, date, content,…
* Mối quan hệ: `user_id` trong bảng posts liên kết với `id` trong bảng users.
* Có thể mở rộng liên kết:
  * Từ posts → comments (mỗi comment thuộc về một post).

📌 **Schema**: toàn bộ mối quan hệ giữa các bảng trong cơ sở dữ liệu.

#### Ưu điểm

* Có thể lấy thông tin từ nhiều bảng chỉ bằng **một truy vấn SQL**.
* Rất nhanh, đáng tin cậy khi làm việc với dữ liệu lớn có cấu trúc rõ ràng.
* Ví dụ phổ biến: **MySQL** (được dùng trong module này).

***

### Cơ sở dữ liệu phi quan hệ (Non-relational Databases – NoSQL)

* Không sử dụng bảng, hàng, cột, khóa chính, quan hệ hay schema cố định.
* Thay vào đó, dữ liệu được lưu theo nhiều **mô hình lưu trữ** khác nhau.
* Đặc điểm:
  * **Linh hoạt** (không cần cấu trúc chặt chẽ).
  * **Khả năng mở rộng cao (scalable)**.
  * Phù hợp cho dữ liệu **phi cấu trúc hoặc bán cấu trúc**.

#### Bốn mô hình lưu trữ chính

1. **Key-Value Store**

<figure><img src="../../.gitbook/assets/image (249).png" alt=""><figcaption></figcaption></figure>

* Lưu dữ liệu dưới dạng cặp **key – value**.
* Thường dùng JSON hoặc XML.
* Ví dụ bảng posts:

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

* Tương tự dictionary trong Python/PHP (`{'key':'value'}`).

1. **Document-Based**
   * Lưu dữ liệu dưới dạng tài liệu (documents), thường ở JSON, BSON.
2. **Wide-Column Store**
   * Lưu dữ liệu theo cột (column-oriented), ví dụ: Cassandra.
3. **Graph Database**
   * Lưu dữ liệu dạng nút (nodes) và cạnh (edges).
   * Tối ưu cho dữ liệu có quan hệ phức tạp (social networks).

#### Ví dụ phổ biến

* **MongoDB** là cơ sở dữ liệu NoSQL phổ biến nhất.



## &#x20;Intro to MySQL

### Giới thiệu về MySQL (Intro to MySQL)

Module này giới thiệu SQL injection thông qua MySQL, và việc tìm hiểu kỹ hơn về MySQL và SQL là rất quan trọng để hiểu cách SQL injection hoạt động cũng như cách khai thác nó một cách đúng đắn. Do đó, phần này sẽ trình bày một số kiến thức cơ bản và cú pháp của MySQL/SQL cùng với các ví dụ sử dụng trong cơ sở dữ liệu MySQL/MariaDB.

***

### Structured Query Language (SQL)

* Cú pháp SQL có thể khác nhau giữa các hệ quản trị cơ sở dữ liệu quan hệ (RDBMS).
* Tuy nhiên, tất cả đều phải tuân theo tiêu chuẩn ISO cho Structured Query Language.
* Trong các ví dụ, ta sẽ dùng cú pháp của MySQL/MariaDB.
* SQL có thể dùng để thực hiện các hành động sau:
  * Truy xuất dữ liệu.
  * Cập nhật dữ liệu.
  * Xóa dữ liệu.
  * Tạo bảng và cơ sở dữ liệu mới.
  * Thêm / xóa người dùng.
  * Gán quyền cho những người dùng này.

***

### Command Line

* Tiện ích `mysql` được dùng để xác thực và tương tác với cơ sở dữ liệu MySQL/MariaDB.
* Cờ `-u` dùng để cung cấp tên người dùng, và `-p` để nhập mật khẩu.
* Cờ `-p` nên để trống để hệ thống nhắc nhập mật khẩu, thay vì truyền trực tiếp trên dòng lệnh, bởi vì mật khẩu có thể bị lưu dưới dạng rõ ràng trong file `bash_history`.

Ví dụ:

```bash
$ mysql -u root -p
Enter password: <password>
...SNIP...

mysql>
```

* Ngoài ra cũng có thể truyền trực tiếp mật khẩu, nhưng nên tránh vì mật khẩu có thể bị ghi lại trong log hoặc lịch sử terminal:

```bash
$ mysql -u root -p<password>
...SNIP...

mysql>
```

**Lưu ý:** không có khoảng trắng giữa `-p` và mật khẩu.

* Ví dụ trên đăng nhập với quyền **superuser (root)** bằng mật khẩu “password”, có toàn quyền để thực hiện tất cả các lệnh.
* Các người dùng DBMS khác sẽ có một số quyền hạn nhất định với những câu lệnh mà họ được phép thực thi.
* Có thể xem quyền hiện tại bằng lệnh `SHOW GRANTS`, sẽ được đề cập sau.
* Nếu không chỉ định host, mặc định sẽ kết nối tới **localhost**.
* Có thể chỉ định host và port từ xa bằng cờ `-h` và `-P`.

Ví dụ:

```bash
$ mysql -u root -h docker.hackthebox.eu -P 3306 -p
Enter password:
...SNIP...

mysql>
```

**Lưu ý:**

* Port mặc định của MySQL/MariaDB là `3306`, nhưng có thể cấu hình sang port khác.
* Khi chỉ định port, phải dùng `P` viết hoa, khác với `p` viết thường dùng cho mật khẩu.

**Lưu ý thực hành:** Để làm theo các ví dụ, hãy dùng công cụ `mysql` trong PwnBox để đăng nhập vào DBMS được cho trong câu hỏi cuối phần này, sử dụng IP và port tương ứng. Tài khoản: `root`, mật khẩu: `password`.

***

### Tạo cơ sở dữ liệu (Creating a database)

* Sau khi đăng nhập vào MySQL bằng `mysql`, ta có thể bắt đầu dùng truy vấn SQL để tương tác với DBMS.
* Ví dụ: tạo cơ sở dữ liệu mới bằng lệnh `CREATE DATABASE`:

```sql
mysql> CREATE DATABASE users;
Query OK, 1 row affected (0.02 sec)
```

* MySQL yêu cầu mọi câu lệnh trên command line phải kết thúc bằng dấu chấm phẩy `;`.
* Ví dụ trên đã tạo một cơ sở dữ liệu mới tên là `users`.
* Liệt kê danh sách các cơ sở dữ liệu bằng lệnh `SHOW DATABASES`:

```sql
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+
```

* Chuyển sang cơ sở dữ liệu `users` bằng lệnh `USE`:

```sql
mysql> USE users;
Database changed
```

**Lưu ý:**

* Các câu lệnh SQL **không phân biệt chữ hoa chữ thường**, nghĩa là `USE users;` và `use users;` giống nhau.
* Tuy nhiên, tên cơ sở dữ liệu thì **có phân biệt chữ hoa chữ thường**, nên không thể dùng `USE USERS;` thay cho `USE users;`.
* Thói quen tốt: viết các từ khóa SQL bằng chữ in hoa để tránh nhầm lẫn.

***

### Bảng (Tables)

* DBMS lưu trữ dữ liệu dưới dạng bảng.
* Một bảng gồm các hàng (row) theo chiều ngang và các cột (column) theo chiều dọc.
* Giao điểm giữa một hàng và một cột gọi là **cell**.
* Mỗi bảng được tạo với một tập hợp cột cố định, trong đó mỗi cột có một **kiểu dữ liệu (data type)** cụ thể.
* Kiểu dữ liệu xác định loại giá trị mà cột có thể chứa.
* Ví dụ: số, chuỗi, ngày, giờ, dữ liệu nhị phân.
* Có những kiểu dữ liệu đặc thù riêng cho từng DBMS. (Danh sách đầy đủ kiểu dữ liệu của MySQL có thể tham khảo tài liệu chính thức.)

Ví dụ: tạo bảng `logins` để lưu thông tin người dùng:

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
);
```

* Trong câu lệnh `CREATE TABLE`, đầu tiên chỉ định tên bảng, sau đó (trong ngoặc đơn) là danh sách cột, gồm tên cột và kiểu dữ liệu, ngăn cách bởi dấu phẩy.
* Sau tên và kiểu dữ liệu, có thể chỉ định thêm các thuộc tính, sẽ được bàn sau.

Ví dụ trên trong MySQL shell:

```sql
mysql> CREATE TABLE logins (
    -> id INT,
    -> username VARCHAR(100),
    -> password VARCHAR(100),
    -> date_of_joining DATETIME
    -> );
Query OK, 0 rows affected (0.03 sec)
```

* Câu lệnh trên đã tạo một bảng tên `logins` với bốn cột:
  * `id`: số nguyên.
  * `username`: chuỗi tối đa 100 ký tự.
  * `password`: chuỗi tối đa 100 ký tự.
  * `date_of_joining`: ngày giờ, lưu thời điểm thêm bản ghi.
* Nếu nhập chuỗi dài hơn 100 ký tự cho `username` hoặc `password` thì sẽ bị lỗi.
* Liệt kê các bảng trong cơ sở dữ liệu hiện tại bằng `SHOW TABLES`:

```sql
mysql> SHOW TABLES;

+-----------------+
| Tables_in_users |
+-----------------+
| logins          |
+-----------------+
1 row in set (0.00 sec)
```

* Xem cấu trúc bảng với `DESCRIBE`:

```sql
mysql> DESCRIBE logins;

+-----------------+--------------+
| Field           | Type         |
+-----------------+--------------+
| id              | int          |
| username        | varchar(100) |
| password        | varchar(100) |
| date_of_joining | date         |
+-----------------+--------------+
4 rows in set (0.00 sec)
```

***

### Thuộc tính bảng (Table Properties)

* Trong câu lệnh `CREATE TABLE`, có nhiều thuộc tính có thể thiết lập cho bảng hoặc từng cột.

Ví dụ:

* **AUTO\_INCREMENT**: tự động tăng giá trị mỗi khi thêm bản ghi mới.

```sql
id INT NOT NULL AUTO_INCREMENT,
```

* **NOT NULL**: đảm bảo một cột không được để trống (bắt buộc nhập giá trị).
* **UNIQUE**: đảm bảo giá trị trong cột là duy nhất.

```sql
username VARCHAR(100) UNIQUE NOT NULL,
```

→ không cho phép có hai người dùng cùng tên đăng nhập.

* **DEFAULT**: thiết lập giá trị mặc định.

```sql
date_of_joining DATETIME DEFAULT NOW(),
```

→ nếu không nhập gì, mặc định lấy ngày giờ hiện tại.

* **PRIMARY KEY**: định nghĩa khóa chính để nhận diện duy nhất mỗi bản ghi.

```sql
PRIMARY KEY (id)
```

***

### Ví dụ hoàn chỉnh

Câu lệnh `CREATE TABLE` với đầy đủ thuộc tính:

```sql
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
);
```



## &#x20;SQL Statements

### Các câu lệnh SQL (SQL Statements)

Bây giờ khi đã hiểu cách dùng tiện ích `mysql` và tạo cơ sở dữ liệu/bảng, chúng ta sẽ xem một số câu lệnh SQL thiết yếu và mục đích sử dụng của chúng.

***

### Câu lệnh INSERT (INSERT Statement)

* `INSERT` được dùng để thêm bản ghi mới vào một bảng cho trước.
* Cú pháp như sau:

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);
```

* Cú pháp trên yêu cầu người dùng cung cấp giá trị cho **tất cả** các cột có trong bảng.

Ví dụ:

```
mysql> INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');

Query OK, 1 row affected (0.00 sec)
```

Ví dụ trên cho thấy cách thêm một login mới vào bảng `logins`, với giá trị phù hợp cho từng cột. Tuy nhiên, chúng ta có thể **bỏ qua** việc điền các cột có **giá trị mặc định**, như `id` và `date_of_joining`. Điều này được thực hiện bằng cách **chỉ rõ danh sách cột** sẽ chèn giá trị để chèn chọn lọc:

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);
```

**Lưu ý:** bỏ qua các cột có ràng buộc `NOT NULL` sẽ dẫn đến lỗi, vì đây là các giá trị bắt buộc.

Áp dụng cho bảng `logins`:

```
mysql> INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');

Query OK, 1 row affected (0.00 sec)
```

Trong ví dụ trên, chúng ta đã chèn cặp `username`–`password` đồng thời bỏ qua các cột `id` và `date_of_joining`.

**Lưu ý:** Các ví dụ đưa mật khẩu **dạng rõ (cleartext)** vào bảng chỉ nhằm mục đích minh họa. Đây là thực hành **không an toàn**; mật khẩu luôn phải **băm/mã hóa** trước khi lưu trữ.

Chúng ta cũng có thể chèn **nhiều bản ghi** một lúc bằng cách phân tách chúng bằng dấu phẩy:

```
mysql> INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');

Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

Truy vấn trên đã chèn hai bản ghi mới trong một lệnh.

***

### Câu lệnh SELECT (SELECT Statement)

Bây giờ khi đã chèn dữ liệu vào các bảng, hãy xem cách truy xuất dữ liệu với câu lệnh `SELECT`. Câu lệnh này cũng có thể dùng cho nhiều mục đích khác, mà ta sẽ gặp ở các phần sau. Cú pháp tổng quát để xem **toàn bộ bảng** như sau:

```sql
SELECT * FROM table_name;
```

Ký tự hoa thị `*` đóng vai trò wildcard và chọn **tất cả cột**. Từ khóa `FROM` dùng để chỉ định bảng cần chọn. Ta cũng có thể chỉ xem dữ liệu trong **các cột cụ thể**:

```sql
SELECT column1, column2 FROM table_name;
```

Truy vấn trên sẽ chỉ chọn dữ liệu có trong `column1` và `column2`.

Ví dụ:

```
mysql> SELECT * FROM logins;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)


mysql> SELECT username,password FROM logins;

+---------------+------------+
| username      | password   |
+---------------+------------+
| admin         | p@ssw0rd   |
| administrator | adm1n_p@ss |
| john          | john123!   |
| tom           | tom123!    |
+---------------+------------+
4 rows in set (0.00 sec)
```

* Truy vấn đầu tiên xem tất cả bản ghi hiện có trong bảng `logins`. Chúng ta thấy bốn bản ghi đã được chèn trước đó.
* Truy vấn thứ hai chỉ chọn hai cột `username` và `password`, bỏ qua hai cột còn lại.

***

### Câu lệnh DROP (DROP Statement)

Chúng ta có thể dùng `DROP` để **xóa** bảng và cơ sở dữ liệu khỏi máy chủ.

```
mysql> DROP TABLE logins;

Query OK, 0 rows affected (0.01 sec)


mysql> SHOW TABLES;

Empty set (0.00 sec)
```

Như có thể thấy, bảng đã bị **xóa hoàn toàn**.

**Cảnh báo:** `DROP` sẽ **xóa vĩnh viễn** bảng mà **không có xác nhận**, vì vậy cần thận trọng khi sử dụng.

***

### Câu lệnh ALTER (ALTER Statement)

Cuối cùng, chúng ta có thể dùng `ALTER` để **đổi tên** bất kỳ bảng/cột nào hoặc **xóa/thêm cột mới** vào một bảng hiện có. Ví dụ dưới đây thêm cột mới `newColumn` kiểu `INT` vào bảng `logins` bằng `ADD`:

```
mysql> ALTER TABLE logins ADD newColumn INT;

Query OK, 0 rows affected (0.01 sec)
```

Để **đổi tên cột**, dùng `RENAME COLUMN`:

```
mysql> ALTER TABLE logins RENAME COLUMN newColumn TO newerColumn;

Query OK, 0 rows affected (0.01 sec)
```

Chúng ta cũng có thể **đổi kiểu dữ liệu** của cột bằng `MODIFY`:

```
mysql> ALTER TABLE logins MODIFY newerColumn DATE;

Query OK, 0 rows affected (0.01 sec)
```

Cuối cùng, có thể **xóa cột** bằng `DROP`:

```
mysql> ALTER TABLE logins DROP newerColumn;

Query OK, 0 rows affected (0.01 sec)
```

Chúng ta có thể dùng bất kỳ câu lệnh nào ở trên với **bất kỳ bảng hiện có**, miễn là tài khoản có đủ **đặc quyền** để thực hiện.

***

### Câu lệnh UPDATE (UPDATE Statement)

Trong khi `ALTER` dùng để thay đổi **thuộc tính bảng**, thì `UPDATE` dùng để **cập nhật các bản ghi cụ thể** trong bảng, dựa trên các điều kiện nhất định. Cú pháp tổng quát:

```sql
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;
```

Chúng ta chỉ định tên bảng, từng cột và giá trị mới của nó, cùng điều kiện để cập nhật bản ghi. Ví dụ:

```
mysql> UPDATE logins SET password = 'change_password' WHERE id > 1;

Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0
```

Kiểm tra lại:

```
mysql> SELECT * FROM logins;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:47:16 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)
```

## &#x20;Query Results

### Kết quả truy vấn (Query Results)

Trong phần này, chúng ta sẽ học cách **kiểm soát đầu ra của truy vấn**.

***

### Sắp xếp kết quả (Sorting Results)

Chúng ta có thể sắp xếp kết quả của bất kỳ truy vấn nào bằng **ORDER BY** và chỉ định cột để sắp xếp:

```
mysql> SELECT * FROM logins ORDER BY password;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

Mặc định, việc sắp xếp được thực hiện theo **thứ tự tăng dần (ASC)**, nhưng ta cũng có thể chỉ định **ASC** hoặc **DESC**:

```
mysql> SELECT * FROM logins ORDER BY password DESC;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

Ta cũng có thể sắp xếp theo **nhiều cột**, để có thứ tự phụ khi một cột có giá trị trùng lặp:

```
mysql> SELECT * FROM logins ORDER BY password DESC, id ASC;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:50:20 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)
```

***

### Giới hạn kết quả (LIMIT results)

Trong trường hợp truy vấn trả về quá nhiều bản ghi, ta có thể dùng **LIMIT** để chỉ lấy số bản ghi mong muốn:

```
mysql> SELECT * FROM logins LIMIT 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

Nếu muốn giới hạn kết quả với **offset**, ta chỉ định offset trước số lượng bản ghi:

```
mysql> SELECT * FROM logins LIMIT 1, 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

**Lưu ý:** offset đánh dấu bản ghi đầu tiên cần lấy, bắt đầu từ **0**. Ví dụ trên bắt đầu từ bản ghi thứ 2 và trả về 2 bản ghi.

***

### Mệnh đề WHERE (WHERE Clause)

Để lọc hoặc tìm dữ liệu cụ thể, ta dùng điều kiện với câu lệnh `SELECT` bằng mệnh đề `WHERE` để tinh chỉnh kết quả:

```sql
SELECT * FROM table_name WHERE <condition>;
```

Ví dụ:

```
mysql> SELECT * FROM logins WHERE id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)
```

Truy vấn trên chọn tất cả bản ghi có `id > 1`. Bản ghi đầu tiên (`id=1`) bị bỏ qua.

Ví dụ khác:

```
mysql> SELECT * FROM logins WHERE username = 'admin';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)
```

* Truy vấn trên chọn bản ghi có `username = 'admin'`.
* Câu lệnh `UPDATE` cũng có thể dùng cùng điều kiện để cập nhật những bản ghi nhất định.

**Lưu ý:** dữ liệu dạng **chuỗi và ngày** nên được đặt trong **nháy đơn `'`** hoặc **nháy kép `"`**, còn số thì có thể dùng trực tiếp.

***

### Mệnh đề LIKE (LIKE Clause)

Một mệnh đề hữu ích khác là **LIKE**, cho phép chọn bản ghi bằng cách khớp với một mẫu (pattern).

Ví dụ: lấy tất cả bản ghi có `username` bắt đầu bằng `admin`:

```
mysql> SELECT * FROM logins WHERE username LIKE 'admin%';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | administrator | adm1n_p@ss | 2020-07-02 15:19:02 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

* Ký hiệu `%` đóng vai trò **wildcard**, khớp với **0 hoặc nhiều ký tự** sau `admin`.
* Ký hiệu `_` khớp với **chính xác một ký tự**.

Ví dụ: chọn tất cả `username` có đúng **3 ký tự** (trường hợp này là `tom`):

```
mysql> SELECT * FROM logins WHERE username LIKE '___';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  3 | tom      | tom123!  | 2020-07-02 15:18:56 |
+----+----------+----------+---------------------+
1 row in set (0.01 sec)
```

<figure><img src="../../.gitbook/assets/image (587).png" alt=""><figcaption></figcaption></figure>





## SQL Operators

### Các toán tử SQL (SQL Operators)

Đôi khi, một biểu thức với một điều kiện duy nhất là chưa đủ để thỏa mãn yêu cầu của người dùng. Vì thế, SQL hỗ trợ **toán tử logic** để sử dụng nhiều điều kiện cùng lúc. Các toán tử logic phổ biến nhất là **AND, OR, NOT**.

***

### Toán tử AND (AND Operator)

* Toán tử `AND` nhận vào hai điều kiện và trả về **true hoặc false** dựa trên kết quả đánh giá của chúng:

```sql
condition1 AND condition2
```

* Kết quả của phép `AND` là **true nếu và chỉ nếu cả hai điều kiện đều đúng**.

Ví dụ:

```
mysql> SELECT 1 = 1 AND 'test' = 'test';

+---------------------------+
| 1 = 1 AND 'test' = 'test' |
+---------------------------+
|                         1 |
+---------------------------+
1 row in set (0.00 sec)


mysql> SELECT 1 = 1 AND 'test' = 'abc';

+--------------------------+
| 1 = 1 AND 'test' = 'abc' |
+--------------------------+
|                        0 |
+--------------------------+
1 row in set (0.00 sec)
```

* Trong MySQL, bất kỳ giá trị nào khác 0 được coi là **true** (thường trả về 1).
* `0` được coi là **false**.
* Như trong ví dụ:
  * Truy vấn đầu tiên trả về `true` vì cả hai điều kiện đều đúng.
  * Truy vấn thứ hai trả về `false` vì `'test' = 'abc'` là sai.

***

### Toán tử OR (OR Operator)

* Toán tử `OR` cũng nhận vào hai biểu thức và trả về `true` nếu ít nhất **một trong hai** đúng.

Ví dụ:

```
mysql> SELECT 1 = 1 OR 'test' = 'abc';

+-------------------------+
| 1 = 1 OR 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)


mysql> SELECT 1 = 2 OR 'test' = 'abc';

+-------------------------+
| 1 = 2 OR 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set (0.00 sec)
```

* Truy vấn đầu tiên trả về `true` vì điều kiện `1 = 1` đúng.
* Truy vấn thứ hai trả về `false` vì cả hai điều kiện đều sai.

***

### Toán tử NOT (NOT Operator)

* Toán tử `NOT` đảo ngược giá trị boolean: **true → false, false → true**.

Ví dụ:

```
mysql> SELECT NOT 1 = 1;

+-----------+
| NOT 1 = 1 |
+-----------+
|         0 |
+-----------+
1 row in set (0.00 sec)


mysql> SELECT NOT 1 = 2;

+-----------+
| NOT 1 = 2 |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)
```

* Truy vấn đầu tiên trả về `false` vì đảo ngược kết quả của `1=1` (true → false).
* Truy vấn thứ hai trả về `true` vì đảo ngược kết quả của `1=2` (false → true).

***

### Toán tử ký hiệu (Symbol Operators)

* Các toán tử **AND, OR, NOT** có thể được biểu diễn bằng ký hiệu:
  * `&&` thay cho `AND`
  * `||` thay cho `OR`
  * `!` thay cho `NOT`

Ví dụ:

```
mysql> SELECT 1 = 1 && 'test' = 'abc';

+-------------------------+
| 1 = 1 && 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)


mysql> SELECT 1 = 1 || 'test' = 'abc';

+-------------------------+
| 1 = 1 || 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)


mysql> SELECT 1 != 1;

+--------+
| 1 != 1 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)
```

***

### Sử dụng toán tử trong truy vấn (Operators in queries)

Ví dụ: chọn tất cả bản ghi mà `username` **khác** `'john'`:

```
mysql> SELECT * FROM logins WHERE username != 'john';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)
```

Ví dụ khác: chọn tất cả người dùng có `id > 1` **và** `username != 'john'`:

```
mysql> SELECT * FROM logins WHERE username != 'john' AND id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```

***

### Độ ưu tiên của toán tử (Multiple Operator Precedence)

SQL hỗ trợ nhiều phép toán khác như cộng, chia, cũng như toán tử bitwise. Do đó, một truy vấn có thể có nhiều biểu thức và phép toán cùng lúc. **Thứ tự thực hiện** được quyết định bởi **độ ưu tiên toán tử**.

Danh sách một số toán tử phổ biến và độ ưu tiên (theo tài liệu MariaDB):

1. Chia (`/`), Nhân (`*`), Modulus (`%`)
2. Cộng (`+`) và Trừ (`-`)
3. So sánh (`=, >, <, <=, >=, !=, LIKE`)
4. `NOT` (`!`)
5. `AND` (`&&`)
6. `OR` (`||`)

👉 Các toán tử ở **trên** được đánh giá **trước** những cái ở **dưới**.

Ví dụ:

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;
```

* Truy vấn có 4 toán tử: `!=`, `AND`, `>`, `-`.
* Theo thứ tự ưu tiên:
  1. Thực hiện phép trừ: `3 - 2 = 1`.
  2.  Truy vấn thành:

      ```sql
      SELECT * FROM logins WHERE username != 'tom' AND id > 1;
      ```
  3. Đánh giá hai phép so sánh (`!=` và `>`), sau đó áp dụng `AND`.

Kết quả:

```
mysql> SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-03 12:03:53 |
|  3 | john          | john123!   | 2020-07-03 12:03:57 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)
```



## Intro to SQL Injections

### Giới thiệu về SQL Injections (Intro to SQL Injections)

Giờ đây khi đã có cái nhìn tổng quan về cách MySQL và các truy vấn SQL hoạt động, hãy tìm hiểu về **SQL injection**.

***

### Việc sử dụng SQL trong các ứng dụng web (Use of SQL in Web Applications)

* Các ứng dụng web thường sử dụng cơ sở dữ liệu (ở đây là MySQL) để lưu trữ và truy xuất dữ liệu.
* Khi một DBMS được cài đặt và chạy trên máy chủ back-end, ứng dụng web có thể bắt đầu dùng nó.

Ví dụ trong ứng dụng PHP:

```php
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);
```

* Kết quả của truy vấn sẽ được lưu trong `$result`.
* Có thể in ra trang web hoặc xử lý thêm:

```php
while($row = $result->fetch_assoc() ){
    echo $row["name"]."<br>";
}
```

* Ứng dụng web cũng thường dùng **dữ liệu do người dùng nhập** khi truy vấn.\
  Ví dụ: chức năng tìm kiếm người dùng:

```php
$searchInput = $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

Nếu không lập trình an toàn, việc dùng trực tiếp dữ liệu người dùng trong truy vấn có thể gây ra lỗ hổng **SQL Injection**.

***

### Injection là gì? (What is an Injection?)

* Ở ví dụ trên, ta chấp nhận input của người dùng và đưa trực tiếp vào truy vấn SQL **mà không có bước lọc (sanitization)**.
* **Sanitization**: loại bỏ các ký tự đặc biệt trong input để chặn các nỗ lực chèn mã độc.
* **Injection** xảy ra khi ứng dụng hiểu nhầm input của người dùng là **mã thực thi** thay vì chuỗi, từ đó thay đổi luồng lệnh.

Ví dụ: bằng cách thêm ký tự `'`, người tấn công có thể thoát khỏi chuỗi input và chèn mã SQL.

***

### SQL Injection

SQL injection xảy ra khi input của người dùng được chèn trực tiếp vào chuỗi truy vấn SQL mà không được lọc an toàn.

Ví dụ:

```php
$searchInput = $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

* Thông thường, input sẽ được thêm vào câu lệnh và trả về kết quả mong muốn.
* Ví dụ: nhập `admin` → câu lệnh thành:

```sql
select * from logins where username like '%admin'
```

* Nhưng nếu nhập:

```
1'; DROP TABLE users;
```

→ `$searchInput` thành:

```php
'%1'; DROP TABLE users;'
```

* Truy vấn SQL cuối cùng:

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

→ Khi chạy, bảng `users` sẽ bị xóa.

**Lưu ý:**

* Ví dụ trên dùng ký tự `;` để chạy thêm câu lệnh mới.
* Thực tế, MySQL **không hỗ trợ nhiều câu lệnh** trong một query như vậy (khác với MSSQL hoặc PostgreSQL).
* Các phần sau sẽ trình bày các cách SQLi **thực tế** trong MySQL.

***

### Lỗi cú pháp (Syntax Errors)

Ví dụ SQL injection ở trên sẽ gây lỗi:

```php
Error: near line 1: near "'": syntax error
```

Do có dấu `'` dư thừa, làm SQL lỗi cú pháp:

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

Để injection thành công, truy vấn sau khi chèn phải **hợp lệ về cú pháp**.

* Thông thường kẻ tấn công **không có source code** để biết chính xác cấu trúc query.
* Vậy làm sao để chèn thành công?

Một cách: dùng **comment** để bỏ phần còn lại của query.\
Một cách khác: dùng nhiều dấu `'` để cân bằng lại cú pháp.

***

### Các loại SQL Injections (Types of SQL Injections)

SQL Injection được phân loại dựa trên cách và nơi ta lấy kết quả.

<figure><img src="../../.gitbook/assets/image (588).png" alt=""><figcaption></figcaption></figure>

**Sơ đồ các loại SQLi:**

* **In-band**: Union-based, Error-based
* **Blind**: Boolean-based, Time-based
* **Out-of-band**

***

#### In-band SQL Injection

* **Union-based**: cần xác định đúng cột để in dữ liệu ra frontend.
* **Error-based**: lợi dụng thông báo lỗi PHP/SQL để lấy dữ liệu.

#### Blind SQL Injection

* Khi kết quả không hiển thị trực tiếp.
* **Boolean-based**: dùng điều kiện `IF` để điều khiển phản hồi (true/false).
* **Time-based**: dùng hàm `SLEEP()` để làm chậm phản hồi nếu điều kiện đúng.

#### Out-of-band SQL Injection

* Khi không có đầu ra trực tiếp.
* Dữ liệu được gửi ra ngoài (ví dụ: DNS record), sau đó attacker thu thập lại.





