Đây là challenge 

![image](https://github.com/Llam-a/BUUCTF/assets/115911041/a7c7ab49-9364-4b9c-bbf4-e2ace8bedef7)

Mình ko thầy điều gì bất thường nên bắt đầu mình viewsource

```
$cat=$_GET['cat'];
        echo $cat;
        if($cat=='dog'){
            echo 'Syc{cat_cat_cat_cat}';
        }
```

Vậy dùng GET truyền tham số cat lên và cho nó bằng “dog” thì lấy được flag

`?cat=dog` là xong

`flag{01e7e281-1394-4af3-bb17-48420186adfe}`
