![Screenshot 2023-12-15 085506](https://github.com/Llam-a/BUUCTF/assets/115911041/6b124fd0-d650-4cde-b155-b18f6e0a25a8)

Do là không có gì đặc biệt nên mình đi quét file và kết quả cho ra có file `.git`, mình dùng GitHacmaster để lấy file về

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/059271c9-d6db-4590-a7c3-0be3b902d62b)

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f4c4a901-b7db-4571-a491-de33f83aa1bc)

Ta thấy tham số exp được xử lí bởi hàm eval() qua method GET, vậy là ta có thể RCE thông qua nó. Nhưng đã bị cái WAF block khá nhiều

Ở cái if đầu tiên nó sẽ block các stream data://, filter://, php://, phar://

Cái if thứ 2, nó xử lí input bằng hàm preg_replace() để replace các kí tự match regex với NULL.Nếu sau khi xứ lí qua hàm này mà kết quả còn lại là `;` thì ta bypass được.Chuỗi regex ở đây sẽ match với những string có dạng như sau: `“a()”, “a(a(a()))”`, thì ta sử dụng `a(a(a(a())))` thì sẽ được.Bởi vì `(?R)` là regex hiện tại của ta và kế đó kế dấu `?` tương trưng cho việc lặp của regex hiện tại 0 lần hoặc 1 lần. Ví dụ, `\w+((?R)?) ---> \w+\w+((?R)?))`

[Recursive pattern](https://www.php.net/manual/en/regexp.reference.recursive.php)

Vậy thì ta có thể hiểu theo cách này

`/[a-z,_]+\(\)/`

Ở cái if thứ 3 thì nó block các chữ sau: `/et|na|info|dec|bin|hex|oct|pi|log/i`

Bây giờ ta cần tìm cách đọc flag chỉ sử dụng function, ta dùng hàm scandir() để có thể list toàn bộ file ở path chỉ định mà flag đang ở current path nên `scandir(.)`.Nhưng mà nó chỉ cho phép các hàm ko tham số nên ta phải dùng cách khác để list các file ở current path

Ta sử dụng function `localeconv()`, nó sẽ trả về  array gồm `decimal_point,thousands_sep,groupin,currency_symbol.mon_decimal_point,mon_thousands_sep,positive_sign,negative_sign,frac_digits,int_curr_symbol`. Mà ta chỉ cần `.` nên `decimal_point` là hợp lí.Và ta sử dụng hàm `current()` để lấy cái phần tử đầu tiên.

`payload:?exp=print_r(scandir(current(localeconv())));`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a1c2b3fe-577f-4872-9dbd-60a758690aa3)

Bởi vì ta cũng biết là flag ở current path nên ta sử dụng hàm array_reverse() đổi ngược lại các thứ tự của phần tử

`payload:?exp=print_r(array_reverse(scandir(current(localeconv()))));`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9b40f9ec-2369-4193-bfad-eda77b5dbaa6)

Tiếp theo dùng hàm next() sử dụng để di chuyển con trỏ tới phần tử tiếp theo trong một mảng.

`payload:?exp=print_r(next(array_reverse(scandir(current(localeconv())))));`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d5baa32e-ab03-4af6-bfab-0d55dbd13dca)

Cuối cùng dùng hàm highlight_file() hoặc show_source để đọc file

`payload:?exp=show_source(next(array_reverse(scandir(current(localeconv())))));`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7953817c-7124-460a-b634-d02413073db3)


Reference:

https://www.rexegg.com/regex-recursion.html

