---
hidden: true
---

# ZC-1

<figure><img src="../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>

## ANALYZE



<figure><img src="../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

Mình thấy hệ thống gồm hai service trong `docker-compose`: `app1` (public, port `8000` mở ra host) và `app2` (internal, **không** map port) — `app1` dùng `STORAGE_URL=http://app2` và `app2` mount `./app2/flag.txt:/flag.txt:ro`.



### app1

<figure><img src="../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>









## EXPLOIT



