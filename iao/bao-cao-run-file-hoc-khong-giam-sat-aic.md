# Báo Cáo run file học không giám sát AIC

## 01\_kmeans\_network\_traffic.py

đầu tiên thì ta tiến hành tạo dữ liệu giả với 3 loại trafic là normal/ddos/scan

sau đó ta tiến hành gắn nhãn cho 3 loại traffic đó&#x20;

tiếp đến nó sẽ tiến hành chuẩn hóa data theo dạng cột

```python
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

Tiếp đến sẽ tiến hành phân thành 3 cum với 42 lần chạy và số lần K-Means được chạy lại với **tâm cụm ban đầu khác nhau là 10 lần**

```python
kmeans = KMeans(n_clusters=3, random_state=42, n_init=10)
pred_labels = kmeans.fit_predict(X_scaled)
```

Và tiến hành đưa vào training

```python
"""
K-Means Clustering for Network Traffic Classification
=====================================================
Clusters network traffic flows into normal vs suspicious groups
based on packet size, duration, and byte count.
"""

import numpy as np
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
import warnings
warnings.filterwarnings("ignore")

# Simulated network traffic: [packets_per_sec, avg_packet_size, duration_sec, total_bytes]
np.random.seed(42)

normal_traffic = np.column_stack([
    np.random.normal(50, 15, 200),    # packets/sec
    np.random.normal(512, 100, 200),  # avg packet size
    np.random.normal(30, 10, 200),    # duration
    np.random.normal(15000, 3000, 200) # total bytes
])
ddos_traffic = np.column_stack([
    np.random.normal(5000, 500, 30),
    np.random.normal(64, 10, 30),
    np.random.normal(2, 1, 30),
    np.random.normal(320000, 50000, 30)
])
scan_traffic = np.column_stack([
    np.random.normal(200, 50, 20),
    np.random.normal(40, 5, 20),
    np.random.normal(0.5, 0.2, 20),
    np.random.normal(8000, 1000, 20)
])

X = np.vstack([normal_traffic, ddos_traffic, scan_traffic])
true_labels = np.array([0]*200 + [1]*30 + [2]*20)

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

kmeans = KMeans(n_clusters=3, random_state=42, n_init=10)
pred_labels = kmeans.fit_predict(X_scaled)

print("K-Means Clustering — Network Traffic Classification")
print("=" * 55)
print(f"Samples: {len(X)} (200 normal, 30 DDoS, 20 port-scan)")
print(f"Inertia: {kmeans.inertia_:.2f}")
print(f"\nCluster distribution: {np.bincount(pred_labels)}")
print(f"Cluster centers (scaled):\n{kmeans.cluster_centers_.round(2)}")

# Check how well clusters align with true labels
from sklearn.metrics import adjusted_rand_score, silhouette_score
print(f"\nAdjusted Rand Index: {adjusted_rand_score(true_labels, pred_labels):.3f}")
print(f"Silhouette Score:    {silhouette_score(X_scaled, pred_labels):.3f}")
```

kết quả đầu ra ta có được là

<figure><img src="../.gitbook/assets/image (832).png" alt=""><figcaption></figcaption></figure>

Phần cluster distribution: \[22 30 198] cho biết K-Means chia dữ liệu thành 3 cụm với số lượng lần lượt là 22 mẫu, 30 mẫu và 198 mẫu. Có thể thấy một cụm rất lớn gần 200 mẫu, nhiều khả năng là nhóm normal. Hai cụm nhỏ hơn có thể tương ứng với DDoS và port-scan.

Phần cluster centers (scaled) là tọa độ tâm của từng cụm sau khi dữ liệu đã được chuẩn hóa. Các giá trị này cho biết đặc trưng trung bình của mỗi cụm, nhưng vì dữ liệu đã scaled nên không nhìn trực tiếp theo đơn vị gốc được.

Adjusted Rand Index = 0.969 là điểm đánh giá mức độ phân cụm đúng so với nhãn thật. Giá trị này rất gần 1, nghĩa là mô hình phân cụm rất tốt và gần khớp với nhãn ban đầu.

Silhouette Score = 0.693 cho thấy các cụm được tách khá rõ. Điểm này càng gần 1 thì các cụm càng tách biệt tốt. Với giá trị 0.693, có thể xem kết quả phân cụm là khá ổn.





## 02\_dbscan\_intrusion\_detection.py

đầu tiên thì ta tiến hành tạo dữ liệu giả với 3 loại traffic là normal, brute-force và exfiltration

normal là các phiên SSH bình thường, brute-force là các lần thử đăng nhập sai nhiều lần, còn exfiltration là hành vi gửi dữ liệu ra ngoài với dung lượng lớn

sau đó ta gộp 3 loại dữ liệu này lại thành một tập dữ liệu chung và tạo nhãn thật để đối chiếu kết quả

```python
X = np.vstack([normal, bruteforce, exfil])true_labels = np.array(["normal"]*300 + ["bruteforce"]*15 + ["exfiltration"]*10)
```

tiếp đến ta tiến hành chuẩn hóa dữ liệu theo từng cột để các đặc trưng như thời gian kết nối, bytes gửi, bytes nhận và số lần login lỗi không bị lệch thang đo

```python
scaler = StandardScaler()X_scaled = scaler.fit_transform(X)
```

sau đó dùng DBSCAN để phân cụm dữ liệu dựa trên mật độ, với eps là khoảng cách tìm điểm lân cận và min\_samples là số điểm tối thiểu để tạo thành một cụm

```python
db = DBSCAN(eps=0.8, min_samples=10)pred = db.fit_predict(X_scaled)
```

DBSCAN không cần chỉ định trước số cụm, mô hình sẽ tự tìm các cụm trong dữ liệu. Những điểm không thuộc cụm nào sẽ được gán nhãn -1 và được xem là noise hoặc anomaly

tiếp theo ta đếm số cụm mà DBSCAN tìm được và số điểm bị xem là bất thường

```python
n_clusters = len(set(pred)) - (1 if -1 in pred else 0)n_noise = np.sum(pred == -1)
```

cuối cùng ta kiểm tra trong các điểm bị đánh dấu là noise có bao nhiêu điểm thuộc normal, brute-force và exfiltration

```python
noise_mask = pred == -1
```

```python
"""
DBSCAN for Intrusion Detection
===============================
Detects anomalous network connections as noise points using
density-based clustering — no need to predefine cluster count.
"""

import numpy as np
from sklearn.cluster import DBSCAN
from sklearn.preprocessing import StandardScaler

np.random.seed(42)

# Normal SSH sessions: [connection_duration, bytes_sent, bytes_recv, failed_logins]
normal = np.column_stack([
    np.random.normal(120, 30, 300),
    np.random.normal(5000, 1000, 300),
    np.random.normal(8000, 2000, 300),
    np.random.poisson(0, 300)
])

# Brute-force SSH attempts
bruteforce = np.column_stack([
    np.random.normal(5, 2, 15),
    np.random.normal(200, 50, 15),
    np.random.normal(100, 30, 15),
    np.random.poisson(50, 15)
])

# Data exfiltration
exfil = np.column_stack([
    np.random.normal(3600, 600, 10),
    np.random.normal(500000, 100000, 10),
    np.random.normal(1000, 200, 10),
    np.zeros(10)
])

X = np.vstack([normal, bruteforce, exfil])
true_labels = np.array(["normal"]*300 + ["bruteforce"]*15 + ["exfiltration"]*10)

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

db = DBSCAN(eps=0.8, min_samples=10)
pred = db.fit_predict(X_scaled)

n_clusters = len(set(pred)) - (1 if -1 in pred else 0)
n_noise = np.sum(pred == -1)

print("DBSCAN — Intrusion Detection")
print("=" * 45)
print(f"Samples: {len(X)} (300 normal, 15 brute-force, 10 exfiltration)")
print(f"Clusters found: {n_clusters}")
print(f"Noise points (anomalies): {n_noise}")

# Check what was flagged as noise
noise_mask = pred == -1
print(f"\nNoise breakdown:")
print(f"  Normal flagged as noise:       {np.sum(noise_mask[:300])}")
print(f"  Brute-force flagged as noise:  {np.sum(noise_mask[300:315])}")
print(f"  Exfiltration flagged as noise: {np.sum(noise_mask[315:])}")

```

Và dựa theo kết quả này ta có được thông tin về Noise breakdown và số cụm tìm được

<figure><img src="../.gitbook/assets/image (833).png" alt=""><figcaption></figcaption></figure>





## 04\_pca\_network\_features.py

### SRC Code : &#x20;

— Unsupervised

```python
"""
PCA for Dimensionality Reduction of Network Features
=====================================================
Reduces high-dimensional network flow features to 2D for
visualization while preserving attack-vs-normal separation.
"""
import numpy as np
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

np.random.seed(42)

# 12-feature network flow data: [src_bytes, dst_bytes, duration, packets,
#   src_port, dst_port, protocol, tcp_flags, ttl, window_size, urg_count, wrong_fragment]
n_normal, n_attack = 400, 50

normal = np.column_stack([
    np.random.normal(5000, 1500, n_normal),
    np.random.normal(8000, 2000, n_normal),
    np.random.normal(60, 20, n_normal),
    np.random.normal(100, 30, n_normal),
    np.random.randint(1024, 65535, n_normal),
    np.random.choice([80, 443, 22, 53], n_normal),
    np.random.choice([6, 17], n_normal),  # TCP/UDP
    np.random.randint(0, 4, n_normal),
    np.random.normal(64, 5, n_normal),
    np.random.normal(65535, 100, n_normal),
    np.zeros(n_normal),
    np.zeros(n_normal),
])

attack = np.column_stack([
    np.random.normal(100, 50, n_attack),
    np.random.normal(500000, 80000, n_attack),
    np.random.normal(5, 2, n_attack),
    np.random.normal(2000, 500, n_attack),
    np.random.randint(1, 1024, n_attack),
    np.random.choice([23, 3389, 445], n_attack),
    np.full(n_attack, 6),
    np.random.randint(10, 20, n_attack),
    np.random.normal(128, 10, n_attack),
    np.random.normal(1024, 100, n_attack),
    np.random.poisson(5, n_attack),
    np.random.poisson(3, n_attack),
])

X = np.vstack([normal, attack])
labels = np.array(["normal"]*n_normal + ["attack"]*n_attack)

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

pca = PCA(n_components=2)
X_pca = pca.fit_transform(X_scaled)

print("PCA — Network Feature Dimensionality Reduction")
print("=" * 50)
print(f"Original features: {X.shape[1]}")
print(f"Reduced to:        {X_pca.shape[1]} components")
print(f"Explained variance: {pca.explained_variance_ratio_.round(3)}")
print(f"Total explained:    {pca.explained_variance_ratio_.sum():.1%}")
print(f"\nPCA component loadings (top 3 features per component):")

feature_names = ["src_bytes", "dst_bytes", "duration", "packets",
                 "src_port", "dst_port", "protocol", "tcp_flags",
                 "ttl", "window_size", "urg_count", "wrong_fragment"]

for i, comp in enumerate(pca.components_):
    top_idx = np.argsort(np.abs(comp))[-3:][::-1]
    top_feats = [(feature_names[j], comp[j]) for j in top_idx]
    print(f"  PC{i+1}: {top_feats}")

print(f"\nNormal centroid in PCA space:  {X_pca[:n_normal].mean(axis=0).round(3)}")
print(f"Attack centroid in PCA space:  {X_pca[n_normal:].mean(axis=0).round(3)}")

```

### Giải thích :&#x20;

Phần đầu tiên sẽ sử dụng thằng numpy để có thể tạo dữ liệu giả cho quá trình training





## 06\_GMM

```python
"""
Gaussian Mixture Models for User Behavior Profiling
====================================================
Models normal user behavior as a mixture of Gaussians;
detects compromised accounts as low-probability events.
"""

import numpy as np
from sklearn.mixture import GaussianMixture
from sklearn.preprocessing import StandardScaler

np.random.seed(42)

# User behavior: [logins_per_day, avg_session_min, files_accessed, bytes_downloaded, login_hour]
# Group A: office workers (9-5)
office = np.column_stack([
    np.random.normal(3, 1, 200),
    np.random.normal(120, 30, 200),
    np.random.normal(20, 5, 200),
    np.random.normal(50000, 10000, 200),
    np.random.normal(10, 2, 200),
])

# Group B: night-shift admins
admins = np.column_stack([
    np.random.normal(5, 1.5, 80),
    np.random.normal(240, 60, 80),
    np.random.normal(50, 15, 80),
    np.random.normal(200000, 50000, 80),
    np.random.normal(22, 2, 80),
])

# Compromised accounts (anomalous)
compromised = np.column_stack([
    np.random.normal(30, 5, 15),
    np.random.normal(10, 3, 15),
    np.random.normal(500, 50, 15),
    np.random.normal(5000000, 500000, 15),
    np.random.uniform(0, 24, 15),
])

X_train = np.vstack([office, admins])
X_test = np.vstack([office[:50], admins[:20], compromised])
test_labels = ["normal"]*70 + ["compromised"]*15

# CHUẨN HÓA DATA
scaler = StandardScaler()
X_train_s = scaler.fit_transform(X_train)
X_test_s = scaler.transform(X_test)

gmm = GaussianMixture(n_components=2, covariance_type="full", random_state=42)
gmm.fit(X_train_s)

log_probs = gmm.score_samples(X_test_s)
threshold = np.percentile(gmm.score_samples(X_train_s), 5)

predictions = ["anomaly" if lp < threshold else "normal" for lp in log_probs]

print("GMM — User Behavior Profiling")
print("=" * 45)
print(f"Training samples: {len(X_train)} (200 office + 80 admin)")
print(f"Test samples: {len(X_test)} (50 office + 20 admin + 15 compromised)")
print(f"GMM components: {gmm.n_components}")
print(f"Log-likelihood threshold (5th pct): {threshold:.2f}")
print(f"\nResults:")
print(f"  Normal test  — avg log-prob: {log_probs[:70].mean():.2f}")
print(f"  Compromised  — avg log-prob: {log_probs[70:].mean():.2f}")

tp = sum(1 for i in range(70, 85) if predictions[i] == "anomaly")
fp = sum(1 for i in range(0, 70) if predictions[i] == "anomaly")
print(f"\n  Compromised detected: {tp}/15")
print(f"  False positives:      {fp}/70")

```

Thuật toán này sử dụng Normal Distribution

<figure><img src="../.gitbook/assets/image (851).png" alt=""><figcaption></figcaption></figure>

và bài này sử dụng co về 2 cụm nên sẽ sử dụng công thức 2 chiều&#x20;

<figure><img src="../.gitbook/assets/image (852).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (854).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (855).png" alt=""><figcaption></figcaption></figure>



```
log_probs = gmm.score_samples(X_test_s)
```

Dòng này tính **log-likelihood** cho từng điểm trong tập test.

Nói đơn giản:

* Điểm nào có `log_probs` cao => mô hình thấy điểm đó “quen thuộc”, giống dữ liệu train.
* Điểm nào có `log_probs` thấp => mô hình thấy điểm đó “lạ”, có khả năng là bất thường.

```
threshold = np.percentile(gmm.score_samples(X_train_s), 5)
```

Dòng này chọn ngưỡng phát hiện bất thường dựa trên tập train.

Cụ thể, nó lấy **percentile thứ 5** của log-likelihood trên dữ liệu train.

Nghĩa là: trong tập train, 5% điểm có xác suất thấp nhất sẽ nằm dưới ngưỡng này.

Ví dụ:

```
threshold = -8.5
```

Thì các điểm test có `log_prob < -8.5` sẽ bị xem là bất thường.

```
predictions = ["anomaly" if lp < threshold else "normal" for lp in log_probs]
```

Dòng này gán nhãn cho từng điểm test:

* Nếu `lp < threshold` => `"anomaly"`
* Nếu `lp >= threshold` => `"normal"`

Tức là điểm nào có xác suất xuất hiện quá thấp so với dữ liệu train thì bị xem là bất thường.



<figure><img src="../.gitbook/assets/image (853).png" alt=""><figcaption></figcaption></figure>



## 09\_lof\_insider\_threat.py

Phần đầu là gọi thư viện  và tạo data thì ko có quá nhiều thứ để nói nên ta sẽ skip và đi thẳng vào phần thuật toán luôn.

<[https://fmit.vn/tu-dien-quan-ly/lof-local-outlier-factor-la-gi](https://fmit.vn/tu-dien-quan-ly/lof-local-outlier-factor-la-gi)>

<figure><img src="../.gitbook/assets/image (868).png" alt=""><figcaption></figcaption></figure>

Ta sẽ tập trung chính vào đoạn thuật toán&#x20;

```python
contamination=0.06 # giả lập là có 6% oulier
```

```python
lof = LocalOutlierFactor(n_neighbors=20, contamination=0.06)
y_pred = lof.fit_predict(X_scaled) ## tiến hành train 
scores = lof.negative_outlier_factor_  ## tính điểm
```

Thì thuật toán này nó sẽ lấy từng điểm và xét 20 điểm gần nó nhất , và nó sẽ lấy khoảng cách với điểm thứ 20 làm mốc để tính được giá trị của K-instance

<figure><img src="../.gitbook/assets/image (869).png" alt=""><figcaption></figcaption></figure>

Và sau đó sẽ tính điểm bằng các công thức bên dưới (only yham khảo)

<figure><img src="../.gitbook/assets/image (876).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (875).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (871).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (870).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (873).png" alt=""><figcaption></figcaption></figure>



và đây là hình minh họa cho việc mỗi điểm tự thực hiện tìm bán kính để xét , nếu nó có điểm  <= 1 thì nó sẽ nằm trong cụm , và ngoài đó sẽ là điểm oulier

<figure><img src="../.gitbook/assets/image (874).png" alt=""><figcaption></figcaption></figure>





```python
"""
Local Outlier Factor for Insider Threat Detection
==================================================
Detects anomalous employee access patterns by measuring
local density deviation of each data point.
"""

import numpy as np
from sklearn.neighbors import LocalOutlierFactor
from sklearn.preprocessing import StandardScaler

np.random.seed(42)

# Employee access patterns: [files_accessed, after_hours_logins,
#   usb_events, email_attachments_sent, print_jobs, vpn_sessions]
normal_employees = np.column_stack([
    np.random.normal(20, 8, 300),
    np.random.poisson(1, 300),
    np.random.poisson(2, 300),
    np.random.normal(5, 2, 300),
    np.random.poisson(3, 300),
    np.random.poisson(1, 300),
])

# Insider threat Type 1: data theft (many files, USB, emails)
data_theft = np.column_stack([
    np.random.normal(200, 30, 10),
    np.random.poisson(8, 10),
    np.random.poisson(20, 10),
    np.random.normal(50, 10, 10),
    np.random.poisson(15, 10),
    np.random.poisson(5, 10),
])

# Insider threat Type 2: sabotage (after-hours, unusual patterns)
sabotage = np.column_stack([
    np.random.normal(100, 20, 8),
    np.random.poisson(15, 8),
    np.random.poisson(0, 8),
    np.random.normal(1, 0.5, 8),
    np.random.poisson(0, 8),
    np.random.poisson(10, 8),
])

X = np.vstack([normal_employees, data_theft, sabotage])
labels = (["normal"]*300 + ["data_theft"]*10 + ["sabotage"]*8)

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

lof = LocalOutlierFactor(n_neighbors=20, contamination=0.06)
y_pred = lof.fit_predict(X_scaled)
scores = lof.negative_outlier_factor_

print("Local Outlier Factor — Insider Threat Detection")
print("=" * 50)
print(f"Samples: {len(X)} (300 normal, 10 data-theft, 8 sabotage)")
print(f"LOF neighbors: 20, contamination: 6%")
print(f"\nDetection Results:")

outlier_mask = y_pred == -1
print(f"  Normal flagged as outlier:      {np.sum(outlier_mask[:300])}/300")
print(f"  Data theft flagged as outlier:  {np.sum(outlier_mask[300:310])}/10")
print(f"  Sabotage flagged as outlier:    {np.sum(outlier_mask[310:])}/8")

print(f"\nLOF Scores (more negative = more anomalous):")
print(f"  Normal employees avg:  {scores[:300].mean():.3f}")
print(f"  Data theft avg:        {scores[300:310].mean():.3f}")
print(f"  Sabotage avg:          {scores[310:].mean():.3f}")

print(f"\nTop 5 most anomalous sample indices:")
top5 = np.argsort(scores)[:5]
for idx in top5:
    label = labels[idx]
    print(f"  Sample {idx} ({label}): LOF score = {scores[idx]:.3f}")
```



<figure><img src="../.gitbook/assets/image (867).png" alt=""><figcaption></figcaption></figure>

Kết quả cho thấy mô hình Local Outlier Factor (LOF) hoạt động rất hiệu quả trong việc phát hiện hành vi bất thường liên quan đến insider threat. Trong tổng số 318 mẫu, mô hình phát hiện đúng toàn bộ 10 trường hợp đánh cắp dữ liệu và 8 trường hợp phá hoại, trong khi chỉ gắn nhãn sai 2/300 nhân viên bình thường là ngoại lệ. Điểm LOF trung bình của nhóm bình thường là -1.068, cao hơn nhiều so với nhóm data theft (-5.446) và sabotage (-5.019), cho thấy các hành vi nguy hiểm có mức độ bất thường rõ rệt. Danh sách 5 mẫu bất thường nhất cũng chủ yếu thuộc nhóm data theft và sabotage, chứng minh LOF có khả năng phân biệt tốt giữa hành vi bình thường và hành vi rủi ro trong hệ thống.

