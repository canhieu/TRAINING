# Penetration Testing Process

<figure><img src="../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

## Penetration Tester Path Syllabus <a href="#penetration-tester-path-syllabus" id="penetration-tester-path-syllabus"></a>

| **`Introduction`**                                                                 |
| ---------------------------------------------------------------------------------- |
| 1. [Penetration Testing Process](https://academy.hackthebox.com/module/details/90) |
| 2. [Getting Started](https://academy.hackthebox.com/module/details/77)             |

| **`Reconnaissance, Enumeration & Attack Planning`**                                         |
| ------------------------------------------------------------------------------------------- |
| 3. [Network Enumeration with Nmap](https://academy.hackthebox.com/module/details/19)        |
| 4. [Footprinting](https://academy.hackthebox.com/module/details/112)                        |
| 5. [Information Gathering - Web Edition](https://academy.hackthebox.com/module/details/144) |
| 6. [Vulnerability Assessment](https://academy.hackthebox.com/module/details/108)            |
| 7. [File Transfers](https://academy.hackthebox.com/module/details/24)                       |
| 8. [Shells & Payloads](https://academy.hackthebox.com/module/details/115)                   |
| 9. [Using the Metasploit Framework](https://academy.hackthebox.com/module/details/39)       |

| **`Exploitation & Lateral Movement`**                                                             |
| ------------------------------------------------------------------------------------------------- |
| 10. [Password Attacks](https://academy.hackthebox.com/module/details/147)                         |
| 11. [Attacking Common Services](https://academy.hackthebox.com/module/details/116)                |
| 12. [Pivoting, Tunneling, and Port Forwarding](https://academy.hackthebox.com/module/details/158) |
| 13. [Active Directory Enumeration & Attacks](https://academy.hackthebox.com/module/details/143)   |

| **`Web Exploitation`**                                                                       |
| -------------------------------------------------------------------------------------------- |
| 14. [Using Web Proxies](https://academy.hackthebox.com/module/details/110)                   |
| 15. [Attacking Web Applications with Ffuf](https://academy.hackthebox.com/module/details/54) |
| 16. [Login Brute Forcing](https://academy.hackthebox.com/module/details/57)                  |
| 17. [SQL Injection Fundamentals](https://academy.hackthebox.com/module/details/33)           |
| 18. [SQLMap Essentials](https://academy.hackthebox.com/module/details/58)                    |
| 19. [Cross-Site Scripting (XSS)](https://academy.hackthebox.com/module/details/103)          |
| 20. [File Inclusion](https://academy.hackthebox.com/module/details/23)                       |
| 21. [File Upload Attacks](https://academy.hackthebox.com/module/details/136)                 |
| 22. [Command Injections](https://academy.hackthebox.com/module/details/109)                  |
| 23. [Web Attacks](https://academy.hackthebox.com/module/details/134)                         |
| 24. [Attacking Common Applications](https://academy.hackthebox.com/module/details/113)       |

| **`Post-Exploitation`**                                                              |
| ------------------------------------------------------------------------------------ |
| 25. [Linux Privilege Escalation](https://academy.hackthebox.com/module/details/51)   |
| 26. [Windows Privilege Escalation](https://academy.hackthebox.com/module/details/67) |

| **`Reporting & Capstone`**                                                             |
| -------------------------------------------------------------------------------------- |
| 27. [Documentation & Reporting](https://academy.hackthebox.com/module/details/162)     |
| 28. [Attacking Enterprise Networks](https://academy.hackthebox.com/module/details/163) |

## Penetration Testing Overview

IT là một phần cốt lõi của gần như mọi công ty. Lượng dữ liệu quan trọng và dữ liệu mật trong hệ thống IT ngày càng tăng. Sự phụ thuộc vào việc hệ thống hoạt động liên tục cũng tăng theo.

Vì vậy, các cuộc tấn công vào mạng doanh nghiệp, hành vi làm gián đoạn tính sẵn sàng của hệ thống và các hình thức gây thiệt hại khác như ransomware ngày càng phổ biến. Dữ liệu bị lấy qua security breach hoặc cyber-attack có thể bị bán cho đối thủ, bị rò rỉ trên diễn đàn công khai hoặc bị dùng cho các mục đích xấu khác.

`Penetration Test` (`Pentest`) là một hoạt động tấn công có tổ chức, có mục tiêu và có ủy quyền nhằm kiểm tra hạ tầng IT và khả năng phòng thủ của tổ chức trước các lỗ hổng bảo mật. Pentest sử dụng các phương pháp và kỹ thuật mà attacker thực tế vẫn dùng.

Với vai trò penetration tester, chúng ta dùng nhiều kỹ thuật và phân tích khác nhau để đánh giá tác động của một lỗ hổng hoặc một chuỗi lỗ hổng lên:

* tính bảo mật (`confidentiality`)
* tính toàn vẹn (`integrity`)
* tính sẵn sàng (`availability`)
* `Mục tiêu của pentest là tìm ra và xác định TẤT CẢ lỗ hổng trong phạm vi hệ thống được kiểm tra, từ đó cải thiện mức độ an toàn của hệ thống đó.`

Một số hình thức đánh giá khác, chẳng hạn `red team assessment`, thường bám theo một kịch bản cụ thể. Chúng chỉ tập trung vào những lỗ hổng cần thiết để đạt một mục tiêu cuối cùng, ví dụ truy cập email của CEO hoặc lấy một flag trên máy chủ quan trọng.

#### Risk Management

Pentest cũng là một phần của `risk management`. Mục tiêu chính của `risk management` trong IT security là:

* xác định rủi ro tiềm ẩn
* đánh giá mức độ rủi ro
* giảm thiểu rủi ro xuống mức chấp nhận được

Các rủi ro này có thể ảnh hưởng đến `confidentiality`, `integrity` và `availability` của hệ thống thông tin và dữ liệu. Để xử lý chúng, tổ chức cần áp dụng các security control và policy phù hợp như access control, encryption và các biện pháp bảo mật khác.

Tuy nhiên, không thể loại bỏ toàn bộ rủi ro. Luôn tồn tại `inherent risk`, tức mức rủi ro vẫn còn ngay cả khi các security control phù hợp đã được áp dụng.

Doanh nghiệp có thể xử lý rủi ro theo nhiều cách:

* chấp nhận rủi ro
* chuyển giao rủi ro
* tránh rủi ro
* giảm thiểu rủi ro

Ví dụ, họ có thể mua bảo hiểm, ký hợp đồng với bên thứ ba, triển khai biện pháp phòng ngừa hoặc chuẩn bị quy trình giảm tác động nếu sự cố xảy ra.

Trong một pentest, chúng ta ghi lại chi tiết các bước đã thực hiện và kết quả đạt được. Tuy nhiên, việc khắc phục lỗ hổng là trách nhiệm của khách hàng hoặc đơn vị vận hành hệ thống.

Vai trò của chúng ta là:

* báo cáo lỗ hổng
* cung cấp bước tái hiện chi tiết
* đưa ra khuyến nghị khắc phục phù hợp

Chúng ta không trực tiếp vá lỗi, sửa mã nguồn hay thay đổi cấu hình sản phẩm của khách hàng. Cũng cần lưu ý rằng pentest không phải hoạt động giám sát liên tục. Nó chỉ phản ánh ảnh chụp tức thời của trạng thái bảo mật tại thời điểm kiểm tra. Điểm này nên được nêu rõ trong penetration test report.

#### Vulnerability Assessments

`Vulnerability analysis` là một khái niệm tổng quát. Nó có thể bao gồm vulnerability assessment, security assessment và penetration test.

Khác với pentest, `vulnerability assessment` hoặc `security assessment` chủ yếu dùng công cụ tự động. Hệ thống sẽ được kiểm tra với các lỗ hổng đã biết bằng những công cụ như [Nessus](https://www.tenable.com/products/nessus), [Qualys](https://www.qualys.com/apps/vulnerability-management/), [OpenVAS](https://www.openvas.org/).

Trong đa số trường hợp, các kiểm tra tự động này không thể tự điều chỉnh theo cấu hình thực tế của mục tiêu. Vì vậy, kiểm thử thủ công bởi một tester có kinh nghiệm vẫn là điều thiết yếu.

Ngược lại, pentest là sự kết hợp giữa kiểm thử tự động và kiểm thử thủ công. Nó thường được thực hiện sau một giai đoạn thu thập thông tin khá sâu, mà phần lớn là thủ công. Pentest được điều chỉnh riêng cho từng hệ thống, nên việc lập kế hoạch, thực thi và chọn công cụ sẽ phức tạp hơn nhiều.

Mọi penetration test hoặc security assessment chỉ nên được thực hiện khi có sự đồng thuận rõ ràng giữa các bên liên quan. Nếu tester không có văn bản ủy quyền rõ ràng, các hành động trong pentest có thể bị xem là hành vi phạm pháp.

Khách hàng chỉ được phép yêu cầu kiểm thử trên tài sản thuộc quyền sở hữu của họ. Nếu họ dùng hạ tầng của bên thứ ba, họ thường phải có chấp thuận bằng văn bản từ các bên đó trước khi kiểm thử. Một số nhà cung cấp như Amazon có policy riêng về penetration testing trên dịch vụ của họ. Vì vậy, trong giai đoạn scoping, cần xác nhận rõ quyền sở hữu tài sản và yêu cầu phê duyệt từ bên thứ ba nếu có.

Một pentest thành công đòi hỏi nhiều khâu tổ chức và chuẩn bị. Chúng ta cần một quy trình rõ ràng để bám theo, nhưng vẫn phải linh hoạt theo môi trường của từng khách hàng. Mỗi môi trường đều có đặc thù riêng.

Trong một số trường hợp, khách hàng chưa từng làm pentest trước đó. Khi đó, chúng ta phải giải thích rõ quy trình, hoạt động dự kiến và hỗ trợ họ xác định phạm vi kiểm thử chính xác.

Thông thường, nhân viên không được thông báo trước về pentest. Tuy vậy, quản lý có thể chọn thông báo cho họ. Việc này liên quan đến quyền được biết trong các tình huống mà nhân viên không có kỳ vọng về quyền riêng tư.

Trong quá trình pentest, chúng ta có thể nhìn thấy dữ liệu cá nhân như tên, địa chỉ, lương và nhiều thông tin nhạy cảm khác. Cách xử lý đúng là giữ kín các dữ liệu này và tuân thủ các quy định như [Data Protection Act](https://www.gov.uk/data-protection).

Ví dụ, nếu truy cập được vào cơ sở dữ liệu chứa số thẻ tín dụng, tên chủ thẻ và mã CVV, chúng ta cần khuyến nghị khách hàng:

* thay đổi mật khẩu ngay khi cần
* cải thiện chính sách mật khẩu
* mã hóa dữ liệu trong cơ sở dữ liệu

### Testing Methods <a href="#testing-methods" id="testing-methods"></a>

Một phần quan trọng của quy trình là xác định điểm bắt đầu của pentest. Mỗi pentest thường được thực hiện từ một trong hai góc nhìn sau:

* `External` or `Internal`

**External Penetration Test**

Nhiều pentest được thực hiện từ góc nhìn `External`, tức từ Internet hoặc như một người dùng ẩn danh bên ngoài. Mục tiêu phổ biến là đánh giá mức độ an toàn của external network perimeter.

Việc kiểm thử có thể được thực hiện từ máy của chúng ta hoặc từ một VPS. Một số khách hàng không quá quan tâm đến `stealth`. Một số khác lại yêu cầu tiếp cận thật im lặng để tránh firewall ban, IDS/IPS detection và các cơ chế cảnh báo.

Họ cũng có thể yêu cầu một cách tiếp cận `hybrid`, nghĩa là ban đầu giữ mức độ yên lặng cao, sau đó tăng dần độ ồn để kiểm tra khả năng phát hiện của đội phòng thủ.

Mục tiêu cuối cùng thường là:

* truy cập các host public-facing
* lấy dữ liệu nhạy cảm
* tìm đường vào internal network

**Internal Penetration Test**

Khác với `External`, `Internal Penetration Test` là kiểm thử từ bên trong mạng doanh nghiệp. Giai đoạn này có thể diễn ra sau khi đã xâm nhập thành công qua bài test `External`, hoặc bắt đầu từ một kịch bản `assumed breach`.

`Internal` pentest cũng có thể bao gồm các hệ thống tách biệt hoàn toàn khỏi Internet. Trường hợp này thường đòi hỏi hiện diện trực tiếp tại cơ sở của khách hàng.

### Types of Penetration Testing <a href="#types-of-penetration-testing" id="types-of-penetration-testing"></a>

Dù bắt đầu pentest theo cách nào, loại hình pentest vẫn rất quan trọng. Nó quyết định lượng thông tin mà chúng ta được cung cấp từ đầu.

| **Type**         | **Information Provided**                                                                                                                                                                                                                                      |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Blackbox`       | `Minimal`. Chỉ cung cấp thông tin tối thiểu, như IP address và domain.                                                                                                                                                                                        |
| `Greybox`        | `Extended`. Cung cấp thêm thông tin như URL cụ thể, hostname, subnet và các dữ liệu liên quan khác.                                                                                                                                                           |
| `Whitebox`       | `Maximum`. Gần như toàn bộ thông tin đều được cung cấp. Điều này cho phép chúng ta nhìn từ bên trong cấu trúc hệ thống và chuẩn bị tấn công bằng thông tin nội bộ. Có thể bao gồm cấu hình chi tiết, admin credentials, source code của web application, v.v. |
| `Red-Teaming`    | Có thể bao gồm physical testing, social engineering và các kỹ thuật khác. Có thể kết hợp với bất kỳ loại nào ở trên.                                                                                                                                          |
| `Purple-Teaming` | Cũng có thể kết hợp với các loại trên. Tuy nhiên, trọng tâm là phối hợp chặt chẽ với đội phòng thủ.                                                                                                                                                           |

Càng ít thông tin ban đầu, cách tiếp cận càng lâu và càng phức tạp. Ví dụ, với `Blackbox`, chúng ta phải tự xác định hệ thống có những server, host và service nào trước khi đi sâu hơn. Phần `recon` này có thể tốn nhiều thời gian, đặc biệt khi khách hàng yêu cầu kiểm thử theo hướng `stealth`.

### Types of Testing Environments <a href="#types-of-testing-environments" id="types-of-testing-environments"></a>

Ngoài phương pháp và loại hình kiểm thử, còn một yếu tố khác là đối tượng cần được kiểm thử. Có thể tóm tắt theo các nhóm sau:

|         |         |                   |                   |               |
| ------- | ------- | ----------------- | ----------------- | ------------- |
| Network | Web App | Mobile            | API               | Thick Clients |
| IoT     | Cloud   | Source Code       | Physical Security | Employees     |
| Hosts   | Server  | Security Policies | Firewalls         | IDS/IPS       |

Cần lưu ý rằng các nhóm này thường có thể kết hợp với nhau. Tùy loại bài test, phạm vi có thể bao gồm nhiều thành phần cùng lúc.

Tiếp theo, chúng ta sẽ đi sâu vào `Penetration Process` để xem từng giai đoạn được tách ra như thế nào và mỗi giai đoạn phụ thuộc vào giai đoạn trước ra sao.

## Penetration Testing Stages

Cách hiệu quả nhất để mô tả quy trình này là chia nó thành các `stages` có quan hệ phụ thuộc lẫn nhau. Nhiều tài liệu mô tả pentest như một vòng tròn khép kín. Tuy nhiên, nếu chỉ một thành phần trong vòng tròn đó không còn phù hợp, toàn bộ luồng làm việc có thể bị ảnh hưởng.

Nói cách khác, pentest không phải là một quy trình cứng để lặp lại y nguyên từng bước. Nó là tập hợp các `stages` cho phép chúng ta linh hoạt thay đổi cách tiếp cận dựa trên kết quả và thông tin mới thu được.

Chúng ta có thể xây dựng playbook riêng cho từng giai đoạn. Dù vậy, mỗi môi trường đều khác nhau, nên luôn phải điều chỉnh liên tục.

![](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/90/0-PT-Process.png)

Ở các phần sau, chúng ta sẽ đi sâu vào từng `stage` và xem chi tiết cách chúng hoạt động. Tài liệu cũng đưa ra một `optional study plan` để học các Tactics, Techniques, and Procedures (`TTPs`) theo đúng trình tự.

Study plan này được chia theo từng nhóm module tương ứng với từng `stage`. Cách chia này giúp tập trung vào đúng chủ đề và tránh bỏ sót kiến thức nền quan trọng. Trong thực tế học và làm, bạn sẽ lặp lại nhiều hoạt động như `Information Gathering`, `Lateral Movement` và `Pillaging` nhiều lần.

Nếu hổng kiến thức ở bất kỳ phần nào, bạn rất dễ gặp khó khăn hoặc hiểu sai khi đi tiếp. Vì vậy, quy trình penetration testing thường được nhìn theo các `stages` sau:

**Pre-Engagement**

`Pre-Engagement` là giai đoạn làm rõ với khách hàng và chốt nội dung trong hợp đồng. Tất cả bài kiểm thử và các thành phần liên quan cần được xác định rõ và ghi lại bằng văn bản.

Trong buổi họp trực tiếp hoặc conference call, chúng ta thường thống nhất:

* `Non-Disclosure Agreement`
* `Goals`
* `Scope`
* `Time Estimation`
* `Rules of Engagement`

**Information Gathering**

`Information Gathering` là giai đoạn thu thập thông tin bằng nhiều cách khác nhau. Chúng ta tìm hiểu về công ty mục tiêu, phần mềm và phần cứng đang dùng để phát hiện các khoảng trống bảo mật có thể giúp tạo `foothold`.

**Vulnerability Assessment**

Khi sang `Vulnerability Assessment`, chúng ta phân tích kết quả từ `Information Gathering`. Mục tiêu là tìm các lỗ hổng đã biết trong hệ thống, ứng dụng và từng phiên bản cụ thể để xác định `attack vector` khả thi.

Giai đoạn này bao gồm cả đánh giá thủ công lẫn tự động. Nó giúp xác định mức độ đe dọa và mức độ dễ bị tấn công của hạ tầng mạng.

**Exploitation**

Trong `Exploitation`, chúng ta dùng các kết quả đã có để thử tấn công vào các `vector` tiềm năng và giành `initial access` vào hệ thống mục tiêu.

**Post-Exploitation**

Ở giai đoạn này, chúng ta đã có quyền truy cập vào máy bị khai thác. Công việc tiếp theo là duy trì quyền truy cập đó nếu cần, kiểm tra khả năng `privilege escalation` và tìm dữ liệu nhạy cảm như `credentials` hoặc dữ liệu quan trọng khác.

Hoạt động tìm và thu thập dữ liệu có giá trị thường được gọi là `pillaging`. Đôi khi, `Post-Exploitation` được thực hiện để chứng minh mức độ ảnh hưởng cho khách hàng. Những lúc khác, nó là bước đệm cho `Lateral Movement`.

**Lateral Movement**

`Lateral Movement` là quá trình di chuyển trong internal network để truy cập thêm các host khác với cùng mức quyền hoặc cao hơn. Đây thường là một quá trình lặp, kết hợp chặt với `Post-Exploitation`.

Ví dụ, ta có `foothold` trên một web server, sau đó leo thang đặc quyền và tìm thấy một mật khẩu trong registry. Tiếp theo, ta `enumeration` thêm và phát hiện mật khẩu đó dùng được trên database server với quyền local admin. Từ đó, ta có thể `pillaging` dữ liệu nhạy cảm trong database và tìm thêm `credentials` để đi sâu hơn vào mạng nội bộ.

Ở giai đoạn này, kỹ thuật được dùng phụ thuộc rất nhiều vào thông tin lấy được từ host đã khai thác.

**Proof-of-Concept**

Trong `Proof-of-Concept`, chúng ta ghi lại từng bước đã thực hiện để đạt được việc compromise hệ thống hoặc một mức truy cập nào đó. Mục tiêu là cho khách hàng thấy rõ cách nhiều điểm yếu có thể được nối lại thành một chuỗi tấn công hoàn chỉnh.

Nếu ghi chép không tốt, khách hàng sẽ khó hiểu chính xác chúng ta đã làm gì. Khi đó, việc khắc phục cũng trở nên khó hơn. Nếu phù hợp, ta có thể viết script để tự động hóa các bước tái hiện phát hiện. Phần này được trình bày sâu hơn trong module `Documentation & Reporting`.

**Post-Engagement**

Trong `Post-Engagement`, chúng ta hoàn thiện tài liệu chi tiết cho cả quản trị viên và phía quản lý của khách hàng để họ hiểu mức độ nghiêm trọng của các lỗ hổng đã phát hiện.

Ở giai đoạn này, chúng ta cũng dọn sạch dấu vết hoạt động trên các host và server trong phạm vi cho phép. Đồng thời, chúng ta hoàn thiện các deliverable, tổ chức buổi walkthrough báo cáo và đôi khi trình bày với cấp quản lý hoặc ban lãnh đạo.

Cuối cùng, dữ liệu kiểm thử sẽ được lưu trữ theo nghĩa vụ hợp đồng và chính sách công ty. Thông thường, dữ liệu này được giữ trong một khoảng thời gian nhất định hoặc cho đến khi hoàn tất `retest` sau khắc phục.

### Importance <a href="#importance" id="importance"></a>

Chúng ta cần nắm chắc quy trình này và dùng nó làm nền tảng cho mọi technical engagement. Các thành phần trong từng `stage` giúp xác định rõ:

* chúng ta còn yếu ở đâu
* phần kiến thức nào đang bị hổng
* giai đoạn nào đang gây khó khăn nhiều nhất

Ví dụ, hãy xem một website là mục tiêu cần kiểm thử:

| **Stage**                     | **Description**                                                                                                                                                                                                               |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `1. Pre-Engagement`           | Bước đầu tiên là chuẩn bị đầy đủ tài liệu cần thiết, thống nhất mục tiêu đánh giá và làm rõ mọi câu hỏi còn tồn tại.                                                                                                          |
| `2. Information Gathering`    | Sau khi hoàn tất `Pre-Engagement`, chúng ta bắt đầu tìm hiểu website của công ty được giao kiểm thử. Mục tiêu là xác định công nghệ đang dùng và hiểu cách web application vận hành.                                          |
| `3. Vulnerability Assessment` | Từ các thông tin đã thu thập, chúng ta tìm các lỗ hổng đã biết và phân tích những chức năng đáng ngờ có thể dẫn đến hành vi ngoài ý muốn.                                                                                     |
| `4. Exploitation`             | Khi đã xác định được lỗ hổng tiềm năng, chúng ta chuẩn bị exploit code, công cụ và môi trường kiểm thử để khai thác các điểm đó trên web server.                                                                              |
| `5. Post-Exploitation`        | Sau khi khai thác thành công, chúng ta tiếp tục `information gathering` từ bên trong hệ thống. Nếu phát hiện dữ liệu nhạy cảm, chúng ta có thể thử `privilege escalation`, tùy theo hệ thống và cấu hình thực tế.             |
| `6. Lateral Movement`         | Nếu các server và host khác trong internal network nằm trong `scope`, chúng ta sẽ dùng thông tin thu được để di chuyển sang các hệ thống đó và mở rộng quyền truy cập.                                                        |
| `7. Proof-of-Concept`         | Chúng ta tạo `proof-of-concept` để chứng minh lỗ hổng thực sự tồn tại. Nếu cần, có thể tự động hóa các bước kích hoạt lỗ hổng để khách hàng dễ tái hiện.                                                                      |
| `8. Post-Engagement`          | Cuối cùng, tài liệu được hoàn thiện và bàn giao cho khách hàng dưới dạng báo cáo chính thức. Sau đó, chúng ta có thể tổ chức buổi walkthrough để giải thích kết quả kiểm thử và hỗ trợ đội ngũ phụ trách remediation khi cần. |





