---
hidden: true
---

# Web Service & API Attacks

## &#x20;Introduction to Web Services and APIs

\
Giới thiệu về Web Services và APIs

Theo **W3C**: _Web services cung cấp phương tiện chuẩn hóa để các ứng dụng phần mềm khác nhau tương tác, chạy trên nhiều nền tảng và/hoặc frameworks._ Đặc điểm: **interoperability**, **extensibility**, và mô tả có thể xử lý bằng máy nhờ **XML**.

Web services cho phép các ứng dụng giao tiếp với nhau, ngay cả khi rất khác biệt. Ví dụ:

* Ứng dụng **Java** chạy trên Linux với **Oracle database**
* Ứng dụng **C++** chạy trên Windows với **SQL Server database**

→ Hai ứng dụng này có thể trao đổi dữ liệu qua Internet nhờ web services.

**API (Application Programming Interface)**: tập hợp quy tắc cho phép truyền dữ liệu giữa phần mềm. **Technical specification** của API quy định cách dữ liệu được trao đổi.

Ví dụ: Một phần mềm cần truy cập giá vé cho ngày cụ thể → nó gọi đến API của phần mềm khác → phần mềm kia trả về dữ liệu/chức năng yêu cầu. **API** chính là interface thực hiện việc trao đổi này.

***

### Web Service vs. API

* Web service là **một loại API**; nhưng không phải API nào cũng là web service.
* Web service cần **network**, API có thể hoạt động offline.
* Web service thường không mở cho developer bên ngoài; nhiều API thì mở.
* Web service thường dùng **SOAP**; API có thể dùng **XML-RPC, JSON-RPC, SOAP, REST**.
* Web service thường dùng **XML**; API có thể dùng nhiều định dạng, phổ biến nhất là **JSON**.

***

### Các công nghệ/tiếp cận Web Service

#### 1. XML-RPC

* Dùng **XML** để encode/decode **remote procedure call (RPC)** và tham số.
* **HTTP** thường là transport.
* Payload: `<methodCall>` gồm `<methodName>` (method cần gọi) và `<params>` (tham số).

**Ví dụ:**

```http
--> POST /RPC2 HTTP/1.0
User-Agent: Frontier/5.1.2 (WinNT)
Host: betty.userland.com
Content-Type: text/xml
Content-length: 181

<?xml version="1.0"?>
<methodCall>
  <methodName>examples.getStateName</methodName>
  <params>
     <param>
       <value><i4>41</i4></value>
     </param>
  </params>
</methodCall>

<-- HTTP/1.1 200 OK
Connection: close
Content-Length: 158
Content-Type: text/xml
Date: Fri, 17 Jul 1998 19:55:08 GMT
Server: UserLand Frontier/5.1.2-WinNT

<?xml version="1.0"?>
<methodResponse>
   <params>
      <param>
        <value><string>South Dakota</string></value>
      </param>
   </params>
</methodResponse>
```

***

#### 2. JSON-RPC

* Dùng **JSON** để gọi chức năng.
* **HTTP** thường là transport.
* Request object gồm:
  * **method**: tên method
  * **params**: danh sách arguments
  * **id**: identifier client đặt, server phải trả về

**Ví dụ:**

```http
--> POST /ENDPOINT HTTP/1.1
Host: ...
Content-Type: application/json-rpc
Content-Length: ...

{"method": "sum", "params": {"a":3, "b":4}, "id":0}

<-- HTTP/1.1 200 OK
...
Content-Type: application/json-rpc

{"result": 7, "error": null, "id": 0}
```

***

#### 3. SOAP (Simple Object Access Protocol)

* Dùng **XML**, nhiều chức năng hơn XML-RPC.
* Định nghĩa:
  * **soap:Envelope** (bắt buộc) – phân biệt SOAP với XML thường
  * **soap:Header** (tùy chọn) – mở rộng SOAP
  * **soap:Body** (bắt buộc) – chứa procedure, parameters, data
  * **soap:Fault** (tùy chọn) – báo lỗi khi API call thất bại
* **WSDL** (tùy chọn): mô tả cách dùng SOAP service
* Transport: HTTP và các giao thức cấp thấp khác

**Ví dụ SOAP message:**

```http
--> POST /Quotation HTTP/1.0
Host: www.xyz.org
Content-Type: text/xml; charset = utf-8
Content-Length: nnn

<?xml version = "1.0"?>
<SOAP-ENV:Envelope
  xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
  SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

  <SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotations">
     <m:GetQuotation>
       <m:QuotationsName>MiscroSoft</m:QuotationsName>
    </m:GetQuotation>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>

<-- HTTP/1.0 200 OK
Content-Type: text/xml; charset = utf-8
Content-Length: nnn

<?xml version = "1.0"?>
<SOAP-ENV:Envelope
 xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
 SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

<SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotation">
    <m:GetQuotationResponse>
       <m:Quotation>Here is the quotation</m:Quotation>
   </m:GetQuotationResponse>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

***

#### 4. WS-BPEL (Web Services Business Process Execution Language)

* Bản mở rộng của SOAP web service.
* Thêm chức năng mô tả và thực thi **business processes**.
* Về cơ bản tương tự SOAP nên thường không được học sâu ở mức nhập môn.

***

#### 5. RESTful (Representational State Transfer)

* Thường dùng **XML** hoặc **JSON**.
* **WSDL** có thể hỗ trợ nhưng ít phổ biến.
* **HTTP** là transport chính.
* Dùng các **HTTP verbs** (GET, POST, PUT, DELETE) để thao tác với resources.

**Ví dụ:**

```http
--> POST /api/2.2/auth/signin HTTP/1.1
HOST: my-server
Content-Type:text/xml

<tsRequest>
  <credentials name="administrator" password="passw0rd">
    <site contentUrl="" />
  </credentials>
</tsRequest>
```

```http
--> POST /api/2.2/auth/signin HTTP/1.1
HOST: my-server
Content-Type:application/json
Accept:application/json

{
 "credentials": {
   "name": "administrator",
  "password": "passw0rd",
  "site": {
    "contentUrl": ""
   }
  }
}
```

***

## &#x20;Web Services Description Language (WSDL)



WSDL là viết tắt của **Web Service Description Language**. WSDL là một tập tin dựa trên **XML** do web services công bố, cung cấp cho client thông tin về các dịch vụ/phương thức được cung cấp, bao gồm nơi chúng nằm và quy ước gọi phương thức (**method-calling convention**).

Một file WSDL của web service không nhất thiết phải luôn luôn truy cập được công khai. Developer có thể không muốn công bố WSDL hoặc có thể đặt nó ở một vị trí ít phổ biến (một cách tiếp cận _security through obscurity_). Trong trường hợp sau, việc fuzzing directory/parameter có thể tiết lộ vị trí và nội dung của file WSDL.

Tiếp tục tới cuối phần này và click “Click here to spawn the target system!” hoặc icon Reset Target. Dùng Pwnbox được cung cấp hoặc VM cục bộ cùng VPN key để truy cập dịch vụ mục tiêu và làm theo hướng dẫn.

Giả sử chúng ta đang đánh giá một SOAP service nằm tại `http://<TARGET IP>:3002`. Chúng ta chưa được biết về file WSDL.

Bắt đầu bằng việc thực hiện directory fuzzing cơ bản với web service.

```
  Web Services Description Language (WSDL)
0xlc13n@htb[/htb]$ dirb http://<TARGET IP>:3002

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Fri Mar 25 11:53:09 2022
URL_BASE: http://<TARGET IP>:3002/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://<TARGET IP>:3002/ ----
+ http://<TARGET IP>:3002/wsdl (CODE:200|SIZE:0)                            

-----------------
END_TIME: Fri Mar 25 11:53:24 2022
DOWNLOADED: 4612 - FOUND: 1
```

Có vẻ như `http://<TARGET IP>:3002/wsdl` tồn tại. Ta kiểm tra nội dung như sau.

```
  Web Services Description Language (WSDL)
0xlc13n@htb[/htb]$ curl http://<TARGET IP>:3002/wsdl 
The response is empty! Maybe there is a parameter that will provide us with access to the SOAP web service's WSDL file. Let us perform parameter fuzzing using ffuf and the burp-parameter-names.txt list, as follows. -fs 0 filters out empty responses (size = 0) and -mc 200 matches HTTP 200 responses.
```

Thực hiện parameter fuzzing bằng `ffuf` với wordlist `burp-parameter-names.txt`. Tham số `-fs 0` lọc bỏ response rỗng và `-mc 200` chỉ lấy mã 200.

```
  Web Services Description Language (WSDL)
0xlc13n@htb[/htb]$ ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -u 'http://<TARGET IP>:3002/wsdl?FUZZ' -fs 0 -mc 200

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://<TARGET IP>:3002/wsdl?FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
 :: Filter           : Response size: 0
________________________________________________

:: Progress: [40/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error
:: Progress: [537/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Erro
wsdl [Status: 200, Size: 4461, Words: 967, Lines: 186]
:: Progress: [982/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Erro:: 
Progress: [1153/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err::
Progress: [1780/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2461/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2588/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Err:: 
Progress: [2588/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```

Kết quả cho thấy `wsdl` là một parameter hợp lệ. Bây giờ ta gửi request tới `http://<TARGET IP>:3002/wsdl?wsdl`.

```
  Web Services Description Language (WSDL)
0xlc13n@htb[/htb]$ curl http://<TARGET IP>:3002/wsdl?wsdl 

<?xml version="1.0" encoding="UTF-8"?>
<wsdl:definitions targetNamespace="http://tempuri.org/"
	xmlns:s="http://www.w3.org/2001/XMLSchema"
	xmlns:soap12="http://schemas.xmlsoap.org/wsdl/soap12/"
	xmlns:http="http://schemas.xmlsoap.org/wsdl/http/"
	xmlns:mime="http://schemas.xmlsoap.org/wsdl/mime/"
	xmlns:tns="http://tempuri.org/"
	xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
	xmlns:tm="http://microsoft.com/wsdl/mime/textMatching/"
	xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"
	xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/">
	<wsdl:types>
		<s:schema elementFormDefault="qualified" targetNamespace="http://tempuri.org/">
			<s:element name="LoginRequest">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="1" name="username" type="s:string"/>
						<s:element minOccurs="1" maxOccurs="1" name="password" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="LoginResponse">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="ExecuteCommandRequest">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="1" name="cmd" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
			<s:element name="ExecuteCommandResponse">
				<s:complexType>
					<s:sequence>
						<s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
					</s:sequence>
				</s:complexType>
			</s:element>
		</s:schema>
	</wsdl:types>
	<!-- Login Messages -->
	<wsdl:message name="LoginSoapIn">
		<wsdl:part name="parameters" element="tns:LoginRequest"/>
	</wsdl:message>
	<wsdl:message name="LoginSoapOut">
		<wsdl:part name="parameters" element="tns:LoginResponse"/>
	</wsdl:message>
	<!-- ExecuteCommand Messages -->
	<wsdl:message name="ExecuteCommandSoapIn">
		<wsdl:part name="parameters" element="tns:ExecuteCommandRequest"/>
	</wsdl:message>
	<wsdl:message name="ExecuteCommandSoapOut">
		<wsdl:part name="parameters" element="tns:ExecuteCommandResponse"/>
	</wsdl:message>
	<wsdl:portType name="HacktheBoxSoapPort">
		<!-- Login Operaion | PORT -->
		<wsdl:operation name="Login">
			<wsdl:input message="tns:LoginSoapIn"/>
			<wsdl:output message="tns:LoginSoapOut"/>
		</wsdl:operation>
		<!-- ExecuteCommand Operation | PORT -->
		<wsdl:operation name="ExecuteCommand">
			<wsdl:input message="tns:ExecuteCommandSoapIn"/>
			<wsdl:output message="tns:ExecuteCommandSoapOut"/>
		</wsdl:operation>
	</wsdl:portType>
	<wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
		<soap:binding transport="http://schemas.xmlsoap.org/soap/http"/>
		<!-- SOAP Login Action -->
		<wsdl:operation name="Login">
			<soap:operation soapAction="Login" style="document"/>
			<wsdl:input>
				<soap:body use="literal"/>
			</wsdl:input>
			<wsdl:output>
				<soap:body use="literal"/>
			</wsdl:output>
		</wsdl:operation>
		<!-- SOAP ExecuteCommand Action -->
		<wsdl:operation name="ExecuteCommand">
			<soap:operation soapAction="ExecuteCommand" style="document"/>
			<wsdl:input>
				<soap:body use="literal"/>
			</wsdl:input>
			<wsdl:output>
				<soap:body use="literal"/>
			</wsdl:output>
		</wsdl:operation>
	</wsdl:binding>
	<wsdl:service name="HacktheboxService">
		<wsdl:port name="HacktheboxServiceSoapPort" binding="tns:HacktheboxServiceSoapBinding">
			<soap:address location="http://localhost:80/wsdl"/>
		</wsdl:port>
	</wsdl:service>
</wsdl:definitions>
```

Ta đã xác định được file WSDL của SOAP service!

**Ghi chú:** File WSDL có thể xuất hiện ở nhiều dạng (ví dụ: `/example.wsdl`, `?wsdl`, `/example.disco`, `?disco`, v.v.). **DISCO** là công nghệ của Microsoft để publish và discover Web Services.

***

### Phân tích file WSDL (WSDL File Breakdown)

File WSDL trên tuân theo layout WSDL phiên bản **1.1** và gồm các thành phần sau.

#### Definition

Root element của mọi file WSDL. Bên trong `definitions` xác định tên web service, khai báo tất cả namespaces được dùng trong tài liệu WSDL và định nghĩa các phần tử dịch vụ khác.

**Ví dụ (giữ nguyên):**

```xml
<wsdl:definitions targetNamespace="http://tempuri.org/" 

    <wsdl:types></wsdl:types>
    <wsdl:message name="LoginSoapIn"></wsdl:message>
    <wsdl:portType name="HacktheBoxSoapPort">
  	  <wsdl:operation name="Login"></wsdl:operation>
    </wsdl:portType>
    <wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
  	  <wsdl:operation name="Login">
  		  <soap:operation soapAction="Login" style="document"/>
  		  <wsdl:input></wsdl:input>
  		  <wsdl:output></wsdl:output>
  	  </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="HacktheboxService"></wsdl:service>
</wsdl:definitions>
```

#### Data Types

Định nghĩa các kiểu dữ liệu được dùng trong các message trao đổi.

**Ví dụ (giữ nguyên):**

```xml
<wsdl:types>
    <s:schema elementFormDefault="qualified" targetNamespace="http://tempuri.org/">
  	  <s:element name="LoginRequest">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="1" name="username" type="s:string"/>
  				  <s:element minOccurs="1" maxOccurs="1" name="password" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="LoginResponse">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="ExecuteCommandRequest">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="1" name="cmd" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="ExecuteCommandResponse">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
    </s:schema>
</wsdl:types>
```

#### Messages

Định nghĩa các thao tác input và output mà web service hỗ trợ — tức là các message sẽ được trao đổi, có thể là toàn bộ document hoặc là các argument để map tới việc gọi method.

**Ví dụ (giữ nguyên):**

```xml
<!-- Login Messages -->
<wsdl:message name="LoginSoapIn">
    <wsdl:part name="parameters" element="tns:LoginRequest"/>
</wsdl:message>
<wsdl:message name="LoginSoapOut">
    <wsdl:part name="parameters" element="tns:LoginResponse"/>
</wsdl:message>
<!-- ExecuteCommand Messages -->
<wsdl:message name="ExecuteCommandSoapIn">
    <wsdl:part name="parameters" element="tns:ExecuteCommandRequest"/>
</wsdl:message>
<wsdl:message name="ExecuteCommandSoapOut">
    <wsdl:part name="parameters" element="tns:ExecuteCommandResponse"/>
</wsdl:message>
```

#### Operation

Định nghĩa các SOAP actions có sẵn cùng với cách mã hoá từng message.

#### Port Type

Gói mọi message input/output thành các operation. Nói cách khác, `portType` định nghĩa web service, các operation có sẵn và các message trao đổi. (Lưu ý: trong WSDL 2.0, phần tử `interface` đảm nhiệm việc định nghĩa operations; phần `types` đảm nhiệm định nghĩa message/types.)

**Ví dụ (giữ nguyên):**

```xml
<wsdl:portType name="HacktheBoxSoapPort">
    <!-- Login Operaion | PORT -->
    <wsdl:operation name="Login">
  	  <wsdl:input message="tns:LoginSoapIn"/>
  	  <wsdl:output message="tns:LoginSoapOut"/>
    </wsdl:operation>
    <!-- ExecuteCommand Operation | PORT -->
    <wsdl:operation name="ExecuteCommand">
  	  <wsdl:input message="tns:ExecuteCommandSoapIn"/>
  	  <wsdl:output message="tns:ExecuteCommandSoapOut"/>
    </wsdl:operation>
</wsdl:portType>
```

#### Binding

Ràng buộc operation với một `portType` cụ thể. Có thể hiểu binding là interface: client sẽ gọi port type tương ứng và, dựa vào chi tiết trong binding, sẽ biết cách truy cập các operation gắn với port đó. Binding cung cấp thông tin truy cập web service như định dạng message, operations, messages, và interfaces (đối với WSDL 2.0).

**Ví dụ (giữ nguyên):**

```xml
<wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
    <soap:binding transport="http://schemas.xmlsoap.org/soap/http"/>
    <!-- SOAP Login Action -->
    <wsdl:operation name="Login">
  	  <soap:operation soapAction="Login" style="document"/>
  	  <wsdl:input>
  		  <soap:body use="literal"/>
  	  </wsdl:input>
  	  <wsdl:output>
  		  <soap:body use="literal"/>
  	  </wsdl:output>
    </wsdl:operation>
    <!-- SOAP ExecuteCommand Action -->
    <wsdl:operation name="ExecuteCommand">
  	  <soap:operation soapAction="ExecuteCommand" style="document"/>
  	  <wsdl:input>
  		  <soap:body use="literal"/>
  	  </wsdl:input>
  	  <wsdl:output>
  		  <soap:body use="literal"/>
  	  </wsdl:output>
    </wsdl:operation>
</wsdl:binding>
```

#### Service

Client gọi web service thông qua tên service được chỉ định trong thẻ `service`. Thông qua phần tử này, client xác định **location** của web service.

**Ví dụ (giữ nguyên):**

```xml
    <wsdl:service name="HacktheboxService">

      <wsdl:port name="HacktheboxServiceSoapPort" binding="tns:HacktheboxServiceSoapBinding">
        <soap:address location="http://localhost:80/wsdl"/>
      </wsdl:port>

    </wsdl:service>
```























