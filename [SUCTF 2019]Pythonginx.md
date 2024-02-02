![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1221b003-b171-49c9-8a95-8883f7d1b0a8)

Dầu tiên mình format lại code cho dễ nhìn.

```python
@app.route('/getUrl', methods=['GET', 'POST'])
def getUrl():
    url = request.args.get("url")
    host = parse.urlparse(url).hostname
    
    if host == 'suctf.cc':
        return "我扌 your problem? 111"
    
    parts = list(urlsplit(url))
    host = parts[1]
    
    if host == 'suctf.cc':
        return "我扌 your problem? 222 " + host
    
    newhost = []
    for h in host.split('.'):
        newhost.append(h.encode('idna').decode('utf-8'))
        
    parts[1] = '.'.join(newhost)
    
    # 去掉 url 中的空格
    finalUrl = urlunsplit(parts).split(' ')[0]
    host = parse.urlparse(finalUrl).hostname
    
    if host == 'suctf.cc':
        return urllib.request.urlopen(finalUrl).read()
    else:
        return "我扌 your problem? 333"
```

Code trên là cách vận hành của endpoint `/getUrl`, khi có yêu cầu gửi đến /getUrl, nó lấy trước tiên giá trị của tham số url với method GET.Sau đó dùng hàm urlparse dể phân tích cú pháp và lấy tên miền của url.

Nếu tên miền - domain là `suctf.cc` thì sẽ trả về ` 我扌 your problem? 111`.Nếu không phải nó tiếp tục xử lí bằng cách chia url thành các phần bằng hàm urlsplit và lấy tên miền từ phần thứ 2 được chia. Sau đó, kiểm tra xem tên miền có phải là 'suctf.cc' hay không. Nếu đúng, trả về chuỗi "我扌 your problem? 222 " kết hợp với tên miền.

Tiếp theo, chuyển đổi tên miền thành dạng Punycode bằng cách mã hóa tên miền thành dạng `idna` và sau đó giải mã lại bằng utf-8. Điều này đảm bảo rằng tên miền sẽ được mã hóa đúng dạng khi có các ký tự đặc biệt hoặc ký tự Unicode

Sau đó nó loại bỏ mọi khoảng trắng trong url và cập nhật phần tên miền trong url.Kiểm tra xem tên miền có phải là 'suctf.cc' hay không. Nếu đúng, sử dụng urlopen từ urllib.request để mở URL đã xử lý và đọc nội dung của nó bằng cách gọi phương thức read().

Sau đó thì mình có check source thì có hint là challenge này liên quan đến ngix.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d9d7f2ff-2c3f-4e3d-9b05-7fe2fb1e34c5)


Trong Unicode, kí tự ℀(U+2100) và ℆ (U+2106), khi idna xử lý kí tự này sẽ biến ℀ thành a / c. Bằng cách này ta có thể bypass được 2 cái if đầu tiên và thỏa mãn cái if thứ 3. Ngoài ra thì ta cần phải biết 1 số file mặc định của nginx.

https://en.wiktionary.org/wiki/Appendix:Unicode/Letterlike_Symbols

```
Configuration file storage directory: /etc/nginx
Main configuration file: /etc/nginx/conf/nginx.conf
Management script: /usr/lib64/systemd/system/nginx.service
Module: /usr/lisb64/nginx/modules
Application: /usr/sbin/nginx
The default storage location of the program: /usr/share/nginx/html
The default storage location of logs: /var/log/nginx
The configuration file directory is: /usr/local/nginx/conf/nginx.conf
```

payload:

```
file://suctf.cℂ/usr/local/nginx/conf/nginx.conf
file://suctf.cℂ/usr/fffffflag
```

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ac755296-b210-4aa1-86dc-9c54a5b5ab95)

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/fefda6b7-2bd4-4606-915f-1ce3bc29a182)



