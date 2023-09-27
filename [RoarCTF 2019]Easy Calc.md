# Overview

Challenge cho ta trang web có chức năng tính toán

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5b421b6e-205d-47d9-93a1-e5328b2b7731)

ViewsourcePage ta có

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0683c889-5609-4bd8-a667-cff6f11c9afd)

Ta thấy rằng biểu thức mà ta nhập vào được xử lí qua file calc.php thông qua method GET. Mở file calc.php

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3293ddc1-de63-45bd-b814-7304cf622d0f)

# Analyse

## PHP's string parsing feature

- Chúng ta biết rằng PHP chuyển đổi chuỗi truy vấn (trong URL hoặc phần thân yêu cầu HTTP) thành một mảng kết hợp của $_GET hoặc $_POST. Ví dụ: `/foo=bar sẽ trở thành Array([foo] => "bar")`.
  
- Đáng chú ý rằng chuỗi truy vấn có thể loại bỏ hoặc thay thế khoảng trắng bằng gạch dưới trong quá trình phân tích. Ví dụ `/?%20news|id%00=42` sẽ được chuyển đổi thành `Array[news_id] => 42)`. 

- Nếu có một quy tắc trong hệ thống IDS/IPS hoặc bảo mật ứng dụng (WAF) mà chặn truy cập khi giá trị của tham số news_id không phải là một giá trị số, thì chúng ta có thể tận dụng lỗ hổng này để bypass.Ví dụ `/news.php?%20news[id%00]=42"+AND+1=0--`. Giá trị của tham số `?%20news[id%00` sẽ được lưu ở `$_GET["news_id"]`

- PHP cần chuyển đổi tất cả các tham số thành tên biến hợp lệ, vì vậy khi phân tích chuỗi truy vấn, nó thực hiện hai điều sau:

-Không loại bỏ khoảng trắng

-Chuyển đổi một số ký tự thành dấu gạch dưới (bao gồm cả khoảng trắng)

Vậy thì ta được cách sau: `http:///wwww.example.com/index?php? num=aaa`

Theo cách này WAF, Không thể tìm biến num, bởi vì hiện tại biến `"num"`, không phải `" num"`.Nhưng khi PHP phân tích, nó sẽ remove đi khoảng trắng và code ta sẽ chạy được.

## WAF

- Có vẻ như WAF không thể nhìn thấy chúng ta, và mình nghĩr mã nguồn trong câu hỏi đó là WAF. Hơn nữa, WAF không có nghĩa là tiêu đề được viết bằng PHP, sau đó WAF phải được viết bằng PHP. Điều này chính xác là vì vậy, WAF trong câu hỏi này sẽ không nhận diện rằng `"num"` và `" num"` thực sự là giống nhau.

# Payload

```
? num=1;var_dump(scandir(chr(47)))
? num=1;var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)))
```

- Giải thích:
-var_dump(scandir(chr(47)) giúp ta liệt kê ra toàn bộ các thư mục dưới đường dẫn /. Bởi vì kí tự `/` đã bị filter trong Blacklist nên ta sử dụng `(chr(47)`.
  
