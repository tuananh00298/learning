## Xây dựng API Rails với GraphQL

1. Giới thiệu
- Thiết kế API là một vấn đề rất phổ biến hiện nay , với sự bùng nổ của ứng dụng hiện nay thì API trở thành tiêu chí chính để trao đổi dữ liệu giữa server và client
- Một vấn đề lớn gặp phải khi phát triển API là cấu trúc request và reponse. Giả sử khi xây dựng ứng dụng mạng xã hội cần thông tin của một user và những thứ có liên quan đến user đó, hay filter theo 1 loạt các điều kiện cần thiết thì cách duy nhất là truyền nhiều params:
    - ví dụ: ```GET /users/123?full=true&with_followers=true&light_followers=true```  --> điều này trở thành điều nhức nhối của rất nhiều lập trình viên.
- Nếu như ứng dụng của bạn đơn giản thì đó chỉ là cơn đau đầu nhưng khi nó được sử dụng ở những ứng dụng phức tạp như google, Facebook thì bạn nghĩ điều gì sẽ xảy ra. Cơn đau đầu giờ đã trở thành ác mộng

- Một trong những giải pháp để giải quyết vấn đề đó là GraphQL - Một ngôn ngữ truy vấn (do Facebook tạo ra) -> đó là cách họ xử lý dữ liệu
- Là cách client lấy dữ liệu cần thiết ở server.

- Quay lại ví dụ trước Ứng dụng của bạn gửi yêu cầu 1 số dữ liệu về 1 người dùng cụ thể nhưng chỉ cần thông tin cơ bản của 20 người theo dõi đầu tiên của anh ấy. GraphQL sẽ gửi dữ liệu như sau:

```
{
 user(id: 123) {
   id
   username,
   email,
   bio,
   profile_picture,
   followers(size: 20) {
     id
     username,
     profile_picture
   }
 }
}
```
- Sau đó API triển khai giao thức GraphQL sẽ phản hồi dữ liệu dạng JSON như sau:

```
{
     "user": {
       "id": 123,
       "username": "foo",
       "email": "foo@myapp.com",
       "bio": "I'm just a sample user, nothing much to say."
       "profile_picture": "https://mycdn.com/somepicture.jpg",
       "followers": [
           {
           "id": 134,
           "username": "bar",
           "profile_picture": "https://mycdn.com/someotherpicture.jpg"
         },
         {
           "id": 153,
           "username": "baz",
           "profile_picture": "https://mycdn.com/anotherpicture.jpg"
         },
   
         // and another 18 followers at maximum
       ]
     }
   }
```
Tiếp theo chúng ta sẽ tìm hiểu xem làm như nào để triển khain GraphQL trong ứng dụng Rails.
# To be continue ...
