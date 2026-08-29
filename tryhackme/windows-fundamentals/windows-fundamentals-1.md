# Windows Fundamentals 1

Task 1&#x20;Windows Editions
----------------------

### Tổng Quan

Hệ điều hành **Windows** có lịch sử lâu đời, bắt đầu từ năm **1985**. Hiện tại, Windows là hệ điều hành **chiếm ưu thế** cả trong môi trường sử dụng cá nhân lẫn mạng doanh nghiệp. Chính vì sự phổ biến này, Windows luôn là **mục tiêu hàng đầu** của hacker và các malware dev.

### Dòng Thời Gian Phát Triển

**Windows XP**

* Là phiên bản rất **phổ biến** và có vòng đời sử dụng dài.
* Được đông đảo người dùng và doanh nghiệp tin dùng trong nhiều năm.

**Windows Vista**

* Microsoft công bố Vista như một sự **cải tổ toàn diện** hệ điều hành Windows.
* Tuy nhiên, Vista gặp **rất nhiều vấn đề** và không được người dùng đón nhận.
* Nhanh chóng bị **loại bỏ dần** khỏi thị trường.

**Giai Đoạn Chuyển Đổi Từ XP Sang Windows 7**

* Khi Microsoft công bố **end-of-life** cho Windows XP, nhiều khách hàng **hoảng loạn**.
* Các tổ chức: doanh nghiệp, bệnh viện,... phải **gấp rút test** phiên bản Windows tiếp theo khả thi — chính là **Windows 7** — với hardware và thiết bị hiện có.
* Các vendor phải **chạy đua với thời gian** để đảm bảo sản phẩm của họ tương thích với Windows 7.
* Nếu không kịp upgrade, khách hàng buộc phải **chấm dứt hợp đồng** và tìm nhà cung cấp khác. Đây là **cơn ác mộng** với rất nhiều bên, và Microsoft đã ghi nhận điều này.

**Windows 7**

* Được phát hành nhanh chóng sau Vista và được đón nhận tốt hơn nhiều.
* Tuy nhiên, không lâu sau cũng bị đánh dấu **end-of-support**.

**Windows 8.x**

* Có vòng đời ngắn, tương tự như Vista.
* Không tạo được ấn tượng mạnh với người dùng.

**Windows 10**

* Phiên bản ổn định và được sử dụng rộng rãi.
* **Tháng 6/2021**: Microsoft công bố lịch **ngừng hỗ trợ** Windows 10.
* > _"Microsoft sẽ tiếp tục hỗ trợ ít nhất một kênh Semi-Annual Channel của Windows 10 cho đến ngày **14 tháng 10, 2025**."_

**Windows 11**

* Là hệ điều hành Windows **hiện tại** dành cho desktop.
* Có **2 phiên bản**: **Home** và **Pro** (với các tính năng khác nhau).
* Chính thức trở thành hệ điều hành cho end-user từ **5 tháng 10, 2021**.

### Windows Server

* Mặc dù phần trên tập trung vào phiên bản dành cho end-user, phiên bản **hiện tại** của hệ điều hành Windows dành cho **server** là **Windows Server 2025**.

> **Ghi chú:** Phiên bản Windows của VM đính kèm trong bài lab là **Windows Server 2019 Standard**, như được hiển thị trong mục **System Information**.

<figure><img src="../../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>



## Task 2 The Desktop (GUI)

### Tổng Quan

Windows Desktop, hay còn gọi là GUI (Graphical User Interface), là màn hình chào đón bạn khi bạn đăng nhập vào máy Windows 10.

Theo truyền thống, bạn cần vượt qua login screen trước. Login screen là nơi bạn nhập account credentials hợp lệ — thường là username và password của một Windows account đã tồn tại trên hệ thống đó, hoặc trong môi trường Active Directory (nếu máy đã domain-joined).

<figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

Ảnh chụp màn hình phía trên là ví dụ về một Windows Desktop điển hình. Mỗi thành phần tạo nên GUI được giải thích ngắn gọn bên dưới:

1. Desktop
2. Start Menu
3. Search Box (Cortana)
4. Task View
5. Taskbar
6. Toolbars
7. Notification Area

### Desktop

Desktop là nơi bạn sẽ có các shortcut đến các chương trình, folder, file, v.v. Các icon này có thể được tổ chức gọn gàng trong các folder sắp xếp theo thứ tự bảng chữ cái, hoặc nằm rải rác ngẫu nhiên trên desktop. Dù trong trường hợp nào, các item này thường được đặt trên desktop để truy cập nhanh.

Giao diện của desktop có thể được thay đổi theo sở thích của bạn. Bằng cách right-click vào bất kỳ đâu trên desktop, một context menu sẽ xuất hiện. Menu này cho phép bạn thay đổi kích thước icon trên desktop, chỉ định cách sắp xếp chúng, copy/paste item vào desktop, và tạo item mới như folder, shortcut, hoặc text document.

<figure><img src="../../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

Trong mục **Display settings**, bạn có thể thay đổi resolution và orientation của màn hình. Trong trường hợp bạn có nhiều màn hình, bạn có thể cấu hình multi-screen setup tại đây.

<figure><img src="../../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

> **Lưu ý:** Trong phiên Remote Desktop, một số display settings sẽ bị vô hiệu hóa.

<figure><img src="../../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

Bạn cũng có thể thay đổi wallpaper bằng cách chọn **Personalize**.

Trong mục **Personalize**, bạn có thể thay đổi background image của Desktop, thay đổi font, theme, color scheme, v.v.

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

### Start Menu

Trong các phiên bản Windows trước, chữ "Start" hiển thị ở góc dưới bên trái của desktop GUI. Trong các phiên bản Windows hiện đại như Windows 10, chữ "Start" không còn xuất hiện nữa mà thay vào đó là Windows Logo. Mặc dù giao diện của Start Menu đã thay đổi, mục đích chung của nó vẫn giữ nguyên.

<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

Start Menu cung cấp quyền truy cập tới tất cả các app/program, file, utility tool, v.v. hữu ích nhất.

Nhấp vào Windows Logo, Start Menu sẽ mở ra. Start Menu được chia thành các phần:

**1. Phần phím tắt tài khoản**

<figure><img src="../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

Phần này cung cấp shortcut nhanh đến các hành động bạn có thể thực hiện với account hoặc login session, như thay đổi user account, lock màn hình, hoặc sign out. Các shortcut khác liên quan đến account gồm folder Documents (icon tài liệu) và folder Pictures (icon hình ảnh). Cuối cùng, icon bánh răng sẽ đưa bạn đến màn hình Settings, và icon nguồn cho phép bạn Disconnect khỏi phiên Remote Desktop, shut down hoặc restart máy tính.

Để mở rộng phần này, nhấp vào icon hình hamburger ở trên cùng.

**2. Phần danh sách ứng dụng**

<figure><img src="../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

Phần này hiển thị tất cả app/program được thêm gần đây ở trên cùng, và tất cả app/program đã cài đặt (được cấu hình để xuất hiện trong Start Menu). Các app/program sẽ được liệt kê theo thứ tự bảng chữ cái, mỗi chữ cái có phần riêng.

> **Lưu ý:** Trong VM của bạn, Google Chrome sẽ không còn hiển thị như một chương trình Recently Added nữa.

Nếu bạn có danh sách app/program đã cài đặt RẤT DÀI, bạn có thể nhảy đến một phần cụ thể bằng cách nhấp vào các tiêu đề chữ cái để mở ra một alphabet grid.

> **Lưu ý:** Các chữ cái màu trắng khớp với các tiêu đề chữ cái.

<figure><img src="../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

**3. Phần Tiles**

<figure><img src="../../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

Phía bên phải của Start Menu là nơi bạn sẽ tìm thấy các icon cho app/program hoặc utility cụ thể. Các icon này được gọi là **tiles**. Một số tiles được thêm vào phần này theo mặc định. Nếu bạn right-click vào bất kỳ tile nào, một menu sẽ xuất hiện cho phép bạn thực hiện các hành động như resize tile, unpin khỏi Start Menu, xem Properties, v.v.

App/program có thể được thêm vào phần Start Menu này bằng cách right-click vào app/program và chọn **Pin to Start**.

<figure><img src="../../.gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

### Taskbar

Một số thành phần được bật và hiển thị theo mặc định. Ví dụ, Toolbar (6) được bật cho mục đích demo.

Nếu bạn muốn tắt một số thành phần này, bạn có thể right-click vào Taskbar để mở context menu cho phép thực hiện thay đổi.

<figure><img src="../../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>



Bất kỳ app/program, folder, file, v.v. nào mà bạn mở/khởi chạy sẽ xuất hiện trên taskbar.

<figure><img src="../../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>



Di chuột qua icon sẽ hiển thị một preview thumbnail cùng với tooltip. Tooltip này rất hữu ích khi bạn mở nhiều app/program, ví dụ như Google Chrome, và bạn muốn tìm đúng instance mà bạn cần đưa lên foreground.

Khi bạn đóng bất kỳ item nào, chúng sẽ biến mất khỏi taskbar (trừ khi bạn đã pin nó vào taskbar).

### Notification Area

<figure><img src="../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

Notification Area thường nằm ở góc dưới bên phải màn hình Windows, là nơi hiển thị ngày giờ. Các icon khác có thể hiển thị trong khu vực này gồm volume icon, network/wireless icon, v.v. Có thể thêm hoặc xóa icon khỏi Notification Area trong Taskbar settings.

Từ đó, cuộn xuống phần Notification Area để thực hiện thay đổi.

<figure><img src="../../.gitbook/assets/image (199).png" alt=""><figcaption></figcaption></figure>

Đây là tài liệu ngắn gọn của Microsoft về Start Menu và Notification Area.

> **Mẹo:** Bạn có thể right-click vào bất kỳ folder, file, app/program, hoặc icon nào để xem thêm thông tin hoặc thực hiện hành động khác trên item đó.

<figure><img src="../../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

```
Hidden

Right-click vào Taskbar → chọn Search → chọn Hidden để ẩn/tắt Search box.

Show Task View button

Right-click vào Taskbar → bỏ chọn (uncheck) Show Task View button để ẩn nút Task View.

Action Center

Ngoài Clock và Network, icon còn lại hiển thị trong Notification Area trên VM của bài lab là Action Center.

```

## Task 3 Introduction to Windows

## Task 4 The File System

### Tổng Quan <a href="#t-e1-bb-95ng-quan" id="t-e1-bb-95ng-quan"></a>

Hệ thống file được sử dụng trong các phiên bản Windows hiện đại là **NTFS** (New Technology File System).

Trước NTFS, có **FAT16/FAT32** (File Allocation Table) và **HPFS** (High Performance File System). Ngày nay, FAT vẫn được dùng trên các thiết bị USB, MicroSD card, v.v. nhưng thường không dùng trên PC/laptop hoặc Windows server.

NTFS là một **journaling file system** — khi xảy ra lỗi, file system có thể tự động sửa chữa folder/file trên disk bằng thông tin lưu trong log file. FAT không có khả năng này.

### NTFS khắc phục các hạn chế của file system cũ <a href="#ntfs-kh-e1-ba-afc-ph-e1-bb-a5c-c-c3-a1c-h-e1-ba-a1n-ch-e1-ba-bf-c-e1-bb-a7a-file-system-c-c5-a9" id="ntfs-kh-e1-ba-afc-ph-e1-bb-a5c-c-c3-a1c-h-e1-ba-a1n-ch-e1-ba-bf-c-e1-bb-a7a-file-system-c-c5-a9"></a>

* Hỗ trợ file lớn hơn **4GB**
* Thiết lập **permission** cụ thể trên folder và file
* Nén folder và file (**compression**)
* Mã hóa (**EFS** — Encryption File System)

> Để kiểm tra file system trên Windows, right-click vào ổ đĩa cài OS (thường là **C:\\**) → chọn **Properties**.

### NTFS Permissions <a href="#ntfs-permissions" id="ntfs-permissions"></a>

Trên NTFS volume, bạn có thể thiết lập permission cho phép hoặc từ chối truy cập file và folder.

| Permission           | Ý nghĩa                                             |
| -------------------- | --------------------------------------------------- |
| Full Control         | Toàn quyền: đọc, ghi, sửa, xóa, thay đổi permission |
| Modify               | Đọc, ghi, xóa file/folder                           |
| Read & Execute       | Đọc và chạy file                                    |
| List Folder Contents | Xem nội dung folder                                 |
| Read                 | Chỉ đọc                                             |
| Write                | Chỉ ghi                                             |

**Cách xem permission:**

<figure><img src="../../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

1. Right-click file hoặc folder cần kiểm tra
2. Chọn **Properties**
3. Chuyển sang tab **Security**
4. Chọn user, computer, hoặc group trong danh sách **Group or user names** để xem permission

> Tham khảo tài liệu Microsoft để hiểu thêm về **Special Permissions**.

### Alternate Data Streams (ADS) <a href="#alternate-data-streams-a-ds" id="alternate-data-streams-a-ds"></a>

**ADS** là một file attribute đặc trưng của NTFS. Mỗi file có ít nhất một data stream (**$DATA**), và ADS cho phép file chứa nhiều hơn một stream.

* **Windows Explorer** mặc định không hiển thị ADS cho user
* Có thể dùng tool bên thứ ba hoặc **PowerShell** để xem ADS

**Về mặt security:**

* Malware writer đã sử dụng ADS để **ẩn dữ liệu độc hại**
* Không phải lúc nào ADS cũng xấu — ví dụ khi bạn download file từ Internet, identifier được ghi vào ADS để đánh dấu file đó được tải từ Internet

[https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest\_ad\_dark\_2022\_11.svg](https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest_ad_dark_2022_11.svg)

## Task 5 The Windows\System32 Folders

### Tổng Quan

**Windows folder** (`C:\Windows`) theo truyền thống là folder chứa hệ điều hành Windows.

Folder này không nhất thiết phải nằm ở ổ C — nó có thể nằm ở ổ khác và thậm chí trong một folder khác.

<figure><img src="../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

Đây là lúc **environment variable** (cụ thể là system environment variable) phát huy tác dụng. System environment variable cho Windows directory là **%windir%**.

> Theo Microsoft: _"Environment variable lưu trữ thông tin về môi trường hệ điều hành, bao gồm OS path, số lượng processor được sử dụng, và vị trí các temporary folder."_

### System32

<figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

Bên trong Windows folder có rất nhiều folder con. Một trong số đó là **System32**.

* System32 chứa các file quan trọng, **thiết yếu** cho hoạt động của hệ điều hành
* Cần **cực kỳ cẩn thận** khi tương tác với folder này
* Xóa nhầm bất kỳ file hoặc folder nào trong System32 có thể khiến Windows **không thể hoạt động**

> **Lưu ý:** Nhiều tool sẽ được đề cập trong chuỗi bài Windows Fundamentals đều nằm trong folder System32.

<figure><img src="../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

## Task 6 User Accounts, Profiles, and Permissions

**1. Loại tài khoản người dùng**

* Có 2 loại chính:
  * **Administrator**
    * Toàn quyền hệ thống: tạo/xóa user, thay đổi cấu hình, cài phần mềm
  * **Standard User**
    * Chỉ thao tác trên file/thư mục cá nhân
    * Không có quyền thay đổi hệ thống

**2. Quản lý tài khoản**

<figure><img src="../../.gitbook/assets/image (700).png" alt=""><figcaption></figcaption></figure>

* Truy cập qua:

<figure><img src="../../.gitbook/assets/image (702).png" alt=""><figcaption></figcaption></figure>

* &#x20;
  * **Settings → Other users**
  * Hoặc Run: `lusrmgr.msc`
*   Administrator có thể:

    * Thêm user mới
    * Xóa user
    * Thay đổi loại tài khoản (Admin / Standard)

    <figure><img src="../../.gitbook/assets/image (703).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (706).png" alt=""><figcaption></figcaption></figure>

**3. User Profile**

* Mỗi user có một profile riêng tại:
  * `C:\Users\<username>`
* Profile được tạo **lần đầu khi đăng nhập**

<figure><img src="../../.gitbook/assets/image (704).png" alt=""><figcaption></figcaption></figure>

* Bao gồm các thư mục mặc định:
*

    <figure><img src="../../.gitbook/assets/image (705).png" alt=""><figcaption></figcaption></figure>

    * Desktop
    * Documents
    * Downloads
    * Music
    * Pictures

**4. Local Users and Groups (lusrmgr.msc)**

<figure><img src="../../.gitbook/assets/image (707).png" alt=""><figcaption></figcaption></figure>

* Gồm 2 phần:
  * **Users**: danh sách tài khoản
  * **Groups**: nhóm quyền
* Cơ chế phân quyền:
  * User được thêm vào group
  * **User sẽ kế thừa quyền của group**
  * Một user có thể thuộc nhiều group

**5. Điểm quan trọng (góc nhìn security)**

* Quản lý quyền theo **principle of least privilege**
* Hạn chế dùng tài khoản Administrator cho hoạt động thường ngày
* Theo dõi:
  * User mới được tạo
  * Thay đổi group membership
* `lusrmgr.msc` là công cụ quan trọng trong:
  * **Privilege escalation analysis**
  * **User enumeration trong pentest**

<figure><img src="../../.gitbook/assets/image (708).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (709).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (711).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (710).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (713).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (712).png" alt=""><figcaption></figcaption></figure>

## Task 7 User Account Control

Phần lớn người dùng cá nhân đăng nhập vào hệ thống Windows với quyền quản trị viên cục bộ (local administrators).

Hãy nhớ từ task trước rằng bất kỳ user nào có quyền Administrator đều có thể thực hiện thay đổi trên hệ thống.

Người dùng không cần chạy với quyền cao (elevated privileges) để thực hiện các tác vụ thông thường như lướt web, làm việc với Word, v.v.

Việc sử dụng quyền cao làm tăng rủi ro bị xâm nhập hệ thống vì malware có thể dễ dàng khai thác.

Khi đó, malware sẽ chạy với quyền của user đang đăng nhập và có thể thay đổi hệ thống.

Để bảo vệ người dùng, Microsoft đã giới thiệu cơ chế User Account Control (UAC).

Cơ chế này xuất hiện từ Windows Vista và tiếp tục được sử dụng ở các phiên bản sau.

Note:\
Theo mặc định, UAC không áp dụng cho tài khoản built-in Administrator.

UAC hoạt động như thế nào?

Khi user có quyền Administrator đăng nhập:\
→ Session hiện tại không chạy với quyền elevated

Khi thực hiện hành động yêu cầu quyền cao:\
→ Hệ thống sẽ hiển thị UAC prompt để xác nhận

Xem Properties của chương trình → tab Security:

<figure><img src="../../.gitbook/assets/image (714).png" alt=""><figcaption></figcaption></figure>

→ Hiển thị danh sách user/group và quyền

→ Standard user không có trong danh sách này

Đăng nhập bằng Standard User và thử cài chương trình:

→ Có thể dùng Remote Desktop

→ Thông tin username/password có trong lusrmgr.msc

Trước khi cài chương trình:

<figure><img src="../../.gitbook/assets/image (715).png" alt=""><figcaption></figcaption></figure>

→ Icon có biểu tượng shield

→ Đây là dấu hiệu cho thấy cần quyền elevated

<figure><img src="../../.gitbook/assets/image (716).png" alt=""><figcaption></figcaption></figure>

Khi chạy chương trình:

→ UAC prompt xuất hiện

→ Username mặc định là Administrator

→ Yêu cầu nhập password của admin

<figure><img src="../../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

Nếu không nhập password sau một thời gian:

→ UAC prompt sẽ biến mất

→ Chương trình không được cài đặt

Cơ chế này giúp giảm khả năng malware xâm nhập hệ thống thành công.

## Task 8 Settings and the Control Panel

### Settings và Control Panel trên Windows

Trên Windows có hai nơi chính để thay đổi cấu hình hệ thống:

#### 1. Settings

<figure><img src="../../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

* Xuất hiện từ Windows 8
* Là giao diện hiện đại, dễ dùng
* Là nơi người dùng thường truy cập đầu tiên để chỉnh hệ thống
* Phù hợp với các thiết lập cơ bản như:
  * Mạng (Network)
  * Giao diện (Wallpaper, Theme)
  * Tài khoản
  * Cập nhật hệ thống

#### 2. Control Panel

<figure><img src="../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

* Là công cụ truyền thống của Windows
* Dùng cho các thiết lập chi tiết và nâng cao hơn
* Ví dụ:
  * Gỡ cài đặt phần mềm
  * Quản lý thiết bị
  * Cấu hình mạng nâng cao

<figure><img src="../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

#### 3. Sự liên kết giữa hai phần

<figure><img src="../../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

* Settings và Control Panel không tách biệt hoàn toàn
* Một số thao tác trong Settings sẽ mở sang Control Panel

Ví dụ:\
Settings → Network & Internet → Change adapter options → mở cửa sổ từ Control Panel

<figure><img src="../../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

#### 4. Xem phần mềm đã cài đặt



* Vào: Control Panel → Programs → Programs and Features
* Hiển thị:
  * Tên phần mềm
  * Nhà phát hành
  * Phiên bản

#### 5. Cách truy cập nhanh

* Mở từ Start Menu
* Hoặc tìm kiếm trực tiếp (search)
  * Ví dụ: gõ "wallpaper" → Windows sẽ gợi ý đúng nơi để chỉnh

<figure><img src="../../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

## Task 9 Task Manager

Task Manager cung cấp thông tin về các ứng dụng và tiến trình đang chạy trên hệ thống. Ngoài ra, nó còn hiển thị các thông tin khác như mức sử dụng CPU và RAM, thuộc mục Performance.

<figure><img src="../../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>





