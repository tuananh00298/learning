# Những lưu ý khi xây dựng Api

###1. API document.
-   Ai chẳng biết việc viết Api doc là cần thiết. Tuy nhiên để có 1 doc hoàn chỉnh cũng tiêu tốn kha khá time. Nhất là trong lúc dự án gấp rút thì mọi người thường chỉ để API docs ở mức ... dùng được case chính.
- API doc là một phần tương tự như Unit Test vậy - lấy ngắn nuôi dài.
- Nếu không được chăm sóc kỹ, thì đến lúc maintain hoặc thay đổi spec thì hậu quả sẽ rất thảm khốc. Bộ mặt của API rất đáng để bỏ công sức để chỉn chu, dưới đây là 1 số lưu ý lúc viết docs: 
    -   Mô tả đầy đủ về params request: gồm những params nào, datatype, require hay optional nào.
    -   Nên đưa ra các ví dụ về HTTP requests và responses với data chuẩn.
    -   Cập nhật docs thường xuyên để sát nhất với API có bất cứ thay đổi gì.
    -   Format, cú pháp cần nhất quán, mô tả rõ ràng, chính xác.
    -   Có thể sử dụng API frameworks nổi tiếng như Swagger, Apiary, Postman, ...
###2. Errors Message
- Ngay cả khi API docs có lởm, nhưng nếu errors messages trả về rõ ràng, ta cũng có thể sử dụng tương đối triệt để API đó.
- Errors message chính xác và rõ ràng thực chất rất quan trọng đối với trải nghiệm của client. Ngay đối với cả API developers, errors message giúp bạn có thể kiểm soát hệ thống, debug và maintain dễ dàng hơn. Vì vậy đừng đẻ errors message quá sơ sài.
###3. Bad error
```.env
    HTTP/1.1 422 Unprocessable Entity
    {
        “error”: Invalid Attribute
    }
```
###4. Good error
```.env
    HTTP/1.1 422 Unprocessable Entity
    Content-Type: application/vnd.api+json
    {
        "errors": [
            {
                "status": "422",
                "source": { "pointer": "/data/attributes/first-name" },
                "title": "Invalid Attribute",
                "details": "First name must contain min three chars."
            }
        ]
    }
```
###5. Authorization.
- Có 3 cơ chế Authorize chính: 
    -   HTTP Basic.
    -   JSON Web Token  (JWT)
    -   OAuth2.
-  Tuỳ thuộc vào service của bạn, mà hãy chọn loại Authorize có mức độ phù hợp, cố gắng giữ nó càng đơn giản càng tốt.
- Ví dụ về OAuth2 - Cơ chế rất phổ biến.
- Luôn sinh ra một cặp access-token và refresh-token. Access-token có thời hạn expire, phải sử dụng refresh-token để lấy access-token mới.
###6. HTTP status code
- Nhiều người không quan tâm tới cái HTTP status code này lúc định nghĩa response trả về. Trừ khi server sập thì mọi request đêuf trả về HTTP status bằng 200
- HTTP status code là mã trạng thái mà Server trả về cho Client sau mỗi request.
- Nếu tận dụng triệt để, ngoài việc để nhìn API chuyên nghiệp hơn, nó cũng giúp ích nhiều trong quá trình phát triển. Mỗi mã code đều có ý nghĩa khác nhau và có tiêu chuẩn chung [HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
- Dựa vào đó hãy chọn ra status codes phù hợp với case response của bạn.
- Từ đó Client có thể dự đoán được vấn đề đang gặp phải và tìm ra phương hướng xử lý phù hợp.
###7. Content-type.
- Nếu không phải vì lý do đặc biệt nào, hãy luôn giữ content-type ở tất cả các API giống nhau. Nó giúp kiến trúc client được đồng nhất, quá trình phát triển App sẽ đơn giản hơn.
- Đồng thời tránh viẹc endpoint A thì chạy được API, nhưng endpoint B lại báo lỗi mặc dù 2 thằng xử lý công việc như nhau.
###8. Object structure
- Duới quan điểm quan sát của client, việc biết được cấu trúc đầy đủ của object trả về rất quan trọng nhất là trong lúc fetch data và debug.
- Vì thế ta nên xây dựng 1 API đặc biệt, trả về toàn bộ thông tin các field của object.
- Nó sẽ tương tự như cái Metadata-API của google này [Metadata API](https://developers.google.com/analytics/devguides/reporting/metadata/v3/)
- Nếu không thì ít nhất, bạn hãy đảm bảo rằng sẽ trả đầy đủ các filds của object kể cả khi field đó = null.
```.env
    {
        "id": "9c7fd1e5-97f0-4b6a-979c-1d7adf5bda16",
        "email": "example@gmail.com",
        "firstname": "John",
        "lastname": "Smith",
        "timezone": null,
        "phone_code": "RU",
        "phone": null,
        "created_at": "2018-03-27T05:40:31.929Z",
        "updated_at": "2018-04-08T09:24:39.683Z",
        "citizenship": null
    }
```
###9. Phân trang.
- Chắc chắn các bạn đã hiểu rõ lợi ích của việc phân trang rồi, nhưng ta hãy cải thiện nó thêm một chút. Dưới đây là 1 số ý kiến:
    -   Nên để client có thể tự định nghĩa được page size lúc request. Client sẽ tự quyết định params.
    -   Ngoài list data trả về, ta cũng nên có thêm meta information - chứa số lượng record trả về trong request đó.
###10. Ảnh và files.
-   Nên trả về upload link của bên thứ 3 để client tự truy cập và download về sau đó
###11. Ngày và giờ.
-   Trong data response trả về từ server, có lẽ Datitime là datatype gây ra nhiều bug ngớ ngẩn nhất. Để tránh việc về nhầm ngày giờ do đó có Timezone khác nhau, ta có thể làm đơn giản hơn:
    -   Convert ngày giờ trả về sang dạng format Integer -> bằng tổng số giât tính từ thời điểm Unix ra đời.
    -   Mô tả về format và cách sử dụng loại data datetime trong Ducoment. Vì đã là 1 chuẩn chung, nên bất cứ client nào cũng có thể convert ngược số giây về format và TimeZone mong muốn của họ.
    ```.env
        {
          ...
          "created_at" => article.created_at.utc.to_i 
        }.to_json 
        #=> { ..., created_at: 1525969414  }
    ```
###12. API Limit.
- Nếu services có chức năng giới hạn request API, thì nên có errors message rõ ràng khi client đã đạt mốc limit. Ngoài ra thông tin về giới hạn limit cũng cần trả về cho phía client.


#####The end...
