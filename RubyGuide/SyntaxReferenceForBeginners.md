# Cú pháp tham khảo cho người mới bắt đầu

I, Mục lục
1. Giới thiệu


- Học Ruby có thể choáng ngợp vs rất nhiều thứ bạn phải nhớ, nên bài viết naỳ sẽ giúp bạn tổng hợp những thứ cần nhớ khi mới bắt
đầu. Chúng tá bắt đầu thôi

II, Tìm hiểu

 1 . String
 -  Là chuỗi các ký tự bên trong dấu (""), hoặc dấu ngoặc đơn (''), sử dụng để thể hiện văn bản và dữ liệu

 ```
 "Tôi thích code"
 'Code thích tôi'
 ```

 - Các phương thức quan trọng thường dùng vs String:
    -   size
    -   include?
    -   Empty?
    -   gsub
    -   split

 2 .  Hashes:
 - Một Hash ({}) là cấu trúc dữ liệu cặp khóa - giá trị (key - value). Và sử dụng nó như 1 từ
 điển. Bạn có thể truy cập từng phần tử bằng khóa của chúng, Khóa là duy nhất.

 ```
 # Create
 h = { a: 1, b: 2, c: 3 }
 # Access
 h[:a]
 # Set
 h[:test] = 10
 ```
 - Chú ý khi tạo Hash thì bạn dùng ngoặc nhọn ({}), Nhưng khi tìm kiếm hay truy xuất phần tử thì phải đùng ngoặc vuông [].
 Điều tưởng chừng như đơn giản này có thể khiến nhiều người mới bối rối vì vậy hãy ghi nhớ nó.

 - Phương thức quan trọng trong Hash:
    - key?
    - fetch
    - new
    - merge
    
 3 . Symbol
 - Là một chuỗi tĩnh được sử dụng để nhận dạng, chúng luôn luôn bắt đầu bằng dấu ":".
 - Khi sử dụng trong ngoặc ngọn, mặt có dấu 2 chấm được đảo ngược:

 ```
 { abc: 1 }
 ```
 - Đây là ký hiệu của symbol ```:abc```
 
 4 . Array
 -  Là một đối tượng dùng để biểu diễn một danh sách của đối tượng.
 -  Một mảng có thể chứa tất cả các loại đối tượng
 ```a= [1, ""abc, []```, có thể chứa cả mảng khác
 -  Bạn có thể truy cập bằng chỉ mục (index) của chúng (a[0]) và mảng chồng với ```a[0][0]```

 ```
 a = []
     a << 10
     a << 20
     a << 30
     a
     # [10, 20, 30]
 ```
-   Phương thức quan trọng:
    -   size
    -   empty?
    -   push/pop
    -   join
    -   flatten
    
5 . Enumerable
-   Một module của Ruby sử dụng để lặp lại các phần tử của bất kỳ lớp nào từng triển khai tưng phương thức
như: Array, Range và Hash
-   Phương thức quan trọng:
    -   map
    -   select
    -   inject
    
6 . File
-   Một lớp sẽ giúp bạn làm việc với file trong Ruby. Bất cứ điều gì thao tác với file như: đọc, ghi, lấy thông tin file và kích thước file.
    ```
    File.read("/tmp/test.txt")
    ```
-   Phương thức quan trọng:
    -   write
    -   read
    
7 . Regular Expression
-   Nếu bạn muốn tìm kiếm các mẫu, chuỗi, một cái j đó cụ thể trong chuỗi, khi đó Regular Expression có thể là thứ bạn muốn tìm kiếm
-   Chúng có thể dùng để xác thực địa chỉ email hoặc số điện thoại, hoặc là để trích xuất thông tin từ văn bản.

```.env
Ví dụ: 
"aaaa1".match?(/[0-9]/)
# true
"".match?(/[0-9]/)
# false
```
8 . Ruby gem và Bundler
-   Ruby gem là gói có thể tải vè sử dụng trong chương trình ruby
-   Các gói này sẽ cung cấp cho bạn chức năng mới
-   Ví dụ trong Rail, bạn có thể dễ dàng thêm xác thực với gem Devise, hoặc phân trang với gem Kaminari

9 . Lớp và lập trình hướng đối tượng
-    Ruby là một ngôn ngữ lập trình hướng đối tượng. Chúng ta có thể nghĩ mọi thứ như một đối tượng. Đối tượng được tạo ra từ các thiết kế và các lớp.
-    Đối tượng có thể biết mọi thứ và làm mọi thứ. Bạn sẽ yêu cầu đối tượng làm mọi thứ với các phương thức

10 . Các loại biến
-   Mỗi biến là một nhãn cho một đối tượng mà nó có thể sử dụng để truy cập đối tượng đó. Quá trình liên kết biến với một đối tượng được gọi là gán biến
```.env
a = 1
```
- Chúng ta có thể sử dụng các loại biến khác nhau trong Ruby

-  Danh sách các biến:
    -   Biến local (something)
    -   Biến instance (@something)
    -   Biến constants (Something/SOMETHING)
    -   Biến global ($something)
-  Sự khác biệt giữa các biến là phạm vi có thể truy cập chúng.
- Chúng ta có thể tạo đối tượng với một kiểu cú pháp đặc biệt, ký hiệu (%). Đây là phím tắt có thể giúp ta tiết kiệm được thời gian.
```.env
array_of_strings = %w(apple orange coconut)
array_of_symbols = %i(a b c)
string = %q(things)
regular_expression = %r([0-9])
```

- %w -  tạo một mảng strings
- %i - tạo một mảng symbols
- %q - tạo một chuỗi mà không sử dụng dấu nháy
-  Ký hiệu % cũng có thể sử dụng như toán tử

11 . Dấu ngoặc đơn ()
-   Dấu ngoặc đơn và dấu ; không bắt buộc trong Ruby nhưng chúng có thể được sử dụng.
-   Quy tắc cơ bản:
    -   Không sử dụng dấu ngoặc đơn khi định nghĩa một phương thức không có tham số truyền vào => `def foo`
    -   Sử dụng dấu ngoặc đơn khi có tham số truyền vào => `def foo(a, b, c)`
    -   Sử dụng dấu ngoặc đơn khi bạn muốn thay đổi độ ưu tiên hay ưu tiên trong phép tính (toán tử)
        => ```(a.size + b.size)*2```
        
12 . Định nghĩa phương thức
```env
def backup_database
  # method body
end
```

13 . Định nghĩa class
```.env
class Fruit
# methods
end
```
14 . Toán tử 3 ngôi

``` true ? "yes" : "No"```

15 . Chuỗi nội suy

```.env
fruit = "orange"
puts "I have an #{fruit}. Would you like a slice of it?"
```

16 . each với khối

```.env
[1,2,3].each do |n|
  puts n
end
```
17 . If / Else
```.env
n = 20
if n > 1
  puts "Greater than 1"
else
  puts "Less than 1"
end
```
18 . Case When
```.env
case 20
when 1..20
  puts "Between 1 & 20"
when 21..40
  puts "Between 21 & 40"
else
  puts "Not within a valid range"
end
```
- Ở đây 1..20 là một đối tượng Range

# The end ...
