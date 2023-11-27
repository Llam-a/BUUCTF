Challenge cho ta 1 trang web có chức năng `login` và `register`.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e93342dc-b315-4ced-a683-2bce3239ef78)

Mình thử reg 1 acc trước rối sau đó login vào.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c7ce8fb8-342d-4803-b910-910b3c3abe72)

Nó hiện ra 4 trường `index`, `post`, `change password` và `logout`.Mình viewsource `index` và `change password` phát hiện ra.

```
index ---> <!-- you are not admin -->
change password ---> <!-- https://github.com/woadsl1234/hctf_flask/ -->
```
Đầu tiên với index, thì mình nghĩ liên quan đến cookie thì check nhưng nó ko phải JWT

`.eJw9kEFrg0AQhf9K2XMOcdVLIIcWrRiYFcMYmb0EG43u6qbFJGg25L93m0IODwYe7-PNu7P9cWzOHVtdxmuzYHtVs9WdvX2xFSOMZ4lyALtRWUQB2X6WZboEU_gyOnCpi0lqCMCKQSa5l0VbQ3zTAcY30puOsJiJxwHghyLbKTIUCk5-hludYR86xkRl7gmEkEx6kzqfMmwt4E5BCb7EwTHIk2U-QfLZgd52YFsOmkJA8ID_ZXPXBdbssWCH83jcX7775vR6Qdh6IGwDYcgXScqzsuDCprPA2JeJ6N3t6tRKRO9-5iQSCmBaP3HKVG3zIqH3s6vbf-dUGWewYahMxRbsem7G527MW7LHL-iWa5o.ZWQE7w.L4_WPe0tSagYUEl2oqDvM_fthDc`

Sau dó là `change password`.Và lí do là đường link bị hết hạn, mình sẽ dùng code của wu khác.


