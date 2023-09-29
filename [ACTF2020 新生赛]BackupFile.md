![image](https://github.com/Llam-a/BUUCTF/assets/115911041/47377073-6fc3-403b-b071-8b87d89e1e26)

Vậy ta sẽ tìm back up file.Mình dùng tool `dirsearch` để tìm link back up file

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3f75cb63-3e1d-49ce-8dc8-e46a85feb8f4)

Mình time được file `index.php.bak`. Sau đó thì wget và đọc file thôi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9be95ff9-7d31-4694-bd8c-7f4e8bd552a0)

Ta thấy đầu vào của ta là Key, phải là 1 số so sánh với chuỗi `$str`.Code này dùng so sánh loose comparison nên `123ffwsfwefwf24r2f32ir23jrw923rskfjwtsw54w3=123`. Vậy là `Key=123` là được rồi
