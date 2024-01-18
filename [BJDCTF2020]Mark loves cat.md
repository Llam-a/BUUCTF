Challenge cho ta trang web như sau

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f12c8075-fe20-4906-b0f5-e6d5b445e2d1)

Sau khi dạo quanh thì mình phát hiện ra ở phần `contact me` khi mà ta send message thì URL của web sẽ đổi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9c64e304-4a1a-4883-94c7-9ef5c131f1cb)

Vậy thì bài này có thể là RCE.Challenge này cũng cho ta [source code](https://github.com/BjdsecCA/BJDCTF2020_January/tree/master/Web/mark/src), vậy là khỏi cần dirsearch

```php
<?php
 
include 'flag.php';
 
$yds = "dog";
$is = "cat";
$handsome = 'yds';
 
foreach($_POST as $x => $y){
    $$x = $y;
}
 
foreach($_GET as $x => $y){
    $$x = $$y;
}
 
foreach($_GET as $x => $y){
    if($_GET['flag'] === $x && $x !== 'flag'){
        exit($handsome);
    }
}
 
if(!isset($_GET['flag']) && !isset($_POST['flag'])){
    exit($yds);
}
 
if($_POST['flag'] === 'flag'  || $_GET['flag'] === 'flag'){
    exit($is);
}
 
echo "the flag is: ".$flag;
```

Sau khi review thì khi mà đạt điều kiện của 1 trong 3 cái if thì nó sẽ cho ra ouput ra 3 cái biến đã được gán trước đó.Nhưng ta lại cần flag, vậy thì ta cần phải tìm cách ghi đè lên.

Với cái if đầu

```php
foreach($_GET as $x => $y){
    if($_GET['flag'] === $x && $x !== 'flag'){
        exit($handsome);
    }
}
```

Đề mà output ra flag thì ta cần `$handsome=$flag`, vậy thì exit($handsome) phải là exit($flag), và điều kiện này cần biến "flag" ($y) bằng với ($x) và không được bằng flag.Vậy giá trị của biến "handsome" được gán giống giá trị của biến "flag". Vì vậy, ta có thể đoán rằng giá trị của biến "flag" cũng là "handsome". Payload cuối cùng sẽ là: `?handsome=flag&flag=handsome`.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2b88f1d5-b735-4edb-baf6-4c2a602da73b)

If thứ 2

```php
if(!isset($_GET['flag']) && !isset($_POST['flag'])){
    exit($yds);
```

Ta cần làm cho $yds = $flag với $x=yds và $y=flag.Dựa theo foreach thứ 2 thì khi payload là ?yds=flag -> $yds=$flag.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1b6e2c3b-25c7-49b5-b8ee-5dd8b2f4e322)

If thứ 3

```php
if($_POST['flag'] === 'flag'  || $_GET['flag'] === 'flag'){   
    exit($is);
}
```

Có 2 method POST và GET, phương thức truyền tham số khác nhau thì cũng khác nhau,ta cần pass tham số flag và output là flag, exit($is) -> exit($flag). =>$is=$flag

Dựa theo foreach sau

```php
foreach($_GET as $x => $y){
    $$x = $$y;
```

Vậy ta cần pass 2 tham số.`is=flag&flag=flag`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a216b3c1-a870-4a7c-86bf-24e8f755678b)
