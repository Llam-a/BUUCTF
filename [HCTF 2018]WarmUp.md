Ta có source code

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1f9a2734-6535-4857-92b4-0e4169a1d085)

Theo đường dẫn mình sẽ vào phần `source.php`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b41d07f4-13fb-4cb6-9b4c-34b487217464)

Ta bắt đầu phân tích.Theo source code thì khi ta truyền tham số file thông qua theo POST hoặc GET.iều khiện là nó phải là string và hàm checkFile trong class emmm phải trả về True. Mà muốn nó trả về True thì 1 trong 4 cái If này phải đúng.

```php
 if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }

            if (in_array($page, $whitelist)) {
                return true;
            }

            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }

            $_page = urldecode($page);
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
```

2 cái if đầu thì không có lỗ hổng, để ý ở 2 if sau

```php
$_page = mb_substr($page, 0, mb_strpos($page . '?', '?'));
```
Đoạn code này sẽ tạo một biến mới là $_page từ biến $page được xử lí qua 2 hàm là mb_substr() và mb_strpos(). Chức năng của chúng cũng tương tự như substr() và strpos().

Ví dụ hàm `mb_substr()`

```php
$str = "Hello, world!";
$substring = mb_substr($str, 0, 5); // Trích xuất 5 ký tự từ vị trí 0
echo $substring; // Kết quả: "Hello"
```
Ví dụ hàm `mb_strops()`

```php
$str = "Xin chào, thế giới!";
$pos = mb_strpos($str, "thế", 0, 'UTF-8'); // Tìm vị trí "thế" trong chuỗi
echo $pos; // Kết quả: 12
```
Vậy thì ta có thể giải thích if của bài như sau 

Dòng mã `$_page = mb_substr($page, 0, mb_strpos($page . '?', '?'));` được sử dụng để tạo một biến `$_page` bằng cách trích xuất một phần của chuỗi `$page`. 

`mb_strpos($page . '?', '?')` : Hàm mb_strpos() tìm vị trí đầu tiên của chuỗi "?" trong chuỗi $page sau khi thêm một dấu "?" vào cuối chuỗi. Điều này đảm bảo rằng nếu không có dấu "?" nào trong $page, thì mb_strpos() sẽ trả về false và không gây ra lỗi.

`mb_substr($page, 0, mb_strpos($page . '?', '?'))` : Hàm mb_substr() sau đó sử dụng vị trí tìm thấy từ mb_strpos() để trích xuất một phần của chuỗi $page. Cụ thể:

$page: Chuỗi gốc mà bạn muốn trích xuất.

0: Vị trí bắt đầu từ ký tự đầu tiên (vị trí 0 của chuỗi).

mb_strpos($page . '?', '?'): Vị trí kết thúc của phần chuỗi bạn muốn trích xuất, được tính bởi mb_strpos(). Nó sẽ là vị trí của dấu "?" đầu tiên trong $page (nếu có).

Dưới đây là một ví dụ để hiểu rõ hơn:

Giả sử $page là chuỗi "example.php?param=value". Sau khi thực hiện dòng mã $_page = mb_substr($page, 0, mb_strpos($page . '?', '?'));, giá trị của $_page sẽ là "example.php". Điều này xảy ra vì mb_strpos() tìm thấy vị trí của dấu "?" đầu tiên trong chuỗi $page, và sau đó mb_substr() trích xuất từ vị trí 0 đến vị trí đó, loại bỏ phần sau dấu "?".

Ví dụ khi qua xử lý của dòng code trên thì:

```
+hint.php -> hint.php
+hint.php?/../aaaa-> hint.php
```

Vậy thì ở đây ta đã có thể dễ dàng đọc bất kì file nào khác rồi. Ví dụ ta có thể đọc file hint.php như sau:

`?file=source.php?/../hint.php`

Đồng thời file này cũng nói cho ta biết flag nằm ở ffffllllaaaagggg

Ta có payload như sau : `	
?file=source.php?/../../../../../ffffllllaaaagggg`
