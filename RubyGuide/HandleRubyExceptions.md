.# Xử lý exception trong Ruby bằng begin và rescue
Nói về 'begin' và 'rescue' trong Ruby, thì đây là 2 từ khóa quan trọng được sử dụng để xử lý các điều kiện lỗi
#### Chúng làm việc như thế nào?
- Trước tiên bạn phải hiểu một số thứ
- Các chương trình Ruby của bạn có thể xảy ra lỗi ở nhiều chỗ khi chúng đang chạy
- Ví dụ:
    - Đọc file không tồn tại
    - Chia cho 0
    - ...
- Khi đó điều gì sẽ xảy ra, khi đó ruby sẽ không crash ngay lập tức. Bạn có cơ hội để xử lý sau lỗi.
Chúng ta sẽ gọi 'exception handling'.
- Ruby sẽ đưa cho bạn một số từ khóa để xử lý lỗi trong đoạn code của bạn
- Những từ khóa đó là `begin` và `rescue`
#### Làm thế nào để xử lý Ruby Exceptions
- Bạn xử lý lỗi trong ruby như thế nào?
- Bạn có thể bọc mã ngoại lệ bằng một khối `begin` và `rescue`
- Sau đây sẽ là cách hoạt động ...
- Phần đầu tiên, có mã mà bạn sẽ chạy và có thể tạo ra một ngoại lệ:
```
  begin
    IO.sysopen('/dev/null')
  rescue
    # ...
  end
```
- Bên trên chúng ta cố mở file với `sysopen`
- Một ngoại lệ (exception) sẽ được đưa ra nếu chúng ta không thể mở file.
- Đó là nơi `rescue` được kích hoạt.
- Đây là nơi mà bạn muốn nói những gì, bạn muốn làm những gì khi xảy ra exception.
```
  begin
    IO.sysopen('/dev/open')
  rescue
    p 'can't open file IO devive'
  end
```
- Nếu bạn muốn ghi lại lỗi này và có thể cung cấp một số loại giá trị mặc định.
- Đừng bỏ qua những lỗi
#### Xử lý nhiều Exception
- Bạn cần biết rằng `rescue` cũng có một số tham số tùy chọn:
- Những tham số đó là gì?
- Đối số này là lớp ngoại lệ mà bạn muốn `rescue`
- Nó phụ thuộc vào dòng mã mà bạn chạy
- Đối với `IO` thì ta có một số tùy chọn sau:
    - Đó có thể là `Errno::ENOENT` cho một tập tin bị thiếu
    - Hoặc `Errno::EACCES` cho quyền cập vào file
- Phần tốt nhất
- Bạn có thể xử lý nhiều ngoại lệ trong cùng một khối `begin` và `rescue`.
```
  begin
    IO.sysopen('/dev/null')
  rescue Errno::ENOENT
    p 'File not found.'
  rescue Errno::EACCES
    p 'Insufficient permissions, not allowed to open file.'
  end
```
- Nếu bạn muốn có cùng một hành động xảy ra cho nhiều lỗi.
```
  begin
    IO.sysopen('/dev/null')
  rescue Errno::ENOENT, Errno::EACCES
    p 'There was an error opening the file.'
  end
```
#### Cách đưa ra `rescue` trong các khối hoặc trong các phương thức.
- Không phải lúc nào bạn cũng sử dụng từ khóa `begin`.
- Có những trường hợp bạn có thể bỏ nó đi.
- ##### Nhưng sử dụng nó ở đâu?
    - Trong các phương thức và trong các khối
```
  def get_null_device
    IO.sysopen('/dev/null')
  rescue Errno::ENOENT
    puts "Can't open IO device."
  end
```
- Trong định nghĩa phương thức nó tự thực hiện công việc của `begin`, vì vậy bạn có thể bỏ qua nó.
- Bạn cũng có thể làm điều này đối với blocks
```
  ["a.txt", "b.txt", "c.txt"].map do |f|
    IO.sysopen(f)
  rescue Errno::ENOENT
    puts "Can't open IO device: #{f}."
  end
```
- Bây giờ ta biết một cách sử dụng `rescue` mà không cần `begin`.
#### Tìm hiều `Inline Rescue` và tại sao nó lại nguy hiểm.
- Bạn có thể sử dụng `rescue` inline.
- Trong một số trường hợp hiếm hoi thì bạn có thể thấy xử lý ngoại lệ này có ích
```
  ["a.txt", "b.txt", "c.txt"].select { |f| File.open(f) rescue nil }.map(&:size)
```
- Điều này cho phép bạn chỉ mở những tệp tồn tại và bỏ qua những tệp không tồn tại.
- Kết quả là bạn nhận được kích thước của các tệp hiện có.
- Không có ngoại lệ được đưa ra.
#### Tại sao lại xảy ra điều đó.
- Nó cho phép bạn giữ dòng code của mình ở trên một dòng.
- Đó là về nó.
- Có một `mối nguy hiểm tiềm ẩn` khi sử dụng `rescue` bởi vì bạn sẽ nhận được tất cả các ngoại lệ giảm dần từ `StandardError`.
- Đó là hầu hết các ngoại lệ.
- Tại sao điều đó lại không tốt?
    - Bởi vì tốt nhất là chỉ xử lý các trường hợp ngoại lệ cụ thể, thay vì nhiều lựa chọn trong 1 `rescue`.
    - Điều này tránh việc không tường minh exception cho các lỗi trong code của bạn.
    - Các lỗi ẩn có thể dẫn đến tất cả các hành vi không mong muốn, vấn đề cùng với nó là khó để tìm và sử chúng.
##### The end ...