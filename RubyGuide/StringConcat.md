# String Concatenation và Interpolation trong Ruby
Kết hợp nhiều string là điều mà bạn phải làm thường xuyên trong Ruby
Nhưng làm thế nào bạn có thể làm được điều đó.
###1. Có 2 cách để làm được điều đó:
-   Nối chuỗi
-   Chuỗi nội suy
###2. Nối chuỗi
```.env
    a = 'Nice to meet you'
    b = ', '
    c = 'Do you like blueberries?'
    a + b + c
    # "Nice to meet you, do you like blueberries"
```
- Bạn có thể sử dụng toán tử `+` để nối 1 chuỗi với 1 chuỗi khác.
- Trong trường hợp này, `a + b + c`, tạo ra một chuỗi mới.
- Bạn không cần sử dụng biến để làm cho nó hoạt động
```.env
    puts 'I like' + ' ' + 'lemon'
    # I like lemon
```
- Một tuỳ chọn khác là sử dụng toán tử `+=`.
```.env
    a = ''
    a += "test"
    a += "test"
    a += "test"
    a
    # 'testtesttest'
```
- Chúng ta sẽ bắt đầu bằng một chuỗi rỗng và chúng ta xây dựng một cái lớn hơn bằng cách thêm vào nó.
- Nhưng ở đây có một vấn đề với nó là ... Nó rất chậm
- Vấn đề này sẽ được giải quyết ở phần tiếp theo.
###3. Ruby nối chuỗi.
- Bạn có thể sử dụng Ruby phương thức `concat` để kết hợp chuỗi hiệu quả.
```.env
    str = ''
    str.concat('a')
    str.concat('a')
    str
    # 'aa'
```
- Nó nhanh bởi vì nó sẽ thay đổi `str`, thay vì tạo mới một cái mới.
- Nhưng nó không giống như `+=` ...
- Có cách khác không?
- Bạn có thể dùng phương thức `<<`, nó là một cách gọi khác là `concat`.
```.env
    str = ''
    str << 'a'
    str << 'a'
```
- Bạn có thể làm như sau:
```.env
    str = ''
    str << 'a' << 'a'
```
- Chỉ có một vấn đề nhỏ.
- Nếu muốn bạn có thể kết hợp string với giá trị, nhưng một giá trị không phải là một string, bạn sẽ nhận được 1 kết quả không mong muốn.
```.env
    "" + 1
    # TypeError: no implicit conversion of Fixnum into String
    "" << 1
    # "\x01"
``` 
- Giải pháp là gì??
- Sử dụng phương thức to_s đổi tất cả đối tượng thành đối tượng chuỗi
```.env
    "" + 1.to_s
```
- Tất nhiên, nó sẽ xấu đi nhanh chóng, vì vậy cũng có thể sử dụng 1 công cụ khác.
###5. Làm thế nào sử dụng chuỗi nội suy trong Ruby.
- Chuỗi nội suy hoặc hợp nhất biến trong chuỗi là 1 kỹ thuật mạnh mẽ. Nó cho phép bạn tạo một chuỗi mới.
```.env
    age = 33
    name = 'Jesus'
    "Hello, my name is #{name} & I'm #{age} years old."
```
- Chúng ta có thể thêm `name` & `age` vào bất cứ đâu trong chuỗi, với điều kiện phải khao báo chúng trước chuỗi.
- Làm cách này Ruby sẽ chuyển tất cả các đối tượng thành chuỗi cho bạn mà không cần dùng đến phương thức `to_s`.
###6. Prepending Strings
- Có một cách khác cũng có thể kết hợp các chuỗi với nhau.
- Thay vì kết hợp các biến và chuỗi nhỏ hơn thành một cái gì đó với chuỗi nội suy.
- Hoặc thêm vào cuối với `<<` & `concat`.
- Bạn cũng có thể dùng `prepend`!
```.env
    str = ""
    str.prepend("1")
    str.prepend("2")
    str.prepend("3")
    # "321"
```
- Nếu bạn nghĩ `append` là một phương thức, thì không. Nó dành cho arrays

##### The end! ...
