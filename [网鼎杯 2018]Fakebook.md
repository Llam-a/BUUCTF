# [网鼎杯 2018]Fakebook

Challenge cho ta trang web có 2 chức năng join và login.Đầu tiên mình thử join

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e11fcf2f-bfcf-4b3e-acc3-f83787f48aa1)

Sau đó thì mình kiểm tra phần username thì thấy rằng có 1 method GET được tạo ở file `view.php` với tham số `no = 1`.Theo mình nghĩ thì là có sqli

Đầu tiên thử các payload boolean đơn giản.

`and 1=1 union select 1,2,3,4`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e131f95b-78ed-4641-ae7c-a2c8a0df6120)

Và có kết quả trả ra mình thử inject thêm để xem có database hay gì quant trông không.

`order by 4#`
