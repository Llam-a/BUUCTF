Ta có form challenge 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/44737ee2-00de-4178-bdc5-bf88ded795af)

Mình thử vài payload đơn giản trước sau đó xác định số column.Với payload `'or 1=1-- -` cho kết quả

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/d2118455-d23e-4b00-9dce-0a77a069adda)

Nhưng nó không có dễ tới mức như vậy, nên mình xác định columns `1'union select 1,2,3-- -`

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/bfcfc454-e714-4353-8890-5fb72dbb7c95)

Vậy là có 3 trường và điểm hiển thị là 2 và 3.Mình sẽ dùng sql map để làm bài này cho nhanh.

Ta lấy request khi login vào và để password là `*` để chọn đó làm điểm inject. Ta sử dụng command này `sqlmap -r blind.txt -dbs --technique=B --string="admin"`

–technique là B(Boolean-Based Blind SQL injection), –string là admin để nó bắt chuỗi admin khi câu lệnh là đúng. Kết quả trả về:





