# Cú pháp tham khảo cho người mới bắt đầu

I, Mục lục
1. Giới thiệu


- Học Ruby có thể choáng ngợp vs rất nhiều thứ bạn phải nhớ, nên bài viết naỳ sẽ giúp bạn tổng hợp những thứ cần nhớ khi mới bắt
đầu. Chúng tá bắt đầu thôi

II, Tìm hiểu
 1. String
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

 2.  Hashes:
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
 3. Symbol
 - Là một chuỗi tĩnh được sử dụng để nhận dạng, chúng luôn luôn bắt đầu bằng dấu ":".
 - Khi sử dụng trong ngoặc ngọn, mặt có dấu 2 chấm được đảo ngược:

 ```
 { abc: 1 }
 ```
 - Đây là ký hiệu của symbol ```:abc```
 4. Array
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
5. Enumerable
-   Một module của Ruby sử dụng để lặp lại các phần tử của bất kỳ lớp nào từng triển khai tưng phương thức
như: Array, Range và Hash
-   Phương thức quan trọng:
    -   map
    -   select
    -   inject
6. File
-   Một lớp sẽ giúp bạn làm việc với file trong Ruby. Bất cứ điều gì thao tác với file như: đọc, ghi, lấy thông tin file và kích thước file.
    ```
    File.read("/tmp/test.txt")
    ```
-   Phương thức quan trọng:
    -   write
    -   read
