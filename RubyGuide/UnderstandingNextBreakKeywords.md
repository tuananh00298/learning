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
- Từ khoá `break` cũng giống như `next` nhưng nó sẽ kết thúc vòng lặp và trả về 1 giá trị, thay vì bỏ qua 1 vòng lặp. 

