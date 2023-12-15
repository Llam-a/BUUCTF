![Screenshot 2023-12-15 085506](https://github.com/Llam-a/BUUCTF/assets/115911041/6b124fd0-d650-4cde-b155-b18f6e0a25a8)

Do là không có gì đặc biệt nên mình đi quét file và kết quả cho ra có file `.git`, mình dùng GitHacmaster để lấy file về

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/059271c9-d6db-4590-a7c3-0be3b902d62b)

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f4c4a901-b7db-4571-a491-de33f83aa1bc)

Ta thấy tham số exp được xử lí bởi hàm eval() qua method GET, vậy là ta có thể RCE thông qua nó. Nhưng đã bị cái WAF block khá nhiều

Ở cái if đầu tiên nó sẽ block các stream data://, filter://, php://, phar://

Cái if thứ 2, nó xử lí input bằng hàm preg_replace() để replace các kí tự match regex với NULL.Nếu sau khi xứ lí qua hàm này mà kết quả còn lại là `;` thì ta bypass được.
