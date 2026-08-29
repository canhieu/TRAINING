---
hidden: true
---

# Attacking GraphQL

## Introduction to GraphQL

GraphQL là một ngôn ngữ truy vấn thường được sử dụng bởi các web API như một giải pháp thay thế cho REST. Nó cho phép phía client lấy đúng dữ liệu cần thiết thông qua cú pháp đơn giản, đồng thời cung cấp nhiều tính năng vốn thường thấy ở các ngôn ngữ truy vấn như SQL. Tương tự REST API, GraphQL API có thể đọc, cập nhật, tạo mới hoặc xóa dữ liệu. Tuy nhiên, GraphQL API thường được triển khai trên một endpoint duy nhất để xử lý toàn bộ truy vấn. Vì vậy, một trong những lợi ích chính của GraphQL so với REST API truyền thống là tối ưu hiệu quả sử dụng tài nguyên và xử lý request.

### **Tổng quan cơ bản**

Một dịch vụ GraphQL thường chạy trên một endpoint duy nhất để nhận truy vấn. Phổ biến nhất, endpoint này nằm tại `/graphql`, `/api/graphql` hoặc một URL tương tự. Để ứng dụng web frontend có thể sử dụng endpoint GraphQL này, nó cần được expose ra ngoài. Tuy nhiên, giống như REST API, chúng ta vẫn có thể tương tác trực tiếp với endpoint GraphQL mà không cần đi qua frontend web application để xác định các lỗ hổng bảo mật.

Ở góc độ trừu tượng, truy vấn GraphQL dùng để chọn các field của object. Mỗi object thuộc về một kiểu dữ liệu cụ thể được backend định nghĩa. Truy vấn được xây dựng theo cú pháp GraphQL, với tên của truy vấn nằm ở phần gốc. Ví dụ, chúng ta có thể truy vấn các field `id`, `username` và `role` của tất cả object kiểu `User` bằng cách chạy truy vấn `users` như sau:

```
{
  users {
    id
    username
    role
  }
}
```

Kết quả phản hồi từ GraphQL cũng có cấu trúc tương ứng và có thể trông như sau:

```
{
  "data": {
    "users": [
      {
        "id": 1,
        "username": "htb-stdnt",
        "role": "user"
      },
      {
        "id": 2,
        "username": "admin",
        "role": "admin"
      }
    ]
  }
}
```

Nếu một truy vấn hỗ trợ tham số, chúng ta có thể thêm tham số được hỗ trợ để lọc kết quả. Ví dụ, nếu truy vấn `users` hỗ trợ tham số `username`, chúng ta có thể truy vấn một user cụ thể bằng cách truyền vào username của họ:

```
{
  users(username: "admin") {
    id
    username
    role
  }
}
```

Chúng ta cũng có thể thêm hoặc bớt các field mà mình quan tâm trong truy vấn. Ví dụ, nếu không quan tâm đến field `role` mà muốn lấy `password` của user, ta có thể điều chỉnh truy vấn như sau:

```
{
  users(username: "admin") {
    id
    username
    password
  }
}
```

Ngoài ra, truy vấn GraphQL hỗ trợ truy vấn lồng nhau (sub-querying), cho phép truy xuất thông tin từ một object có tham chiếu đến object khác. Ví dụ, giả sử truy vấn `posts` trả về một field `author` chứa object kiểu user. Khi đó, ta có thể truy vấn `username` và `role` của author như sau:

```
{
  posts {
    title
    author {
      username
      role
    }
  }
}
```

Kết quả sẽ chứa tiêu đề của tất cả bài viết cùng với dữ liệu đã truy vấn của author tương ứng:

```
{
  "data": {
    "posts": [
      {
        "title": "Hello World!",
        "author": {
          "username": "htb-stdnt",
          "role": "user"
        }
      },
      {
        "title": "Test",
        "author": {
          "username": "test",
          "role": "user"
        }
      }
    ]
  }
}
```

GraphQL còn hỗ trợ nhiều thao tác phức tạp hơn nữa. Tuy nhiên, phần tổng quan nhập môn này là đủ cho mục đích của module hiện tại. Để tìm hiểu chi tiết hơn, hãy tham khảo mục **Learn** trên website chính thức của GraphQL.



## Attacking GraphQL

## Information Disclosure

Khai thác bất kỳ dịch vụ nào đều đòi hỏi quá trình enumeration và reconnaissance kỹ lưỡng để xác định mọi vector tấn công khả thi. Với vai trò là attacker, mục tiêu của chúng ta là thu thập càng nhiều thông tin về dịch vụ càng tốt.

### Identifying the GraphQL Engine&#xD;

Sau khi đăng nhập vào ứng dụng web mẫu và kiểm tra toàn bộ chức năng, chúng ta có thể quan sát thấy nhiều request gửi tới endpoint `/graphql` có chứa truy vấn GraphQL.

<figure><img src="../../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

Ví dụ, request GraphQL truy vấn `posts` với các field như `uuid`, `title`, `body`, `category` và `author (username)`. Response trả về mã trạng thái 200 cùng dữ liệu bài viết, chẳng hạn `uuid` là `"1"`, `title` là `"Lorem ipsum 1"` và phần nội dung `body`.

Từ đó, chúng ta có thể khẳng định chắc chắn rằng ứng dụng web này có triển khai GraphQL. Bước đầu tiên là xác định GraphQL engine mà ứng dụng đang sử dụng bằng công cụ `graphw00f`. ([https://github.com/dolevf/graphw00f](https://github.com/dolevf/graphw00f)) . Công cụ này sẽ gửi nhiều truy vấn GraphQL khác nhau, bao gồm cả các truy vấn lỗi định dạng, rồi xác định engine GraphQL dựa trên hành vi của backend và các thông báo lỗi trả về.

Sau khi clone repository Git, chúng ta có thể chạy công cụ này bằng script Python `main.py`. Ta sẽ chạy công cụ ở chế độ fingerprint (`-f`) và detect (`-d`). Có thể cung cấp base URL của ứng dụng web để `graphw00f` tự động tìm GraphQL endpoint:

```bash
canhieu@htb[/htb]$ python3 main.py -d -f -t http://172.17.0.2

                +-------------------+
                |     graphw00f     |
                +-------------------+
                  ***            ***
                **                  **
              **                      **
    +--------------+              +--------------+
    |    Node X    |              |    Node Y    |
    +--------------+              +--------------+
                  ***            ***
                     **        **
                       **    **
                    +------------+
                    |   Node Z   |
                    +------------+

                graphw00f - v1.1.17
          The fingerprinting tool for GraphQL
           Dolev Farhi <dolev@lethalbit.com>
  
[*] Checking http://172.17.0.2/
[*] Checking http://172.17.0.2/graphql
[!] Found GraphQL at http://172.17.0.2/graphql
[*] Attempting to fingerprint...
[*] Discovered GraphQL Engine: (Graphene)
[!] Attack Surface Matrix: https://github.com/nicholasaleks/graphql-threat-matrix/blob/master/implementations/graphene.md
[!] Technologies: Python
[!] Homepage: https://graphene-python.org
[*] Completed.
```

Kết quả cho thấy `graphw00f` đã xác định GraphQL engine là `Graphene`. Ngoài ra, công cụ còn cung cấp liên kết tới trang tương ứng trong bộ tài liệu `GraphQL-Threat-Matrix`, nơi chứa các thông tin chuyên sâu hơn về engine GraphQL đã được nhận diện

<figure><img src="../../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

Trong trường hợp này, engine được nhận diện là `Graphene`, sử dụng công nghệ `Python`, với trang chủ là `https://graphene-python.org`. Công cụ cũng dẫn chiếu tới ma trận bề mặt tấn công dành cho Graphene.

Theo thông tin trong `GraphQL-Threat-Matrix`, một số đặc điểm bảo mật đáng chú ý gồm:

* `Field Suggestions` và `Introspection` được bật mặc định
* Không hỗ trợ mặc định các cơ chế như `Query Depth Limit`, `Query Cost Analysis`, `Automatic Persisted Queries`, và `Debug Mode`
* `Batch Requests` bị tắt mặc định

Cuối cùng, khi truy cập trực tiếp endpoint `/graphql` trên trình duyệt, ta có thể thấy ứng dụng web chạy giao diện `GraphiQL`. Điều này cho phép nhập và thực thi truy vấn GraphQL trực tiếp, thuận tiện hơn nhiều so với việc gửi truy vấn qua Burp, vì không cần lo làm sai cú pháp JSON.

### Introspection <a href="#introspection" id="introspection"></a>

`Introspection` là một tính năng của GraphQL cho phép người dùng truy vấn API GraphQL để tìm hiểu cấu trúc của hệ thống backend. Nhờ đó, người dùng có thể sử dụng các truy vấn introspection để liệt kê toàn bộ các truy vấn mà schema của API hỗ trợ. Các truy vấn introspection này thường truy vấn field `__schema`.

Ví dụ, chúng ta có thể xác định toàn bộ các kiểu dữ liệu GraphQL mà backend hỗ trợ bằng truy vấn sau:

```
{
  __schema {
    types {
      name
    }
  }
}
```

Kết quả trả về sẽ bao gồm các kiểu mặc định cơ bản như `Int`, `Boolean`, đồng thời cũng chứa các kiểu tùy chỉnh như `UserObject`.

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

Khi đã biết một kiểu dữ liệu cụ thể, ta có thể tiếp tục truy vấn để lấy tên của toàn bộ field thuộc kiểu đó bằng truy vấn introspection sau:

```
{
  __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```

Trong kết quả, chúng ta có thể thấy các thông tin thường có ở một đối tượng user như `username` và `password`, cùng với kiểu dữ liệu tương ứng của chúng.

<figure><img src="../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

Ngoài ra, chúng ta cũng có thể lấy danh sách toàn bộ truy vấn mà backend hỗ trợ bằng truy vấn sau:

```
{
  __schema {
    queryType {
      fields {
        name
        description
      }
    }
  }
}
```

Việc nắm được toàn bộ các truy vấn được hỗ trợ sẽ giúp xác định các vector tấn công tiềm năng có thể được dùng để thu thập thông tin nhạy cảm.

Cuối cùng, có thể sử dụng một truy vấn introspection tổng quát để dump toàn bộ thông tin về các type, field và query mà backend hỗ trợ:

```
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description

  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}

fragment InputValue on __InputValue {
  name
  description
  type { ...TypeRef }
  defaultValue
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

Kết quả của truy vấn này thường rất lớn và phức tạp. Tuy nhiên, chúng ta có thể trực quan hóa schema bằng công cụ `GraphQL-Voyager`. Trong module này, tác giả sử dụng bản demo của GraphQL-Voyager. Nhưng trong một engagement thực tế, nên tự triển khai công cụ theo hướng dẫn trên GitHub để bảo đảm không có dữ liệu nhạy cảm rời khỏi hệ thống nội bộ.

Trong bản demo, người dùng có thể chọn `CHANGE SCHEMA`, sau đó chọn `INTROSPECTION`. Sau khi dán kết quả của truy vấn introspection ở trên vào ô nhập liệu và nhấn `DISPLAY`, schema GraphQL của backend sẽ được trực quan hóa. Khi đó, chúng ta có thể khám phá toàn bộ các query, type và field mà hệ thống hỗ trợ.

<figure><img src="../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>









