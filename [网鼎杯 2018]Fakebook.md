## [网鼎杯 2018]Fakebook

Challenge cho ta 2 chức năng `/login` và `/join`.Test từng chức năng

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c47e494e-8905-47ec-b69c-85486e4ed683)

Sau khi đăng kí xong thì trả về `Blog is not valid.`, làm lại nhiều lần thì kết quả vẫn auto như thế.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b27f4807-76e7-48ba-9bec-1be465c96599)

Sau đó mình đi scan đi tìm file ẩn, có file `robots.txt`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/366a804d-485c-4006-b9a7-8af128208686)

Ta được đường dẫn sau 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/6185be5f-27d1-484a-a914-2f76a2d5c21f)

Truy cập link ta có source code

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
        $ch = curl_init();

        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
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

Review source ta thấy có hàm `isValidBlog()` khiến cho blog ta nhập vào cần phải có url hợp lệ.Mình thử join lại với blog khác lúc đầu.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9ff18c69-6005-4cfe-82bc-33cec81d2f75)

Sau khi join thành công, mình thử vào username ta thấy có 1 method GET được tạo ở file view.php với tham số `no` có giá trị bằng 1

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7f94cb94-a181-4b62-90d2-eae64ab5ef32)

Thêm 1 ý nữa sau khi mình view source, có xuất hiện vuln SSRF.Đây là ví dụ:

```php
<?php
// Lấy URL từ tham số GET
$user_input = $_GET['url'];

// Thực hiện yêu cầu HTTP đến URL được cung cấp
$response = file_get_contents($user_input);

// Hiển thị nội dung của phản hồi
echo $response;
?>
```

Cũng như source của bài

```php
function get($url)
    {
        $ch = curl_init();

        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        if($httpCode == 404) {
            return 404;
        }
        curl_close($ch);

        return $output;
    }
```

Thì theo source có thể điều khiển tham số url thông qua đường dẫn URL. Nếu không có kiểm tra cẩn thận, điều này có thể dẫn đến lỗ hổng SSRF, nơi attacker có thể nhập URL trỏ đến máy chủ nội bộ hoặc các nguồn khác không an toàn.

Và bài nãy cũng bị dính lỗi SQLi UNION với UNION SELECT bị filter.






