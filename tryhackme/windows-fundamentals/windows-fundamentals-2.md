# Windows Fundamentals 2

## Task 1 Introduction

## Task 2 System Configuration and Advanced System Settings

### System Configuration

<figure><img src="../../.gitbook/assets/image (718).png" alt=""><figcaption></figcaption></figure>



System Configuration (MSConfig) là công cụ dùng cho việc xử lý sự cố nâng cao, với mục đích chính là hỗ trợ chẩn đoán các vấn đề liên quan đến quá trình khởi động hệ thống.

Bạn có thể tham khảo tài liệu đi kèm (mở ở tab mới) để biết thêm chi tiết về công cụ này.

Có nhiều cách để mở System Configuration, một trong số đó là thông qua Start Menu.

Lưu ý: Bạn cần quyền quản trị viên (local administrator) để mở công cụ này.

Công cụ này có 5 tab ở phía trên. Dưới đây là tên của từng tab, và chúng ta sẽ lần lượt tìm hiểu trong phần này:

1. General
2. Boot
3. Services
4. Startup
5. Tools

<figure><img src="../../.gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

In the **General** tab, we can select what devices and services for Windows to load upon boot. The options are: **Normal**, **Diagnostic**, or **Selective**.&#x20;

In the **Boot** tab, we can define various boot options for the Operating System.&#x20;

<figure><img src="../../.gitbook/assets/image (720).png" alt=""><figcaption></figcaption></figure>

### Services tab

The **Services** tab lists all services configured for the system regardless of their state (running or stopped). A service is a special type of application that runs in the background. &#x20;



<figure><img src="../../.gitbook/assets/image (721).png" alt=""><figcaption></figcaption></figure>

### Startup tab

<figure><img src="../../.gitbook/assets/image (722).png" alt=""><figcaption></figcaption></figure>

* Trên máy Windows Server, tab **Startup** trong msconfig sẽ không có thông tin đáng chú ý.
* Microsoft khuyến nghị dùng **Task Manager (taskmgr)** để quản lý startup (bật/tắt).
* MSConfig **không phải** công cụ quản lý startup.

#### Lưu ý trên Windows Server

<figure><img src="../../.gitbook/assets/image (723).png" alt=""><figcaption></figcaption></figure>

* Không có tab Startup trong Task Manager
* Tab Startup trong msconfig cũng trống
* Cách xem startup apps:

Bước thực hiện:

1. Nhấn `Win + R`
2. Gõ `shell:startup`
3. Nhấn Enter

→ Hiển thị các chương trình tự chạy khi user đăng nhập (dưới dạng shortcut hoặc executable)

### Tools tab

* Tab **Tools** chứa danh sách các công cụ hệ thống



<figure><img src="../../.gitbook/assets/image (725).png" alt=""><figcaption></figcaption></figure>

* Mỗi tool có mô tả ngắn
* Có thể chạy bằng:
  * Command hiển thị
  * Run / Command Prompt
  * Nút **Launch**

### Advanced System Settings

<figure><img src="../../.gitbook/assets/image (726).png" alt=""><figcaption></figcaption></figure>

#### Truy cập

* Search: `View advanced system settings`

→ Mở cửa sổ **System Properties**

#### Page File (Virtual Memory)

* Windows dùng page file khi RAM đầy
* Giúp tránh crash hoặc chậm hệ thống

Cách xem:

<figure><img src="../../.gitbook/assets/image (727).png" alt=""><figcaption></figcaption></figure>

* Advanced → Performance → Settings → Advanced

Thông tin hiển thị:

<figure><img src="../../.gitbook/assets/image (728).png" alt=""><figcaption></figcaption></figure>

* Ổ đĩa chứa page file
* Initial size
* Maximum size
* Có tự động quản lý hay không

#### Startup and Recovery

* Dùng để cấu hình khi hệ thống crash

Cách truy cập:

<figure><img src="../../.gitbook/assets/image (729).png" alt=""><figcaption></figcaption></figure>

* Advanced → Startup and Recovery → Settings

#### Crash Dump

<figure><img src="../../.gitbook/assets/image (730).png" alt=""><figcaption></figcaption></figure>

Windows có thể tạo file dump khi lỗi nghiêm trọng (BSOD)

Các loại dump:

* Automatic memory dump
* Kernel memory dump
* Small memory dump (256 KB)
* Complete memory dump
* None

→ Xác định mức độ thông tin được lưu khi hệ thống crash

<figure><img src="../../.gitbook/assets/image (732).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (731).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (734).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (733).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (735).png" alt=""><figcaption></figcaption></figure>



## Task 3 Change UAC Settings

## Task 4 Computer Management

## Task 5 System Information

## Task 6 Resource Monitor

## Task 7 Command Prompt

## Task 8 Registry Editor

## Task 9 Conclusion
