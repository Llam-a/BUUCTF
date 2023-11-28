Challenge cho ta 1 trang web có chức năng `login` và `register`.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e93342dc-b315-4ced-a683-2bce3239ef78)

Mình thử reg 1 acc trước rối sau đó login vào.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c7ce8fb8-342d-4803-b910-910b3c3abe72)

Nó hiện ra 4 trường `index`, `post`, `change password` và `logout`.Mình viewsource `index` và `change password` phát hiện ra.

```
index ---> <!-- you are not admin -->
change password ---> <!-- https://github.com/woadsl1234/hctf_flask/ -->
```
Đầu tiên với index, thì mình nghĩ liên quan đến cookie.

`.eJw9kEFrg0AQhf9K2XMOcdVLIIcWrRiYFcMYmb0EG43u6qbFJGg25L93m0IODwYe7-PNu7P9cWzOHVtdxmuzYHtVs9WdvX2xFSOMZ4lyALtRWUQB2X6WZboEU_gyOnCpi0lqCMCKQSa5l0VbQ3zTAcY30puOsJiJxwHghyLbKTIUCk5-hludYR86xkRl7gmEkEx6kzqfMmwt4E5BCb7EwTHIk2U-QfLZgd52YFsOmkJA8ID_ZXPXBdbssWCH83jcX7775vR6Qdh6IGwDYcgXScqzsuDCprPA2JeJ6N3t6tRKRO9-5iQSCmBaP3HKVG3zIqH3s6vbf-dUGWewYahMxRbsem7G527MW7LHL-iWa5o.ZWQE7w.L4_WPe0tSagYUEl2oqDvM_fthDc`

Sau dó là `change password`.Và lí do là đường link bị vấn đề gì đó, sau khi lục lọi thì mình tìm ra một github có chứa [source code ](https://github.com/mo-xiaoxi/CTF_Web_docker/tree/master/HCTF2018/admin/hctf_flask)

Ở file `config` cho ta secret key, ta chỉ cần dùng nó để decode, encode của cookie là xong,

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/12dcbeee-3a29-4150-aac3-911ca339c123)

Mình tiếp tục tìm xem có gì đặc biệt ko.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d419cee0-2da3-4d27-ae60-de0d28560591)

Cái này ở file `user.sql`, khá đúng với việc ta cần làm chỉ cần decode cookie thành admin r sumbit vào là có được flag.Và để decode và encode cookie mình dùng tool [ở đây](https://github.com/noraj/flask-session-cookie-manager).

`decode:  python3 flask_session_cookie_manager3.py decode -c '[cookie]' -s '[secret key]`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b0637b84-6d4b-4d17-a16d-a146e28f9714)

Sau dó thì chỉ cần đổi user là admin rồi encode 

`encode: python3 flask_session_cookie_manager3.py encode -s "[secret key]" -t "[string]"`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/673df9f0-4599-43d4-86d2-b99868c70a2b)

Sau dó thì sumbit lại cookie.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7932e382-4f04-4517-b548-c7f33f9b7230)








