# Làm sao để sử dụng Ruby Sequel Gem
Sequel là gì?
Sequel là một gem cho phép bạn truy cập vào cơ sở dữ liệu của bạn, không yêu cầu Rails.
Bạn có thể kết nối cơ sở dữ liệu của bạn theo nhiều cách khác nhau.
#### Chủ yếu là 2 con đường:
 - Gửi các dòng lệnh cơ bản
 - Sử dụng ORM
Ở đây chúng ta sử dụng SQL và cơ sở dữ liệu MYSQL
```.env
    require 'mysql2'
    client  = Mysql2::Client.new(host: "localhost")
    results = client.query("SELECT * FROM users WHERE age > 21")
```
- Kết quả?
- Một mảng băm với cơ sở dữ liệu.
##### Bây giờ:
- Nếu bạn muốn làm việc với dữ liệu hướng đối tượng, chúng ta sẽ cần phải chuyển các kết quả thành đối tượng.
- Đó là một phần quan trọng của những gì mà ORM làm.
##### ORM là gì?
- ORM là viết tắt của "Object-Relational-Mapping".
- Sequel, được đặt theo tên của SQL ( một ngôn ngữ truy vấn cho cơ sở dữ liệu), là một ORM.
- Hãy khám phá cách sử dụng nó nhé!
##### Ví dụ kết nối với một cơ sở dữ liệu.
- Bước đầu tiên để bắt đầu sử dụng Sequel là kết nối với một cơ sở dữ liệu.
```.env
    require 'sequel'
    DB = Sequel.sqlite('/tmp/testing.db')
```
- Điều này tạo một đối tượng `Sequel::Databases` và gán nó cho `DB`.
- Chúng ta sử dụng cơ sở dữ liệu SQLite3 ở đây.
- Có các loại cơ sở dữ liệu khác:
    - Sequel.postgres
    - Sequel.mysql2
    - Sequel.oracle
- Điều gì sẽ xảy ra tiếp theo. Chúng ta cần tạo một bảng để lưu trữ dữ liệu.
```.env
    unless DB.table_exists?(:fruits)
        DB.create_table :fruits do
            primary_key :id
            column :name, String
            column :amount, Integer
        end
    end
```
- Đây là migration mà bạn có thể sử dụng, nhưng nó mà tuỳ chọn. Bây giờ chúng ta hãy sẵn sàng bắt đầu thêm đối tượng và
truy vấn cơ sở dữ liệu.
##### Cách sử dụng dữ liệu Sequel
- Bạn cần sử dụng đối tượng dataset để tương tác với một bảng cụ thể trên cơ sở dữ liệu.
```.env
  table = DB[:fruits]
  # Sequel::SQLite::Dataset
```
- Bây giờ chúng ta cần thêm 1 số record bằng phương thức `insert`
```.env
    table.insert(name: 'Orange', amount: 10)
    table.insert(name: 'Apple', amount: 2)
    table.insert(name: 'Banana', amount: 7)
```
- Hãy đếm nhé!
```.env
  table.count
  # 3
```
- Hãy lấy tất cả các bản ghi trong bảng dữ liệu nhé!
```.env
  table.all
  # [
  #   { id: 1, name: "Orange", amount: 10 },
  #   { id: 2, name: "Apple", amount: 2 },
  #   { id: 3, name: "Banana", amount: 7 }
  # ]
```
- Điều này như một mảng các hash, điều đó chính xác là điều này.
- `Sequel` cho phép bạn truy vấn đến cơ sở dữ liệu mà không cần `model`, nhưng chúng ta có thể sử dụng models.
- Đây là cách ...
##### Đây là cách sử dụng Models trong Sequel
- Một model `Sequel` trông rất giống với một model `ActiveRecord`
```.env
  class Fruit < Sequel::Model
  end
``` 
- Models hoạt động chính xác như một tập dữ liệu, chúng bao bọc các kết quả trong lớp model.
```.env
    Fruit.first
    # Fruit @values={:id=>1, :name=>"Orange", :amount=>10}
```
- Chúng ta có thể sử dụng chúng như phương thức `Sequel::Dataset`.
- Vì vậy mà không có magic ở đây.
```.env
    Fruit.map(:name)
    # ["Orange", "Apple", "Banana"]
    Fruit.where(name: "Apple").or(amount: 10).map(:name)
    # ["Orange", "Apple"]
    Fruit.first[:amount]
    # 10
```
##### Sequel với ActiveRecord
- Bây giờ chúng ta đã học được `Sequel` tuyệt vời như thế nào, bạn có thể tự hởi nó như thế nào khi so sánh với `ActiveRecord`
- `ActiveRecord` là ORM mặc định trong rails, và Rails thực sự thích các quy ước.
- Vì vậy không nên thử thay thế `ActiveRecord` bằng `Sequel` trong ứng dụng Rails.
- Nếu bạn sử dụng các Framework khác như Sinatra, thì `Sequel` là một lựa chọn tốt.
##### The end!
