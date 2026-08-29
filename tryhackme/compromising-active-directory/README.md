---
hidden: true
---

# Compromising Active Directory

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

OSINT and Phishing\



Có  2 phương pháp phổ biến đẻ chiếm được cred AD là OSINT hoặc là Phishing

### OSINT&#x20;

osint được dùng để tìm kiếm thông tin public hoặc disclored , nó có thể xảy ra vì 1 vài lí do sau :&#x20;

* Users who ask questions on public forums such as [Stack Overflow(opens in new tab)](https://stackoverflow.com/) but disclose sensitive information such as their credentials in the question.
* Developers that upload scripts to services such as [Github(opens in new tab)](https://github.com/) with credentials hardcoded.
* Credentials being disclosed in past breaches since employees used their work accounts to sign up for other external websites. Websites such as [HaveIBeenPwned(opens in new tab)](https://haveibeenpwned.com/) and [DeHashed(opens in new tab)](https://www.dehashed.com/) provide excellent platforms to determine if someone's information, such as work email, was ever involved in a publicly known data breach.

Bằng cách sử dụng các kỹ thuật **OSINT**, ta có thể tìm thấy các thông tin đăng nhập bị lộ công khai.

Nếu may mắn tìm được credential, ta vẫn cần kiểm tra xem chúng còn hợp lệ hay không, vì thông tin từ OSINT có thể đã cũ.

tham khảo <[https://tryhackme.com/room/redteamrecon](https://tryhackme.com/room/redteamrecon)>

### Phising

**Phishing** là một phương pháp phổ biến để xâm nhập vào môi trường AD. Kẻ tấn công thường dụ người dùng nhập thông tin đăng nhập vào một trang web giả mạo, hoặc yêu cầu họ chạy một ứng dụng độc hại.

Ứng dụng đó có thể âm thầm cài **RAT (Remote Access Trojan)** ở nền. Vì RAT chạy dưới quyền của chính người dùng, kẻ tấn công có thể mạo danh tài khoản AD của người đó.

Vì vậy, phishing là một chủ đề rất quan trọng đối với cả **Red Team** và **Blue Team**: Red Team nghiên cứu cách tấn công, còn Blue Team tập trung phát hiện và phòng thủ.

tham khảo <[https://tryhackme.com/module/phishing](https://tryhackme.com/module/phishing)>

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## NTLM Authenticated Services

{% file src="../../.gitbook/assets/passwordsprayer-1647011410194.zip" %}



