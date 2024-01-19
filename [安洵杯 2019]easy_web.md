![image](https://github.com/Llam-a/BUUCTF/assets/115911041/101ec721-a444-410b-93fb-b8e4d4ce7bbf)

 Challenge này không cho ta gì hết ngoại trừ 2 bức ảnh.Để ý URL : `/index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=` Ta thấy giá trị của tham số `img` là 1 dãy base64.
 
Sau khi mò nhiều lần thì cái bức ảnh ảnh này phải decode base64 2 lần, cuối cùng thì nó ra HEX.

```
img=TXpVek5UTTFNbVUzTURabE5qYz0
decode base64 lần 1:MzUzNTM1MmU3MDZlNjc=
decode base64 lần 2: 3535352e706e67
Hex: 555.png
```
Sau khi HEX thì nó ra là `555.png` mà đó là cái bức ảnh này ![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5f2198c5-02c2-4b7d-98bf-239697335b22)

Mình thử viewsource page thì cái bức ảnh này là 1 dãy base64, mình làm tương tự như tham số `img`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e7c9c6e2-87ba-425b-9ef5-886f2a20dd54)

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/61d2b597-bc9f-4456-80a7-c8e396f310d5)

Cũng ko có gì mới.Bây giờ mình sẽ thử với giá trị `index.php`- là cái background.Reverse `index.php`

```
Hex: 696e6465782e706870
Base64 lần 1: Njk2ZTY0NjU3ODJlNzA2ODcw
Base64 lần 2: TmprMlpUWTBOalUzT0RKbE56QTJPRGN3
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ae99708d-8bed-482e-8efd-52bcfb94da73)

Bây giờ làm tương tự như ban đầu

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/74df9c3e-2662-45a2-803f-49ba00285e28)

Và nó cho ta source code.

```php
<?php
error_reporting(E_ALL || ~ E_NOTICE);
header('content-type:text/html;charset=utf-8');
$cmd = $_GET['cmd'];
if (!isset($_GET['img']) || !isset($_GET['cmd'])) 
    header('Refresh:0;url=./index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=');
$file = hex2bin(base64_decode(base64_decode($_GET['img'])));

$file = preg_replace("/[^a-zA-Z0-9.]+/", "", $file);
if (preg_match("/flag/i", $file)) {
    echo '<img src ="./ctf3.jpeg">';
    die("xixi～ no flag");
} else {
    $txt = base64_encode(file_get_contents($file));
    echo "<img src='data:image/gif;base64," . $txt . "'></img>";
    echo "<br>";
}
echo $cmd;
echo "<br>";
if (preg_match("/ls|bash|tac|nl|more|less|head|wget|tail|vi|cat|od|grep|sed|bzmore|bzless|pcre|paste|diff|file|echo|sh|\'|\"|\`|;|,|\*|\?|\\|\\\\|\n|\t|\r|\xA0|\{|\}|\(|\)|\&[^\d]|@|\||\\$|\[|\]|{|}|\(|\)|-|<|>/i", $cmd)) {
    echo("forbid ~");
    echo "<br>";
} else {
    if ((string)$_POST['a'] !== (string)$_POST['b'] && md5($_POST['a']) === md5($_POST['b'])) {
        echo `$cmd`;
    } else {
        echo ("md5 is funny ~");
    }
}

?>
<html>
<style>
  body{
   background:url(./bj.png)  no-repeat center center;
   background-size:cover;
   background-attachment:fixed;
   background-color:#CCCCCC;
}
</style>
<body>
</body>
</html>
```

Ban đầu nó sẽ kiểm tra hai tham số `img` và `cmd` có tồn tại trong url không, nếu 1 trong 2 không tồn tại thì nó chuyển hướng sang url:`/index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=`.

Và giá trị của `img` được base64 2 lần và chuyển thành chuỗi hex được gán cho biến file.Các giá được trong biến file sẽ được filter `"/[^a-zA-Z0-9.]+/", ""`.Và nó sẽ kiểm tra chuỗi của biến file có từ flag hay ko, nếu có nó sẽ in ra `xixi～ no flag`.Nếu không thì nó sẽ đọc cái biến file decode sang base64 rồi lưu trong biến txt.Sau đó thì nó in ra 1 hình ảnh và content của hình ảnh đó được lấy từ biến txt.

Tới cái if tiếp theo nó sẽ filter khá nhiều lệnh trong biến cmd.Nếu ko có thì nó sẽ chuyển sang cái if tiếp theo. Nó sẽ kiểm tra hai biến a và b có khác nhau và có cùng giá trị hàm băm md5 hay không.Nếu điều kiện này đúng thì nó in ra câu lệnh được lưu tron biến cmd.Khi nó so sánh hàm băm thì nó dùng strict comparison.









