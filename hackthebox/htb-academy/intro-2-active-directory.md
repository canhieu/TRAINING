---
hidden: true
---

# Intro 2 Active Directory

## Active Directory Fumda

### Active Directory Structure

* Active Directory (AD) là dịch vụ thư mục dùng trong môi trường mạng Windows, giúp quản lý tập trung các tài nguyên của tổ chức như người dùng, máy tính, nhóm, thiết bị mạng, file chia sẻ, máy chủ, máy trạm và chính sách nhóm.
* AD cung cấp chức năng **xác thực** và **phân quyền** trong hệ thống Windows Domain. Dịch vụ AD DS lưu trữ thông tin như tên đăng nhập, mật khẩu và quyền truy cập của người dùng.
* AD xuất hiện từ Windows Server 2000 và vẫn được sử dụng rộng rãi. Tuy nhiên, do thiết kế tương thích ngược và nhiều tính năng không an toàn mặc định, AD dễ bị cấu hình sai, đặc biệt trong các hệ thống lớn.
* Các lỗi và cấu hình sai trong AD có thể bị lợi dụng để xâm nhập vào mạng nội bộ, di chuyển giữa các hệ thống, leo thang đặc quyền và truy cập trái phép vào tài nguyên quan trọng như cơ sở dữ liệu, file chia sẻ hoặc mã nguồn.
* Về bản chất, AD giống như một cơ sở dữ liệu lớn mà người dùng trong domain có thể truy cập. Ngay cả tài khoản người dùng thông thường, không có quyền cao, cũng có thể xem và liệt kê nhiều đối tượng trong AD.

|                          |                             |
| ------------------------ | --------------------------- |
| Domain Computers         | Domain Users                |
| Domain Group Information | Organizational Units (OUs)  |
| Default Domain Policy    | Functional Domain Levels    |
| Password Policy          | Group Policy Objects (GPOs) |
| Domain Trusts            | Access Control Lists (ACLs) |

Active Directory được tổ chức theo cấu trúc cây phân cấp. Cấp cao nhất là **Forest**, bên trong có một hoặc nhiều **Domain**. Mỗi domain có thể có thêm các domain con.

**Forest** là ranh giới bảo mật lớn nhất, nơi tất cả đối tượng được quản lý bởi quyền quản trị. **Domain** là khu vực chứa và quản lý các đối tượng như người dùng, máy tính và nhóm.

Trong domain có các **Organizational Units (OU)** mặc định như **Domain Controllers**, **Users**, **Computers**. Quản trị viên cũng có thể tạo OU mới khi cần.

OU có thể chứa các đối tượng hoặc OU con, giúp tổ chức hệ thống dễ hơn và áp dụng các **Group Policy** khác nhau cho từng nhóm đối tượng.

At a very (simplistic) high level, an AD structure may look as follows:

```bash
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
│               └── barbara.jones
├── CORP.INLANEFREIGHT.LOCAL
└── DEV.INLANEFREIGHT.LOCAL
```

Có thể hiểu **INLANEFREIGHT.LOCAL** là **root domain**, bên trong chứa các domain con như **ADMIN**, **CORP**, và **DEV**, cùng các đối tượng như user, group, computer,...

Trong các tổ chức có nhiều thương vụ mua lại, nhiều domain hoặc forest thường được liên kết với nhau bằng **trust relationship**. Cách này nhanh và dễ hơn việc tạo lại toàn bộ user trong domain hiện tại.

Tuy nhiên, nếu không quản lý đúng cách, **domain trust** có thể gây ra nhiều rủi ro bảo mật.

<figure><img src="../../.gitbook/assets/image (857).png" alt=""><figcaption></figcaption></figure>



Hình minh họa có hai forest: **INLANEFREIGHT.LOCAL** và **FREIGHTLOGISTICS.LOCAL**. Mũi tên hai chiều thể hiện **bidirectional trust**, nghĩa là user ở forest này có thể truy cập tài nguyên ở forest kia và ngược lại.

Mỗi forest có nhiều **child domain**. Tuy nhiên, việc hai **root domain** tin cậy nhau không có nghĩa là tất cả child domain của hai forest đều tự động tin cậy nhau.

Ví dụ, user thuộc **admin.dev.freightlogistics.local** mặc định sẽ **không thể xác thực** vào máy trong domain **wh.corp.inlanefreight.local**, dù hai root domain có trust hai chiều.

Muốn hai child domain này giao tiếp trực tiếp, cần tạo thêm một **trust relationship** riêng giữa chúng.



<figure><img src="../../.gitbook/assets/image (858).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (859).png" alt=""><figcaption></figcaption></figure>



