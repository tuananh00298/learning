# Zeitwerk & Module Autoloading trong ruby

Bài viết này sẽ giải thích Class và Module autoloading trong Ruby. Bao gồm Gem 'Zeitwerk' với 1 phần trong Rails 6

###1. Bắt đầu với 1 câu hỏi
- Làm như thế nào chúng ta có thể kết hợp nhiều tệp mã nguồn thành 1.
- Chúng ta sẽ dùng `require` (Yêu cầu, Bắt buộc), yêu cầu 1 file sẽ làm cho Ruby đọc nó và chạy các đoạn code bên trong. Nhưng nếu như chúng ta có 1 danh sách file bắt buộc, chúng ta có thể không cần tất cả chúng.
- Ít nhất không phải ngay bây giờ, `Làm như thế nào chúng ta có thể tải tệp khi cần để, chúng ta có thể khởi động ứng dụng nhanh hơn?`.

--> Đó là nơi mà `autoloading` được thể hiện.

###2. Làm như thế nào để sử dụng Autoloading.
- Ruby có xây dựng 1 chức năng `autoloading`. Nó dựa trên các cơ chế (`constants`)
- Khi một hằng số như `Coconut` được tìm thấy lần đầu tiên, Ruby sẽ cố gắng tải tệp liên kết của nó.
- Nhưng đầu tiên, bạn cần đăng ký cho hằng số với tên tệp,
```.env
    autoload :Coconut, "lib/coconut.rb"
```
- Bây giờ Ruby biết nơi để tìm bất cứ khi nào bạn muốn sử dụng `Coconut` Class hoặc Module. Nhưng bạn biết điều gì sẽ tốt hơn không?
- Thử tưởng tượng nết bạn không đăng ký hằng số thủ công và Ruby biết chính xác nơi đâu để tìm mọi thứ.

--> Đây chính xác là những điều `Rails` làm, nhưng trước Rails 6 không sử dụng cơ chế `autoload`.

###3. Autoloading trong rail TRƯỚC Rails 6.
- Tôi không hoàn toàn hiểu làm như thế nào nó làm việc, nhưng Rails sử dụng `const_mising`, thay vì `autoload` tự động tìm và tải tệp vào dự án của bạn.
##### Tại sao điều đó lại hiệu quả?
- Bởi vì nó có nghĩa là bạn không phải sử dụng `require` trong dự án Rails của mình.
- Quy ước về cấu hình..
- Tất cả sẽ diễn ra tự động nếu như bạn tuân theo các nguyên tắc.
- ##### Các nguyên tắc đó là:
    -   Đặt tên file ở nơi có thể tìm thấy nó (như định nghĩa bởi: ActiveSupport::Dependencies.autoload_paths).
    -   Định nghĩa một `Class` / `Module` cho mỗi file.
    -   Tên của tệp sau tên lớp, sử dụng dấu gạch dưới(UsersController => users_controller)
- Bất cứ khi nào code của bạn tham chiếu đến bất cứ tên lớp nào trong sô này, Rails sẽ biết tìm chúng ở đâu.
##### Rất hữu ích đúng không nào.....

###4. Autoloading trong Rails sau Rails 6.
- Rails 6 sẽ giới thiệu 1 cơ chế tải liên tục mới. Nó gọi là Zeitwerk.
##### Điều này khác với tự động tải Rails theo kiểu 'cổ điển' như thế nào?
- Không như `const_missing` được sử dụng ở phiên bản Rails cũ hơn, thiếu thông tin, lồng module, dẫn đến một số hạn chế
- Zeitwerk sử dụng Ruby's `autoload`.
- Việc triển khai mới này tuân theo các quy ước đặt tên tệp tương tự.
- Trong thực tế: Zeitwerk sử dụng tên file để suy ra tên lớp, `users_controller` => `UsersController`.
- Mà hoàn toàn trái ngược trước đây, bạn vẫn có thể sử dụng `autoload_paths`, nhu7ưng bạn chỉ cần add BASE paths, như `"/lib"`, thay vì `"/lib/**"`
#####Tại sao?
- Đó là cách hoạt động với ` namespaces & nesting`
- Nếu bạn có `"lib"` trong autoload, và bạn muốn định nghĩa `"Fruit::Orange"`, sau đó tên file phải là `"lib/fruit/orange.rb"`.
- Nhưng néu bạn có `"lib/fruit"` trong autoload, sau đó bạn phải sử dụng một thư mục khác, vì vậy tên tệp của bạn phải thành `"lib/fruit/fruit/oảnge.rb"` cho cùng 1 lớp.




