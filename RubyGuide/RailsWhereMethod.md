# Cách sử dụng phương thức Rails Where
 Trong Rails bạn có thể truy vấn trong cơ sở dữ liệu thông qua model của bạn để truy cập dữ liệu của bạn.
 Bạn có thể sử dụng phương thức `ActiveRecord`.
 Như `where`, `find`, hoặc `find_by`
#####1. Như kết quả bạn nhận được:
- Với `find_by`, một bản ghi duy nhất hoặc `nil`
- Với `where`, một đối tượng `ActiveRecord::Relation`.
- Với `find`, một bản ghi duy nhất, tìm bằng khoá chính của nó (thông thường là `id`), sẽ đưa ra một lỗi nếu không tìm thấy.
#####2. Nói cách khác 
- Nếu bạn mong muốn 1 bản ghi, sử dụng `find_by`, cho nhiều bản ghi sử dụng `where`
- Nhưng `where` có nhiều cách sử dụng, nó thường làm cho những người mới bắt đầu bối rối.
- Không vấn đề gì.
- Chúng ta sẽ xem xét các cách khác nhau mà bạn có thể sử dụng `where` trong ứng dụng của mình.
#####3. Điều kiện where cơ bản.
- Mục đích của sử dụng where là để tạo một câu truy vấn lọc thông tin trong cơ sở dữ liệu của bạn, vì vậy bạn chỉ nhận được các row trong cơ sở dữ liệu mà bạn mong muốn
######Ví dụ:
- Đưa ra một model `Book`, với `title`, `author` và `category`
- Bạn có thể tìm tất cả những quyển sách của một tác giả cụ thể, hoặc tất cả những cuốn sách theo danh sách cụ thể.
###### Câu truy vấn:
```.env
    Book.where(category: "Ruby")
```
- Câu lệnh đó sẽ trả về tất cả các quyển sách có `category` là "Ruby"
- Bạn cũng có thể kết hợp nhiều điều kiện:
```.env
    Book.where(category: "Ruby", author: "Nguyễn Văn A")
```
- Câu lệnh trả về những quyển sách phù hợp với 2 điều kiện `category` và `author`
- Và bạn có thể kết hợp `where` với 1 `scope`:
```.env
    # scope definition
    class Book
      scope :long_title, -> { where("LENGTH(title) > 20") }
    end
    # controller code
    @books = Book.long_title.where(category: "Ruby")
```
- Sử dụng 1 hash (`fruit: "apple"`), cho phép bạn kiểm tra nếu một cột bằng với cái j đó.
- Nhưng điều gì sẽ xảy ra nếu bạn không tìm kiếm với điều kiện bằng.
- Khi đó bạn sẽ phải sử dụng một chuỗi.
###4. Rails Where: lớn hơn và nhỏ hơn.
- Nếu bạn muốn kiểm tra cho điều kiện "Lớn hơn", "Nhỏ hơn", hoặc một số thứ như ...
#####Ví dụ:
```.env
    Book.where('LENGTH(title) > 20')
```
- Nếu bạn muốn phép nội suy khác:
```.env
    Book.where('LENGTH(title) > ?', params[:min_length])
```
- `?` là gọi một `placeholder` (đánh dấu điểm giữ vị trí), và nó sử dụng cho bảo mật chống tấn công "SQL injection".
- Một tuỳ chọn khác bạn có thể sử dụng tên để đánh dấu vị trí:
```.env
    Book.where('LENGTH(title) > :min', min: params[:min_length])
```
- Cách sử dụng này ít phổ biến hơn `?`, nhưng nó có ích hơn khi sử dụng nhiều giá trị nội suy trong câu truy vấn.
###5. Làm thế nào để sử dụng điều kiện Where Not hoặc Where Or.
-  Hãy xem câu truy vấn phức tạp hơn.
- Nếu bạn muốn kiểm tra câu lệnh không đúng, thì bạn có thể kết hợp vs phương thức `where` với `not`.
```.env
    Book.where.not(category: 'Java')
```
- 1 ví dụ khác
```.env
    Book.where.not(category: nil)
```
- Những gì về điều kiện `OR`
- Bạn có thể kết hợp `where` với phương thức `or` lấy tất cả các row phù hợp vs bất kỳ hai điều kiện nào trở lên.
- Nó làm việc như thế nào:
```.env
    Book.where(category: "programming").or(Book.where(category: "Ruby"))
```
- Đó là kết hợp 2 `where` trong 1.
##### Hãy thử 1 lần!
###5. Rails Where In Array
- Tất cả các ví dụ chúng ta thấy đều tìm kiếm một giá trị cụ thể. Nhưng bạn có thể tìm kiếm với nhiều giá trị. Làm thế nào sử dụng 1 array.
```.env
    Book.where(id: [1, 2, 3])
```
 - Điều này sẽ tạo ra một truy vấn "IN" và nó sẽ tìm kiếm đông thời ở tất cả các `id` này.
 ```.env
    SELECT "books".* FROM "books" Where "books"."id" IN(1, 2, 3)
```
- Bạn sẽ tìm thấy query này trong rails logs.
###6. Rails Joins Association Condition
- Nếu bạn muốn khớp dựa trên một giá trị liên kết, bạn sẽ phải cung cấp liên kết đó với phương thức `joins`.
```.env
    Book.joins(:comments).where(comments: { id: 2 })
```
- Với câu truy vấn đố bạn lấy tất cả các bản ghi `books`, có 1 comment, và có `comment` id = 2.
- Câu truy vấn phiên bản string:
```.env
    Book.joins(:comments).where("comments.id = 2")
```
- Ở đây `comments` là tên của bảng, và `id` là tên của cột.
###7. Làm thế nào để tìm kiếm trong văn bản với 1 điều kiện LIKE.
- Nếu bạn muốn triển khai 1 phương thức tìm kiếm, bạn có thể cho phép so sánh khớp 1 phần.
- Bạn có thể dùng truy vấn "LIKE".
```.env
    Book.where("title LIKE ?", "%" + params[:q] + "%")
```
- Nó sẽ tìm kiếm tất cả những `title` có văn bản tìm kiếm cung cấp bởi tham số (params[:q]), ở bất cứ đâu trong `title`.
- Truy vấn rất hiệu quả. Biểu tượng `%` là gì? - Đó là một ký tự đại diện.
### The end

