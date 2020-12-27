# Cách Sidekiq hoạt động
Có bao giờ bạn tự hỏi làm thế nào mà sidekiq hoạt động và điều gì xảy ra vào thời điểm bạn sắp xếp hàng chờ cho công việc và 
thời điểm nó thực hiện. Tôi đã làm, kiểm tra từng bước hoạt động trong quá trình. Trong khi đó tôi khám phá ra nhiều tính năng hữu ích của Redis
và xem nó là 1 trong những công cụ yêu thích mà tôi làm việc trong nhiều năm, nó được thiết kế như thế nào.
Hãy bắt đầu mọi thứ mà mọi nhà phát triển Rails đang sử dụng quen Sidekiq - 1 Worker đơn giản:
```.env
  class MySimpleWorker
    include Sidekiq::Worker
  
    def perform(text)
      puts text
    end
  end
```
- Bây giờ có 1 câu hỏi là có gì xảy ra trong thời điểm thực hiện dòng lệnh dưới đây và in ra dòng lệnh `I was performed!`
```.env
  MySimpleWorker.perform_async('I was performed')
```
####1. Thêm 1 công việc vào trong hàng chờ(queue)
- Khi bạn gọi 1 worker với phương thức `perform_async`, worker sẽ được xếp hàng đợi và thực thi đồng bộ thay vì thực sự thực thi ngay sau khi gọi nó.
Nếu bạn muốn thức hiện worker ngay lập tức thì bạn nên gọi `MysimpleWorker.new.perform('I was performed')` - có nghĩa là câu lệnh của bạn sẽ thực thi luôn mà không đi vào sidekiq nữa.
####2. Hash tuỳ chọn.
- Sau khi bạn gọi `perform_async` một class kế thừa Sidekiq::Worker, hash với 1 tuỳ chọn được hình thành. Hash bao gồm tham số truyền vào gồm: `worker` và tên `class`.
```.env
  { 'arg' => ['I was performed'], 'class' => MySimpleWorker }
```
- Nếu bạn muốn xâu chuỗi các queue lại với nhau thì gọi hàm `set` như sau:
```.env
  MysimpleWorker.set(queue: 'normal').perform_async('I was performed')
```
- Sau đó hash từ phương thức `set` sẽ được hợp nhất với các tuỳ chọn hàng đợi và tuỳ chọn hash của chúng tôi trông như sau:
```.env
  { "args" => ["I was performed"], "class" => MySimpleWorker, "queue" => "normal" }
```
####3. Tuỳ chọn chỉ định
- Sau khi các mục được sắp xếp hàng đợi, một validation(xác thực) sẽ thực hiện kiểm tra nếu một queue không hợp lệ được đưa ra. 
Bên cạnh đó, tuỳ chọn mặc định cũng được set - nó có nghĩa là hàng đợi `default` sẽ được chỉ định trừ khi bạn chỉ định tên hàng đợi trong định nghĩa worker hoặc sử dụng phương thức `set` để đặt hàng đợi khi gọi worker.
- Khi validate thành công, các thuộc tính theo sau được gán cho tuỳ chọn `job` là:
    - `class` - Một chuỗi đại diện cho tên lớp của worker
    - `queue` - Một chuỗi đại diện cho tên của hàng đợi mà công việc sẽ thực hiện.
    - `jid` - Một chuỗi đại diện cho `id` duy nhất cho 1 công việc nhất định. Nó được tạo ra bằng cách gọi hàm `SecureRandom.hex(12)`.
    - `created_at` - Thời gian khi tạo job. Nó sẽ được cài đặt `Timel.now.to_f` nếu không được chỉ định trong tuỳ chọn của worker.
####4. Gọi middleware
- Trước khi `jobs` được đẩy vào Redis, Sidekiq thực thi middleware đã được cài đặt. Middleware là code được cấu hình để được thực thi trước
hoặc sau khi một tin nhắn được xử lý. Một ví dụ về Middleware là công việc của sidekiq uniq đang xác minh xem hành động đó có bị trùng lặp không.
- Lớp Middleware nhận cùng các đối số đã được gán ở bước bao gồm `jid`, `args`, `class`, `created_at`.
####5. Đẩy dữ liệu vào Redis
- Khi Middleware không từ chối công việc, đây là thời điểm đẩy dữ liệu vào Redis để nó có thể được xử lý. 
2 hành động tuỳ thuộc vào trường hợp cụ thể. Bây giờ tôi sẽ mô tả trường hợp khi bạn muốn xử lý công nhân không đồng bộ càng sớm càng tốt.
Trong 2 trường hợp khi bạn muốn xử lý công việc trong khoảng thời gian ở tương lai. Bạn có thể hoàn thành việc đó khi gọi đoạn code dưới đây:
```.env
  MySimpleWorker.perform_in(5.minutes, "I was performed!")
```
- Bây giờ, hãy giả sử rằng bạn muốn thực hiện công việc càng sớm càng tốt. Công việc (tất cả các đối số được yêu cầu để thực hiện thành công, được giao trong các giai đoạn trước)
được chuyển sang định dạng JSON. Các phương pháp đó là `sadd` và `lpush`.
####6. `sadd`
- Hãy tưởng tượng rằng một mảng chuỗi, với 1 cái tên duy nhất mà bạn có thể tham chiếu đến và mảng này được lưu trữ ở Redis.
```.env
  ['default', 'high']
```
- Nếu bạn muốn thêm phần tử tiếp theo vào mảng này, trước tiên Redis sẽ xác minh xem phần tử đã cho có trong mảng chưa.
Nếu phần tử đã tồn tại thì việc bổ sung sẽ bị bỏ qua, ngược lại thì phần tử đó sẽ được thêm vào cuối mảng.
- Trong, Ruby bạn có thể sao chép các hành động bằng cách tạo lớp sau:
```.env
  class Redis
    def initialize
      @lists = {}
    end
  
    def sadd(name, item)
      @lists[name] ||= []
      return @lists[name] if @lists[name].include?(item)
  
      @lists[name] << item
      @lists[name]
    end
  end
  
  conn = Redis.new
  conn.sadd("queues", "default") # => ["default"]
  conn.sadd("queues", "default") # => ["default"]
  conn.sadd("queues", "high") # => ["default", "high"]
```
- Sidekiq đang sử dụng `sadd` phương thức trên danh sách `queses` để thêm tên hàng đợi của công việc mà nó xử lý:
```.env
  conn.sadd("queues", queue)
``` 
####7. `lpush`
- Hãy giả tưởng tượng rằng bạn có 1 mảng các chuỗi và mỗi khi bạn thêm 1 phần tử vào nó, nó sẽ được đẩy ở đâu. Đó là cách `lpush`
hoạt động của lệnh trong Redis.
- Trong Ruby, bạn có thể sao chép hành vi này bằng cách tạo lớp sau:
```.env
  class Redis
    def initialize
      @lists = {}
    end
  
    def lpush(name, *items)
      @lists[name] ||= []
  
      items.each do |item|
        @lists[name].unshift(item)
      end
    end
  end
```
- Sidekiq đang sử dụng `lpush` để đẩy công việc vào hàng đợi cho bạn.
```.env
  conn.lpush("queue:#{queue}", to_push)
```
#### --> Khi gọi thành công và công việc đã được xếp vào hàng đợi, bạn nhận được giá trị `jid` như 1 giá trị trả về.
`id` đó là duy nhất cho công việc của bạn vừa đẩy vào hàng chờ. Công việc bây giờ đã được đẩy vào hàng chờ, và nếu bạn có Sidekiq đang chạy bằng câu lệnh 
`bundle exec sidekiq`, nó sẽ chọn nó lên đầu và bắt đầu xử lý nó. Quá trình chọn 1 công việc sẽ diễn ra như thế nào, tôi sẽ giới thiệu trong bài bên dưới.
####8. Chọn công việc trong queue.
- Tôi đề cập ở trên rằng công việc của bạn sẽ được chọn ngay sau khi bạn chạy câu lệnh `bundle exec sidekiq`.
Nhưng điều gì thực sự được kích hoạt sau đó? Hãy cùng khám phá.
####9. Khởi tạo 1 đối tượng CLI
- Đầu tiên khi câu lệnh được gọi là thực hiện phương thức `Sidekiq::CLI.instance`. Lớp `CLI` thì có chứa `Singleton` module điều đó có nghĩa là bằng cách gọi`.instance` trên 1 class,
chúng ta chắc chắn rằng sẽ chỉ có 1 instance được khởi tạo.
- Bạn có thể kiểm tra `Singleton` làm việc như thế nào bằng việc sử dụng đoạn mã đơn giản này
```.env
  class Test
    include Singleton
  end
  
  Test.instance == Test.instance # => true
```
- Nó có nghĩa là thậm chí nếu bạn gọi `bundle exec sidekiq` 2 lần trong các terminal riêng biệt thì, thể hiện `CLI` sẽ như nhau.
####10. Phân tích `CLI`
- Điều thứ 2 mà Sidekiq làm là sau khi thực hiện câu lệnh sẽ bắt đầu phân tích. Có 2 bước phân tích là thực hiện từng cái 1:
    - Tuỳ chọn chỉ định (options assignment)
       - Tuỳ chọn thông qua từ câu lệnh `bunle exec sidekiq` là phân tích và gán. Vì vậy nếu bạn thông qua file `-C cònig/sidekiq.yml`.
       Nó sẽ được phát hiện và vị trí file cấu hình sẽ được chỉ định vị trí để sử dụng trong tương lai. Nếu file cấu hình không tồn tại nó sẽ xảy ra lỗi.
       - Mặc định tuỳ chọn cũng được cài đặt nếu bạn không cung cấp các giá trị khởi tạo Sidekiq. Tuỳ chọn mặc định bao gồm `concurrency` và `queues list`.
    - Logger
       - Nhật ký được đặt ở mức debug nếu tuỳ chọn `verbose` được chỉ định. Bạn có thể cài đặt điều này nếu sử dụng dòng lệnh `-v` hoặc `--verbose`.
    - Validation
       Bước cuối cùng của phân tích là bước kiểm tra (validation) nếu giá trị của tuỳ chọn `concurrency` và `timeout` không nhỏ hơn 0 và nếu sidekiq có quyền truy cập vào
       ứng dụng Rails hoặc Ruby. Bạn có thể trỏ đến Sidekiq bằng cách sử dụng tuỳ chọn `--require`.
####11. Chạy
- Giai đoạn này bao gồm nhiều bước nhỏ hơn cần thiết để đảm bảo rằng Sidekiq có thể thực hiện tốt các nhiệm vụ.
Các bước được thực hiện như sau:
    - Nếu sidekiq ở môi trường phát triển, quảng cáo được in trong bảng điều khiển
    - Thông tin về chứng chỉ sẽ được in ra
    - Thông tin phiên bản PRO sẽ được in ra nếu bạn dùng bản PRO
    - Lỗi phát sinh nếu phiên bản Redis thấp hơn 4
    - Lỗi phát sinh nếu kích thước nhóm quá nhỏ so với Sidekiq (nó cần số kết nối của bạn là trong tuỳ chọn `concurrency` +2 )
    - Phần mềm trung gian (server middleware) thì được chạm vào để nó không được tải 1 cách lười biếng bởi nhiều chủ đề.
    - Thông tin về phần mềm trung gian của ứng dụng khách và máy chủ được in ra.
- Bước cuối cùng là bắt đầu trình khởi chạy là chủ đề tiếp theo
####12. Launching
- Nếu bạn chạy Sidekiq trong môi trường development, `Starting processing, hit Ctrl-C to stop` thông tin sẽ được in ra.
Sau đó trình khởi chạy sẽ hoạt động.
- Phương thức `run` khởi động sẽ làm 2 việc: bắt đầu kiểm tra và quản lý.
####13. Poller
- Một luồng mới được gọi `scheduler` để xử lý các công việc trong tương lai. Có 2 nhóm được tạo ở đây là `retry` và `schedule`
- Về cơ bản thì hàng chờ `retry` làm việc giống như `scheduled` chỉ có 1 khác biệt là: thời gian thực hiện được đặt tự động tuỳ thuộc vào số lần thử lại.
- Mỗi 1 nhóm thì phương thức `zrangebyscore` từ Redis được thực hiện. Cái tên rất kỳ lạ, để hiểu rõ hơn về phương thức này chúng ra phải lùi 1 bước
về bước làm thế nào công việc được đẩy sang Redis giả sử là thực hiện sau đó chứ không phải là ngay lập tức.
- Khi đó 1 công việc thực hiện trong tương lai, Sidekiq sẽ gọi phương thức `zadd` từ Redis. Phương thức này thêm 1 thành phần vào danh sách với 1 số điểm.
Số điểm trong trường hợp này là thời điểm nhất định job sẽ thực hiện.
- Nếu bạn gọi đoạn code sau:
```.env
  MySimpleWorker.perform_in(3.minutes, "I was performed!")
```
- Sau đó score sẽ như sau:
```.env
  Time.now.to_f + 3.minutes.to_f
```
- Vì vậy chúng ta có 1 số công việc và số điểm trong danh sách và sau đó chúng ta gọi `zrangebyscore`
```.env
  now = Time.now.to_f.to_s
  jobs = conn.zrangebyscore(sorted_set, "-inf", now, limit: [0, 100])
```
- Điều đó có nghĩa chúng ta kéo tất cả các items từ `sorted_set`, có thể là `schedule` hoặc `retry`, bắt đầu từ bất cứ item nào trong 
quá khư `now` - bằng cách này có thể lấy tất cả các công việc từ trước tới giờ.
- Sau đó mỗi công việc được chuyển đến phương thức zrem từ Redis, phương thức nàu sẽ xoá khỏi mục danh sách với số điểm và được đẩy sang sidekiq giống như gọi phương thức `perform_async`
trên worker - Quá trình này chúng được miêu tả từ trước đó bắt đầu từ đầu.
- Sau đó tất cả các job được xử lý, tiến trình sẽ ngủ 1 lúc(thời gian chờ đợi tiến hành được gọi là khoảng thời gian nhóm ngẫu nhiên) sau đó nó được thực hiện lại.
####14. Manager
...
##### The end ...
