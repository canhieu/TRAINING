# Type\_Jugging



## ANALYZE

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

Tôi sẽ tiến hành thử nhập bừa 1 input bất kì để kiểm tra hành vi của trang web



<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

Thì ta thấy được nó yêu cầu format là md5 có 32 kí tự

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

Quay lại hint thì ta có được là magic hash

> A **magic hash** in the context of **MD5** (and other hashes) refers to a special value that, when compared in PHP using the loose comparison operator `==`, can cause unexpected results due to PHP's **type juggling**

```php
var_dump("0e12345" == "0e67890"); // true
```

## EXPLOIT

[https://github.com/spaze/hashes/blob/master/md5.md](https://github.com/spaze/hashes/blob/master/md5.md)



<figure><img src="../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>



## FLAG

```
FLAG{type_juggling_magic_hash_0e_1337}
```

