Bài này có cho source code thì mình check trước

Đầu tiên với file `search.php`.Theo source thì ta bị filter khá nhiều, đặc biệt là ở if thì ta thấy rằng dù ó đăng nhập thành công thì nó cũng sẽ so sánh md5 của password mình nhập vào với password trong database.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ec647f7e-d5d0-410a-ae57-af4ac1fd5be1)

Có 1 trick là mysql sẽ tự động tạo dữ liệu giả khi truy vấn dữ liệu không tồn tại,ví dụ:

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/8ad4b7fb-1fb7-47de-b4c9-869b2aab1f0e)

Bây giờ mình sẽ check column bằng `1'union select 1,2,3-- -`.Kết quả trả ra là `wrong user!`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/af8fae66-f491-43fa-a44f-145c7a61c00e)

Vậy giờ chỉ cần inject thôi.Là bởi ví nó sẽ so sánh md5 của password mình nhập với password trong database. Vậy thì với trick mysql mà mình demo ở trên thì cái phần password của payload ta sẽ md5 nó từ cái password ta nhập vào.

Payload:

```
username: 123' union select 1,'admin','e10adc3949ba59abbe56e057f20f883e'-- -
password: 123456
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/edf9f93d-f4d8-4e80-bdbd-45b3ed64e449)





