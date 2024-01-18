![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a7c9075c-c3dc-48bf-8ba4-8458fd600508)

Challenge ko cho ta gì hết,ngoại trừ dòng báo lỗi. Mình check xem nó có file robots.txt ko

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ba877d05-ba03-4a3d-94ca-a22c6b71512e)

Và nó hiện ra đường dẫn khác nhưng mà không có gì hết

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d0a20be6-59d8-477a-b2f2-d4da07c4a786)

Mình thử dùng burpsuite để bắt package xem có gì đặc biệt không

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/877153a1-5d98-477d-8d3e-07bb7095f197)

Response có cho ta 1 đường dẫn khác `/fl4g.php`.Theo đường dẫn đó, ta được source code

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b6c59d44-ffd4-4a34-a98c-2c890434ac3b)

```php
<?php
header('Content-type:text/html;charset=utf-8');
error_reporting(0);
highlight_file(__file__);


//level 1
if (isset($_GET['num'])){
    $num = $_GET['num'];
    if(intval($num) < 2020 && intval($num + 1) > 2021){
        echo "鎴戜笉缁忔剰闂寸湅浜嗙湅鎴戠殑鍔冲姏澹�, 涓嶆槸鎯崇湅鏃堕棿, 鍙槸鎯充笉缁忔剰闂�, 璁╀綘鐭ラ亾鎴戣繃寰楁瘮浣犲ソ.</br>";
    }else{
        die("閲戦挶瑙ｅ喅涓嶄簡绌蜂汉鐨勬湰璐ㄩ棶棰�");
    }
}else{
    die("鍘婚潪娲插惂");
}
//level 2
if (isset($_GET['md5'])){
   $md5=$_GET['md5'];
   if ($md5==md5($md5))
       echo "鎯冲埌杩欎釜CTFer鎷垮埌flag鍚�, 鎰熸縺娑曢浂, 璺戝幓涓滄緶宀�, 鎵句竴瀹堕鍘�, 鎶婂帹甯堣桨鍑哄幓, 鑷繁鐐掍袱涓嬁鎵嬪皬鑿�, 鍊掍竴鏉暎瑁呯櫧閰�, 鑷村瘜鏈夐亾, 鍒灏忔毚.</br>";
   else
       die("鎴戣刀绱у枈鏉ユ垜鐨勯厭鑲夋湅鍙�, 浠栨墦浜嗕釜鐢佃瘽, 鎶婁粬涓€瀹跺畨鎺掑埌浜嗛潪娲�");
}else{
    die("鍘婚潪娲插惂");
}

//get flag
if (isset($_GET['get_flag'])){
    $get_flag = $_GET['get_flag'];
    if(!strstr($get_flag," ")){
        $get_flag = str_ireplace("cat", "wctf2020", $get_flag);
        echo "鎯冲埌杩欓噷, 鎴戝厖瀹炶€屾鎱�, 鏈夐挶浜虹殑蹇箰寰€寰€灏辨槸杩欎箞鐨勬湸瀹炴棤鍗�, 涓旀灟鐕�.</br>";
        system($get_flag);
    }else{
        die("蹇埌闈炴床浜�");
    }
}else{
    die("鍘婚潪娲插惂");
}
?>
鍘婚潪娲插惂
```

Bây giờ thì chúng ta phải bypass từng level thôi.

Đối với level 1, nó sẽ kiểm tra num có được truyền qua tham số GET hay không.Tiếp theo nó sẽ kiểm tra $num có nhỏ hơn 2020 và giá trị của ($num + 1) có lớn hơn 2021 không.Để ý rằng trong cái if đó sử dụng hàm intval() - được sử dụng để chuyển đổi một giá trị thành một số nguyên (integer).

Nhưng mà sau khi mình test thì lỗi hàm intval() chỉ xuất hiện ở lỗi phiên bản php version 7.0 trở xuống

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/70e88c01-4b20-46d6-b27f-40ae67c586cf)


Vậy thì `?num=2e4`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ab452a36-a720-4c59-b0d2-d8f563cf0826)

Tiếp theo với level 2,nó sẽ so sánh với biến $md5 ta nhập vào với md5($md5) và nó sử dụng loose comparision `==` để so sánh vd: `123=123asdsdfas`.Vậy thì ta cần tìm giá trị cho biến md5 sao cho khi md5 hash lên thì có giá trị đầu bằng biến md5.Trong PHP, có 1 chuỗi nếu bắt đầu là `0e`, thì chuỗi đó là 0.Vậy thì ta cần tìm giá trị cho md5 có đầu là 0e và sau khi md5 thì cũng có đầu là 0e.Do md5 có rất nhiều magic nên mình tìm [ở đây](https://github.com/spaze/hashes/blob/master/md5.md) khá lâu.

Hoặc python script:

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/89d5e60d-6006-433f-a846-db2c98159785)

`md5=0e215962017`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d9ba7996-b5d1-4698-b529-ac9868343590)

Tiếp theo là level cuối

```php
if (isset($_GET['get_flag'])){
    $get_flag = $_GET['get_flag'];
    if(!strstr($get_flag," ")){
        $get_flag = str_ireplace("cat", "wctf2020", $get_flag);
        echo "鎯冲埌杩欓噷, 鎴戝厖瀹炶€屾鎱�, 鏈夐挶浜虹殑蹇箰寰€寰€灏辨槸杩欎箞鐨勬湸瀹炴棤鍗�, 涓旀灟鐕�.</br>";
        system($get_flag);
    }else{
        die("蹇埌闈炴床浜�");
    }
}else{
    die("鍘婚潪娲插惂");
}
```

Ở lệnh if đầu tiên nó sẽ kiểm tra xem giá trị của $get_flag có chứa khoảng trắng hay không.Tiếp theo là hàm str_ireplace() để thay thế chuỗi "cat" bằng chuỗi "wctf2020" trong biến $get_flag. Hàm này thực hiện việc thay thế không phân biệt chữ hoa chữ thường (case-insensitive).

Mình thử dùng lệnh ls để kiểm tra 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4a5a9c78-d264-4127-81d4-3491e4d00671)

Vậy là ở level này chúng ta phải RCE, để bypass khoảng cách thì sử dụng `$IFS$9`.Còn lênh cat ta có thể sử dụng lệnh khác `tail` hoặc `ca\t`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/914a90b9-ef26-4694-808c-dfbc716cebdb)








