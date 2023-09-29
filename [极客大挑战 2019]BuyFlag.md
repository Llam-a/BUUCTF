Challenge cho ta trang web 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d1b5a34f-e4b1-4ad5-9e1c-73300b6f6337)

Mình check menu thì có đường dẫn đến trang `pay flag`.Và nó yêu cầu cần 100000000 để mua flag và cần là Cuit's student. Vậy thì chỉ cần burp suite thôi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0ace8580-83b1-48ba-9065-b9aa0dad9a4a)

Check source code xem có hint gì ko

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/27aee83e-c21a-4795-98d2-814a033c59ee)

Ta có thể POST lên password và password không được là số nhưng phải bằng 404, vậy ta sẽ sử dụng loose comparison bằng cách post lên password là 1 string bắt đầu bằng 404. Nhưng sau khi post lên thì nó vẫn hiển thì là “Only Cuit’s students can buy the FLAG”

Ban đầu thì mình có thử dùng request header để đổi user agent nhưng mà không được, sau đó mình check cookie thì là 0 nên mình đổi sang 1 thì kết quả là nhận.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ae5af4ad-0a63-41ff-bd48-3bd018c106df)

Tiếp theo đó là password và money.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/09c80e48-c201-46a1-9f95-0f35aa0baaa2)

Mình để money=10e40 vì 10e40=100000000. Mình có thử 100000000 nhưng kết quả là number too long.

