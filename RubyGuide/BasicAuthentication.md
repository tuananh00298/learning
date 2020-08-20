# Giới thiệu Basic Authentication

1.Giới thiệu.
- Trong quá trình triển khai dự án, khi dựng server trên môi trường test/staging, để đảm bảo thông tin của dự án không bị lộ ra ngoài. Khách hàng có thể đưa ra một số yêu cầu bảo mật cơ bản, một trong số đó là `` Basic Authentication ``
- ``Basic Authentication`` là 1 trong những giao thức authentication phổ biến, được áp dụng rộng rãi, xuất hiện từ HTTP/1.0.
2. Cách thức hoạt động.
- ``Basic Authentication`` đóng gói username và password vào thành 1 string, ngăn bởi dấu `:`. Sau đó encode string đó bằng cách sử dụng Base64 encoding. Mặc dù kết quả của quá trình này là một chuỗi lộn xộn  tuy nhiên thực tế thì Base64 encoding hoàn toàn có thể decode dễ dàng. Mục đích của Base64 encoding vốn không phải là mã hoá bảo mật mà chỉ để làm cho thông tin username và password có thể `HTTP Compatible` do các kí tự quốc tế có thể không hợp lệ trong HTTP headears.
```
  GET /gallery/personal/images/image1.jpg HTTP/1.1
  Authorization: Basic Zm9vOmJhcg==
  ```
- Nhin lại trong ví dụ trong phần `HTTP authentication`, thực tế ví dụ này sử dụng ``Basic Authentication`` và chuỗi `Zm9vOmJhcg==` thực tế đơn giản là string sau khi Base64 encoded của string foo:bar
3. Triển khai trong ứng dụng Rails.
- Đầu tiên tạo 1 file `http_auth` trong folder controller của bạn.
```
# app/controllers/concerns/http_auth.rb
module HttpAuthConcern  
   extend ActiveSupport::Concern
   included do
       before_action :http_authenticate
   end
   def http_authenticate
       return true unless Rails.env == 'production'
       authenticate_or_request_with_http_basic do |username, password|
           username == 'username' && password == 'password'
       end
   end
end
```
- Bây giờ bạn cần include module này trong `Applicationcontroller`.
```
# app/controllers/application_controller.rb
   class ApplicationController < ActionController::Base
       include HttpAuthConcern
       # rest of your code
   end
```
- Nếu bạn không muốn kiểm tra môi trường thì bạn chỉ cần sử dụng phương thức `http_basic_authenticate_with`.
```
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
    # Có thể thêm tuỳ chọn không check basic ta có tuỳ chọn: (except: :index)
    http_basic_authenticate_with username: 'username', password: 'password', except: :index
end
```
