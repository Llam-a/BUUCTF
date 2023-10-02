Challenge cho ta source sau

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ccd005ad-4c98-4c7e-91fe-0b1311ad9d76)

Format lại code 

```php
include 'flag.php';
$flag='MRCTF{xxxxxxxxxxxxxxxxxxxxxxxxx}';
if(isset($_GET['gg'])&&isset($_GET['id'])) {
    $id=$_GET['id'];
    $gg=$_GET['gg'];
    if (md5($id) === md5($gg) && $id !== $gg) {
        echo 'You got the first step';
        if(isset($_POST['passwd'])) {
            $passwd=$_POST['passwd'];
            if (!is_numeric($passwd))
            {
                 if($passwd==1234567)
                 {
                     echo 'Good Job!';
                     highlight_file('flag.php');
                     die('By Retr_0');
                 }
                 else
                 {
                     echo "can you think twice??";
                 }
            }
            else{
                echo 'You can not get it !';
            }

        }
        else{
            die('only one way to get the flag');
        }
}
    else {
        echo "You are not a real hacker!";
    }
}
else{
    die('Please input first');
}
}Please input first
```

Code cho 2 tham số id và gg được tuyền theo method GET có tồn tại hay không và md5 có bằng nhau hay không, ta có thể bypass bằng cách đổi dữ liệu của nó thành array.Bởi vì so sánh sử dụng loose comparison nên ta sẽ bypass bằng chuỗi chữ sau `1234567` vì `1234567=1234567asdasd`. Ta có payload như sau:

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2c478761-3530-4e3b-b76f-1017523071dc)


