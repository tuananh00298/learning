(# How to Use Logs in Ruby to Track Events & Error Message
Ghi logs(nhật ký) là một tiến trình lưu lại thông tin xung quanh những gì ứng dụng Ruby đã và đang làm
Nó rất hiệu quả, cả ở môi trường phát triển(development) và môi trường sản xuất(production)
###1. Tại sao?
- Bởi vì mục đích của việc ghi log là thu thập thông tin về những gì đã xảy ra, những gì đúng và những gì sai trong quá trình ứng dụng(App) của bạn đang hoạt động.
###2. Các loại thông tin có thể ghi lại:
-   Câu truy vấn SQL chậm (vậy bạn có thể tìm và sửa chúng).
-   Thông điệp lỗi (Error message)
-   Cho tiết thông tin yêu cầu(URL, Controller, params, ...)
-   Với những thông tin này, bạn có thể phán đoán và tìm ra nguồn gốc của vấn đề trong ứng dụng của mình nhanh hơn. Trong 1 ứng dụng Rails, Log có thể được lưu trữ trong thư mục `/log`.
-   Trong môi trường phát triển, file `dovelopment.log` được sử dụng và bạn thấy log có luôn trong terminal khi chạy lệnh `rails s`.
--> Bây giờ hãy học cách làm thế nào có thể ghi `log` trong Ruby và sử dụng lớp(class) `Logger`.
###3. Logger class
-  Ruby Logger Class sẽ cung cấp cho bạn cách tạo ra logs với 1 định dạng mặc định và các mức độ lỗi khác nhau.
-  ##### Làm cách nào để tạo ra lỗi:
```.env
    logger = Logger.new("my_log.txt")
```
- Đoạn code trên sẽ ghi nhật ký vào file `my_log.txt`
- Nếu bạn muốn hiển thị lỗi trên terminal, nơi ứng dụng đang chạy, bạn có thể truy cập vào `STDOUT`
- ##### Giống như:
```.env
logger = Logger.new(STDOUT)
```
- Việc ghi log có thể có nhiều mức độ khác nhau, tuỳ thuọc vào mức độ quan trọng của chúng.
- ##### Các mức độ đó là: 
    -   DEBUG
    -   INFO
    -   WARN
    -   ERROR
    -   FATAL
    -   UNKNOWN
- Mỗi cấp độ sẽ có những phương thức thể hiện tương ứng.
- Nếu bạn muốn tạo log (info), `cách tạo` nó sẽ là: 
```.env
logger.info("Tôi sẽ ghi log info vào đây cho bạn!")
```
- Đoạn code này thực tế sẽ trông như sau:
```.env
# I, [2019-08-08T19:22:00.152079 #642]  INFO -- : Tôi sẽ ghi log info vào đây cho bạn!
```
- Việc có các level khác nhau sẽ giúp cho bạn lọc một cách tốt hơn, vì vậy bạn có thể tìm những gì bạn muốn nhanh hơn.
- Nó cũng có thể giúp bạn xem có bao nhiêu lỗi và tin nhắn cảnh báo bạn tạo theo thời gian.
 ##### Công cụ tuyệt vời!
 ###4. Làm thế nào có thể định dạng logs của bạn.
 - Sử dụng đầu ra mặc định cho Logs của bạn là tốt bởi vì công cụ này có thể được tạo xung quanh nó và nhiều người có thể hiểu được nó làm việc như thế nào.
 - Đây là dòng lệnh khác: 
 ```.env
    # I, [2019-08-08T19:22:00.152079 #642]  INFO -- : test
```
- Một số thành phần trong đoạn log:
    -   "I" thể hiện chữ cái đầu tiên trong cấp độ logs
    -   Bên trong dấu ngoặc đơn, có thể thấy timestamp và quy trình id (PID) của truy trình tạo ra message này
    -   INFO là tên đầy đủ của cấp độ này
    -   Sau dấu ':' chúng ta có chính là thông báo thực tế được ghi lại.
- Nhưng định dạng này không phải là lý tưởng cho tất cả mọi người.
- Vì vậy Ruby cho phép chúng ta thay đổi nó:
```.env
    logger.formatter = proc { |severity, datetime, progname, msg| "#{severity}, #{datetime}, #{msg}\n" }
```
- Kết quả sẽ trả ra như sau:
```.env
    DEBUG, 2019-08-08 19:39:01 +0200, testing
```
- Bạn có thể sử dụng điều này để bổ sung thông tin hữu ích cho bạn và team của bạn.
###5. Làm thế nào để giới hạn kích cỡ của file log.
-   Logs có thể trở thành tệp thực sự lớn trừ khi bạn theo dõi kích cỡ của chúng.
-   Nếu bạn dùng hết dung lượng bộ nhớ bởi log, sau đó tất cả các điều kỳ lạ có thể xảy ra với hệ thống của bạn.
##### Có một giải pháp:
-    Bạn có thể giới hạn kích cỡ file log của bạn.
##### Cách thực hiện: 
```.env
    MEGABYTE = 1024 ** 2
    ONE_HUNDRED_MEGABYTES = MEGABYTE * 100
    logger = Logger.new("my_log.txt", 1, ONE_HUNDRED_MEGABYTES)
```
-   Tham số thức nhất là nơi chúng ta lưu log messages
-   Tham số thứ hai là số lượng file log bạn muốn giữ, và tham số cuối là kích cỡ tối đa của tệp.
###6. Đọc và lọc log.
- Bạn đã có nhật ký của mình.
- Làm thế nào bạn có thể đọc và tìm chúng khi bạn cần.
- Logs là một văn bản thuần tuý, vì vậy bạn có thể dùng bất cứ công cụ xử lý văn bản nào bạn thích.
##### Ví dụ:
`GREP`
- Nó là công cụ dòng lệnh bạn có thể sử dụng để tìm kiếm file văn bản.
- Đây là cách thực hiện:
```.env
grep INFO my_log.txt
```
- Điều này cho bạn mọi dòng đã được ghi là "INFO".
- Bạn có thể tìm kiếm những text khác trong file nhật ký của bạn khi sử dụng `grep`, chỉ cần thay thế `INFO` bằng những gì bạn tìm kiếm.
- Bất cứ những UNIX công cụ bạn có thể sử dụng được gọi là `less`
- Nó cho phép bạn có thể cuộn qua các bản ghi bằng bàn phím và nó có chức năng tìm kiếm.
- Bạn có thể sử dụng Công cụ của riêng bạn để quản lý log và tìm kiếm chúng.
- Giống như `gem 'logster'`
### The end ...
