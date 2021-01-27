# Làm thế nào để lên lịch công việc trong Ruby bằng Gem Whenever

Cron là một hệ thống lập lịch công việc có sẵn trong Linux & MacOS.
Nó có thể sử dụng ở bất cứ chương trình nào ở bất cứ thời gian nào
Điều này bao gồm mã của Ruby.
Nếu có một công việc định kỳ nào đó bạn muốn chạy tự động hàng ngày, hàng tuần hoặc mỗi giờ, thì Cron có thẻ là những gì mà bạn mong muốn.
#### Ví dụ những công việc bao gồm:
- Chạy cập nhật DB hàng tuần
- Tạo báo cáo hoạt động hàng tuần của web
- Gửi lời nhắc nhở bằng email
Hãy cùng khám phá làm như thế nào để bạn có thể tạo cron làm việc cho bạn.
#### Cron cơ bản
- Mỗi người dùng có thể có một file cron nơi bạn định nghĩa các công việc bạn muốn chạy.
- Bạn có thể sửa tệp đó với câu lệnh `crontab  -e`
- Hoặc danh sách những công việc đã được tạo với `crontab -l`
```.env
    0 0 * * * /opt/rubies/ruby-2.5.1/bin/rake db:backup
```
- Cú pháp hơi phức tạp và bạn có thể muốn chỉnh sửa các tệp này mọi lúc.
- Có rất nhiều gem có thể trở lên rõ ràng hơn.

##### Làm thế nào để sử dụng gem Whenever
- Bởi vì cú pháp của cron có thể rất khó nhớ chúng ta có thể sử dụng một gem như `whenever`
- Gem này có thể giúp bạn định nghĩa cron những việc trong Ruby.
- Đầu tiên phải cài đặt gem.
##### Sau đó tạo file config bằng câu lệnh:
```.env
    wheneverize .
    # [add] config/schedule.rb
```
- Mở nó lên, bạn sẽ thấy một số câu lệnh ví dụ có sẵn, xoá chúng nếu bạn muốn.
- Giả sử chúng tôi muốn một công việc dự phòng chạy hàng giờ.
- Viết điều đó trong file config `config/schedule.rb`
```.env
    every 1.hour do
        rake 'db:backup'
    end
```
- Các tuỳ chọn bao gồm:
    -   rake (bắt đầu một công việc đã được định nghĩa trong dự án hiện tại của bạn, chú ý đó là `db:backup` không được xác định theo mặc định,
    nhưng bạn có thể thử `db:version`).
    - runner (Ruby class + method, như `Archive.backup_now`)
    - Câu lệnh (Câu lệnh hệ thống)
##### Bây giờ:
- Chạy `whenever --update-crontab` câu lệnh sẽ tạo những mục cron.
- Bạn có thể xem các công việc mới của mình với câu lệnh `crontab -l`
- Đây là những gì tôi có thể nhận:
```.env
    0 * * * * /bin/bash -l -c 'cd /home/jesus/testing && RAILS_ENV=production bundle exec rake db:backup --silent'
```
- Lưu ý rằng các tác vụ sẽ chạy trong  chế độ `production`.
- Nếu muốn thay đổi điều đó sẽ làm thế nào?
##### Bạn có thể sử dụng cài đặt:
```.env
    set :environment, "development"
```
- Điều này có trong file `schedule.rb`, ở trên đầu trang.
- Bạn có thể sử dụng câu lệnh:
```.env
    whenever --update-crontab --set environment=development
```
- Điều này có thể tạo tất cả tác vụ với `development` làm môi trường khi bạn chạy lệnh này.
##### Nhiều hơn ví dụ hơn:
- Ví dụ này cho thấy bạn có thể cụ thể hơn như nào.
- Tác vụ hàng tuần:
```.env
    every :monday, at: '6:00 PM' do
        runner "Mail.send_weekly_newsletter"
    end
```
- Ví dụ này cho thấy cách chạy cùng một tác vụ nhiều lần trong ngày.
- Chạy tác vụ 2 lần trong ngày:
```.env
    every :day, at: ['12:00 AM', '12:00 AM'] do
      command 'rm tmp/testing.txt'
    end
```
- Khi bạn có tác vụ trong file crontab của bạn (kiểm tra `crontab -l`) bạn đã sẵn sàng để làm
- Tiếp theo:
- Làm thế nào bạn biết nếu hàng chờ tác vụ làm việc đúng?
- Bạn có thể cài đặt ghi nhật ký và đọc đầu ra. Cho phép ghi nhật ký bằng cách thêm dòng dưới đây vào đàu file `schedule.rb` của bạn.
```.env
    set :output, "log/cron.log"
```
- Nhớ chạy câu lệnh `whenever --update-crontab` mỗi khi bạn thay đổi thông tin trong file
- File nhật ký vẫn trống?
- Bạn sẽ phải chờ đợi đến khi có lịch để xem có chuyện gì xảy ra không.
- Nếu bạn thấy lịch này trong nhật ký:
```.env
    /bin/bash: bundle: command not found
```
- Điều đó có nghĩa là cron không chạy cùng một phiên bản ruby của bạn. Nếu bạn sử dụng trình quản lý phiên bản Ruby.
- Hãy đảm bảo rằng nó chắc chắn tải từ `.bash_profile` file, thay vì `.bashrc`, để tác vụ cron của bạn có thể sử dụng nó.
##### Các sự lựa chọn có thể thay thế Whenever.
- Whenever là một gem duy trì tốt và nó hoạt động rất tốt.
- Nhưng những gì có thể thay thế nó
- Bạn có thể lựa chọn những thay thế dưới đây:
    - [Heroku Scheduler](https://elements.heroku.com/addons/scheduler), nó là một hàng chờ công việc miễn phí cho người dùng Heroku.
    Không linh hoạt như cron nhưng nó sẽ hoàn thành công việc
    - [Rufus Scheduler](https://github.com/jmettraux/rufus-scheduler), một gem Ruby có thể thay thế cron bằng một bộ lên lịch trong bộ nhớ.
    - [Sidecloq](https://github.com/mattyr/sidecloq), [Sidekiq-cron](https://github.com/ondrejbartas/sidekiq-cron), cả 2 gem đề bổ sung tác vụ vào trong
    bộ nhớ bạn có thể dùng.
- Chú ý cả 2 phiên bản của sidekiq đều dùng cú pháp thực tế của scheduling để lập lịch, vì vậy câu lệnh nó sẽ không đẹp như cú pháp của whenever.
- Nhưng gì tốt hơn đó là chúng có giao diện người dùng.
##### The end ...
