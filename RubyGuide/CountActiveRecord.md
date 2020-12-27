# Count thing in ActiveRecord
- Việc đếm các bản ghi trong DB có vẻ đơn giản:
    -   Hãy tìm hiểu các phương thức đếm bản ghi trong ActiveRecord.
####1. Phương thức `count`.
-  Trong Ruby thì phương thức `count` được dùng để đếm các bản ghi trong 1 mảng. Tuy nhiên, lớp`ActiveRecord::Relation`
cũng có phương thức `count` của chính nó. Khi gọi phương thức sẽ thực hiện truy vấn `COUNT()`.
```.env
  User.all.count
  # (4.8ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => 20000
```
- Nhưng kêt qủa không được ghi nhớ, chúng sẽ truy vấn các bản ghi trong DB mỗi khi ta gọi lại `count`.
```.env
  users = User.all
  
  users.count
  # (4.8ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => 20000
  
  users.count
  # (4.3ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => 20000
```
- Như tôi đã đề cập trước đó, Ruby có 1 phương thức `count`, vì vậy nếu chúng ta sử dụng nó trong trường hợp này ta có thể 
viết như sau. Trước tiên chúng ta sẽ chuyển kết quả truy vấn sang dạng mảng sau đó sẽ gọi `count` của Ruby:
```.env
  User.all.to_a.count
  # User Load (18.8ms)  SELECT `users`.* FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => 20000
```
- Nếu gọi như vậy thì thì thời gian để đếm các bản ghi sẽ chậm hơn 4 lần so với được gọi như trên theo ActiveRecord, vì vậy chúng ta hãy chú ý về hiệu suất query nhé!
####2. Phương thức size.
-  Như phương thức `count` thì phương thức `size` cũng đều được Ruby và Rails cung cấp trong ActiveRecord. Lần này việc sử dụng 
trong ActiveRecord:Relation phức tạp hơn 1 chút vì nó hoạt động khác nhau tuỳ theo trường hợp.
###### Không load các bản ghi.
- Khi bản ghi của bạn chưa được tải, phương thức `size` sẽ thực hiện truy vấn `COUNT()` để lấy bản ghi:
```.env
  users = User.all; nil
  users.loaded? # => nil
  users.size
  # (4.8ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => 20000
``` 
#####Khi bản ghi tải rồi.
-  Trong trường hợp ngược lại, khi bản ghi của bạn được load rồi thì, cách thức của phương thức `size` như trong Ruby
và đếm các bản ghi không thực hiện truy vấn
```.env
  users = User.all; nil
  users.loaded? # => nil
  users.load
  users.loaded? # => true
  users.size
  # => 20000
```
##### Bản ghi liên kết từ counter_cache
-  Có trường hợp `size` được gọi trên các bản ghi không được tải sẽ không thực hiện truy vấn `COUNT()` đến DB.
Nó xảy ra khi liên kết (association) có tuỳ chọn `counter_cache` được định nghĩa.
```.env
  class Address < ActiveRecord::Base
    belongs_to :user, counter_cache: true
  end
  
  class User < ActiveRecord::Base
    has_many :addresses
  end
```
-  Model `User` phải định nghĩa column `addresses_count`. Điều này giữ cho bản ghi được liên kết với người dùng nhất định. 
Bây giờ khi gọi phương thức `size` nó sẽ không được tải, câu truy vấn `COUNT()` sẽ không được thực hiện.
```.env
  user = User.last
  user.addresses.size
  # => 5
``` 
-  Nếu không có tuỳ chọn `counter_cache` sẽ thực hiện truy vấn vào DB:
```.env
  user = User.last
  user.addresses.size
  # (0.3ms)  SELECT COUNT(*) FROM `addresses` WHERE `addresses`.`user_id` = 14
  # => 5
``` 
####3. Phương thức `length`.
- `ActiveRecord::Relation` không có phương thức `length` riêng vì vậy kết quả luôn phải đổi sang mảng và sau đó mới đếm.
```.env
  users = User.all; nil
  users.loaded? # => nil
  users.length
  # User Load (18.7ms)  SELECT `users`.* FROM `users` WHERE `templates`.`deleted_at` IS NULL
  # => 20000
  
  users.loaded? # => true
  users.length
  # => 20000
``` 
-  Bạn không lên gọi `length` khi các bản ghi chưa được tải vì nó sẽ tải tất cả các đối tượng vào trong bộ nhớ.
####4. Kiểm tra xem có bất cứ bản ghi nào không.
-  Điều gì sẽ xảy ra nếu chúng ta không muốn đếm các bản ghi nhưng muốn xác minh xem có bản ghi nào trong câu truy vấn không.
Chúng ta nên thận trọng ở đây. Chúng ta có 1 số phương thức thực hiện những hành động này:
    -  Sử dụng `any?` để kiểm chứng nếu bất cứ phần tử nào trong collection (bộ sưu tập) tồn tại.
    -  Sử dụng `empty?` để kiểm chứng nếu trong bộ sưu tập không chứa bất cứ phần tử nào.
    -  Sử dụng `present?` để kiểm chứng nếu phần tử trong collection tồn tại.
    -  Sử dụng các biểu thức `.size == 0`, `.count == 0` và `.length == 0`.
- Đôi với `count`, `length`, `size` ta đã tìm hiểu về chúng giờ chúng ta sẽ đi tìm hiểu các phương thức còn lại:
####5. Sử dụng `any?`.
-  Là phương thức kiểm tra mọi phần tử trong mảng, và nếu ít nhất 1 giá trị là `true` thì sau đó kết quả nó trả về `true`.
```.env
  users = User.all; nil
  users.loaded? # => nil
  users.length
  # User Load (18.7ms)  SELECT `users`.* FROM `users` WHERE `templates`.`deleted_at` IS NULL
  # => 20000
  
  users.loaded? # => true
  users.length
  # => 20000
```
-  Về hoạt động trong đối tượng ActiveRecord::Relation, `.any?` có hoạt động giống như `.size`. Nó thực hiện truy vấn khi những bản ghi không được tải,
nó hoạt động trên bản ghi được tải, và nó không phục vụ truy vấn `COUNT()` khi quan hệ đang sử dụng tuỳ chọn `counter_cache`.
```.env
  users = User.all; nil
  users.loaded?
  # => nil
  users.any?
  # (4.8ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => true
  
  users.load
  users.loaded?
  # => true
  users.any?
  # => true
```
####6. Sử dụng `empty?`
- Khi xử lý với đối tượng `ActiveRecord::Relation`, phương thức `empty?` hoạt động như `any?`. Khi bản ghi chưa được tải,
nó xử lý truy vấn `COUNT()`; nếu không, nó sẽ xác minh một tập hợp chứa ít nhất một phần tử hay không.
- Sự khác biệt chính giữa `any?` và `empty?`. Đó là phương thức `any?` đi tìm ít nhất một phần tử có giá trị là `true`,
trong khi `empty?` thì không quan tâm về giá trị của bộ sưu tập. Nó chỉ xác minh nếu bộ sưu tập có các phần tử:
```.env
  users = User.all; nil
  users.loaded?
  # => nil
  users.any?
  # (4.8ms)  SELECT COUNT(*) FROM `users` WHERE `users`.`deleted_at` IS NULL
  # => true
  
  users.load
  users.loaded?
  # => true
  users.any?
  # => true
```
####7. Sử dụng `empty?`.
- Cách sử dụng phương thức `present?` không được khuyến nghị trừ khi bạn chắc chắn các bản ghi đã được tải.
Khi bản ghi không được tải, thì tương tự như phương pháp `length`, phương pháp `present?` sẽ tải tất cả các object trong 
bộ nhớ sau đó sẽ kiểm tra sự tồn tại của phần tử:
```.env
  users = User.all; nil
  users.loaded?
  # => nil
  users.present?
  # User Load (19.2ms)  SELECT `users`.* FROM `users` WHERE `templates`.`deleted_at` IS NULL
  # => true
  
  users.loaded?
  # => true
  users.present?
  # => true
```
####8. Sự lựa chọn tối ưu nhất.
- Xem tất cả những trường hợp đã thảo luận ở trên, lựa chọn tốt nhất để đếm mọi thứ thì nên sử dụng phương thức `.size`
và nếu kiểm tra có sự tồn tại phần tử phần tử nào trong bộ sưu tập thì sử dụng `empty?` hoặc `any?`.
- Tuy nhiên trong 1 vài trường hợp không hẳn những lựa chọn trên luôn là tối ưu nhất:
```.env
  <% if @users.any? %>
    <h1>List of users</h1>
    <% @users.each do |user| %>
      ...
    <% end %>
  <% end %>
```
- Trong ví dụ trên, chúng ta sẽ thực hiện truy vấn `COUNT()`, và sau đó là câu truy vấn khác để tải các bản ghi.
Thay vì sử dụng `@users.any?`, chúng ta có thể sử dụng `@users.present?` vì điều này sẽ tải tất cả các bản ghi vào trong bộ nhớ,
nên chúng ta chỉ cần thực hiện đúng 1 câu query `SELECT *` để làm cả 2 việc trên.
####9. So sánh hiệu suất của `.count`, `.length`, `.size`.
- Trong Ruby thuần, tất cả 2 phương thức trên đều đếm tất cả các phần tử trong mảng. Câu hỏi ở đây là, phương thức nào là nhanh nhất.
- Chúng ta hãy đi tìm hiểu về chúng, kiểm tra hiệu suất của chúng, chúng ta sẽ sử dụng 2 mảng, 1 mảng với 1k phần tử, và 1k phần tử là đối tượng đơn giản.
Chúng ta sẽ kiểm tra tất cả các số trong phần tử trong mỗi mảng đó 50k lần.
- Benchmarks code:
```.env
    # test data
    array1 = Array.new(1_000) { rand(1..9) }
    
    class User; end
    
    array2 = Array.new(1_000) { User.new }
    
    # benchmarks
    require 'benchmark'
    n = 50_000
    
    # array of numbers
    Benchmark.bm do |benchmark|
      benchmark.report(".size") do
        n.times { array1.size }
      end
    
      benchmark.report(".count") do
        n.times { array1.count }
      end
    
      benchmark.report(".length") do
        n.times { array1.length }
      end
    end
    
    # array of objects
    Benchmark.bm do |benchmark|
      benchmark.report(".size") do
        n.times { array2.size }
      end
    
      benchmark.report(".count") do
        n.times { array2.count }
      end
    
      benchmark.report(".length") do
        n.times { array2.length }
      end
    end
```
- Kết quả như sau:
```.env
  .size - needed 0.002902 to count numbers and 0.002844 to count objects
  .count - needed 0.003553 to count numbers and 0.003531 to count objects
  .length - needed 0.002339 to count numbers and 0.002703 to count objects
```
- Nhanh nhất là phương thức `.length`, và chậm nhất là phương thức `.count`
