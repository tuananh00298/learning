# Làm thế nào để sử dụng phương thức Uniq để xoá những bản ghi trùng trong Ruby.
Với phương thức `uniq` bạn có thể xoá tất cả những phần tử bị trùng trong mảng.
- Hãy xem nó làm việc như thế nào.
###1. Bạn có 1 mảng như sau:
```.env
n = [1, 1, 2, 2, 1, 3, 4, 5]
```
- Ở đây chúng ta có số `1` và `2` bị trùng.
- Gọi `uniq` trên mảng này sẽ giúp loại đi những phần tử thừa và trả về một mảng mới với các số duy nhất.
```.env
n = [1, 1, 2, 2, 1, 3, 4, 5]
# => [1, 2, 3, 4, 5]
```
- Chú ý khi `uniq` không thay đổi `n`(Giữ nguyên mảng), vì vậy chúng ta cần gọi `uniq!`, hoặc lưu kết quả vào một mảng 
mới.
```.env
uniq_number = n.uniq
```
- Khá dễ dàng phải không?
- Nhưng có một số người không biết rằng `uniq` có một khối block.
- Với 1 `block`, bạn có thể làm những điều gì mạnh mẽ hơn.
###2. Làm thế nào để sử dụng phương thức Uniq với một khối Block.
- Khi chúng ta gọi `uniq`, nó sẽ tạo ra một Hash từ các phần tử trong array của bạn.
- Mọi phần tử trở thành một `key` trong Hash.
- Bởi vì `key` luôn uniq, chúng ta có thể lấy danh sách tất cả các key trong Hash, danh sách này sau đó sẽ trở thành một 
mảng mới có danh sách các phần tử duy nhất.
##### Bây giờ:
- Nếu bạn muốn thay đổi những gì trở lên duy nhất, bạn sẽ phải duyệt qua 1 block.
##### Ví dụ:
```.env
    fruits = %(orange apple banana)
```
`Orange` và `Banana` có những độ dài như nhau là 6 ký tự.
- Nếu bạn muốn sử dụng `uniq` giống như này:
```.env
fruits.uniq(&:size)
# ["orange", "apple"]
```
- Sau đó chúng ta sẽ xoá `banana` bởi vì nó sẽ là một bản ghi có cùng độ dài là 6 ký tự, khi chúng ta so sánh kích thước của chúng.
##### Một ví dụ khác:
```.env
    objects = [1, 2, "a", "b", :c, :d]
    objects.uniq(&:class)
    # => [1, "a", :c]
```
Điều này sẽ cho chúng ta một mảng đối tượng duy nhất theo `class`.
- Bạn đã thấy sức mạnh của điều này.
- Khi chúng ta duyệt qua block bởi `uniq`, bạn đã có thể xác định chính xác theo những quy tắc nào được coi là duy nhất.
###3. Làm thế nào để sử dụng Uniq với nhiều điều kiện.
- Bạn có thể sử dụng nhiều điều kiện!
- Giả sử bạn có một lớp `User`, với:
    -   `age`
    -   `name`
    -   `country`
- Bạn muốn chỉ có 1 người có quốc gia cũng giống như tuổi.
```.env
[david, petter, rephael].uniq {
    [person.age, person.country]
}
```
- Với đoạn code này, cả 2 điều kiện phải phù hợp trước một cái gì đó có thể được coi là duy nhất.
### Nó làm việc như thế nào?
- Không có `block`, object sẽ trở thành key của Hash. Với một block, thì giá trị thu được sẽ trở thành `key`(khoá) Hash và
đối tượng trở thành `value`(giá trị) của Hash
##### Sau đó
- Ruby sẽ lấy giá trị và trả về chúng như một mảng mới.
- Đây chỉ là chi tiết những gì `uniq` đã triển khai để có thể ra kết quả. Chúng khá thú vị phải không ???

#####Trên đây bạn có thể học được xung quanh những gì về phương thức `uniq` trong Ruby. Chính xác tại sao lại sử dụng nó,tại sao nó hữu ích, và nó làm việc như thế nào.


