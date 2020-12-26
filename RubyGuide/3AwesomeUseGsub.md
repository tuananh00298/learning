# Cách tuyệt vời để sử dụng Gsub
Hãy nói về cách Ruby sử dụng Gsub. Đầu tiên bạn sẽ cần 1 chuỗi để sử dụng phương thức này
Tại sao?
Bởi vì sử dụng `gsub` trên 1 chuỗi để thay thế các phần của nó.
###1. Giới thiệu
- Trong thực tế `sub` trong `gsub` là viết tắt của `substitude`, và chữ `g` có nghĩa là `global`
```.env
    string = 'white chocolate'
```
- Giả sử chúng ta muốn thay thế `white` với `dark`
```.env
    string.gsub("white", "dark")
```
- Điều này sẽ cho chúng ta biết: Cho chuỗi `str`, câu lệnh này sẽ thay thế tất cả những lần xuất hiện từ đầu tiên (`white`) với từ thứ 2 (`dark`)
- Có nghĩa là chúng ta ăn chocolate ăn ngon hơn nhiều.
- Oh chờ chút, nó chỉ là 1 chuỗi - chúng ta không thể ăn cái đó.
Bất cứ con đường nào.
- Phương thức `gsub` trong ruby có thể làm được nhiều điều hơn là thay thế đơn giản.
- Chúng ta hãy xem một số ví dụ.
###2. Thay thế các chuỗi bằng biểu thức chính quy.
- Thay thế 1 từ duy nhất là được. Nhưng điều gì sẽ xảy ra nếu như bạn có thể thay thế các chuỗi với 1 khuôn mẫu.
####### Ví dụ như:
- Một năm, một địa chỉ, một số điện thoại, ... Bạn có thể!
```.env
    'a1'.gsub(/d/, '2')
    # 'a2'
```
- Tham số đầu tiên là một biểu thức chính quy, và nó có quá nhiều thứ trình bày ở đây. Nhưng nó là một ngôn ngữ theo khuôn mẫu
- Trong trường hợp này, `\d` tìm kiếm các số, như `1` trong 'a1'
- Bạn có thể làm điều này:
```.env
    'a1'.gsub(/(\w)(\d)/, '\2\1)
    # 'a1'
```
- Chúng ta sẽ thay đổi thứ tự. Điều này hoạt động với tính năng được gọi là 'capture groups'(bắt nhóm)
- Chúng ta có thể sử dụng nhóm như `\1` cho nhóm đầu tiên, `\2` cho nhóm thứ 2. Các nhóm được tạo bằng dấu ngoặc đơn.
###3. Nâng cao với block
- Nhiều thứ sẽ trở lên thú vị khi bạn bắt đầu sử dụng `Gsub` với một khối.
- ##### Tại sao?
- Bởi vì trong một khối bạn có thể sử sụng logic để quyết định thay thế thứ gì đó. Thay vì sử dụng một giá trị tĩnh.
```.env
    'dog'.gsub(/\w+/) { |animal| animal == 'dog' ? 'cat' : 'dog' }
```
- Chúng ta sẽ tìm những con động vật với `\w+`, điều đó có nghĩa là 'một hoặc nhiều chữ số'.
- ##### Sau đó:
    -   Nếu nó là 1 `dog`, chúng ta sẽ replace nó với một `cat`.
    -   Nếu từ đó là bất cứ con nào khác, chúng ta sẽ thay thế nó với `dog`.
- Loại logic này không thể thực hiện với giá trị tĩnh, tham số thứ 2 của `gsub`.
###4. Thay thế danh sách theo quy định của bạn với một `hash`
- Nếu bạn có 1 danh sách thay thế bạn có thể sử dụng một `hash`.
```.env
    colors = { 
        'B' => 'Blue',
        'G' => 'Green',
        'R' => 'Red'
     }
```
- Công việc này như tra cứu từ điển, ở đâu có từ khoá sẽ thay thế bằng giá trị của chúng.
```.env
    'BBBGR'.gsub(/\w/, colors)
    # 'BlueBlueBlueGreenRed'
```
- Đảm bảo rằng đối số đầu tiên của bạn sẽ hợp với các `keys` (khoá)
- Trong trường hợp này, `\w` khớp với các ký tự riêng lẻ, vì vậy nó sẽ khớp với `B` sau đó thay thế nó với `Blue`.
- ######Như vậy bạn đã tìm hiểu phương thức `gsub` trong Ruby! Nó là một phươgn thức mạnh mẽ điều đó cho phép bạn thay thế hoặc thay thế ký tự trong chuỗi của bạn.
- ###### Nó có nhiều cách sử dụng:
    -   Xoá các ký tự không hợp lệ.
    -   Thay thế các ký tự đánh dấu và viết tắt bằng ký tự đầy đủ của chúng.
    -   Sử dụng các mẫu và logic để thay thế 1 chuỗi.
 
 #####The end! ...
