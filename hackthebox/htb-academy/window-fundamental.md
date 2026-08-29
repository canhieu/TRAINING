---
hidden: true
---

# Window fundamental

## Introduction

### Tổng quan về Windows trong bối cảnh Pentest

* Windows và Linux là hai hệ điều hành phổ biến nhất trong môi trường doanh nghiệp.
* Phần lớn hệ thống gặp trong pentest (on-premise, cloud) đều chạy một trong hai nền tảng này.
* Cần hiểu:
  * Cách **tấn công (attack surface, exploit, misconfig)**
  * Cách **phòng thủ (hardening, monitoring)**
  * Cách sử dụng Windows như **platform để pivot/lateral movement**

### Lịch sử phát triển Windows

#### Giai đoạn đầu

* 1985: Windows ra đời (GUI shell cho MS-DOS)
* Các thành phần ban đầu:
  * File Manager
  * Program Manager
  * Print Manager

#### Windows 95

* Lần đầu:
  * Tích hợp hoàn toàn với DOS
  * Có Internet support
  * Xuất hiện Internet Explorer

#### Các phiên bản sau

* Windows XP, Vista, 7, 8 → đến Windows 11
* Nhiều edition:
  * Consumer
  * Enterprise

### Windows Server & Enterprise Features

#### Khởi đầu

* 1993: Windows NT 3.1 Advanced Server

#### Các cải tiến quan trọng

* IIS (Web server)
* Networking protocols
* Administrative Wizards

#### Windows 2000

* Introduce:
  * Active Directory (AD)
  * MMC (Microsoft Management Console)
  * Dynamic disk

#### Windows Server 2003

* Server roles
* Built-in firewall
* Volume Shadow Copy

#### Windows Server 2008

* Failover clustering
* Hyper-V (virtualization)
* Server Core
* Event Viewer cải tiến
* AD cải tiến mạnh

#### Các phiên bản mới hơn

* Server 2012, 2016, 2019
* Tính năng mới:
  * Kubernetes support
  * Linux containers
  * Advanced security

### End-of-Life & Legacy Systems

* Windows cũ:
  * Không còn được update security
* Ví dụ:
  * Server 2008, 2012 hết support: 14/01/2020
* Chỉ còn:
  * 2012 R2 trở lên được support

#### Thực tế pentest

* Nhiều doanh nghiệp vẫn chạy:
  * Legacy OS
* Lý do:
  * Phụ thuộc ứng dụng cũ
  * Budget hạn chế

#### Rủi ro

* Dễ bị exploit:
  * Ví dụ: SMBv1 (EternalBlue)
* Microsoft đôi khi vẫn phát hành patch khẩn cấp

### Mapping phiên bản Windows

| OS                            | Version |
| ----------------------------- | ------- |
| Windows NT 4                  | 4.0     |
| Windows 2000                  | 5.0     |
| Windows XP                    | 5.1     |
| Server 2003                   | 5.2     |
| Vista / Server 2008           | 6.0     |
| Windows 7 / Server 2008 R2    | 6.1     |
| Windows 8 / Server 2012       | 6.2     |
| Windows 8.1 / Server 2012 R2  | 6.3     |
| Windows 10 / Server 2016/2019 | 10.0    |

### Enumeration Windows Version (Thực tế)

#### PowerShell command

```
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

#### Ý nghĩa

* Xác định:
  * OS version
  * Build number
* Dùng để:
  * Mapping CVE
  * Xác định patch level
  * Chọn exploit phù hợp

### WMI & Enumeration nâng cao

#### Khái niệm

* WMI (Windows Management Instrumentation):
  * Framework quản lý và truy vấn thông tin hệ thống

#### Các class quan trọng

* `Win32_OperatingSystem` → OS info
* `Win32_Process` → danh sách process
* `Win32_Service` → service
* `Win32_Bios` → BIOS info

#### BIOS

* Firmware trên motherboard
* Quản lý:
  * Power
  * I/O
  * System configuration

#### Use case pentest

* Service misconfiguration
* Process injection target
* Persistence mechanism
* Remote enumeration

#### Remote usage

* Có thể query remote machine qua parameter:
  * `-ComputerName`

### Local Access Concepts

#### Định nghĩa

* Truy cập trực tiếp vào máy:
  * Keyboard
  * Mouse
  * Screen

#### Thực tế doanh nghiệp

* Security model dựa trên:
  * Máy nội bộ
  * Người dùng onsite

#### Xu hướng

* Remote work tăng mạnh
* Technical roles:
  * IT
  * Dev
  * Security

→ Thường xuyên truy cập nhiều máy cả local và remote

### Remote Access Concepts

#### Định nghĩa

* Truy cập máy qua network

#### Điều kiện

* Phải có local access ban đầu để thiết lập remote

#### Vai trò

* Core của:
  * MSP (Managed Service Provider)
  * MSSP (Managed Security Service Provider)

#### Lợi ích

* Centralized management
* Automation
* Remote work
* Incident response nhanh

#### Protocol phổ biến

* VPN
* SSH
* FTP
* VNC
* WinRM
* RDP

### Remote Desktop Protocol (RDP)

#### Kiến trúc

* Client → Server model
* Client nhập:
  * IP / hostname
* Server:
  * Máy target

#### Port

* Default: `3389`

#### Networking analogy

* Subnet = street
* IP = house
* Port = cửa vào

#### Packet flow

* Packet → IP → Port → Application

### Sử dụng RDP trong thực tế

#### Windows client

* Tool: `mstsc.exe`
* Cho phép:
  * Save profile
  * Save credentials

#### Rủi ro pentest

* File `.rdp`:
  * Có thể chứa credential
* Thường bị lộ trong:
  * User desktop
  * Shared folder

### RDP từ Linux (Attack Perspective)

#### Tool: xfreerdp

```
xfreerdp /v:<targetIp> /u:htb-student /p:Password
```

#### Ưu điểm

* CLI-based
* Scriptable
* Hỗ trợ:
  * Drive redirection
  * Clipboard

#### Các tool khác

* Remmina
* rdesktop



## Core of the  OS

## Operating System Structure

#### Root Directory

* Root của Windows: `<drive_letter>:\` (thường là `C:\`)
* Đây là nơi:
  * Hệ điều hành được cài đặt (boot partition)
* Các ổ khác:
  * Có thể là physical hoặc virtual (ví dụ: `E:\`)

### Các thư mục chính trong Windows

#### Bảng chức năng thư mục

| Directory                    | Function                                                                    |
| ---------------------------- | --------------------------------------------------------------------------- |
| Perflogs                     | Có thể chứa log hiệu năng Windows, mặc định trống                           |
| Program Files                | Trên hệ 32-bit: chứa 16-bit và 32-bit program; trên 64-bit: chỉ chứa 64-bit |
| Program Files (x86)          | Trên hệ 64-bit: chứa 32-bit và 16-bit program                               |
| ProgramData                  | Thư mục ẩn, chứa data cần thiết cho application, dùng chung cho mọi user    |
| Users                        | Chứa user profiles                                                          |
| Default                      | Template profile cho user mới                                               |
| Public                       | Thư mục chia sẻ giữa user, có thể truy cập qua network                      |
| AppData                      | Chứa dữ liệu và config riêng cho từng user                                  |
| Windows                      | Chứa phần lớn file hệ điều hành                                             |
| System / System32 / SysWOW64 | Chứa DLL và Windows API                                                     |
| WinSxS                       | Windows Component Store (chứa component, update, service pack)              |

### Cấu trúc chi tiết Users & AppData

#### Users

* Chứa profile của từng user đăng nhập
* Bao gồm:
  * `Default`
  * `Public`

#### AppData (per-user)

Path:

```
C:\Users\<username>\AppData
```

**Cấu trúc**

| Folder   | Chức năng                                              |
| -------- | ------------------------------------------------------ |
| Roaming  | Data theo user profile, có thể sync giữa các máy       |
| Local    | Data chỉ tồn tại trên máy local                        |
| LocalLow | Giống Local nhưng integrity thấp hơn (sandbox/browser) |

### Thư mục hệ thống quan trọng

#### System / System32 / SysWOW64

* Chứa:
  * DLL
  * Core OS component

#### Hành vi quan trọng

* Khi application load DLL mà không chỉ rõ path:
  * Windows sẽ search trong các thư mục này

#### Ý nghĩa bảo mật

* Có thể bị:
  * DLL Hijacking
  * Privilege Escalation

### WinSxS (Windows Component Store)

* Chứa:
  * Toàn bộ component Windows
  * Patch / update / service pack

#### Vai trò

* System repair
* Rollback update

### Duyệt file system bằng command line

#### Sử dụng `dir`

```
dir c:\ /a
```

<figure><img src="../../.gitbook/assets/image (737).png" alt=""><figcaption></figcaption></figure>

#### Ví dụ output

* Một số mục đáng chú ý:
  * `$Recycle.Bin`
  * `Program Files`
  * `Users`
  * `Windows`
  * `pagefile.sys`
  * `hiberfil.sys`

### Hiển thị cây thư mục với `tree`

#### Command

```
tree "c:\Program Files (x86)\VMware"
```

#### Ví dụ output (rút gọn giữ nguyên cấu trúc)

```
C:\PROGRAM FILES (X86)\VMWARE
├───VMware VIX
│   ├───doc
│   ├───samples
│   └───Workstation-15.0.0
│       ├───32bit
│       └───64bit
└───VMware Workstation
    ├───env
    ├───hostd
    ├───ico
    ├───messages
    ├───OVFTool
    ├───Resources
    ├───tools-upgraders
    └───x64
```

### Duyệt toàn bộ hệ thống file

#### Command

```
tree c:\ /f | more
```

#### Ý nghĩa

* `/f`: hiển thị file
* `| more`: chia trang output

<figure><img src="../../.gitbook/assets/image (738).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (739).png" alt=""><figcaption></figcaption></figure>



## File system

#### Các loại File System

Windows hỗ trợ 5 loại file system:

* FAT12
* FAT16
* FAT32
* NTFS
* exFAT

Lưu ý:

* FAT12 và FAT16:
  * Không còn dùng trên hệ thống hiện đại
* Trọng tâm:
  * NTFS (quan trọng nhất trong pentest và thực tế)

### FAT32 (File Allocation Table)

#### Đặc điểm

* Sử dụng 32-bit để định danh cluster
* Phổ biến trên:
  * USB
  * SD card
  * Có thể dùng cho HDD

#### Ưu điểm

* Tương thích thiết bị:
  * PC, camera, console, smartphone...
* Cross-platform:
  * Windows (từ Windows 95)
  * macOS
  * Linux

#### Nhược điểm

* Giới hạn file:
  * < 4GB
* Không có:
  * Data protection
  * File compression
* Encryption:
  * Phải dùng tool bên thứ 3

### NTFS (New Technology File System)

#### Tổng quan

* Default file system từ Windows NT 3.1
* Cải tiến so với FAT32:
  * Metadata tốt hơn
  * Performance tốt hơn

#### Ưu điểm

* Reliability:
  * Có thể restore khi crash/power loss
* Security:
  * Granular permission (file & folder)
* Hỗ trợ:
  * Partition rất lớn
* Journaling:
  * Log mọi thay đổi (create/modify/delete)

#### Nhược điểm

* Không tương thích tốt:
  * Mobile device
  * Thiết bị cũ (TV, camera)

### NTFS Permissions

#### Các loại permission chính

| Permission Type      | Description                                           |
| -------------------- | ----------------------------------------------------- |
| Full Control         | Read, write, change, delete                           |
| Modify               | Read, write, delete                                   |
| List Folder Contents | Xem folder + execute file (chỉ áp dụng folder)        |
| Read and Execute     | Xem + execute file                                    |
| Write                | Ghi file, thêm file vào folder                        |
| Read                 | Xem nội dung                                          |
| Traverse Folder      | Cho phép đi xuyên qua folder để truy cập file sâu hơn |

#### Lưu ý quan trọng

* Permission inheritance:
  * File/folder inherit từ parent
* Có thể:
  * Disable inheritance
  * Set permission riêng

#### Ví dụ thực tế (Traverse Folder)

User không có quyền vào:

```
c:\users\bsmith\documents\webapps\
```

Nhưng có Traverse → vẫn truy cập được:

```
backup_02042020.zip
```

### Quản lý permission với icacls

#### Xem permission

```
icacls c:\windows
```

#### Ví dụ output

```
NT SERVICE\TrustedInstaller:(F)
NT AUTHORITY\SYSTEM:(M)
BUILTIN\Administrators:(M)
BUILTIN\Users:(RX)
```

### Inheritance flags

| Flag | Meaning               |
| ---- | --------------------- |
| (CI) | Container inherit     |
| (OI) | Object inherit        |
| (IO) | Inherit only          |
| (NP) | Do not propagate      |
| (I)  | Inherited from parent |

### Basic permission flags

| Flag | Meaning        |
| ---- | -------------- |
| F    | Full access    |
| D    | Delete         |
| N    | No access      |
| M    | Modify         |
| RX   | Read & execute |
| R    | Read only      |
| W    | Write          |

### Ví dụ thực tế với icacls

#### Xem permission thư mục Users

```
icacls c:\Users
```

#### Output mẫu

```
NT AUTHORITY\SYSTEM:(OI)(CI)(F)
BUILTIN\Administrators:(OI)(CI)(F)
BUILTIN\Users:(RX)
Everyone:(RX)
```

### Grant permission

#### Cấp quyền full cho user joe

```
icacls c:\users /grant joe:f
```

#### Kết quả

* joe có quyền:
  * Full control trên `c:\users`
* Nhưng:
  * Không có quyền trên subfolder (vì không có CI/OI)

### Revoke permission

```
icacls c:\users /remove joe
```

### Khả năng của icacls

* Grant / revoke permission
* Deny access
* Enable/disable inheritance
* Change ownership
* Áp dụng trong:
  * Local system
  * Domain environment

[https://ss64.com/nt/icacls.html](https://ss64.com/nt/icacls.html)

<figure><img src="../../.gitbook/assets/image (740).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (741).png" alt=""><figcaption></figcaption></figure>



## NTFS vs. Share Permissions

Microsoft sở hữu hơn 70% thị phần toàn cầu về hệ điều hành desktop với Windows. Điều này giải thích tại sao phần lớn các malware author lựa chọn viết malware cho Windows và tại sao nhiều người cho rằng Windows kém an toàn hơn các hệ điều hành khác. Từ góc độ kinh doanh, việc đầu tư tài nguyên để viết malware cho Windows là hợp lý vì đây là mục tiêu có giá trị cao.

Quan điểm cho rằng bất kỳ hệ điều hành nào miễn nhiễm với malware là một sai lầm kỹ thuật. Nếu phần mềm có thể được viết cho một hệ điều hành thì virus cũng có thể được viết cho hệ điều hành đó. Virus, theo định nghĩa, là phần mềm được viết với mục đích độc hại và có thể tồn tại trên bất kỳ OS nào.

Nhiều biến thể malware viết cho Windows có thể lan truyền qua network thông qua các network share với permission lỏng lẻo. Ngoài ra, lỗ hổng EternalBlue vẫn còn ảnh hưởng đến các hệ thống Windows chưa được vá chạy SMBv1 và thường mở đường cho ransomware làm tê liệt tổ chức.

### SMB (Server Message Block)

SMB là giao thức được sử dụng trong Windows để truy cập tài nguyên chia sẻ như file và printer. Nó được sử dụng trong môi trường enterprise ở mọi quy mô.

Lưu ý: Khi gặp diagram, cần đọc kỹ vì nó mô tả luồng client → server → resource.

<figure><img src="../../.gitbook/assets/image (742).png" alt=""><figcaption></figcaption></figure>

### NTFS permissions và Share permissions

Hai loại permission này không giống nhau nhưng thường áp dụng lên cùng một resource.

### Share permissions

| Permission   | Description                                                                       |
| ------------ | --------------------------------------------------------------------------------- |
| Full Control | Cho phép tất cả hành động của Change và Read, đồng thời thay đổi NTFS permissions |
| Change       | Cho phép read, edit, delete và thêm file/subfolder                                |
| Read         | Cho phép xem nội dung file và subfolder                                           |

### NTFS Basic permissions

| Permission           | Description                                                      |
| -------------------- | ---------------------------------------------------------------- |
| Full Control         | Add, edit, move, delete file/folder và thay đổi NTFS permissions |
| Modify               | Xem và chỉnh sửa file/folder (bao gồm thêm/xóa)                  |
| Read & Execute       | Đọc nội dung và execute program                                  |
| List folder contents | Xem danh sách file và subfolder                                  |
| Read                 | Đọc nội dung file                                                |
| Write                | Ghi thay đổi vào file và tạo file mới                            |
| Special Permissions  | Các quyền nâng cao                                               |

### NTFS special permissions

| Permission                     | Description                                               |
| ------------------------------ | --------------------------------------------------------- |
| Full control                   | Toàn quyền trên file/folder                               |
| Traverse folder / execute file | Truy cập subfolder dù bị deny ở parent, execute program   |
| List folder/read data          | Xem file/folder và mở file                                |
| Read attributes                | Xem attribute cơ bản (system, archive, read-only, hidden) |
| Read extended attributes       | Xem attribute mở rộng                                     |
| Create files/write data        | Tạo file và chỉnh sửa file                                |
| Create folders/append data     | Tạo subfolder, append data                                |
| Write attributes               | Thay đổi attribute                                        |
| Write extended attributes      | Thay đổi extended attributes                              |
| Delete subfolders and files    | Xóa subfolder và file                                     |
| Delete                         | Xóa file và folder                                        |
| Read permissions               | Xem permission                                            |
| Change permissions             | Thay đổi permission                                       |
| Take ownership                 | Chiếm quyền ownership                                     |

NTFS permissions áp dụng trên hệ thống local và mặc định inherit từ parent folder. Share permissions áp dụng khi truy cập qua SMB từ hệ thống khác. Truy cập local hoặc RDP chỉ chịu NTFS. NTFS cung cấp granular control chi tiết hơn.

### Creating a Network Share

Để hiểu rõ SMB và NTFS, chúng ta tạo network share trên Windows 10.

Trong môi trường thực tế:

* Enterprise: SAN / NAS / Windows Server
* Desktop share: small business hoặc foothold của attacker/pentester

### Creating the Folder

<figure><img src="../../.gitbook/assets/image (743).png" alt=""><figcaption></figcaption></figure>

Tạo folder mới trên Desktop Windows.

### Making the Folder a Share

<figure><img src="../../.gitbook/assets/image (744).png" alt=""><figcaption></figcaption></figure>

Sử dụng Advanced Sharing.

* Share name mặc định theo tên folder
* Có thể giới hạn số user truy cập đồng thời
* Best practice: cấu hình theo nhu cầu

### ACL (Access Control List)

<figure><img src="../../.gitbook/assets/image (745).png" alt=""><figcaption></figcaption></figure>

ACL chứa các ACE gồm user và group (security principals). Cả SMB permissions và NTFS permissions đều áp dụng đồng thời lên resource.

### Share Permissions ACL (Sharing Tab)

Mặc định:

* Group: Everyone
* Permission: Read

### Using smbclient to list available shares

```
Canhieu@htb[/htb]$ smbclient -L SERVER_IP -U htb-student
Enter WORKGROUP\htb-student's password: 

    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    Company Data    Disk      
    IPC$            IPC       Remote IPC
```

### Connecting to the Company Data share

```
Canhieu@htb[/htb]$ smbclient '\\SERVER_IP\Company Data' -U htb-student
Password for [WORKGROUP\htb-student]:
Try "help" to get a list of possible commands.

smb: \>
```

### Windows Defender Firewall Considerations

Windows Defender Firewall có thể block truy cập SMB khi kết nối từ hệ khác (ví dụ Linux không cùng workgroup).

Workgroup:

* Authentication dùng local SAM

Domain:

* Authentication dùng Active Directory

Firewall profiles:

* Public
* Private
* Domain

Best practice:

* Enable rule cụ thể
* Không disable firewall toàn bộ

### NTFS Permissions ACL (Security Tab)

<figure><img src="../../.gitbook/assets/image (746).png" alt=""><figcaption></figcaption></figure>

htb-student có Full Control.

Dấu check màu xám:

* Permission được inherit từ parent

Mặc định parent:

* C:\\

Admin có quyền kiểm soát permission nên là target phổ biến của spear phishing.

### Mounting to the Share

```
Canhieu@htb[/htb]$ sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

Nếu lỗi:

```
Canhieu@htb[/htb]$ sudo apt-get install cifs-utils
```

### Displaying Shares using net share

```
C:\Users\htb-student> net share

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\WINDOWS                      Remote Admin
Company Data C:\Users\htb-student\Desktop\Company Data

The command completed successfully.
```

C$ là default administrative share cho ổ C:. Nếu có quyền phù hợp, có thể truy cập toàn bộ filesystem từ xa.

### Computer Management

<figure><img src="../../.gitbook/assets/image (747).png" alt=""><figcaption></figcaption></figure>

Dùng để theo dõi:

* Shares
* Sessions
* Open Files

### Viewing Share access logs in Event Viewer

Event Viewer ghi lại toàn bộ hoạt động:

* Truy cập
* Authentication
* File operations

Log đóng vai trò như nhật ký hệ thống và rất quan trọng trong quá trình incident response.

<figure><img src="../../.gitbook/assets/image (748).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (749).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (750).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (751).png" alt=""><figcaption></figcaption></figure>



Được. Tôi trình bày lại theo kiểu dễ đọc hơn, nhưng vẫn giữ đầy đủ nội dung, thuật ngữ kỹ thuật, command output và không cắt ý. Nguồn:

## Windows Services & Processes

### Windows Services

Services là một thành phần quan trọng của Windows operating system. Chúng cho phép tạo và quản lý các process chạy lâu dài. Windows services có thể được khởi động tự động khi system boot mà không cần user can thiệp. Những service này có thể tiếp tục chạy trong background ngay cả sau khi user đã log out khỏi account của họ trên hệ thống.

Applications cũng có thể được thiết kế để cài đặt dưới dạng service, ví dụ như một network monitoring application được cài trên server. Services trên Windows chịu trách nhiệm cho nhiều chức năng trong operating system, chẳng hạn như:

* Networking functions
* Thực hiện system diagnostics
* Quản lý user credentials
* Kiểm soát Windows updates
* Nhiều chức năng hệ thống khác

Windows services được quản lý thông qua Service Control Manager (SCM), có thể truy cập bằng `services.msc` MMC add-in.

Add-in này cung cấp GUI interface để tương tác và quản lý services, đồng thời hiển thị các thông tin như:

* Name
* Description
* Status
* Startup Type
* User mà service chạy dưới quyền

Ngoài GUI, cũng có thể query và quản lý services qua command line bằng `sc.exe` hoặc PowerShell cmdlets như `Get-Service`.

```powershell
PS C:\htb> Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl


Name                : AdobeARMservice
DisplayName         : Adobe Acrobat Update Service
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess

Name                : Appinfo
DisplayName         : Application Information
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {RpcSs, ProfSvc}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess, Win32ShareProcess
```

Service status có thể xuất hiện dưới các trạng thái:

* Running
* Stopped
* Paused
* Starting
* Stopping

Service cũng có thể được cấu hình để start theo các chế độ:

* Manual
* Automatic
* Delayed start khi system boot

Windows có ba category services:

* Local Services
* Network Services
* System Services

Thông thường, services chỉ có thể được tạo, sửa đổi và xóa bởi user có administrative privileges. Misconfigurations liên quan đến service permissions là một privilege escalation vector phổ biến trên Windows systems.

Trong Windows, có một số critical system services không thể bị stop và restart nếu không reboot hệ thống. Nếu cập nhật bất kỳ file hoặc resource nào đang được một trong các service này sử dụng, chúng ta phải restart hệ thống.

### Critical Windows services

| Service                   | Description                                                                                                                                                                                                                                  |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| smss.exe                  | Session Manager SubSystem. Chịu trách nhiệm xử lý các session trên hệ thống.                                                                                                                                                                 |
| csrss.exe                 | Client Server Runtime Process. Phần user-mode của Windows subsystem.                                                                                                                                                                         |
| wininit.exe               | Khởi động file Wininit `.ini`, file này liệt kê toàn bộ các thay đổi sẽ được thực hiện với Windows khi máy tính được restart sau khi cài đặt một chương trình.                                                                               |
| logonui.exe               | Được sử dụng để hỗ trợ user log in vào PC.                                                                                                                                                                                                   |
| lsass.exe                 | Local Security Authentication Server xác minh tính hợp lệ của các lần user logon vào PC hoặc server. Nó tạo ra process chịu trách nhiệm authenticate user cho Winlogon service.                                                              |
| services.exe              | Quản lý việc khởi động và dừng services.                                                                                                                                                                                                     |
| winlogon.exe              | Chịu trách nhiệm xử lý secure attention sequence, load user profile khi logon, và khóa máy tính khi screensaver đang chạy.                                                                                                                   |
| System                    | Một background system process chạy Windows kernel.                                                                                                                                                                                           |
| svchost.exe with RPCSS    | Quản lý system services chạy từ dynamic-link libraries (các file có phần mở rộng `.dll`) như "Automatic Updates", "Windows Firewall", và "Plug and Play". Sử dụng Remote Procedure Call (RPC) Service (RPCSS).                               |
| svchost.exe with Dcom/PnP | Quản lý system services chạy từ dynamic-link libraries (các file có phần mở rộng `.dll`) như "Automatic Updates", "Windows Firewall", và "Plug and Play". Sử dụng Distributed Component Object Model (DCOM) và Plug and Play (PnP) services. |

Liên kết được nhắc tới trong tài liệu có chứa danh sách các Windows components, bao gồm các key services.

### Processes

Processes chạy trong background trên Windows systems. Chúng hoặc chạy tự động như một phần của Windows operating system, hoặc được khởi động bởi các installed application khác.

Các process liên quan tới installed applications thường có thể bị terminate mà không gây ảnh hưởng nghiêm trọng tới operating system. Tuy nhiên, một số process là critical. Nếu các process này bị terminate, một số thành phần của operating system sẽ không còn hoạt động bình thường.

Một số ví dụ về critical processes:

* Windows Logon Application
* System
* System Idle Process
* Windows Start-Up Application
* Client Server Runtime
* Windows Session Manager
* Service Host
* Local Security Authority Subsystem Service (LSASS)

### Local Security Authority Subsystem Service (LSASS)

`lsass.exe` là process chịu trách nhiệm thực thi security policy trên Windows systems.

Khi một user cố gắng log on vào hệ thống, process này sẽ:

* Xác minh lần logon đó
* Tạo access tokens dựa trên permission levels của user

LSASS cũng chịu trách nhiệm cho các thay đổi password của user account.

Mọi event liên quan đến process này, ví dụ:

* Logon attempts
* Logoff attempts
* Các hoạt động xác thực khác

đều được ghi lại trong Windows Security Log.

LSASS là một mục tiêu có giá trị rất cao vì có nhiều tool tồn tại để trích xuất cả:

* Cleartext credentials
* Hashed credentials

được lưu trong memory bởi process này.

### Sysinternals Tools

SysInternals Tools suite là một tập hợp các portable Windows applications có thể được sử dụng để administer Windows systems, và phần lớn không yêu cầu cài đặt.

Các tool có thể được:

* Download từ Microsoft website
* Hoặc load trực tiếp từ một internet-accessible file share bằng cách gõ `\\live.sysinternals.com\tools` vào Windows Explorer window

Ví dụ, có thể chạy trực tiếp `procdump.exe` từ share này mà không cần download xuống disk.

```cmd
C:\htb> \\live.sysinternals.com\tools\procdump.exe -accepteula

ProcDump v9.0 - Sysinternals process dump utility
Copyright (C) 2009-2017 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Monitors a process and writes a dump file when the process exceeds the
specified criteria or has an exception.

Capture Usage:
   procdump.exe [-mm] [-ma] [-mp] [-mc Mask] [-md Callback_DLL] [-mk]
                [-n Count]
                [-s Seconds]
                [-c|-cl CPU_Usage [-u]]
                [-m|-ml Commit_Usage]
                [-p|-pl Counter_Threshold]
                [-h]
                [-e [1 [-g] [-b]]]
                [-l]
                [-t]
                [-f  Include_Filter, ...]
                [-fx Exclude_Filter, ...]
                [-o]
                [-r [1..5] [-a]]
                [-wer]
                [-64]
                {
                 {{[-w] Process_Name | Service_Name | PID} [Dump_File | Dump_Folder]}
                |
                 {-x Dump_Folder Image_File [Argument, ...]}
                }
                
<SNIP>
```

Bộ tool này bao gồm các công cụ như:

* Process Explorer\
  Một phiên bản nâng cao của Task Manager
* Process Monitor\
  Có thể được sử dụng để monitor:
  * File system activity
  * Registry activity
  * Network activity\
    liên quan tới bất kỳ process nào đang chạy trên hệ thống
* TCPView\
  Được sử dụng để monitor internet activity
* PSExec\
  Có thể được dùng để quản lý hoặc kết nối tới các system từ xa thông qua SMB protocol

Những tool này rất hữu ích cho penetration tester để:

* Phát hiện các process đáng quan tâm
* Tìm các đường privilege escalation khả thi
* Hỗ trợ lateral movement

### Task Manager

Windows Task Manager là một công cụ mạnh để quản lý Windows systems. Nó cung cấp thông tin về:

* Running processes
* System performance
* Running services
* Startup programs
* Logged-in users
* Logged-in user processes
* Services

Task Manager có thể được mở bằng nhiều cách:

* Right-click vào taskbar và chọn Task Manager
* Nhấn `Ctrl + Shift + Esc`
* Nhấn `Ctrl + Alt + Del` rồi chọn Task Manager
* Mở Start Menu và gõ `Task Manager`
* Gõ `taskmgr` từ CMD hoặc PowerShell console

Task Manager hiển thị processes cùng mức sử dụng CPU, memory, disk và network, đồng thời làm nổi bật các process như Google Chrome và Windows PowerShell.

<figure><img src="../../.gitbook/assets/image (754).png" alt=""><figcaption></figcaption></figure>

#### Các tab chính trong Task Manager

| Tab             | Description                                                                                                                                                                                                                              |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Processes tab   | Hiển thị danh sách running applications và background processes cùng với mức sử dụng CPU, memory, disk, network và power của từng mục.                                                                                                   |
| Performance tab | Hiển thị graph và dữ liệu như CPU utilization, system uptime, memory usage, disk, networking và GPU usage. Từ đây cũng có thể mở Resource Monitor để xem chuyên sâu hơn về mức sử dụng tài nguyên CPU, Memory, Disk và Network hiện tại. |

Resource Monitor hiển thị processes cùng chi tiết về memory usage và physical memory graph.

<figure><img src="../../.gitbook/assets/image (755).png" alt=""><figcaption></figcaption></figure>

| Tab             | Description                                                                                                                 |
| --------------- | --------------------------------------------------------------------------------------------------------------------------- |
| App history tab | Hiển thị resource usage của user account hiện tại cho từng application trong một khoảng thời gian.                          |
| Startup tab     | Hiển thị application nào được cấu hình để start khi boot và mức ảnh hưởng của chúng tới quá trình khởi động.                |
| Users tab       | Hiển thị các user đang log in và các process/resource usage liên quan tới session của họ.                                   |
| Details tab     | Hiển thị name, process ID (PID), status, username liên quan, CPU và memory usage của từng running application.              |
| Services tab    | Hiển thị name, PID, description và status của từng installed service. Services add-in cũng có thể được truy cập từ tab này. |

### Process Explorer

Process Explorer là một phần của Sysinternals tool suite.

Công cụ này có thể hiển thị:

* Handle nào được process sử dụng
* DLL nào được load khi một program chạy

Process Explorer hiển thị danh sách các process hiện đang chạy. Từ đó, chúng ta có thể:

* Xem process đó đang sử dụng những handle nào trong một view
* Xem các DLL và memory-swapped files đã được load trong một view khác

Ngoài ra, có thể search trong tool để xác định process nào liên kết trở lại một handle hoặc DLL cụ thể.

Tool này cũng có thể được sử dụng để phân tích parent-child process relationships nhằm:

* Xem application spawn ra những child process nào
* Hỗ trợ troubleshoot các vấn đề như orphaned processes có thể bị bỏ lại khi một process bị terminate

Nếu bạn muốn, tôi có thể tiếp tục chuẩn hóa các phần sau theo đúng format này để cả bộ tài liệu nhìn đồng nhất.

<figure><img src="../../.gitbook/assets/image (756).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (757).png" alt=""><figcaption></figcaption></figure>



### Service Permissions

Services cho phép quản lý các long-running processes và là một thành phần quan trọng của Windows operating systems. Sysadmins thường bỏ qua services như một threat vector tiềm năng có thể bị lợi dụng để load malicious DLLs, execute applications mà không cần admin account, escalate privileges, hoặc thậm chí maintain persistence.

Các threat vectors này trong Windows services thường xuất hiện do service permissions bị misconfiguration bởi 3rd party software, hoặc do lỗi cấu hình dễ mắc phải của admins trong quá trình cài đặt.

Bước đầu tiên để nhận thức được tầm quan trọng của service permissions là hiểu rằng chúng tồn tại và cần được chú ý. Trên server operating systems, các critical network services như DHCP và Active Directory Domain Services thường được cài đặt bằng account của admin đang thực hiện quá trình install.

Một phần của quá trình install bao gồm việc gán một service cụ thể chạy bằng credentials và privileges của một user được chỉ định. Theo mặc định, service này thường được cấu hình trong context của user hiện đang logged-on.

Ví dụ, nếu chúng ta logged on dưới user Bob trên server trong lúc cài đặt DHCP, service đó sẽ được cấu hình chạy dưới account Bob nếu không chỉ định khác. Vấn đề có thể phát sinh là gì? Nếu Bob rời tổ chức hoặc bị sa thải, business practice thông thường là disable account của Bob trong quá trình offboarding. Khi đó, điều gì xảy ra với DHCP và các services khác đang chạy bằng account Bob?

Các services đó sẽ fail to start. DHCP, hay Dynamic Host Configuration Protocol, chịu trách nhiệm cấp phát IP addresses cho computers trong network. Nếu service này dừng trên Windows DHCP server, clients yêu cầu IP address sẽ không nhận được IP. Điều này có nghĩa là một service misconfiguration có thể dẫn đến downtime và mất productivity.

Best practice là tạo một individual user account riêng để chạy critical network services. Các account này được gọi là service accounts.

Chúng ta cũng cần chú ý đến service permissions và permissions của các directories mà service execute từ đó, vì attacker có thể thay thế path đến executable bằng malicious DLL hoặc executable file.

### Kiểm tra Services bằng services.msc

<figure><img src="../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

Như đã đề cập trong phần processes và services, chúng ta có thể sử dụng services.msc để xem và quản lý gần như mọi thông tin liên quan đến services. Hãy xem kỹ hơn service liên quan đến Windows Update, cụ thể là `wuauserv`.

Services window showing Windows Update properties with service name, description, and status running.

Cần lưu ý các properties khác nhau có thể xem và cấu hình. Việc biết service name đặc biệt hữu ích khi sử dụng command-line tools để kiểm tra và quản lý services.

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

`Path to the executable` là full path đến program và command sẽ được execute khi service start. Nếu NTFS permissions của destination directory được cấu hình yếu, attacker có thể thay thế original executable bằng file được tạo cho mục đích malicious. NTFS permissions sẽ được đề cập kỹ hơn trong phần NTFS vs. Share permissions.

Services window showing Windows Update properties with log on options for Local System account and specific user account.

Hầu hết services mặc định chạy với LocalSystem privileges, đây là mức quyền cao nhất trên một Windows OS riêng lẻ. Không phải application nào cũng cần quyền ở cấp Local System account, vì vậy nên đánh giá theo từng trường hợp khi cài đặt new applications trong Windows environment.

Best practice là xác định applications nào có thể chạy với mức quyền thấp nhất có thể, nhằm tuân thủ principle of least privilege.

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

Một số built-in service accounts đáng chú ý trong Windows:

* LocalService
* NetworkService
* LocalSystem

Lưu ý: Chúng ta cũng có thể tạo new accounts và sử dụng chúng chỉ cho mục đích chạy service.

Windows Update Properties showing recovery options for service failures, including actions like restarting the service or computer.

Tab recovery cho phép cấu hình các hành động khi service fail. Lưu ý rằng service này có thể được cấu hình để run a program sau lần failure đầu tiên. Đây là một vector khác mà attacker có thể lợi dụng để chạy malicious programs thông qua một legitimate service.

### Kiểm tra Services bằng sc

`sc` cũng có thể được sử dụng để cấu hình và quản lý services. Hãy thử một vài commands.

```
        cmd-session
C:\Users\htb-student>sc qc wuauserv
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\system32\svchost.exe -k netsvcs -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

Command `sc qc` được sử dụng để query service. Đây là lúc việc biết service name trở nên hữu ích. Nếu muốn query một service trên một device trong network, chúng ta có thể chỉ định hostname hoặc IP address ngay sau `sc`.

```
        cmd-session
C:\Users\htb-student>sc //hostname or ip of box query ServiceName
```

Chúng ta cũng có thể sử dụng `sc` để start và stop services.

```
        cmd-session
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```

Lưu ý rằng chúng ta bị denied access khi thực hiện hành động này nếu không chạy trong administrative context. Nếu chạy command prompt với elevated privileges, chúng ta sẽ được phép thực hiện hành động đó.

```
        cmd-session
C:\WINDOWS\system32> sc config wuauserv binPath=C:\Winbows\Perfectlylegitprogram.exe

[SC] ChangeServiceConfig SUCCESS

C:\WINDOWS\system32> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Winbows\Perfectlylegitprogram.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

Nếu đang điều tra một tình huống nghi ngờ system có malware, `sc` cho phép chúng ta nhanh chóng search và analyze các commonly targeted services cũng như newly created services. Công cụ này cũng thân thiện hơn với scripting so với GUI tools như services.msc.

Một cách hữu ích khác để kiểm tra service permissions bằng `sc` là sử dụng command `sdshow`.

```
        cmd-session
C:\WINDOWS\system32> sc sdshow wuauserv

D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)S:(AU;FA;CCDCLCSWRPWPDTLOSDRCWDWO;;;WD)
```

Thoạt nhìn, output có vẻ rất khó hiểu. Nó gần như khiến ta nghĩ rằng command đã chạy sai, nhưng thực tế chuỗi này có ý nghĩa rõ ràng.

Mỗi named object trong Windows là một securable object, và thậm chí một số unnamed objects cũng là securable. Nếu một object là securable trong Windows OS, nó sẽ có security descriptor.

Security descriptors xác định object’s owner và primary group chứa Discretionary Access Control List, gọi là DACL, và System Access Control List, gọi là SACL.

Thông thường, DACL được dùng để kiểm soát access vào object, còn SACL được dùng để audit và log các access attempts. Phần này tập trung vào DACL, nhưng cùng concept cũng có thể áp dụng cho SACL.

```
        text
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
```

Chuỗi ký tự này được gom lại với nhau và phân tách bằng các cặp dấu ngoặc `(` và `)` theo một format gọi là Security Descriptor Definition Language, viết tắt là SDDL.

Chúng ta có thể có xu hướng đọc từ trái sang phải vì đó là cách đọc thông thường trong tiếng Anh, nhưng khi làm việc với computers, cách diễn giải có thể khác. Hãy đọc toàn bộ security descriptor của Windows Update service, tức `wuauserv`, theo thứ tự sau, bắt đầu với ký tự đầu tiên và cặp ngoặc đầu tiên:

`D: (A;;CCLCSWRPLORC;;;AU)`

* `D:` các ký tự tiếp theo là DACL permissions
* `AU:` định nghĩa security principal là Authenticated Users
* `A;;` access is allowed
* `CC` SERVICE\_QUERY\_CONFIG là full name, đây là query đến service control manager, hay SCM, để lấy service configuration
* `LC` SERVICE\_QUERY\_STATUS là full name, đây là query đến SCM để lấy current status của service
* `SW` SERVICE\_ENUMERATE\_DEPENDENTS là full name, dùng để enumerate danh sách dependent services
* `RP` SERVICE\_START là full name, dùng để start service
* `LO` SERVICE\_INTERROGATE là full name, dùng để query service về current status của nó
* `RC` READ\_CONTROL là full name, dùng để query security descriptor của service

Khi đọc security descriptor, rất dễ bị mất phương hướng do thứ tự ký tự trông có vẻ ngẫu nhiên. Tuy nhiên, về bản chất chúng ta đang xem các access control entries trong một access control list.

Mỗi cặp 2 ký tự nằm giữa các dấu chấm phẩy đại diện cho các actions được phép thực hiện bởi một user hoặc group cụ thể.

```
;;CCLCSWRPLORC;;;
```

Sau cụm dấu chấm phẩy cuối cùng, các ký tự chỉ định security principal, tức User hoặc Group, được phép thực hiện các actions đó.

```
;;;AU
```

Ký tự ngay sau dấu ngoặc mở và trước cụm dấu chấm phẩy đầu tiên xác định các actions là Allowed hay Denied.

```
A;;
```

Toàn bộ security descriptor liên quan đến Windows Update service, tức `wuauserv`, có ba tập access control entries vì có ba security principals khác nhau. Mỗi security principal có các permissions cụ thể được áp dụng.

### Kiểm tra Service Permissions bằng PowerShell

Sử dụng PowerShell cmdlet `Get-Acl`, chúng ta có thể kiểm tra service permissions bằng cách target path của một service cụ thể trong registry.

```
        powershell-session
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List

Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\wuauserv
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Users Allow  -2147483648
         BUILTIN\Administrators Allow  FullControl
         BUILTIN\Administrators Allow  268435456
         NT AUTHORITY\SYSTEM Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  268435456
         CREATOR OWNER Allow  268435456
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  ReadKey
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  -2147483648
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         ReadKey
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         -2147483648
Audit  :
Sddl   : O:SYG:SYD:AI(A;ID;KR;;;BU)(A;CIIOID;GR;;;BU)(A;ID;KA;;;BA)(A;CIIOID;GA;;;BA)(A;ID;KA;;;SY)(A;CIIOID;GA;;;SY)(A
         ;CIIOID;GA;;;CO)(A;ID;KR;;;AC)(A;CIIOID;GR;;;AC)(A;ID;KR;;;S-1-15-3-1024-1065365936-1281604716-3511738428-1654
         721687-432734479-3232135806-4053264122-3456934681)(A;CIIOID;GR;;;S-1-15-3-1024-1065365936-1281604716-351173842
         8-1654721687-432734479-3232135806-4053264122-3456934681)
```

Lưu ý rằng command này trả về specific account permissions ở format dễ đọc, đồng thời cũng trả về SDDL. Ngoài ra, SID đại diện cho từng security principal, tức User hoặc Group, cũng xuất hiện trong SDDL. Đây là thông tin không có khi chạy `sc` từ command prompt.

