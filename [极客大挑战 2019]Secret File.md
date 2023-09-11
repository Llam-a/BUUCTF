Ta có form của challenge 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/83344b50-ac60-4e60-a6e9-987f29adabe3)

Không có gì đặc biệt nên mình xem source code thì có 1 đường dẫn khác `Archive_room.php`. Theo đường dẫn đó ta đến được

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0a6361b0-983d-42b4-955e-b42674ea70d4)

Nhấp vào secret thì dẫn đến đường link khác `end.php`.Mình sử dụng burp suite để bắt request

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/40c65179-cbd8-4d0f-92eb-22dce3bd3e89)

Và ta có một đường dẫn khác `secr3t.php`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/17841a3c-5e64-4202-8ad6-eb56ebb38071)

Theo source này là LFI, tham số file được truyền qua GET. Vậy để lấy được flag ta cần phải bypass cái if này

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/0ed118ea-18d4-469d-bc39-4c17665028f4)

Cái if ở đây nó sẽ check xem file của mình nó có bị dính ../; tp; input; data không, nếu có thì trả về Oh no!. Mình lên hactrick để kiếm paypload bypass filter này.
	
`secr3t.php?file=php://filter/convert.base64-encode/resource=flag.php`

Kết quả trả về 1 chuỗi base64, decode thì ra được flag trong đó
