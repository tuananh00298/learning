# Chức năng, Phương thức và những cải tiến mới trong Ruby 2.7

I. Introduce
  - Đây sé là phiên bản 2.x cuối cùng, vì năm tới chúng ta sẽ có bản Ruby 3.0
  
II. Các phương thức

1. Tally
  - Phương thức đếm tất cả các phần tử trong mảng và trả về một hash với số lượng của chúng

    ```%w(a b c b b c a).tally```

  - Thì kết quả của nó sẽ là: ```{'a'=>2, 'b'=>3, 'c'=>2}```

2. Filter_map
  - Là phương thức kết hợp giữa `select` & `map` là một.
  - ####why?
    + Bởi vì lọc danh sách sau đó tương tác với kết quả:
     ```
        (1..8).select(&:even?).map {|n| n ** 2}
        # OR 
        (1..8).map{ |n| n ** 2 if n.even? }.compact
     ```
  - Chúng ta có thể sử dụng cả 2 nhưng tôi sẽ nghiêng về phía trường hợp 1 vì nó rõ ràng hơn.
  - Nhưng giờ phiên bản 2.7 ta có thể thực hiện điều này 1 lần
  ```
    (1..8).filter_map { |n| n ** 2 if n.event?}
    # [4, 16, 36, 64]
  ```
3. Enumerator#produce
 - Một cách để tạo ra chuỗi k bao giờ kết thúc
 ```.env
    Enumerator.produce(4, &:next).take(5)
    # [4, 5, 6, 7, 8]
```
 - có 2 tham số truyền vào nó sẽ lấy từ tham số đầu tiên liên tiếp cho đến điều kiện kết thúc. Ở ví dụ trên chúng ta sẽ lấy
 từ 4 đến liên tiếp lấy 5 phần tử.
 
III. IRB được nâng cấp ở mặt giao diện
 - Sửa được nhiều dòng (xoá sửa được các dòng trước trong block).
 - Đánh dấu cú pháp.
 - Lưu lịch sử được bật mặc định.
 - Tự động hoàn thành câu lệnh tính theo mặc định.
 - Tích hợp RDOC
