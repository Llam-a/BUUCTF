![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0ea26c32-a285-4e25-9d5f-6e8b452851b7)

Challenge cho ta trang web login, lúc đầu mình tưởng bài này là sql nên làm mãi ko có kết quả gì.Sau đó thì mình click vào help

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/758bd7c5-4367-46b6-a3fd-0c822788b971)

Nó hiện ra lỗi ko tìm thấy tập tin của java.Sau đó thì mình sử dụng burpsuite để bắt package xem có gì đặc biệt ko

Mình có đổi GET sang POST để có nhận gì được ko

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7c9eb041-4946-4024-b837-a9a7c1c840d5)

Sau đó mình dùng postman để tải file help.doc thử xem

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4469d224-c81d-4080-b4b6-d6d3460f8f57)

Được kết quả sau 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/6a16e434-07b8-4e08-bdd1-e1946f4db564)

Mình lại tiếp tục research cái lỗi khi mà bấm vào help.Thì mình phát hiện ra về `WEB-INF`-là một thư mục đặc biệt chứa các tệp cấu hình và tài nguyên cụ thể cho ứng dụng web.

```
myapp
├── WEB-INF
│   ├── classes
│   │   └── com
│   │       └── myapp
│   │           └── MyServlet.class
│   ├── lib
│   │   └── mylibrary.jar
│   └── web.xml (web. xml defines mappings between URL paths and the servlets that handle requests with those paths. The web server uses this configuration to identify the servlet to handle a given request and call the class method that corresponds to the request method.)
├── index.jsp
└── other.jsp
```

```
Vulnerability causes: Thông thường, một số ứng dụng web sử dụng nhiều máy chủ web cùng nhau để giải quyết các khuyết điểm về hiệu suất của một trong số các máy chủ web, tận dụng lợi ích của cân bằng tải và hoàn thành một số chính sách bảo mật phân cấp. Khi sử dụng kiến trúc này, do cấu hình không đúng của ánh xạ thư mục hoặc tệp tài nguyên tĩnh, có thể gây ra một số vấn đề bảo mật, dẫn đến việc có thể đọc được web.xml và các tệp khác.

Vulnerability detection and exploit methods: Bằng cách tìm tệp web.xml, suy luận đường dẫn của tệp lớp, và sau đó trực tiếp truy cập vào tệp lớp, rồi giải mã tệp lớp để lấy mã nguồn của trang web.

Nói chung, bộ máy jsp mặc định không cho phép truy cập vào thư mục WEB-INF, và khi Nginx phối hợp với Tomcat để cân bằng tải hoặc nhóm, nguyên nhân của vấn đề thực sự rất đơn giản, Nginx sẽ không xem xét các vấn đề bảo mật do cấu hình của các loại bộ máy khác (Nginx không phải là một bộ máy jsp) và đưa chúng vào các quy định bảo mật của riêng mình (do đó mức độ kết nối quá cao).

Việc chỉnh sửa tệp cấu hình Nginx để vô hiệu hóa việc truy cập vào thư mục WEB-INF là tốt:
location ~ ^/WEB-INF/* { deny all; } } } hoặc return 404; hoặc thứ khác!
```

Theo cách exploit ở trên thì bằng cách tìm tệp web.xml, suy luận đường dẫn của tệp lớp, và cuối cùng lấy được tệp lớp, rồi giải mã tệp lớp để lấy mã nguồn của trang web.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3552bcb4-a69f-4dfc-a5fd-0fa77637c4c1)


Có 1 cái đường dẫn là `Dcom.wm.ctf.FlagController` nằm trong thẻ `servlet-class` file sẽ có extension là .class.

Và thư mục classes trong WEB-INF có bao gồm servlet.Mình tiếp tục theo đường dẫn đó

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/78cd0a24-4280-449a-b40f-71b96f57ebf2)

Mình thấy có 1 đoạn base64

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/65a0cdbd-3e83-497c-ae5b-41207ede4457)


