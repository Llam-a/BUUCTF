![image](https://github.com/Llam-a/BUUCTF/assets/115911041/90713a6e-7fb5-49b9-a286-94172ca2581f)

Đầu tiên ta có form của challenge, mình sẽ thử inject sqli

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/cd279332-fb12-468f-843c-cbb64e71c90a)

Có xuất hiện báo lỗi sqli, sau đó mình thử vài payload cơ bản

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/67427291-0e7f-405f-82b8-9b00905c646f)

Kết quả trả lại là bị filter đi rồi. Mình sẽ thử Stacked SQLi

`1';show tables;#`

Và có trả ra kết quả

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/929b34ce-7ae0-4b53-8b5e-9874461ba93b)

Dự đoán là flag sẽ ở `FlagHere`.Mình sẽ thử các payload cơ bản với lại `;`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f0d16e6c-58b2-4e86-aa18-b430aef09940)

Nhưng đã bị filter `select`.Mình sẽ dùng `handler`, cách này mình học được từ giải PicoCtf2023

[Readhere](https://dev.mysql.com/doc/refman/8.0/en/handler.html#:~:text=HANDLER%20enables%20you%20to%20traverse%20a%20database%20in,provide%20an%20interactive%20user%20interface%20to%20the%20database.)

`1';handler FlagHere open;handler FlagHere read first;handler FlagHere close;#`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bf7219b2-a87e-4f80-96a3-f7a336d72f8c)


