![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e9554314-92d7-442d-9cc5-a66475d4b4f5)

Đầu tiên mình thử upload Trojan đơn giản xem có gì ko.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c040ed50-4931-47dc-8594-8c0f292ad4bb)

Và mình nhận được thông báo là nó chỉ chấp nhận file jpg,png,gif. Sau đó mình có dùng burpsuite để bắt package nhưng ko có trả ra package lúc mình upload file.Nên mình đổi extension của file thành `.gif` thử xem sao

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5cc4dfea-0716-4ef4-a30e-bf0adfbd746e)

Upload đã thành công, mình check burpsuite xem sao

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9ab86079-9730-4688-965d-9f5c79e5b3d6)

Bây giờ chỉ cần upload bình thường thôi, và sửa lại extension của file để ta có thể đọc được flag thôi.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a89b4ddf-868e-494d-99f7-13647a301f36)

Sau đó dùng antsword để đọc flag

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7f10cd2f-b967-4700-a32b-3a46a1bbc8db)
