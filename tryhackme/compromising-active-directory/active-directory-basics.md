---
hidden: true
---

# Active Directory Basics

<figure><img src="../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

## Windows Domain

<figure><img src="../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

Khi network nhỏ, ví dụ 5 computers và 5 employees, admin có thể quản lý từng máy riêng lẻ: tạo users, cấu hình account, xử lý lỗi trực tiếp tại chỗ.

Tuy nhiên, khi business mở rộng lên hàng trăm computers, nhiều users và nhiều offices, việc quản lý thủ công từng máy sẽ không còn hiệu quả.

Để giải quyết vấn đề này, Windows domain được sử dụng. Windows domain là một nhóm users và computers được quản trị tập trung bởi một tổ chức. Thành phần trung tâm của domain là Active Directory, viết tắt là AD.

Server chạy Active Directory services được gọi là Domain Controller, viết tắt là DC.

### Lợi ích chính của Windows Domain

Windows domain giúp đơn giản hóa việc quản trị network thông qua:

* Centralised identity management: Quản lý toàn bộ users trong network từ Active Directory.
* Managing security policies: Cấu hình và áp dụng security policies cho users và computers trên toàn domain.

### Ví dụ thực tế

Trong môi trường school, university hoặc company, bạn thường có một username và password dùng được trên nhiều computers khác nhau.

Khi bạn log in vào một machine, credentials sẽ được gửi về Active Directory để kiểm tra. Nhờ đó, user account không cần tồn tại cục bộ trên từng computer.

Active Directory cũng cho phép admin áp dụng policies, ví dụ chặn user truy cập Control Panel hoặc không cho user có administrative privileges trên máy.

## Active Directory

Active Directory Domain Service, viết tắt là AD DS, là thành phần lõi của Windows Domain. AD DS hoạt động như một catalogue lưu thông tin về các objects trong network, ví dụ: users, groups, machines, printers, shares và nhiều loại object khác.

### Users

Users là một trong những object phổ biến nhất trong Active Directory.

Users được xem là security principals, nghĩa là chúng có thể được domain authenticate và có thể được gán privileges trên resources như files hoặc printers.

User thường đại diện cho hai loại entity:

* People: đại diện cho nhân sự trong tổ chức, ví dụ employees cần truy cập network.
* Services: đại diện cho các services như IIS hoặc MSSQL. Service users khác regular users vì chúng chỉ nên có privileges cần thiết để chạy service cụ thể.

### Machines

Machines cũng là object trong Active Directory. Mỗi computer join vào Active Directory domain sẽ tạo ra một machine object.

Machines cũng được xem là security principals và có account riêng giống như regular user, nhưng quyền của account này trong domain thường bị giới hạn.

Machine account thường là local administrator trên chính computer được gán. Thông thường, account này không dành cho user đăng nhập trực tiếp, nhưng nếu có password thì vẫn có thể dùng để log in.

Lưu ý: Machine Account passwords được rotate tự động và thường gồm khoảng 120 ký tự ngẫu nhiên.

Cách nhận diện machine account khá đơn giản: tên account là computer name kèm dấu `$`.

Ví dụ:

```
DC01$
```

Machine tên `DC01` sẽ có machine account là `DC01$`.

### Security Groups

Security Groups được dùng để gán access rights cho nhiều users cùng lúc thay vì gán quyền từng user riêng lẻ.

Ví dụ, thay vì cấp quyền truy cập shared folder cho từng user, admin có thể cấp quyền cho một group. Khi user được thêm vào group đó, họ tự động inherit privileges của group.

Groups cũng là security principals, nên có thể được cấp privileges trên resources trong network.

Một group có thể chứa:

* Users
* Machines
* Groups khác

Một số default security groups quan trọng trong domain:

| Security Group     | Mô tả                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| Domain Admins      | Có administrative privileges trên toàn domain, bao gồm cả DCs.                                  |
| Server Operators   | Có thể administer Domain Controllers nhưng không thể thay đổi administrative group memberships. |
| Backup Operators   | Có thể access bất kỳ file nào, bỏ qua permissions, thường dùng cho backup.                      |
| Account Operators  | Có thể create hoặc modify accounts trong domain.                                                |
| Domain Users       | Chứa toàn bộ user accounts trong domain.                                                        |
| Domain Computers   | Chứa toàn bộ computers trong domain.                                                            |
| Domain Controllers | Chứa toàn bộ DCs trong domain.                                                                  |

### Active Directory Users and Computers

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

Để cấu hình users, groups hoặc machines trong Active Directory, cần log in vào Domain Controller và mở:

```
Active Directory Users and Computers
```

Công cụ này cho phép xem hierarchy của users, computers và groups trong domain.

Các objects thường được tổ chức trong Organizational Units, viết tắt là OUs. OU là container object dùng để phân loại users và machines.

OUs thường được dùng để áp dụng policies cho các nhóm users có yêu cầu quản trị giống nhau. Ví dụ, Sales department có thể cần policies khác với IT department.

Lưu ý quan trọng: một user chỉ có thể thuộc một OU tại một thời điểm.

### OU trong domain THM

<figure><img src="../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

Trong machine của bài lab, có một OU tên là `THM`, bên trong gồm năm child OUs:

* IT
* Management
* Marketing
* R\&D
* Sales

Cách tổ chức OU thường phản ánh cấu trúc business để dễ deploy baseline policies theo từng department.

<figure><img src="../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

Trong từng OU, admin có thể thực hiện các task cơ bản như:

* Create user
* Delete user
* Modify user
* Reset password

Reset password là thao tác đặc biệt hữu ích cho helpdesk.

### Default Containers trong Active Directory

Ngoài OU `THM`, Windows tự động tạo một số default containers:

<table><thead><tr><th width="263.79998779296875">Container</th><th>Chức năng</th></tr></thead><tbody><tr><td>Builtin</td><td>Chứa default groups có sẵn cho Windows hosts.</td></tr><tr><td>Computers</td><td>Machines mới join domain sẽ nằm ở đây mặc định.</td></tr><tr><td>Domain Controllers</td><td>OU mặc định chứa các DCs trong network.</td></tr><tr><td>Users</td><td>Chứa default users và groups áp dụng ở domain-wide context.</td></tr><tr><td>Managed Service Accounts</td><td>Chứa accounts được services sử dụng trong Windows domain.</td></tr></tbody></table>

### Security Groups vs OUs

OUs và Security Groups đều dùng để phân loại users hoặc computers, nhưng mục đích khác nhau.

OUs dùng để áp dụng policies và configurations cho users hoặc computers theo vai trò, department hoặc yêu cầu quản trị. Một user chỉ nên nằm trong một OU tại một thời điểm.

Security Groups dùng để cấp permissions trên resources. Ví dụ: cấp quyền truy cập shared folder, network printer hoặc application resource.

Tóm gọn:

| Thành phần     | Mục đích chính                     |
| -------------- | ---------------------------------- |
| OU             | Áp dụng policies và configurations |
| Security Group | Cấp permissions vào resources      |

## Managing Users in AD

Your first task as the new domain administrator is to check the existing AD OUs and users, as some recent changes have happened to the business. You have been given the following organisational chart and are expected to make changes to the AD to match it:

![THM Organisational Chart](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/88f0ade5a672ae681639e6049406a4ec.png)

Deleting extra OUs and users

The first thing you should notice is that there is an additional department OU in your current AD configuration that doesn't appear in the chart. We've been told it was closed due to budget cuts and should be removed from the domain. If you try to right-click and delete the OU, you will get the following error:

![OU delete error](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/38edaf4a8665c257c62556096c69cb6f.png)

By default, OUs are protected against accidental deletion. To delete the OU, we need to enable the **Advanced Features** in the View menu:

![Enabling advanced features](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/15b282b6e3940f4c26c477a8c21f8266.png)

This will show you some additional containers and enable you to disable the accidental deletion protection. To do so, right-click the OU and go to Properties. You will find a checkbox in the Object tab to disable the protection:

![Disable OU delete protection](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/ad6b6d886c0448d14ce4ec8c62250256.png)

Be sure to uncheck the box and try deleting the OU again. You will be prompted to confirm that you want to delete the OU, and as a result, any users, groups or OUs under it will also be deleted.

After deleting the extra OU, you should notice that for some of the departments, the users in the AD don't match the ones in our organisational chart. Create and delete users as needed to match them.

Delegation

One of the nice things you can do in AD is to give specific users some control over some OUs. This process is known as **delegation** and allows you to grant users specific privileges to perform advanced tasks on OUs without needing a Domain Administrator to step in.

One of the most common use cases for this is granting `IT support` the privileges to reset other low-privilege users' passwords. According to our organisational chart, Phillip is in charge of IT support, so we'd probably want to delegate the control of resetting passwords over the Sales, Marketing and Management OUs to him.

For this example, we will delegate control over the Sales OU to Phillip. To delegate control over an OU, you can right-click it and select **Delegate Control**:

![Delegating OU control](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/74f8d615658a03aeb1cfdb6767d0a0a3.png)

This should open a new window where you will first be asked for the users to whom you want to delegate control:

**Note:** To avoid mistyping the user's name, write "phillip" and click the **Check Names** button. Windows will autocomplete the user for you.

![Delegating Sales OU to Phillip](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/2814715e1dbadaef334973028e02da69.png)<br>

Click OK, and on the next step, select the following option:

![Delegating password resets](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/3f81df2b38e35ca5729aee7a76c6b220.png)

Click next a couple of times, and now Phillip should be able to reset passwords for any user in the sales department. While you'd probably want to repeat these steps to delegate the password resets of the Marketing and Management departments, we'll leave it here for this task. You are free to continue to configure the rest of the OUs if you so desire.

Now let's use Phillip's account to try and reset Sophie's password. Here are Phillip's credentials for you to log in via RDP:

![THM key](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/fb7768e14470fc6b51d6fe2cc991cd6f.png)

| Username | phillip    |
| -------- | ---------- |
| Password | Claire2008 |

Note: When connecting via RDP, use `THM\phillip` as the username to specify you want to log in using the user `phillip` on the `THM` domain.

While you may be tempted to go to **Active Directory Users and Computers** to try and test Phillip's new powers, he doesn't really have the privileges to open it, so you'll have to use other methods to do password resets. In this case, we will be using Powershell to do so:

Windows PowerShell (As Phillip)

```powershell
PS C:\Users\phillip> Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose

New Password: *********

VERBOSE: Performing the operation "Set-ADAccountPassword" on target "CN=Sophie,OU=Sales,OU=THM,DC=thm,DC=local".
```

Since we wouldn't want Sophie to keep on using a password we know, we can also force a password reset at the next logon with the following command:

WindowsPowerShell(as Phillip)

```powershell
PS C:\Users\phillip> Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose

VERBOSE: Performing the operation "Set" on target "CN=Sophie,OU=Sales,OU=THM,DC=thm,DC=local".
```

<figure><img src="../../.gitbook/assets/image (860).png" alt=""><figcaption></figcaption></figure>

## Managing Computers in AD

By default, all the machines that join a domain (except for the DCs) will be put in the container called "Computers". If we check our DC, we will see that some devices are already there:

![Computers OU](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/a1d41d5437e73d62ede10f2015dc4dfc.png)

We can see some servers, some laptops and some PCs corresponding to the users in our network. Having all of our devices there is not the best idea since it's very likely that you want different policies for your servers and the machines that regular users use on a daily basis.

While there is no golden rule on how to organise your machines, an excellent starting point is segregating devices according to their use. In general, you'd expect to see devices divided into at least the three following categories:

**1. Workstations**

Workstations are one of the most common devices within an Active Directory domain. Each user in the domain will likely be logging into a workstation. This is the device they will use to do their work or normal browsing activities. These devices should never have a privileged user signed into them.<br>

**2. Servers**

Servers are the second most common device within an Active Directory domain. Servers are generally used to provide services to users or other servers.

**3. Domain Controllers**

Domain Controllers are the third most common device within an Active Directory domain. Domain Controllers allow you to manage the Active Directory Domain. These devices are often deemed the most sensitive devices within the network as they contain hashed passwords for all user accounts within the environment.

Since we are tidying up our AD, let's create two separate OUs for `Workstations` and `Servers` (Domain Controllers are already in an OU created by Windows). We will be creating them directly under the `thm.local` domain container. In the end, you should have the following OU structure:

![final OU structure](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/09405010962071f21c6dee7b4eb8c59a.png)

Now, move the personal computers and laptops to the Workstations OU and the servers to the Servers OU from the Computers container. Doing so will allow us to configure policies for each OU later.

<figure><img src="../../.gitbook/assets/image (861).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (862).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (863).png" alt=""><figcaption></figcaption></figure>

## Group Policies

Việc chia user và computer vào các **OU** không chỉ để sắp xếp, mà chủ yếu để áp dụng **chính sách riêng** cho từng OU.

Windows quản lý các chính sách này bằng **Group Policy Objects (GPO)**. GPO là tập hợp các cấu hình có thể áp dụng cho OU, giúp đặt quy định và tiêu chuẩn bảo mật khác nhau cho từng nhóm user hoặc máy tính.

<figure><img src="../../.gitbook/assets/image (864).png" alt=""><figcaption></figcaption></figure>

Khi mở công cụ quản lý Group Policy, bạn sẽ thấy toàn bộ cấu trúc **OU** đã tạo trước đó.

Để cấu hình chính sách, trước tiên cần tạo một **GPO** trong mục **Group Policy Objects**, sau đó **link GPO đó đến OU** mà bạn muốn áp dụng chính sách.

<figure><img src="../../.gitbook/assets/image (865).png" alt=""><figcaption></figcaption></figure>

We can see in the image above that 3 GPOs have been created. From those, the `Default Domain Policy` and `RDP Policy` are linked to the `thm.local` domain as a whole, and the `Default Domain Controllers Policy` is linked to the `Domain Controllers` OU only.

Một GPO sẽ áp dụng cho **OU được link** và cả các **OU con bên trong nó**. Vì vậy, nếu một GPO được link ở cấp domain, các OU bên dưới như **Sales** cũng có thể bị ảnh hưởng.

Ví dụ, **Default Domain Policy** được link trực tiếp với domain **thm.local**, nên chính sách này có thể áp dụng cho toàn bộ domain và các OU bên trong.

<figure><img src="../../.gitbook/assets/image (866).png" alt=""><figcaption></figcaption></figure>

GPO có thể dùng **Security Filtering** để chỉ áp dụng chính sách cho một số user hoặc computer cụ thể trong OU. Mặc định, GPO áp dụng cho nhóm **Authenticated Users**, tức là gần như tất cả user và máy tính đã đăng nhập trong domain.

Tab **Settings** hiển thị nội dung thật sự của GPO, tức là các cấu hình mà GPO áp dụng.

Mỗi GPO có thể có hai loại cấu hình:

* **Computer Configuration**: áp dụng cho máy tính.
* **User Configuration**: áp dụng cho người dùng.

Trong ví dụ này, **Default Domain Policy** chỉ chứa các cấu hình dành cho máy tính.

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

Bạn có thể mở rộng các mục trong GPO bằng nút **“show”** để xem chi tiết từng cấu hình.

Trong ví dụ này, **Default Domain Policy** chứa các cấu hình cơ bản thường áp dụng cho hầu hết domain, như:

* Chính sách mật khẩu.
* Chính sách khóa tài khoản khi đăng nhập sai nhiều lần.

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

Since this GPO applies to the whole domain, any change to it would affect all computers. Let's change the minimum password length policy to require users to have at least 10 characters in their passwords. To do this, right-click the GPO and select **Edit**:

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

Trong một **GPO** có rất nhiều chính sách khác nhau, nên không thể giải thích hết trong một bài học.

Bạn nên tự khám phá thêm vì nhiều chính sách khá dễ hiểu. Nếu muốn biết chi tiết một chính sách nào đó, có thể **double-click** vào chính sách đó và đọc tab **Explain** để xem phần giải thích.

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

GPO distribution \\

GPO được phân phối qua thư mục chia sẻ mạng **SYSVOL**, nằm trên **Domain Controller**. Mọi user trong domain thường có quyền truy cập SYSVOL để máy tính có thể đồng bộ GPO định kỳ.

Mặc định, SYSVOL nằm tại:

```powershell
C:\Windows\SYSVOL\sysvol\
```

Khi chỉnh sửa GPO, máy tính có thể mất tối đa khoảng **2 giờ** để nhận chính sách mới. Muốn cập nhật ngay trên một máy, chạy lệnh:

```powershell
gpupdate /force
```

Trong bài lab THM Inc., ta sẽ tạo GPO để:

1. **Chặn user không thuộc IT truy cập Control Panel**\
   → Chỉ user phòng IT được phép thay đổi cài đặt hệ thống.
2. **Tự động khóa màn hình sau 5 phút không hoạt động**\
   → Áp dụng cho workstation và server để tránh việc người dùng bỏ máy mà phiên đăng nhập vẫn mở.

Với chính sách chặn Control Panel, ta tạo GPO mới tên **Restrict Control Panel Access** và chỉnh trong phần **User Configuration**, vì chính sách này áp dụng theo người dùng.

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

Lưu ý rằng chúng ta đã bật chính sách **Prohibit Access to Control Panel and PC settings**.

Sau khi GPO được cấu hình, chúng ta sẽ cần liên kết nó với tất cả các OU tương ứng với những người dùng không được phép truy cập **Control Panel** trên PC của họ. Trong trường hợp này, chúng ta sẽ liên kết các OU **Marketing**, **Management** và **Sales** bằng cách kéo GPO vào từng OU đó.

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

#### Auto Lock Screen GPO

Đối với GPO đầu tiên, liên quan đến việc khóa màn hình cho workstation và server, chúng ta có thể áp dụng trực tiếp nó lên các OU **Workstations**, **Servers** và **Domain Controllers** mà chúng ta đã tạo trước đó.

Mặc dù giải pháp này có thể hoạt động, một cách khác là đơn giản áp dụng GPO vào **root domain**, vì chúng ta muốn GPO ảnh hưởng đến tất cả các máy tính. Vì các OU **Workstations**, **Servers** và **Domain Controllers** đều là OU con của root domain, chúng sẽ kế thừa các chính sách của nó.

**Lưu ý:** Bạn có thể nhận thấy rằng nếu GPO của chúng ta được áp dụng vào root domain, nó cũng sẽ được kế thừa bởi các OU khác như **Sales** hoặc **Marketing**. Vì các OU này chỉ chứa người dùng, nên mọi **Computer Configuration** trong GPO của chúng ta sẽ bị chúng bỏ qua.

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

Authentication Methods \\

Khi sử dụng **Windows Domain**, toàn bộ thông tin đăng nhập được lưu trong **DC**.

Khi một user đăng nhập vào một dịch vụ bằng tài khoản domain, dịch vụ đó sẽ hỏi **DC** để kiểm tra thông tin đăng nhập có đúng không.

Trong Windows Domain có 2 giao thức xác thực mạng chính:

* **Kerberos**: Được dùng trong các phiên bản Windows hiện đại. Đây là giao thức mặc định trong các domain hiện nay.
* **NetNTLM**: Giao thức xác thực cũ, vẫn được giữ lại để tương thích với hệ thống cũ.

Mặc dù **NetNTLM** đã lỗi thời, phần lớn hệ thống mạng vẫn bật cả **Kerberos** và **NetNTLM**. Phần tiếp theo sẽ giải thích kỹ hơn cách từng giao thức hoạt động.

### Kerberos Authentication

**Kerberos** là giao thức xác thực mặc định trên các phiên bản Windows hiện đại. Khi user đăng nhập vào một dịch vụ bằng Kerberos, họ sẽ được cấp **ticket**. Có thể hiểu ticket như “bằng chứng” cho thấy user đã xác thực thành công trước đó.

Quy trình Kerberos cơ bản:

1. User gửi **username** và **timestamp** đã được mã hóa bằng khóa tạo từ mật khẩu của họ đến **KDC**.\
   **KDC** thường nằm trên **Domain Controller** và chịu trách nhiệm tạo ticket Kerberos.
2. KDC gửi lại cho user một **Ticket Granting Ticket (TGT)**.\
   TGT cho phép user xin thêm các ticket khác để truy cập từng dịch vụ cụ thể mà không cần gửi lại mật khẩu mỗi lần.
3. Cùng với TGT, user nhận được một **Session Key** để dùng cho các yêu cầu tiếp theo.

Một điểm quan trọng là **TGT được mã hóa bằng hash mật khẩu của tài khoản `krbtgt`**, nên user không thể đọc nội dung bên trong TGT.

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

Khi user muốn truy cập một dịch vụ trong mạng như **file share**, **website** hoặc **database**, họ sẽ dùng **TGT** để yêu cầu **KDC** cấp một **TGS**.

**TGS** là ticket chỉ dùng để truy cập **một dịch vụ cụ thể** mà nó được tạo ra.

Để xin TGS, user gửi đến KDC:

* Username.
* Timestamp được mã hóa bằng **Session Key**.
* TGT.
* **SPN** — tên định danh cho biết user muốn truy cập dịch vụ nào và trên server nào.

Sau đó, KDC gửi lại cho user:

* **TGS**.
* **Service Session Key** để dùng khi xác thực với dịch vụ.

TGS được mã hóa bằng khóa tạo từ **Service Owner Hash**. **Service Owner** là tài khoản user hoặc machine account đang chạy dịch vụ đó.

Bên trong TGS có chứa một bản sao của **Service Session Key**, để dịch vụ có thể giải mã TGS và lấy key này dùng cho quá trình xác thực.

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

Sau đó, **TGS** sẽ được gửi đến dịch vụ mà user muốn truy cập để xác thực và thiết lập kết nối.

Dịch vụ sẽ dùng **password hash** của tài khoản đang chạy dịch vụ đó để giải mã **TGS** và kiểm tra **Service Session Key** có hợp lệ hay không.

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

### NetNTLM Authentication

NetNTLM works using a challenge-response mechanism. The entire process is as follows:

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

Quy trình xác thực **NetNTLM** hoạt động như sau:

1. Client gửi yêu cầu xác thực đến server muốn truy cập.
2. Server tạo một số ngẫu nhiên và gửi lại cho client dưới dạng **challenge**.
3. Client dùng **NTLM password hash** của mình kết hợp với challenge và một số dữ liệu khác để tạo **response**, rồi gửi response đó lại cho server.
4. Server chuyển tiếp **challenge** và **response** đến **Domain Controller** để kiểm tra.
5. Domain Controller dùng challenge để tính lại response, rồi so sánh với response mà client gửi.\
   Nếu trùng khớp thì client được xác thực, nếu không thì bị từ chối truy cập.
6. Server gửi kết quả xác thực lại cho client.

Điểm quan trọng: **mật khẩu hoặc hash của user không được truyền trực tiếp qua mạng**.

**Lưu ý:** Quy trình trên áp dụng khi dùng **domain account**. Nếu dùng **local account**, server có thể tự kiểm tra response mà không cần hỏi Domain Controller, vì hash mật khẩu được lưu cục bộ trong **SAM** của máy đó.

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

## Trees, Forests and Trusts&#x20;

Cho đến thời điểm này, chúng ta đã thảo luận cách quản lý một domain đơn lẻ, vai trò của Domain Controller và cách nó kết nối computers, servers và users.

![Single Domain](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/69f2441bbafd4cfe57a101d87f3c5950.png)

Khi công ty phát triển, network của họ cũng phát triển theo. Có một domain duy nhất là đủ tốt ở giai đoạn đầu, nhưng theo thời gian, một số nhu cầu mới có thể khiến bạn phải dùng nhiều hơn một domain.

### Trees

Hãy tưởng tượng rằng công ty của bạn đột ngột mở rộng sang một quốc gia mới. Quốc gia đó có các luật và quy định khác, buộc bạn phải cập nhật các GPO để tuân thủ. Ngoài ra, giờ đây bạn có đội ngũ IT ở cả hai quốc gia, và mỗi đội IT cần quản lý các resources thuộc về quốc gia của mình mà không can thiệp vào đội còn lại. Dù bạn có thể tạo một cấu trúc OU phức tạp và dùng delegations để làm việc này, một cấu trúc AD quá lớn sẽ khó quản lý và dễ dẫn đến lỗi do con người.

May mắn là Active Directory hỗ trợ tích hợp nhiều domains để bạn có thể phân chia network thành các đơn vị được quản lý độc lập. Nếu bạn có hai domains cùng chia sẻ một namespace giống nhau — `thm.local` trong ví dụ này — thì các domain đó có thể được ghép lại thành một **Tree**.

Nếu domain `thm.local` được tách thành hai subdomains cho chi nhánh UK và US, bạn có thể xây dựng một tree với root domain là `thm.local` và hai subdomains là `uk.thm.local` và `us.thm.local`, mỗi subdomain có AD, computers và users riêng:

![Tree](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/abea24b7979676a1dcc0c568054544c8.png)

Cấu trúc phân chia này giúp chúng ta kiểm soát tốt hơn việc ai có thể truy cập thứ gì trong domain. Đội IT của UK sẽ có DC riêng chỉ quản lý resources của UK. Ví dụ, một user ở UK sẽ không thể quản lý users ở US. Theo cách đó, Domain Administrators của mỗi chi nhánh sẽ có toàn quyền với DC của chi nhánh mình, nhưng không có quyền với DC của chi nhánh khác. Policies cũng có thể được cấu hình độc lập cho từng domain trong tree.

Khi nói về trees và forests, cần giới thiệu thêm một security group mới. Nhóm Enterprise Admins sẽ cấp cho user administrative privileges trên toàn bộ domains của một enterprise. Mỗi domain vẫn sẽ có Domain Admins với quyền quản trị trên domain riêng của họ, trong khi Enterprise Admins có thể kiểm soát toàn bộ enterprise.<br>

### Forests

Các domains bạn quản lý cũng có thể được cấu hình trong các namespace khác nhau. Giả sử công ty của bạn tiếp tục phát triển và cuối cùng mua lại một công ty khác tên là `MHT Inc.` Khi hai công ty sáp nhập, nhiều khả năng bạn sẽ có các domain trees khác nhau cho từng công ty, và mỗi tree được quản lý bởi phòng IT riêng. Sự kết hợp của nhiều trees có namespace khác nhau trong cùng một network được gọi là một **forest**.

![Forest](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/03448c2faf976db890118d835000bab7.png)

### Trust Relationships

Việc tổ chức nhiều domains thành trees và forest cho phép bạn có một network được phân tách tốt về mặt quản trị và resources. Nhưng đến một thời điểm nào đó, một user ở THM UK có thể cần truy cập một shared file trên một server của MHT ASIA. Để điều này xảy ra, các domains được sắp xếp trong trees và forests sẽ được liên kết với nhau bằng **trust relationships**.

Nói đơn giản, có trust relationship giữa các domains cho phép bạn authorise một user từ domain `THM UK` truy cập resources của domain `MHT EU`.

Trust relationship đơn giản nhất có thể thiết lập là **one-way trust relationship**. Trong one-way trust, nếu `Domain AAA` trusts `Domain BBB`, điều đó có nghĩa là một user ở BBB có thể được authorise để truy cập resources trên AAA:

![Trusts](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/af95eb1a4b6c672491d8989f79c00200.png)

Hướng của one-way trust relationship ngược với hướng truy cập.

Bạn cũng có thể tạo **two-way trust relationships** để cả hai domains có thể authorise users của nhau. Theo mặc định, khi ghép nhiều domains dưới cùng một tree hoặc forest, hệ thống sẽ tạo một two-way trust relationship.

Cần lưu ý rằng việc có trust relationship giữa các domains không tự động cấp quyền truy cập vào mọi resources ở domain khác. Khi trust relationship đã được thiết lập, bạn có khả năng authorise users qua lại giữa các domains, nhưng việc thực sự cấp quyền cho gì hay không vẫn phụ thuộc vào bạn.

<figure><img src="../../.gitbook/assets/image (877).png" alt=""><figcaption></figcaption></figure>
