# AWS xác định danh tính và quản lý truy cập cơ bản
IAM(Identity and Access Management) là một dịch vụ web, nó sẽ giúp bạn điều hướng truy cập đến tài nguyên của cho mỗi user của bạn
Bạn có thể cấu hình IAM để điều hướng ai có thể truy cập tài nguyên AWS của bạn, và những gì họ có thể làm với những tài nguyên đó
##### Why use IAM?
- Chia sẻ quyền truy cập đến một tài khoản AWS
- Mà không cần chia sẻ mật khẩu root và khoá truy cập của họ.
- Nếu không có IAM bạn phải tạo nhiều tài khoản AWS khác nhau mỗi tài khoản lại có thanh toán riêng và đăng ký các Product AWS.
hoặc các nhân viên phải chia sẻ thông tin xác thực bảo mật của một tài khoản AWS.
- Ngoài ra nếu không có IAM, bạn không thể kiểm soát các tác vụ mà một người dùng hoặc hệ thống cụ thể có thể thực hiện 
và những tài nguyên AWS gì họ có thể sử dụng
- Chi tiết quyền:
    - Người dùng có thể cài đặt quyền cho những người dùng khác
    - Cho phép bạn kiểm soát
        - Tất cả hoặc truy cập 1 phần đến mỗi dịch vụ cá nhân và tài nguyên trong 1 tài khoản
        - Quyền chỉ đọc đến tài nguyên, chẳng hạn như s3, truy cập đến thông tin thanh toán của 1 tài, nhưng không có quyền khác.
        - MFA - Multi-Factor Authentication
            - Bạn có thể thêm các xác thực nhiều yếu tố riêng lẻ cho từng người dùng
            - Điều đó có ý nghĩa rằng khi một người dùng đăng nhập, họ sẽ phải cung cấp mật khẩu hoặc key truy cập và một mã nhập một lần từ thiết bị riêng biệt
            - Khách hàng có thể cấu hình MFAs cho người dùng thông qua màn hình điều khiển, hoặc cấu hình chúng cho người dùng lập trình giống như thông qua AWS APIs, SDKs hoặc là AWS CLI
            - Một thiết bị MFA cho mỗi người dùng IAM
            - Truy cập an toàn đến tài nguyên AWS cho những ứng dụng chạy trên Amazon EC2 
    - Khách hàng không thể sử dụng IAM cho SSH keys hoặc chứng chỉ Windows RPD, người dùng phải chia sẻ SSH keys trên tài khoản AWS
Có 3 quyền bạn có thể cung cấp cho người dùng truy cập vào tài khoản của bạn
    - Truy cập thông thường, người dùng có tài khoản AWS 
        - Bạn muốn thêm người dùng dưới sự bảo vệ của tài khoản AWS của mình và muốn sử dụng IAM để tạo người dùng và tạo người dùng và quản lý quyền của họ
    - Truy cập của người dùng không phải AWS (không có tài khoản), thông qua liên kết xác thực danh tính giữa các hệ thống uỷ quyền của bạn và AWS
        - Bạn có người dùng không phải AWS trong hệ thống nhận dạng và uỷ quyền
    - Truy cập nhiều tài khoản giữa các tài khoản AWS


