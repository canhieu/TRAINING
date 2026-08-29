# Unstoppable force meets immovable object 2

<figure><img src="../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

<figure><img src="../.gitbook/assets/image (379).png" alt=""><figcaption></figcaption></figure>

When accessing the lab, we encounter a login form. Nothing seems particularly remarkable at first glance, so we move on to review the source code.

```python
from flask import Flask, redirect, render_template, request, url_for
from secret import FLAG

app = Flask(__name__)


def complex_custom_hash(data_string):
    if not isinstance(data_string, str):
        raise TypeError("Input must be a string.")

    data_bytes = data_string.encode("utf-8")

    P = 2**61 - 1
    B = 101

    hash_val = 0

    for byte_val in data_bytes:
        hash_val = (hash_val * B + byte_val) % P

    length_mix = (len(data_bytes) * 123456789) % P
    hash_val = (hash_val + length_mix) % P

    chunk_size = 40
    num_chunks = 64 // chunk_size

    folded_hash = 0
    temp_hash = hash_val

    for _ in range(num_chunks):
        chunk = temp_hash & ((1 << chunk_size) - 1)
        folded_hash = (folded_hash + chunk) % (1 << chunk_size)
        temp_hash >>= chunk_size

    final_small_hash = folded_hash

    scrambled_hash = 0
    for _ in range(3):
        scrambled_hash = (
            final_small_hash ^ (final_small_hash >> 7) ^ (final_small_hash << 3)
        ) & ((1 << chunk_size) - 1)
        final_small_hash = scrambled_hash

    return f"{scrambled_hash:04x}"


@app.route("/", methods=["GET", "POST"])
def home():
    if request.method == "POST":
        username = request.form["username"]
        password = request.form["password"]
        if username != password and complex_custom_hash(
            password
        ) == complex_custom_hash(username):
            return FLAG
        return redirect(url_for("home"))

    url_for("static", filename="style.css")
    return render_template("index.html")


if __name__ == "__main__":
    app.run(debug=False, host="0.0.0.0")
```

First,

```python
@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        username = request.form["username"]
        password = request.form["password"]

        if username != password and complex_custom_hash(password) == complex_custom_hash(username):
            return render_template("flag.html", flag=SECRET_FLAG)
```

#### The Conditions for Successful Authentication Are:

1. `username != password`
2. `hash(username) == hash(password)` using the `complex_custom_hash` function.

If both conditions are met, it returns `flag.html`.

Next is the hash function:

```python
def complex_custom_hash(s: str) -> str:
    seed = 0x1337
    scrambled_hash = seed
    for c in s:
        scrambled_hash ^= ord(c)
        scrambled_hash = (scrambled_hash << 5 | scrambled_hash >> 3) & 0xFFFFFFFF
        scrambled_hash += 0x4242
        scrambled_hash ^= 0xDEADBEEF
    return f"{scrambled_hash:04x}"
```

* This function uses several XOR operations, bit shifts (left/right), and fixed constants.
*   Finally, it returns only **4 hex characters**:

    ```python
    return f"{scrambled_hash:04x}"
    ```

    → Which means only `2^16 = 65536` possible values ⇒ **Very weak hash, highly prone to collisions**.

***

## EXPLOIT

Now, we will proceed to find two different strings that produce the same `complex_custom_hash`.

```python
import random, string
from main import complex_custom_hash   # provided already

seen = {}

while True:
    s = ''.join(random.choices(string.ascii_letters + string.digits, k=6))
    h = complex_custom_hash(s)
    if h in seen and seen[h] != s:
        print(f"[+] Found collision:\nUsername: {seen[h]}\nPassword: {s}\nHash: {h}")
        break
    seen[h] = s
```

And the result is:

```
[+] Found collision:
Username: fCBs2h
Password: 108syk
Hash: 57b4ff9a58
```

In the final step, we simply log in using the discovered `username` and `password`.

<figure><img src="../.gitbook/assets/image (380).png" alt=""><figcaption></figcaption></figure>

***

## FLAG

```
Blitz{b1r7hd4y_p4r4d0x_3475_5h177y_h45h35_l1k3_7h15}
```
