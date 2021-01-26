# Rake trong Ruby là gì và nó làm việc như thế nào
Rake là một trình chạy nhiệm vụ phổ biến trong Ruby.
Task là gì?
    - Tạo bản sao lưu cho dữ liệu của bạn
    - Chạy test của bạn
    - Thu thập và báo cáo thống kê
- Đây là các vụ nhỏ nếu không có Rake sẽ nằm rải rác ở tất cả các nơi khác nhau trong dự án của bạn.
- Rake tập trung quyền truy cập cho các tác vụ của bạn.
- Rake cũng làm cho một số việc dễ dàng hơn, chẳng hạn như tìm các tệp phủ hợp hơn với một mẫu nhất định và đã được sửa đổi gần đây
##### Một điều nữa:
- Không nhầm lẫn giữa Rake và Rack, những cái tên rất giống nhau, nhưng hoàn toàn khác nhau.
    - **Rake** là một công cụ chạy các tác vụ
    - **Rake** giúp cho các máy chủ Ruby và frameworks làm việc cùng nhau.
- Bây giờ:
- Hãy khám phá Rake chi tiết hơn.
 ##### Ai sử dụng Rake?
 - Rails!
 - Nếu bạn làm bất cứ việc gì với Rails, bạn có thể đã làm việc với câu lệnh `rake db:migrate`
 - Hoặc `rake routes`
- Đó là hành động ngay tại đó.
- Lưu ý rằng Rails, kể từ phiên bản 5.0 cho phép bạn gọi hầu hết các lệnh `rake` bằng `rails`
- Nói cách khác:
    - Bạn có thể `rails db:migrate`, nhưng `Rake` vẫn đang thực hiện công việc.
##### Làm thế nào để bạn có thể biết một tác vụ
- Ở đây là một tác vụ Rake đơn giản
```.env
    desc "Print reminder about eating more fruit."
    task :apple do
        p 'eat more apples'
    end
```
- Bạn có thể đẩy code vào trong file có tên `Rakefile`, hoặc nếu bạn sử dụng trong Rails, bạn có thể lưu nó dưới dạng `lib/tasks/apple.rake`
```.env
    
```
