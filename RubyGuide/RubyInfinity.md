# Ruby Infinity: Nó làm việc như thế nào và tại sao nó quan trọng

`Infinity trong Ruby` là gì?
- Nó là một thứ có điểm đầu nhưng không có kết thúc.
- Trong Ruby, chúng ta có thể diễn đạt khái niệm vô cùng này bằng hằng số `Float::INFINITY`.
- Bạn có thể tự hỏi làm thế nào mà điều này hữu ích. Chúng ta hãy đi đến ví dụ để có thể hiểu rõ hơn.
### Infinity như một kết quả của phép tính số học.
- Ruby trả về một đối tượng `Infinity`, là kết quả của phép tính nhất định
- Ví dụ:
- Bạn có thể quan với lỗi chia cho 0.
### Nó đây:
```.env
    1/0
    # ZeroDivisionError: divided by 0
```
Nhưng ...
-   Nếu bạn sử dụng float, bạn sẽ nhận một cái gì đó giống như:
```.env
1/0.0
# Infinity
```
`Infinity`! - Nhưng đó không phải là tất cả. Nếu bạn thử chia 0 cho 0.0, sau đó bạn sẽ nhận được một giá trị đặc biệt khác.
### Take a look:
````.env
0/0.0
# NaN
````
##### NaN này có nghĩa là gì?
- Nó có nghĩa là `Not a Number`, và như tôi biết, nó là nơi duy nhất trong Ruby cọn sẽ tìm thấy giá trị đó.
- Nó là một phần của IEEE 754 Specification, nó giải thích cách hoạt động của phép toán `floating-point`
- Một số toán tử có liên quan: 
    -   nan?
    -   finite?
    -   infinite?
- Bạn có thể sử dụng những phương thức này để kiểm tra các giá trị đặc biệt.
- Phương thức này có tồn tại ở Floats
- Trước Ruby 2.4 bạn cũng có thể sử dụng `finite?` và `infinite?` với `Integers`.
##### Bạn có thể tạo Ranges Infinite
- Điều đó thật thú vị, chúng ta hãy xem thêm các ví dụ.
- Ở đây là một range vô cùng:
```.env
    (1..Float::INFINITY).take(10)
```
- Điều này có thể hữu dụng khi chúng ta không biết điểm kết thúc của `range`.
- Có một vấn đề, nó chỉ làm việc với những số lấy làm giá trị bắt đầu:
```.env
    ("a"..Float::INFINITY)
    # ArgumentError: bad value for range
``` 
- Giải pháp ở đây là gì?
- Với Ruby 2.6, bạn có thể là nó như sai:
```.env
    ("a"..).take(5)
    # ["a", "b", "c", "d", "e"]
```
- Đó không phải là một sai lầm. Range này không có giá trị cuối, chỉ bắt đầu bằng giá trị "a", sau đó là ".." sẽ làm cho nó không có điểm cuối.
- Đó là cú pháp mới. Sử dụng Ruby 2.6+ nếu bạn muốn nó làm việc.
##### The end ...
 
