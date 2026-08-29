# Information Gathering - Web Edition

Web reconnaissance là bước đầu tiên trong mọi hoạt động security assessment hoặc penetration testing. Nó giống như giai đoạn điều tra ban đầu của một thám tử, nơi ta thu thập cẩn thận các manh mối và bằng chứng về mục tiêu trước khi xây dựng kế hoạch hành động. Trong môi trường số, điều này có nghĩa là tích lũy thông tin về một website hoặc web application để xác định các lỗ hổng tiềm ẩn, security misconfiguration và các asset có giá trị.

Mục tiêu chính của web reconnaissance là hiểu đầy đủ digital footprint của mục tiêu. Việc này bao gồm:

* `Identifying Assets`: Phát hiện toàn bộ domain, subdomain và IP address liên quan để dựng bản đồ hiện diện trực tuyến của mục tiêu.
* `Uncovering Hidden Information`: Web reconnaissance hướng đến việc tìm ra các directory, file và technology không dễ thấy, nhưng có thể đóng vai trò là điểm vào cho attacker.
* `Analyzing the Attack Surface`: Bằng cách xác định open port, service đang chạy và phiên bản phần mềm, bạn có thể đánh giá các lỗ hổng và điểm yếu tiềm năng của mục tiêu.
* `Gathering Intelligence`: Thu thập thông tin về nhân viên, email address và technology đang sử dụng có thể hỗ trợ social engineering attack hoặc giúp xác định các lỗ hổng gắn với phần mềm cụ thể.

Web reconnaissance có thể được thực hiện bằng kỹ thuật active hoặc passive. Mỗi kỹ thuật có ưu điểm và hạn chế riêng:

| Type                   | Mô tả                                                                                     | Risk of Detection | Ví dụ                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------- |
| Active Reconnaissance  | Tương tác trực tiếp với hệ thống mục tiêu, ví dụ gửi probe hoặc request.                  | Cao hơn           | Port scanning, vulnerability scanning, network mapping                                  |
| Passive Reconnaissance | Thu thập thông tin mà không tương tác trực tiếp với mục tiêu, dựa trên dữ liệu công khai. | Thấp hơn          | Search engine query, WHOIS lookup, DNS enumeration, phân tích web archive, social media |

### WHOIS <a href="#whois" id="whois"></a>

WHOIS là một query and response protocol được dùng để truy xuất thông tin về domain name, IP address và các internet resource khác. Về bản chất, nó là một directory service cho biết ai sở hữu domain, khi nào domain được đăng ký, thông tin liên hệ và nhiều dữ liệu khác. Trong web reconnaissance, WHOIS lookup có thể là một nguồn thông tin rất hữu ích, vì nó có thể tiết lộ danh tính của chủ sở hữu website, thông tin liên hệ của họ và các chi tiết khác có thể dùng cho điều tra sâu hơn hoặc social engineering attack.

Ví dụ, nếu bạn muốn biết ai sở hữu domain `example.com`, bạn có thể chạy lệnh sau trong terminal:

```bash
whois example.com
```

Lệnh này sẽ trả về nhiều thông tin, gồm registrar, ngày đăng ký, ngày hết hạn, nameserver và thông tin liên hệ của chủ sở hữu domain.

Tuy nhiên, cần lưu ý rằng dữ liệu WHOIS có thể không chính xác hoặc bị che giấu có chủ đích. Vì vậy, luôn nên kiểm chứng thông tin từ nhiều nguồn. Các privacy service cũng có thể che đi chủ sở hữu thực sự của một domain, khiến việc lấy thông tin chính xác qua WHOIS trở nên khó hơn.

### DNS <a href="#dns" id="dns"></a>

Domain Name System (DNS) hoạt động như GPS của Internet, chuyển các domain name dễ nhớ sang các địa chỉ IP dạng số mà máy tính dùng để giao tiếp. Tương tự cách GPS chuyển tên điểm đến thành tọa độ, DNS bảo đảm trình duyệt của bạn truy cập đúng website bằng cách ghép tên của website với IP address tương ứng. Điều này giúp bạn không phải ghi nhớ các địa chỉ số phức tạp, từ đó việc duyệt web trở nên liền mạch và hiệu quả hơn.

Lệnh `dig` cho phép bạn query trực tiếp DNS server để lấy thông tin cụ thể về domain name. Ví dụ, nếu bạn muốn tìm IP address gắn với `example.com`, bạn có thể chạy lệnh sau:

```bash
dig example.com A
```

Lệnh này yêu cầu `dig` query DNS để lấy `A` record của `example.com`. `A` record là bản ghi ánh xạ hostname sang IPv4 address. Kết quả trả về thường sẽ bao gồm IP address được yêu cầu, cùng với các chi tiết bổ sung về query và response. Khi thành thạo `dig` và hiểu các loại DNS record khác nhau, bạn sẽ có khả năng trích xuất thông tin có giá trị về hạ tầng và hiện diện trực tuyến của mục tiêu.

DNS server lưu nhiều loại record khác nhau. Mỗi loại phục vụ một mục đích riêng:

| Record Type | Mô tả                                                           |
| ----------- | --------------------------------------------------------------- |
| A           | Ánh xạ một hostname tới một IPv4 address.                       |
| AAAA        | Ánh xạ một hostname tới một IPv6 address.                       |
| CNAME       | Tạo alias cho một hostname và trỏ nó tới một hostname khác.     |
| MX          | Chỉ định mail server chịu trách nhiệm xử lý email cho domain.   |
| NS          | Ủy quyền một DNS zone cho một authoritative name server cụ thể. |
| TXT         | Lưu trữ thông tin dạng text tùy ý.                              |
| SOA         | Chứa thông tin quản trị về một DNS zone.                        |

### Subdomains <a href="#subdomains" id="subdomains"></a>

Subdomain về cơ bản là phần mở rộng của domain chính. Chúng thường được dùng để tổ chức các khu vực hoặc service khác nhau trong một website. Ví dụ, một công ty có thể dùng `mail.example.com` cho email server hoặc `blog.example.com` cho blog của họ.

Từ góc nhìn reconnaissance, subdomain rất có giá trị. Chúng có thể mở ra attack surface bổ sung, làm lộ hidden service và cung cấp manh mối về cấu trúc nội bộ của network mục tiêu. Subdomain có thể đang host development server, staging environment hoặc thậm chí các ứng dụng bị bỏ quên chưa được bảo vệ đúng cách.

Quá trình phát hiện subdomain được gọi là subdomain enumeration. Có hai hướng tiếp cận chính:

| Approach              | Mô tả                                                                                                | Ví dụ                                                   |
| --------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `Active Enumeration`  | Tương tác trực tiếp với DNS server của mục tiêu hoặc dùng tool để probe subdomain.                   | Brute-forcing, DNS zone transfer                        |
| `Passive Enumeration` | Thu thập thông tin về subdomain mà không tương tác trực tiếp với mục tiêu, dựa trên nguồn công khai. | Certificate Transparency (CT) logs, search engine query |

`Active enumeration` có thể đầy đủ hơn nhưng mang rủi ro bị phát hiện cao hơn. Ngược lại, `passive enumeration` kín đáo hơn nhưng có thể không tìm ra toàn bộ subdomain. Kết hợp cả hai kỹ thuật sẽ làm tăng đáng kể khả năng thu được danh sách subdomain đầy đủ hơn của mục tiêu, từ đó mở rộng hiểu biết của bạn về hiện diện trực tuyến và các lỗ hổng tiềm năng của họ.

#### Subdomain Brute-Forcing <a href="#subdomain-brute-forcing" id="subdomain-brute-forcing"></a>

Subdomain brute-forcing là một kỹ thuật chủ động trong web reconnaissance để tìm các subdomain không dễ lộ ra khi dùng passive method. Nó hoạt động bằng cách tạo có hệ thống nhiều tên subdomain khả dĩ và kiểm tra chúng với DNS server của mục tiêu để xem chúng có tồn tại hay không. Cách tiếp cận này có thể làm lộ hidden subdomain đang chứa thông tin giá trị, development server hoặc ứng dụng có lỗ hổng.

Một trong những tool linh hoạt nhất cho subdomain brute-forcing là `dnsenum`. Đây là một command-line tool mạnh, kết hợp nhiều kỹ thuật DNS enumeration, bao gồm brute-forcing dựa trên dictionary, để tìm subdomain liên quan đến mục tiêu.

Để dùng `dnsenum` cho subdomain brute-forcing, thông thường bạn sẽ cung cấp domain mục tiêu và một wordlist chứa các tên subdomain tiềm năng. Tool sẽ lần lượt query DNS server với từng subdomain có thể có và báo lại những subdomain tồn tại.

Ví dụ, lệnh sau sẽ thử brute-force subdomain của `example.com` bằng wordlist tên `subdomains.txt`:

```bash
dnsenum example.com -f subdomains.txt
```

```bash
canhieu@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```

#### Zone Transfers <a href="#zone-transfers" id="zone-transfers"></a>

DNS zone transfer, còn được gọi là request `AXFR` (Asynchronous Full Transfer), có thể là một mỏ vàng thông tin cho web reconnaissance. Zone transfer là cơ chế dùng để sao chép dữ liệu DNS giữa các server. Khi zone transfer thành công, nó cung cấp một bản sao đầy đủ của DNS zone file, vốn chứa rất nhiều chi tiết về domain mục tiêu.

Zone file này liệt kê toàn bộ subdomain của domain, IP address tương ứng, cấu hình mail server và nhiều DNS record khác. Với một reconnaissance expert, điều này giống như có được bản thiết kế của hạ tầng DNS của mục tiêu.

Để thử thực hiện zone transfer, bạn có thể dùng lệnh `dig` với tùy chọn `axfr`. Ví dụ, để yêu cầu zone transfer từ DNS server `ns1.example.com` cho domain `example.com`, bạn sẽ chạy:

```bash
dig @ns1.example.com example.com axfr
```

Tuy nhiên, zone transfer không phải lúc nào cũng được cho phép. Nhiều DNS server được cấu hình để chỉ cho phép zone transfer tới các authorized secondary server. Dù vậy, các server bị misconfigured có thể cho phép zone transfer từ bất kỳ nguồn nào và vô tình làm lộ thông tin nhạy cảm.



#### bo sung cach tim subdomain

```bash
canhieu@DESKTOP-DBGES7N:~$ sublist3r -d inlanefreight.com | grep inlanefreight.com
[-] Enumerating subdomains now for inlanefreight.com
Process DNSdumpster-8:
Traceback (most recent call last):
  File "/usr/lib/python3.12/multiprocessing/process.py", line 314, in _bootstrap
    self.run()
  File "/usr/lib/python3/dist-packages/sublist3r.py", line 269, in run
    domain_list = self.enumerate()
                  ^^^^^^^^^^^^^^^^
  File "/usr/lib/python3/dist-packages/sublist3r.py", line 649, in enumerate
    token = self.get_csrftoken(resp)
            ^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3/dist-packages/sublist3r.py", line 644, in get_csrftoken
    token = csrf_regex.findall(resp)[0]
            ~~~~~~~~~~~~~~~~~~~~~~~~^^^
IndexError: list index out of range
www.inlanefreight.com
blog.inlanefreight.com
my.inlanefreight.com
support.inlanefreight.com
```

#### Virtual Hosts <a href="#virtual-hosts" id="virtual-hosts"></a>

Virtual hosting là kỹ thuật cho phép nhiều website cùng chia sẻ một IP address. Mỗi website gắn với một hostname riêng, và hostname này được dùng để chuyển request đến đúng site. Đây có thể là một cách tiết kiệm chi phí để tổ chức host nhiều website trên một server, nhưng nó cũng tạo ra thách thức cho web reconnaissance.

Vì nhiều website dùng chung một IP address, chỉ scan IP thôi sẽ không cho bạn biết tất cả site đang được host. Bạn cần một tool có thể thử nhiều hostname khác nhau với IP address đó để xem hostname nào phản hồi.

Gobuster là một tool linh hoạt, có thể dùng cho nhiều dạng brute-forcing khác nhau, bao gồm virtual host discovery. Chế độ `vhost` của nó được thiết kế để enumerate virtual host bằng cách gửi request đến IP address mục tiêu với các hostname khác nhau. Nếu một virtual host được cấu hình cho một hostname cụ thể, Gobuster sẽ nhận được response từ web server.

Để dùng Gobuster brute-force virtual host, bạn cần một wordlist chứa các hostname tiềm năng. Đây là một ví dụ:

```bash
gobuster vhost -u http://192.0.2.1 -w hostnames.txt
```

Trong ví dụ này, `-u` chỉ định IP address mục tiêu và `-w` chỉ định file wordlist. Gobuster sẽ lần lượt thử từng hostname trong wordlist và báo lại những hostname tạo ra response hợp lệ từ web server.

#### Certificate Transparency (CT) Logs <a href="#certificate-transparency-ct-logs" id="certificate-transparency-ct-logs"></a>

Certificate Transparency (CT) logs là một nguồn dữ liệu rất giá trị về subdomain cho passive reconnaissance. Các log công khai này ghi lại SSL/TLS certificate đã được cấp cho domain và subdomain của chúng. Ban đầu, chúng là một biện pháp bảo mật để ngăn certificate giả mạo. Với reconnaissance, chúng cung cấp một cửa sổ để nhìn vào các subdomain có thể đã bị bỏ sót.

Website `crt.sh` cung cấp giao diện tìm kiếm cho CT logs. Để trích xuất subdomain hiệu quả từ `crt.sh` ngay trong terminal, bạn có thể dùng lệnh như sau:

```bash
curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | sort -u
```

Lệnh này lấy dữ liệu dạng JSON từ `crt.sh` cho `example.com`, trong đó ký tự `%` đóng vai trò wildcard. Sau đó, lệnh trích xuất domain name bằng `jq`, loại bỏ tiền tố wildcard (`*.`) bằng `sed`, rồi sắp xếp và loại bỏ bản ghi trùng lặp.

### Web Crawling <a href="#web-crawling" id="web-crawling"></a>

Web crawling là quá trình khám phá tự động cấu trúc của một website. Một web crawler, hay spider, sẽ lần lượt đi qua các web page bằng cách theo các link, mô phỏng hành vi duyệt web của người dùng. Quá trình này giúp map kiến trúc của site và thu thập thông tin có giá trị nằm trong các page.

Một file quan trọng định hướng cho web crawler là `robots.txt`. File này nằm ở root directory của website và quy định khu vực nào crawler không được phép truy cập. Phân tích `robots.txt` có thể làm lộ hidden directory hoặc khu vực nhạy cảm mà chủ sở hữu website không muốn search engine index.

`Scrapy` là một Python framework mạnh và hiệu quả cho các dự án web crawling và scraping ở quy mô lớn. Nó cung cấp cách tiếp cận có cấu trúc để định nghĩa crawling rule, trích xuất dữ liệu và xử lý nhiều output format khác nhau.

Đây là một ví dụ cơ bản về Scrapy spider để trích xuất link từ `example.com`:

```python
import scrapy

class ExampleSpider(scrapy.Spider):
    name = "example"
    start_urls = ['http://example.com/']

    def parse(self, response):
        for link in response.css('a::attr(href)').getall():
            if any(link.endswith(ext) for ext in self.interesting_extensions):
                yield {"file": link}
            elif not link.startswith("#") and not link.startswith("mailto:"):
                yield response.follow(link, callback=self.parse)
```

Sau khi chạy Scrapy spider, bạn sẽ có một file chứa dữ liệu đã scrape, ví dụ `example_data.json`. Bạn có thể phân tích kết quả này bằng các command-line tool quen thuộc. Ví dụ, để trích xuất toàn bộ link:

```bash
jq -r '.[] | select(.file != null) | .file' example_data.json | sort -u
```

Lệnh này dùng `jq` để trích xuất link, `awk` để tách file extension, `sort` để sắp xếp và `uniq -c` để đếm số lần xuất hiện. Bằng cách xem kỹ dữ liệu đã trích xuất, bạn có thể phát hiện pattern, anomaly hoặc file nhạy cảm đáng để điều tra sâu hơn.

### Search Engine Discovery <a href="#search-engine-discovery" id="search-engine-discovery"></a>

Tận dụng search engine cho reconnaissance nghĩa là dùng chỉ mục nội dung web rất lớn của chúng để tìm thông tin về mục tiêu. Đây là một kỹ thuật passive, thường được gọi là thu thập Open Source Intelligence (OSINT), và nó có thể mang lại insight giá trị mà không cần tương tác trực tiếp với hệ thống của mục tiêu.

Bằng cách dùng search operator nâng cao và các query chuyên biệt thường được gọi là `Google Dorks`, bạn có thể khoanh đúng những thông tin cụ thể đang bị chôn trong search result. Dưới đây là bảng một số search operator hữu ích cho web reconnaissance:

| Operator        | Mô tả                                            | Ví dụ                                |
| --------------- | ------------------------------------------------ | ------------------------------------ |
| `site:`         | Giới hạn search result trong một website cụ thể. | `site:example.com "password reset"`  |
| `inurl:`        | Tìm một term cụ thể trong URL của page.          | `inurl:admin login`                  |
| `filetype:`     | Giới hạn kết quả theo một loại file cụ thể.      | `filetype:pdf "confidential report"` |
| `intitle:`      | Tìm một term trong title của page.               | `intitle:"index of" /backup`         |
| `cache:`        | Hiển thị phiên bản đã cache của một webpage.     | `cache:example.com`                  |
| `"search term"` | Tìm chính xác cụm từ nằm trong dấu ngoặc kép.    | `"internal error" site:example.com`  |
| `OR`            | Kết hợp nhiều search term.                       | `inurl:admin OR inurl:login`         |
| `-`             | Loại trừ term cụ thể khỏi search result.         | `inurl:admin -intext:wordpress`      |

Bằng cách kết hợp sáng tạo các operator này và xây dựng query có chủ đích, bạn có thể tìm ra tài liệu nhạy cảm, directory bị lộ, login page và các thông tin giá trị khác có thể hỗ trợ cho hoạt động reconnaissance.

### Web Archives <a href="#web-archives" id="web-archives"></a>

Web archive là các kho lưu trữ số chứa snapshot của website theo thời gian, cung cấp hồ sơ lịch sử về quá trình thay đổi của chúng. Trong số đó, Wayback Machine là nguồn tài nguyên toàn diện và dễ tiếp cận nhất cho web reconnaissance.

Wayback Machine là một dự án của Internet Archive. Nó đã archive web trong hơn hai thập kỷ và lưu lại hàng tỷ web page từ khắp nơi trên thế giới. Kho dữ liệu lịch sử khổng lồ này có thể là nguồn tài nguyên vô giá cho security researcher và investigator.

| Feature                | Mô tả                                                                                        | Use Case in Reconnaissance                                                                      |
| ---------------------- | -------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `Historical Snapshots` | Xem các phiên bản cũ của website, bao gồm page, content và thay đổi về design.               | Xác định content hoặc functionality trước đây của website hiện không còn tồn tại.               |
| `Hidden Directories`   | Khám phá directory và file có thể đã bị xóa hoặc bị ẩn trong phiên bản hiện tại của website. | Tìm thông tin nhạy cảm hoặc backup đã từng vô tình để lộ ở các phiên bản trước.                 |
| `Content Changes`      | Theo dõi thay đổi trong content của website, gồm text, image và link.                        | Nhận diện pattern cập nhật content và đánh giá sự thay đổi của security posture theo thời gian. |

Bằng cách tận dụng Wayback Machine, bạn có thể có được góc nhìn lịch sử về hiện diện trực tuyến của mục tiêu và có thể phát hiện các lỗ hổng đã bị bỏ sót trong phiên bản website hiện tại.
