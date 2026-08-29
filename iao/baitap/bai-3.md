# Bài 3

## Penetration Testing

### 1. Định nghĩa và Mục đích của Penetration Testing

#### 1.1. Định nghĩa

* Là cuộc tấn công thử nghiệm có tổ chức, được cấp phép vào hệ thống CNTT nhằm tìm kiếm lỗ hổng an ninh một cách chủ động.

#### 1.2. Mục đích

* Phát hiện **toàn bộ** lỗ hổng tiềm ẩn, đánh giá tác động thực tế và đưa ra khuyến nghị khắc phục để bảo vệ tính bảo mật, toàn vẹn và sẵn sàng của hệ thống.

### 2. Phân biệt các phương pháp Pentest

#### 2.1. Blackbox Testing

* **Định nghĩa:** Pentester không được cung cấp bất kỳ thông tin hay quyền truy cập nào (Zero Knowledge).
* **Đặc điểm:**
  * Mô phỏng hacker tấn công từ ngoài Internet vào.
  * **Ưu:** Khách quan, phản ánh đúng thực tế tấn công.
  * **Nhược:** Tốn thời gian, có thể bỏ sót lỗi sâu.

#### 2.2. Greybox Testing

* **Định nghĩa:** Pentester được cung cấp một phần thông tin như là công nghệ của web và tài khoản user được phân theo các cấp độ như superadmin - admin - mod - normal user .
* **Đặc điểm:**
  * Mô phỏng tấn công từ nội bộ hoặc khi hacker đã vượt qua lớp vỏ ngoài.
  * **Ưu:** Hiệu quả cao về chi phí và thời gian, tìm được lỗi phân quyền.
  * **Nhược:** Vẫn phụ thuộc vào lượng thông tin được cấp.

#### 2.3. Whitebox Testing

* **Định nghĩa:** Pentester có toàn quyền thông tin (Full Knowledge: Source code, Admin, tài liệu).
* **Đặc điểm:**
  * Kết hợp review code và kiểm thử động.
  * **Ưu:** Tìm lỗi toàn diện nhất, phát hiện lỗi logic sâu.
  * **Nhược:** Yêu cầu kỹ năng cao, tốn công sức phân tích.

### 3. Quy trình Penetration Testing

#### 3.1. Quy trình PTES (Penetration Testing Execution Standard)

PTES (Penetration Testing Execution Standard) là một **chuẩn quy trình** mô tả từ A–Z cách thực hiện một dự án pentest chuyên nghiệp: từ bước thỏa thuận với khách hàng, thu thập thông tin, phân tích rủi ro, khai thác cho đến hậu khai thác và báo cáo.

Mục đích chính:

* Đảm bảo **mọi pentest đều có cấu trúc** rõ ràng, không làm kiểu “scan vài tool rồi viết report”.
* Tạo **ngôn ngữ chung** giữa khách hàng và pentester: khi nói “pentest theo PTES” thì hai bên hiểu tương đối giống nhau về phạm vi, độ sâu, kỳ vọng.
* Gắn kết **kỹ thuật** với **business**: không chỉ liệt kê lỗ hổng, mà còn chỉ ra hệ thống nào, dữ liệu nào, quy trình nào bị ảnh hưởng, mức độ nghiêm trọng.

<table><thead><tr><th width="40">.</th><th>Quy trình</th><th>Thực hiện</th><th>Mô tả</th></tr></thead><tbody><tr><td>1</td><td>Pre-engagement Interactions</td><td>Tương tác trước khi thực hiện</td><td>Chuẩn bị, scope, RoE, thời gian, phương thức test</td></tr><tr><td>2</td><td>Intelligence Gathering</td><td>Thu thập thông tin tình báo</td><td>OSINT, recon nhiều level, chọn mục tiêu</td></tr><tr><td>3</td><td>Threat Modeling</td><td>Mô hình hóa mối đe dọa</td><td>Gắn tài sản – mối đe dọa – mục tiêu tấn công</td></tr><tr><td>4</td><td>Vulnerability Analysis</td><td>Phân tích lỗ hổng</td><td>Tìm, validate, ưu tiên lỗ hổng</td></tr><tr><td>5</td><td>Exploitation</td><td>Khai thác</td><td>Thực thi tấn công, chiếm quyền có chủ đích</td></tr><tr><td>6</td><td>Post Exploitation</td><td>Hậu khai thác</td><td>Đánh giá giá trị hệ thống đã chiếm, pivot, pillaging</td></tr><tr><td>7</td><td>Reporting</td><td>Báo cáo</td><td>Executive + technical report gắn với rủi ro business</td></tr></tbody></table>

Các giai đoạn chính của PTES:

#### &#x20;1. Pre-engagement Interactions

Đây là pha “đặt nền móng” – nếu làm hời hợt, toàn bộ pentest phía sau dễ lệch hướng.

Các nội dung chính:

* **Xác định mục tiêu**:
  * Bảo mật (primary): kiểm tra khả năng phòng thủ, phát hiện, phản ứng – không chỉ “cho đủ compliance”.​
  * Compliance, pháp lý (secondary): PCI DSS, HIPAA, v.v.
* **Scope**:
  * Hệ thống, IP range, domain, ứng dụng, API, môi trường (prod/UAT/dev), kênh tấn công (network, web, social, physical).
  * Rõ phần “out-of-scope” để tránh phạm luật/làm sập ngoài ý muốn.
* **Rules of Engagement (RoE)**:
  * Thời gian test, thời điểm allowed (ngoài giờ/giờ hành chính).
  * Có được phép DoS, brute-force mạnh, phishing thật, physical entry không?
  * Quy trình nếu gây outage, thấy dấu hiệu compromise thật, hoặc phát hiện dữ liệu cực kỳ nhạy cảm.
* **Pháp lý**:
  * Permission to Test có chữ ký người có thẩm quyền; ghi rõ scope, chấp nhận rủi ro instability.​
* **Risk rating & reporting**:
  * Chọn mô hình scoring (CVSS, DREAD, FAIR, custom) và cách gắn với business impact trong báo cáo.

Với người làm pentest, PTES làm cho pha này có cấu trúc rõ ràng, hạn chế scope creep và rủi ro pháp lý.

#### 2. Intelligence Gathering (IG)

Mục tiêu: **gather càng nhiều thông tin hữu ích càng tốt, trong giới hạn scope & RoE** – về tổ chức, hạ tầng, con người, đối tác – để xây dựng attack plan và threat model sau này.

PTES định nghĩa **3 level IG** như một “maturity model”:

* **Level 1 – Compliance Driven**\
  Phần lớn tự động, “bấm nút” với tool: WHOIS, DNS, cơ bản về IP range, port scan tối thiểu. Đủ để tick box cho PCI/FISMA/HIPAA.​
* **Level 2 – Best Practice**\
  L1 + manual analysis: sơ đồ tổ chức, các chi nhánh, đối tác, công nghệ dùng (job posting, document metadata), bức tranh business tương đối.
* **Level 3 – State-Sponsored / Red Team**\
  Toàn bộ L1+L2, cộng thêm phân tích sâu, social network analysis, HUMINT, OSINT nặng, phân tích báo cáo tài chính, ngành, chiến lược, quan hệ chính trị, v.v. – giống cách làm của threat actor sophisticate.

Các hoạt động lớn:

* **OSINT** (Passive / Semi-passive / Active):
  * Passive: tra cứu WHOIS, BGP, tài liệu công khai, search engine cache – không đụng trực tiếp tài nguyên của target.
  * Semi-passive: DNS bình thường, đọc metadata tài liệu trên web, crawl site… sao cho giống traffic user thông thường.
  * Active: port scan, DNS brute-force, banner grabbing, SNMP sweep, v.v. bắt đầu “chạm” hệ thống mục tiêu.
* **Corporate intelligence**:
  * Cấu trúc tổ chức, trụ sở, chi nhánh, thị trường, sản phẩm/dịch vụ, quan hệ đối tác, vendor, RFP/RFQ, báo cáo tài chính, báo cáo phân tích thị trường.
* **Individual / Human intelligence**:
  * Email pattern, nhân sự chủ chốt, social media, job posting, professional registry, v.v.
* **Footprinting & external target list**:
  * Mục tiêu là xây một danh sách target: host, domain, user, ứng dụng, dịch vụ… có ưu tiên.

Ý nghĩa thực tế: IG tốt giúp **giảm brute-force mù**, thay bằng những attack path có bối cảnh rõ, giống real attacker hơn.

#### 3. Threat Modeling

PTES coi **threat modeling là trái tim để nối kỹ thuật với business**. Mục tiêu: hiểu rõ **tài sản (assets)** và **kẻ tấn công (threat community/agent)**, từ đó ưu tiên test cho đúng chỗ “đau”.

Một threat model theo PTES phải tối thiểu có 4 thành phần:​

1. **Business assets** – dữ liệu, hệ thống, con người, đối tác, nhà cung cấp:
   * Dữ liệu tổ chức: tài chính, R\&D, marketing roadmap, technical design, source code, PII, PHI, tài khoản ngân hàng.​
   * Human assets: management, engineer, HR, và những người có thể bị leverage cho social engineering, insider threat.​
2. **Business processes** – quy trình tạo ra doanh thu/giá trị:\
   Chuỗi xử lý đơn hàng, thanh toán, phát hành sản phẩm, quy trình vận hành dịch vụ online, v.v. Cần map các asset, người, hạ tầng kỹ thuật hỗ trợ chúng.​
3. **Threat communities/agents** – ai là kẻ tấn công có ý nghĩa:
   * Nội bộ: nhân viên, quản lý, admin, contractor, partner có VPN.​
   * Bên ngoài: competitor, organized crime, hacktivist, script kiddies, nation-state….​
4. **Threat capability & motivation**:
   * Khả năng kỹ thuật, công cụ, khả năng có exploit 0-day hay chỉ dùng public exploit.
   * Accessibility với asset (internal access, internet-exposed, third party).
   * Động cơ: lợi nhuận, hacktivism, trả thù, mở đường tấn công đối tác, v.v..​

PTES đề xuất quy trình high-level:

* Thu thập tài liệu, sơ đồ hệ thống, quy trình.
* Phân loại **primary / secondary assets** (vd: DB CRM là primary, DB HR dùng chung server là secondary).
* Phân loại threats và threat communities (relevant vs non-relevant).
* Map threat communities ↔ tài sản, tạo ra **attack scenarios** quan trọng.

Điểm khác biệt: Model này phải **được đưa vào báo cáo cuối** và liên kết với risk scoring, chứ không chỉ là bước “lý thuyết”.

#### 4. Vulnerability Analysis

Ở pha này, PTES không chỉ bảo “chạy scanner” mà mô tả chi tiết cách kết hợp automated & manual, active & passive, research & validation để có **danh sách lỗ hổng đáng tin cậy, ít false positive**.

Các điểm chính:

* **Scope & depth/breadth**:
  * Depth: mức độ sâu (auth vs unauth, nội bộ vs external, test toàn bộ param hay chỉ một phần, v.v.).
  * Breadth: bao phủ host, segment, ứng dụng nào; confirm tất cả target trong inventory đều được quét, hoặc giải thích vì sao không.​
* **Active testing**:
  * Network scanner: port-based, service-based, banner grabbing.
  * Web app scanner: crawl + test, directory brute forcing, version/vuln check theo server/app/version, kiểm tra method (PUT/DELETE, WebDAV, TRACE, v.v.).​
  * Protocol-specific: VPN, VoIP, Citrix, DNS, mail, v.v..​
* **Passive techniques**:
  * Metadata analysis, internal traffic monitoring (nếu có chỗ đặt sensor hợp lệ), để phát hiện thông tin nhạy cảm “rò rỉ”.​
* **Validation & correlation**:
  * Correlate kết quả nhiều tool theo 2 chiều: specific (CVE/ID) & categorical (mapping theo standard như PCI/NIST/OWASP Top 10).​
  * Manual validation để loại false positive và phát hiện lỗ hổng mà tool không nhận ra.​
* **Research**:
  * Tra CVE, OSVDB, Bugtraq, vendor advisory, exploit-db, framework modules để hiểu tính khai thác được (exploitability) và điều kiện môi trường.​
  * Review hardening guide, common misconfig, default password, v.v.

Pha này là bước “dịch” từ IG + Threat Modeling thành **attack tree cụ thể** cho Exploitation.​

#### 5. Exploitation

Mục tiêu: **thiết lập quyền truy cập vào hệ thống/tài nguyên** (initial foothold và/hoặc privilege escalation), **theo cách có chủ đích và càng ít noise càng tốt**.

Các nguyên tắc:

* **Precision strike**:
  * Không “quét exploit loạn xạ”, mà ưu tiên các vector có xác suất thành công cao, impact lớn, phù hợp threat model.
* **Bypass countermeasures**:
  * Anti-virus, EDR, HIDS/HIPS → encoding, packing, encrypting, process injection, in-memory payload, whitelist bypass.​
  * DEP, ASLR → ROP chain, kỹ thuật bypass memory protection.​
  * WAF, IPS → evasion, payload obfuscation, chuỗi tấn công chậm/lẻ để tránh signature.
* **Human angle**:
  * Social engineering, phishing, pretexting, physical intrusion… nếu được scope cho phép.
* **Zero-day angle (nếu cần)**:
  * Fuzzing, reverse engineering, source code review để tìm lỗ hổng mới khi không còn vector public exploit hợp lệ, thường chỉ dùng cho engagement cao cấp/red team.

Kết quả cần chứng minh rõ:

* Đã compromise hệ thống nào, qua lỗ hổng nào, với cấp quyền nào.
* Đã bypass được kiểm soát nào (AV/WAF/IDS/EDR?) và dưới điều kiện gì.
* Gắn exploit với mục tiêu trong pre-engagement (ví dụ: đạt được quyền đọc dữ liệu PCI, chiếm tài khoản admin ứng dụng quan trọng, v.v.).

#### 6. Post Exploitation

Pha này PTES đi khá sâu, bởi đây là nơi chứng minh **tác động thật sự lên business** – không chỉ “shell là xong”.

Các mục tiêu:

1. **Đánh giá giá trị hệ thống đã chiếm**:
   * Dữ liệu gì trên host? Vai trò gì trong kiến trúc? Có DC, DB, App critical, file server, backup, CA, v.v.?
2. **Mở rộng chỗ đứng (pivoting & lateral movement)**:
   * Từ máy đã chiếm sang subnet khác, server khác, cloud, VPN, v.v.
3. **Pillaging**:
   * Thu thập bằng chứng và dữ liệu nhạy cảm **trong phạm vi cho phép**: report access, không giữ dữ liệu hơn cần thiết.
4. **Persistence & exfiltration testing** (nếu scope cho phép):
   * Đặt backdoor có auth, reverse shell hạn chế IP, VPN nội bộ, v.v.
   * Kiểm tra đường exfiltration, đo hiệu quả DLP/firewall/IDS trong phát hiện/lọc dữ liệu ra ngoài.

PTES dành phần lớn nội dung để mô tả chi tiết:

* **Rules of Engagement đặc thù post-exploitation**:
  * Không đụng vào service được đánh dấu critical, trừ khi được phê duyệt.
  * Ghi lại toàn bộ thay đổi (config, account, file…) và rollback về trạng thái ban đầu, hoặc nói rõ cái gì không thể rollback.
  * Bảo vệ dữ liệu cá nhân và dữ liệu regulated (PII, PHI, PCI…); không ghi password thật vào báo cáo, chỉ ghi hash/mask.
* **Infrastructure analysis**:
  * Network config: interface, route, ARP, DNS, VPN, proxy.
  * Listening services, directory service, neighbor discovery (CDP/LLDP, NetBIOS, mDNS).
* **Dịch vụ giá trị cao**:
  * File/print server, DB server, directory/LDAP, DNS, DHCP, backup, CA, virtualization platform, monitoring & management (SNMP, RDP, SSH, vCenter…), messaging/mail.
* **User & endpoint data**:
  * History, credential store, browser history & cookies, IM logs (trong giới hạn policy).
* **Data exfiltration**:
  * Lập bản đồ mọi đường có thể (HTTP(S), DNS, email, FTP, cloud, VPN…).
  * Thử exfiltrate với dữ liệu giả hoặc dataset đã thỏa thuận, đo khả năng phát hiện/chặn của hệ thống hiện tại.
* **Cleanup**:
  * Xóa tool, backdoor, account tạo mới; khôi phục cấu hình.
  * Hủy dữ liệu thu được sau khi khách hàng chấp nhận báo cáo, chứng minh hủy.

Từ góc nhìn khách hàng, đây là pha cho thấy **nếu thật sự bị hack thì chuyện gì có thể xảy ra**, và hệ thống phát hiện/phản ứng hiện tại thực sự hoạt động thế nào.

#### 7. Reporting

PTES dành hẳn một chuẩn riêng cho báo cáo, chia thành hai phần: **Executive Summary** và **Technical Report**.

1. **Executive Summary** – cho lãnh đạo & non-technical stakeholder:
   * **Background**: Lý do test, bối cảnh business, phạm vi high-level.​
   * **Overall posture**: Đánh giá tổng quan: rủi ro đang ở mức nào? (vd “elevated risk”), team có đạt được mục tiêu không, systemically vấn đề nằm ở đâu (process, patching, config…).
   * **Risk ranking/profile**: Trình bày risk score tổng thể, giải thích phương pháp tính, highlight một vài lỗ hổng nghiêm trọng nhất.
   * **General findings**: Thống kê, biểu đồ – phân bố mức độ severity, loại lỗ hổng, nguyên nhân gốc (process, training, patching, architecture…).​
   * **Recommendation summary & strategic roadmap**: Lộ trình ưu tiên theo thời gian (0–3 tháng, 3–6, >6), bám theo threat model & business priority.
2. **Technical Report** – cho security team, admin, developer:
   * **Introduction**: người tham gia, scope, mục tiêu chi tiết, strength of test (black/grey/white box), threat/grading structure.​
   * **Information Gathering**: Passive, active, corporate, personnel intelligence – mô tả rõ đã thu được gì, bằng cách nào.​
   * **Vulnerability Assessment**: Cách tìm, phân loại, validation, tổng hợp kết quả.​
   * **Exploitation/Vulnerability Confirmation**: Timeline các attack, host nào exploit được, level of access, đường escalation, link tới mục tiêu đề ra.​
   * **Post-Exploitation**:\
     Privilege escalation, truy cập data critical/compliance, persistent access, exfiltration, hiệu quả countermeasure (FW/WAF/IDS/IPS/EDR, quy trình IR…).​
   * **Risk/Exposure**: Gắn từng finding/chuỗi exploit với:
     * Xác suất (threat capability, control strength, compound vulnerabilities)
     * Hậu quả (loss magnitude primary/secondary)
     * Root cause là process, not “thiếu patch” đơn thuần.
   * **Conclusion**: Tóm lại posture, xu hướng, và khuyến nghị duy trì/chu kỳ test trong tương lai.​

Điểm mạnh: PTES buộc pentest report **gắn chặt với threat model và business impact**, chứ không phải chỉ là “scan report + vài dòng note”.

#### 3.2. Quy trình Pentest theo OWASP WSTG 4.2

OWASP WSTG 4.2 cung cấp **11 danh mục kiểm thử chính** (categories) cho pentest web application, mỗi danh mục có nhiều test case chi tiết (87+ checklist). Đây không phải là "7 bước" như PTES mà là **checklist có hệ thống** để đảm bảo không bỏ sót lỗ hổng nào.​

**Các giai đoạn chính:**

1. **Information Gathering (WSTG-INFO)**\
   Thu thập thông tin về web server, ứng dụng, framework, entry points, execution paths.
   * Fingerprint web server, enumerate applications, review metafiles, map architecture.
   * Mục tiêu: Vẽ **bề mặt tấn công** (attack surface), tìm điểm vào (entry points).​
2. **Configuration and Deployment Management Testing (WSTG-CONF)**\
   Kiểm tra cấu hình server, ứng dụng, hạ tầng triển khai.
   * Test network config, HTTP methods, HSTS, file extensions, backup files, admin interfaces.
   * Mục tiêu: Phát hiện **misconfiguration** gây rò rỉ thông tin hoặc truy cập trái phép.​
3. **Identity Management Testing (WSTG-IDNT)**\
   Kiểm tra quản lý danh tính người dùng.
   * Test role definitions, user registration, account provisioning, account enumeration, weak username policy.
   * Mục tiêu: Đảm bảo **quy trình tạo tài khoản** an toàn, không đoán/enum được user.​
4. **Authentication Testing (WSTG-ATHN)**\
   Kiểm tra cơ chế xác thực (login).
   * Test credentials over HTTP, default creds, weak lockout, bypass auth, weak password policy, remember password, browser cache.
   * Mục tiêu: **Bảo vệ thông tin xác thực**, ngăn bypass hoặc brute-force login.​
5. **Authorization Testing (WSTG-AUTHZ)**\
   Kiểm tra phân quyền sau khi login.
   * Test directory traversal, bypassing authz, privilege escalation, IDOR, OAuth weaknesses.
   * Mục tiêu: Đảm bảo **user chỉ truy cập được quyền của mình**, không leo thang đặc quyền.​
6. **Session Management Testing (WSTG-SESS)**\
   Kiểm tra quản lý phiên (session).
   * Test session fixation, cookies attributes, CSRF, logout, session timeout, session hijacking, JWT.
   * Mục tiêu: **Session an toàn**, không bị đánh cắp hoặc giả mạo.​
7. **Input Validation Testing (WSTG-INPV)**\
   Kiểm tra xử lý input từ user (injection attacks).
   * Test XSS (reflected/stored), SQLi (Oracle/MySQL/PostgreSQL), LDAPi, XMLi, XPathi, Commandi, Codei, SSRF, Host Header Injection.
   * Mục tiêu: **Xác thực/sanitize input** để ngăn injection attacks.​
8. **Testing for Error Handling (WSTG-ERRH)**\
   Kiểm tra cách ứng dụng xử lý lỗi.
   * Test error messages lộ thông tin (stack trace, DB info), verbose error handling.
   * Mục tiêu: **Ẩn thông tin nhạy cảm** trong error message, tránh hỗ trợ attacker.​
9. **Testing for Weak Cryptography (WSTG-CRYP)**\
   Kiểm tra mã hóa và crypto.
   * Test weak algorithms (MD5, SHA1), SSL/TLS config, weak keys, hardcoded secrets.
   * Mục tiêu: Đảm bảo **crypto mạnh**, không dùng thuật toán lỗi thời.​
10. **Business Logic Testing (WSTG-BUSL)**\
    Kiểm tra logic nghiệp vụ (không phải lỗi code).
    * Test workflow bypass, race conditions, business logic flaws (ví dụ: mua hàng giá 0đ).
    * Mục tiêu: Phát hiện **lỗi logic** có thể bị lợi dụng gây thiệt hại tài chính.​
11. **Client-side Testing (WSTG-CLNT)**\
    Kiểm tra bảo mật phía client (browser).
    * Test DOM XSS, client-side URL redirect, CSS injection, HTML injection, client-side resource manipulation.
    * Mục tiêu: Ngăn **tấn công phía client** như DOM-based XSS, defacement.​

### 4. Reconnaissance&#x20;

#### 4.1. Định nghĩa và Vai trò

Reconnaissance (Recon) là bước đầu tiên trong quá trình kiểm thử thâm nhập, nhằm thu thập thông tin về mục tiêu trước khi tiến hành quét và khai thác. Một phần quan trọng của Recon là OSINT, tức là sử dụng các nguồn công khai và công cụ nguồn mở để thu thập và phân tích thông tin.

Recon giúp pentester hiểu rõ mục tiêu như tổ chức, công nghệ sử dụng, cơ sở hạ tầng và nhân viên, từ đó xây dựng chiến lược tấn công phù hợp. Các thông tin này giúp tăng hiệu quả các kỹ thuật như phishing, brute-force, và khai thác lỗ hổng, đồng thời giảm thời gian kiểm thử bằng cách tập trung vào các mục tiêu và dịch vụ cụ thể.

Thông tin trong Recon thường được chia thành ba nhóm chính:

* **Organization:** thông tin về tổ chức, sản phẩm, hoạt động và tin tức
* **Infrastructure:** thông tin kỹ thuật như IP, domain, hostname, server và phần mềm
* **Employees:** thông tin về nhân viên như email, username và quyền hạn

Recon cũng giúp xác định danh sách các mục tiêu nằm trong phạm vi kiểm thử, làm cơ sở cho các bước tiếp theo như scanning, enumeration và exploitation.

#### 4.2. Phân loại Reconnaissance

#### 4.2.1. Passive Reconnaissance

**Định nghĩa:**\
Passive Reconnaissance là quá trình thu thập thông tin về mục tiêu mà không tương tác trực tiếp với hệ thống của mục tiêu. Thông tin được lấy từ các nguồn công khai (OSINT), do đó không tạo traffic đến target và khó bị phát hiện.

**Mục tiêu:**

* Thu thập thông tin domain
* Xác định IP, DNS, subdomain
* Xác định công nghệ và hạ tầng
* Thu thập thông tin tổ chức và nhân viên

**Đặc điểm:**

* Không gửi packet trực tiếp đến target
* Không bị ghi log bởi hệ thống mục tiêu
* Độ stealth cao
* Thông tin có thể không đầy đủ hoặc không chính xác hoàn toàn

**Ví dụ hành động và công cụ:**

* Tra cứu thông tin domain:

```bash
whois example.com
```

* Thu thập DNS records:

```bash
nslookup example.com
dig example.com
```

* Thu thập subdomain:
* DNSDumpster
* crt.sh
* Thu thập thông tin server public:
* Shodan.io
* Thu thập thông tin từ nguồn công khai:
* Google
* LinkedIn
* Website công ty

***

#### 4.2.2. Active Reconnaissance

**Định nghĩa:**\
Active Reconnaissance là quá trình thu thập thông tin bằng cách gửi trực tiếp các request hoặc packet đến hệ thống mục tiêu. Phương pháp này cho phép thu thập thông tin chính xác hơn, nhưng có thể bị phát hiện và ghi log.

**Mục tiêu:**

* Xác định host đang hoạt động
* Xác định open ports
* Xác định service và version
* Xác định entry point

**Đặc điểm:**

* Có tương tác trực tiếp với target
* Có thể bị phát hiện bởi firewall, IDS, IPS
* Thông tin chính xác và chi tiết hơn
* Có mức độ rủi ro cao hơn Passive Recon

**Ví dụ hành động và công cụ:**

**Kiểm tra host hoạt động:**

```bash
ping example.com
```

**Xác định đường đi network:**

```bash
traceroute example.com
```

**Kết nối và kiểm tra service:**

```bash
telnet example.com 80
```

```bash
nc example.com 80
```

**Quét port và service bằng Nmap:**

Quét port:

```bash
nmap example.com
```

Quét service version:

```bash
nmap -sV example.com
```

Quét toàn diện:

```bash
nmap -A example.com
```



#### 4.2.3. Các công cụ sử dụng

#### Công cụ cho Passive Recon

Passive Recon sử dụng các nguồn công khai và không tương tác trực tiếp với hệ thống mục tiêu.

**1. Công cụ tra cứu Domain và DNS:**

* **whois** – tra cứu thông tin đăng ký domain
* **nslookup** – truy vấn DNS records
* **dig** – truy vấn DNS chi tiết hơn nslookup
* **host** – tra cứu DNS nhanh

**2. Công cụ thu thập subdomain và hạ tầng:**

* **DNSDumpster** – liệt kê subdomain và DNS info
* **crt.sh** – tìm subdomain từ SSL certificate
* **SecurityTrails** – lịch sử DNS và domain
* **Amass (passive mode)** – thu thập subdomain từ OSINT

**3. Công cụ tìm kiếm thông tin công khai (OSINT):**

* **Google Dorks** – tìm file nhạy cảm, admin page, login page
* **theHarvester** – thu thập email, domain, hostname
* **Maltego** – thu thập và phân tích thông tin OSINT
* **Recon-ng** – framework OSINT tự động

**4. Công cụ thu thập thông tin server public:**

* **Shodan** – tìm server, open ports, service public
* **Censys** – tìm thông tin host và service
* **ZoomEye** – tương tự Shodan

**5. Nguồn thông tin công khai khác:**

* LinkedIn – thông tin nhân viên
* GitHub – source code, credentials bị lộ
* Website công ty – công nghệ sử dụng
* Social media – thông tin tổ chức



#### Công cụ cho Active Recon

Active Recon tương tác trực tiếp với mục tiêu để thu thập thông tin kỹ thuật.

**1. Công cụ kiểm tra host và network:**

* **ping** – kiểm tra host hoạt động
* **traceroute / tracert** – xác định đường đi network

**2. Công cụ kiểm tra port và service:**

* **telnet** – kết nối đến port cụ thể
* **netcat (nc)** – kiểm tra port và banner grabbing

Ví dụ:

```bash
nc example.com 80
```

**3. Công cụ quét port và service:**

* **Nmap** – quét port, service, version, OS detection

Ví dụ:

```bash
nmap -sV example.com
```

**4. Công cụ quét lỗ hổng:**

* **Nessus** – vulnerability scanner
* **OpenVAS** – open-source vulnerability scanner
* **Nikto** – quét lỗ hổng web server

Ví dụ:

```bash
nikto -h http://example.com
```

**5. Công cụ tương tác và phân tích web:**

* **Web browser** – phân tích website, source code
* **Burp Suite** – phân tích và chặn HTTP request
* **curl** – gửi HTTP request thủ công

Ví dụ:

```bash
curl -I http://example.com
```

