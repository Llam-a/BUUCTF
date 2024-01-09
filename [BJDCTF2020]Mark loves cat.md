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


