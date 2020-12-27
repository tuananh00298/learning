 # Tìm hiểu từ khoá NEXT và BREAK trong Ruby

###1. NEXT
- Từ khoá **next** và **break** là một phần trong ngôn ngữ Ruby, nếu bạn muốn hiểu đầy đủ về Ruby, bạn cần biết chúng hoạt động như thế nào.
- Chúng được sử dụng trong tất cả các vòng lặp trong Ruby: **While**, **Until**, **each**.
- Khi viết vòng lặp bạn có thể muốn bỏ qua một lần lặp hoặc kết thúc sớm vòng lặp - Đó là nơi xuất hiện của từ khoá **next** **break**.
- **next** từ khoá cho phép bạn bỏ qua 1 vòng lặp
- Giả sử bạn đang lặp `string` và có một số lý do bạn không muốn đếm các `string` có số lượng size là 4 bạn có thể viết nó như sau:
```.env
    strings = ['one', 'two', 'four', 'five']
    strings.inject(0) do |sum, str|
        next if str.size == 4
        sum + str.size
    end
    # nil
``` 
- Mã này không hoạt động bởi vì `next` sẽ trả `nil` mặc định, giống như là gọi `return` mà không có giá trị trả về.
- Sử dụng `inject` sẽ lấy giá trị cuối cùng được trả lại và nó sẽ trở thành tham số truyền vào cho lần lặp tiếp theo. Nói cách khác, `sum` sẽ trở thành `nil`.
- Để giải quyết vấn đề đó đổi dòng `next` như sau:
```.env
    next sum if str.size == 4
```
- Điều này làm cho `next` sẽ trả về giá trị của `sum`

###2. BREAK
- Từ khoá `break` cũng giống như `next` nhưng nó sẽ kết thúc vòng lặp và trả về 1 giá trị, thay vì bỏ qua 1 lần vòng lặp.
- Nó có thể dùng để quay lại sớm từ 1 vòng lặp, hoặc có thể dùng để kết thúc vòng lặp không có điều kiện.
```.env
    count = 0
    loop do
      break if count == 10
      puts count
      count += 1
    end
```
 - Đoạn code trên dùng để in ra từ 1 đến 9. Một giải pháp thay thế tốt hơn là dùng `times`, hoặc `upto` (`0.upto(9)`), hoặc cũng có thể dùng `range` `(0..9)`.
 ```.env
    0.upto(9).each(&method(:puts))
```
 - Phương thức `0upto(9)` chỉ là phương thức rút gọn dùng để in ra các số `method(:puts)` -> dùng để gọi phương thức puts.
 ###3. NOTE: Tránh nhầm lẫn Next là một phương thức trong Ruby.
 - Đừng vì một cái gì đó tồn tại dưới dạng từ khoá, điều đó không có nghĩa là nó không thể sử dụng làm tên phương thức.
 - Một vài Class trong Ruby dùng `next` như 1 phương thức: như  `String` và `Integer`
 ```.env
    1.next
    # 2
```
--->> Kết quả không có gì đáng ngạc nhiên đúng không.
```.env
    "zz".next
    # "aaa"
```
- Điều đó có vè thú vị hơn 1 chút.
 #### Đoạn code trên làm việc như thế nào?
 - Mọi Class đều thực hiện phiên bản tiếp theo của nó, trong đó có chứa logic cho giá trị tiếp theo.
 - Cho `Integers`, đó chỉ là `+1`
 - Cho `String` nó tăng ký tự cuối cùng lên 1 (chữ cái tiếp theo/ +1 cho số), sau đó nếu có dấu chuyển sẽ là (9 -> 0, z -> a), nó sễ tăng ký tự tiếp theo ở bên trái.
 
#####Kết luận: 
-   Bạn đã tìm hiểu xung quanh từ khóa `next` và `break` trong Ruby, cách chúng làm như thế nào giúp bạn bỏ qua vòng lặp, hoặc dừng sớm 1 vòng lặp.

####The end.

