Challenge cho ta 1 trang web có chức năng upload file

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4e523962-e45b-4943-bf07-a4fd1bd4f219)

Mình thử upload 1 file vuln

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f995cf20-95ad-415c-882c-9122dab4cb52)

Challenge đã filter file php của mình,tiếp theo mình thử đổi extension file để xem bài này là whitelist hay blacklist.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b19ae013-a551-4eb4-8071-0989cfd2fbd8)

Kết quả trả ra vẫn bị filter, nên mình sẽ đổi MINE type thành `image/png`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1355d3cb-76c4-4cb7-967f-8ac1eb1f2410)

Vậy là upload đã thành công, challenge này thuộc blacklist filter các extension và Content_type hay MINE type.Và sau khi upload thành công thì challenge cũng sẽ echo cái path mà ta đã upload file lên.

Bài này mình sẽ làm theo hướng ghi đè qua 1 file `.htaccess`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ab72dd76-6e3c-42e8-abfc-9bdc801f279f)

Lúc này tất cả các file có đuôi là hack sẽ được execute dưới dạng file php.

Tiếp theo thì mình upload Trojan bình thường thôi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/af4cb163-3ab5-44b2-9eec-20a26d702a05)

Dùng antsword để đọc flag

![Screenshot 2024-01-02 173054](https://github.com/Llam-a/BUUCTF/assets/115911041/21d4ebd0-a689-41d9-a93b-6357f56b3ac7)


