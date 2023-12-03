Bài này có cho source code thì mình check trước

Đầu tiên với file `search.php`.Theo source thì ta bị filter khá nhiều, đặc biệt là ở if thì ta thấy rằng dù ó đăng nhập thành công thì nó cũng sẽ so sánh md5 của password mình nhập vào với password trong database.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ec647f7e-d5d0-410a-ae57-af4ac1fd5be1)

Có 1 trick là mysql sẽ tự động tạo dữ liệu giả khi truy vấn dữ liệu không tồn tại,
