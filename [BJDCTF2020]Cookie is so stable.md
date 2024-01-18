![image](https://github.com/Llam-a/BUUCTF/assets/115911041/5bd037b1-b53e-47a4-a6fe-05f6eaf622ec)

Mình check xem `Flag` và `Hint` có gì đặc biệt ko.Hint thì nó cho là `Why not take a closer look at cookies?`

Ở flag nó cho sumbit username, sumbit thử xem sao

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/68734cef-375f-4ea6-9cfb-79adbe637ecf)

Sau đó thì mình check thử cookies xem sao thì thấy user = admin.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c0347169-fcef-432d-8fc0-e3c7877a9aff)

Sau khi thử với `{{7*7}}` thì nó hiện ra kết quả là 49. Vậy challenge này là ssti.Ở php thì có twig với smarty.Mình đã thử test smarty nhưng ko có kết quả nên sang thử Twig xem sao

Payload:`{{_self.env.registerUndefinedFilterCallback("system")}}{{_self.env.getFilter("cat ../../../flag")}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b8b6da18-b9e5-4b8a-a8f5-5853c42bc25b)

