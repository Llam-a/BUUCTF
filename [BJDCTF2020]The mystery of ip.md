# [BJDCTF2020]The mystery of ip

Challenge cho giao diện sau 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/425a331a-c157-4d82-97c4-1394031e67ee)

Mình check tab `flag`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bee472ba-b64d-485f-a04e-06c58eee7e89)

Sau đó thì check `hint` nhưng ko có gì đặc biệt mình viewsourcecode

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/371af456-a0f6-4d41-a733-40f5a2452d0a)

Vậy có liên quan đến IP, mình sử dụng burpsuite để chỉnh thành local(X-Forwarded-For) thử xem sao, coi co thể tùy chỉnh theo ý muốn được ko.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4efe7a3f-0942-41a2-9822-474348cd0dda)

Ta có thể tùy chỉnh được, vậy có suy ra là chall này có SSTI vuln.

```
SSTI (Server-Side Template Injection) là một lỗ hổng bảo mật phổ biến trong các ứng dụng web. Nó xảy ra khi ứng dụng web cho phép người dùng nhập dữ liệu vào các template và không kiểm tra hoặc xử lý đúng cách dữ liệu này. Khi người dùng có thể kiểm soát giá trị của trường X-Forwarded-For (một trường tiêu đề HTTP thường được sử dụng để truyền tải địa chỉ IP nguồn), nếu giá trị này được truyền trực tiếp vào một biểu mẫu (template) mà không có sự kiểm tra cẩn thận, nó có thể dẫn đến SSTI.

Khi SSTI xảy ra, người tấn công có thể chèn mã tấn công vào template và thực thi mã trên máy chủ. Việc kiểm soát trường X-Forwarded-For chỉ là một phần trong quá trình khai thác lỗ hổng SSTI. Có thể có nhiều cách khác nhau để khai thác lỗ hổng này, và việc kiểm soát X-Forwarded-For chỉ là một cách trong số đó.
```

Sau đó thì mình thử inject `{{7*7}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/47e47a7b-8fad-4d3a-bae7-bb7118068244)

Ta tiếp tục inject, challenge được build bằng php, php thì thường có 2 template server `Smarty` và `Twig`. Mình thử inject Smarty `{system('ls')}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/81315460-c7ec-4e76-8be9-1f4170722852)

Tiếp tục đọc flag `{system('cat /flag')}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/6d806261-603d-4ef9-82ef-3ba780f460d0)
