# RDB Công cụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

> [!TIP]
>
> RDB được sử dụng để xem và khắc phục dữ liệu cơ sở dữ liệu quan hệ trên Nền tảng Vận hành và thường được sử dụng để xác nhận các bản ghi kinh doanh, bản ghi cấu hình, trạng thái công việc, nhật ký hoạt động và các dữ liệu có cấu trúc khác.
>
> Trước khi sử dụng, hãy đảm bảo rằng tài khoản hiện tại của bạn có quyền sử dụng công cụ trung gian và đã chọn đúng môi trường triển khai.

> Người dùng RDB công cụ trực tiếp truy cập dữ liệu cơ sở dữ liệu. Trước khi truy vấn, vui lòng xác nhận các bảng và điều kiện lọc để tránh thực hiện các truy vấn chi phí cao hoặc thao tác sai trên dữ liệu sản xuất.

## 1. Truy cập RDB

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **Dịch vụ Hệ thống** ở phía trên.
3. Mở rộng **Công cụ Middleware** trong bảng điều hướng bên trái.
4. Chọn **RDB**.

Trang thường bao gồm các khu vực kết nối cơ sở dữ liệu, chọn bảng, SQL nhập liệu và kết quả truy vấn.

## 2. Chọn kết nối cơ sở dữ liệu

1. Trên RDB trang, chọn phiên bản cơ sở dữ liệu bạn cần truy cập.
2. Xác nhận tên phiên bản, địa chỉ cơ sở dữ liệu hoặc định danh môi trường khớp với mục tiêu khắc phục sự cố hiện tại.
3. Chọn cơ sở dữ liệu mục tiêu.
4. Mở rộng danh sách bảng và xác nhận bảng mục tiêu tồn tại.

> Nếu phiên bản cơ sở dữ liệu trống hoặc kết nối thất bại, vui lòng kiểm tra trước cấu hình middleware, quyền tài khoản và kết nối mạng.

## 3. Xem Cấu Trúc Bảng

1. Chọn bảng mục tiêu từ danh sách bảng bên trái.
2. Xem tên trường, kiểu trường, khóa chính và thông tin chỉ mục.
3. Xác nhận điều kiện truy vấn tiếp theo dựa trên ý nghĩa của các trường.

Nên tập trung vào những thông tin sau:

| Thông tin | Mô tả |
| --- | --- |
| Khóa Chính | Được sử dụng để truy vấn chính xác một bản ghi. |
| ID Doanh Nghiệp | Như Tenant ID, User ID, Task ID, File ID. |
| Trường Trạng Thái | Được sử dụng để xác nhận trạng thái luồng hiện tại của nghiệp vụ. |
| Trường Thời Gian | Được sử dụng để giới hạn phạm vi thời gian truy vấn. |
| Trường Có Chỉ Mục | Ưu tiên sử dụng làm bộ lọc truy vấn để giảm quét toàn bộ bảng. |

## 4. Sử Dụng Common SQL

'Common SQL' được sử dụng để thực hiện nhanh các truy vấn đặt trước, phù hợp cho các tình huống kiểm tra tần suất cao như chứng chỉ, ứng dụng, tập tin và người dùng.

1. Nhấp **Common SQL** nằm trên SQL trình soạn thảo.
2. Chọn SQL bạn cần sử dụng từ danh sách thả xuống.
3. Nếu bạn cần kiểm tra nội dung câu lệnh trước, nhấp vào **Xem Trước** bên cạnh mục tương ứng SQL.
4. Xác minh mô tả, cơ sở dữ liệu, tên bảng và SQL nội dung trong cửa sổ xem trước.
5. Sau khi xác nhận mọi thứ đúng, nhấn **Thực Thi**.

Các truy vấn thường dùng SQL có thể chứa chỗ giữ chỗ:

| Chỗ Giữ Chỗ | Loại Tham Số | Ví dụ |
| --- | --- | --- |
| `%s` | Chuỗi | app ID, provider file ID, history guid, provider user ID |
| `%d` | Số | ID Người Dùng Nội Bộ |

Nếu SQL chứa chỗ giữ chỗ, một **Điền vào SQL Tham Số** Cửa sổ sẽ xuất hiện khi thực thi.

1. Điền vào từng tham số theo hướng dẫn.
2. Với các tham số chuỗi, điền ID đầy đủ mà không thêm dấu ngoặc kép.
3. Điền các tham số số bằng số thuần.
4. Nhấp **Thực thi Truy vấn**.

Các tham số thường được sử dụng SQL hiện chủ yếu bao gồm các tình huống sau:

| Tình huống | Mô tả |
| --- | --- |
| Truy vấn Chứng chỉ | Truy vấn chứng chỉ ứng dụng và ID ứng dụng. |
| Truy vấn theo appid được chỉ định | Truy vấn chi tiết ứng dụng theo ID ứng dụng. |
| Truy vấn theo guid tệp khách hàng được chỉ định | Truy vấn chi tiết tệp theo `provider_file_id`. |
| Truy vấn theo guid tệp nội bộ được chỉ định | Truy vấn chi tiết tệp theo `history_guid`. |
| Truy vấn theo id người dùng nội bộ được chỉ định | Truy vấn chi tiết người dùng theo ID người dùng nội bộ. |
| Truy vấn theo id người dùng khách hàng được chỉ định | Truy vấn chi tiết người dùng theo `provider_user_id`. |

> Ngay cả trước khi sử dụng các tham số thường dùng SQL, cần xác nhận môi trường mục tiêu và giá trị tham số. Tham số chung SQL chỉ nhằm giảm chi phí viết thủ công và không đảm bảo rằng kết quả truy vấn sẽ đáp ứng mục tiêu của cuộc điều tra này.

## 5. Thực thi Truy vấn

1. Điền câu truy vấn vào SQL khu vực nhập.
2. Ưu tiên sử dụng `SELECT` câu truy vấn và không thực thi các câu lệnh insert, update, hoặc delete.
3. Truy vấn mặc định LIMIT là 10 và có thể điều chỉnh thủ công.
4. Nhấp **Thực thi Truy vấn**.
5. Ưu tiên thực hiện EXPLAINvà **xác nhận thực thi** trước khi bắt đầu truy vấn.

Ví dụ:

```sql
SELECT *
FROM example_table
WHERE id = 'example-id';
```

## 6. Xem Kết quả Truy vấn

1. Xem các bản ghi trả về trong khu vực kết quả.
2. Kiểm tra xem các trường chính có đáp ứng mong đợi không.
3. Nếu kết quả trống, kiểm tra cơ sở dữ liệu, tên bảng, điều kiện truy vấn và phạm vi thời gian.
4. Nếu có quá nhiều kết quả, hãy thêm các điều kiện lọc chính xác hơn và truy vấn lại.

## 7. Sử dụng Lịch sử Truy vấn

'Lịch sử Truy vấn' được sử dụng để xem các SQL câu lệnh đã được thực thi trên trang hiện tại, thuận tiện để tái sử dụng các câu lệnh khắc phục sự cố, kiểm tra kết quả thực thi và sao chép SQL."

> [!NOTE]
>
> Lịch sử truy vấn được lưu trữ cục bộ trong trình duyệt hiện tại và sẽ không được lưu trữ vĩnh viễn. Mỗi cơ sở dữ liệu/bảng giữ tối đa 100 bản ghi gần nhất, và hiện tại không có thời gian hết hạn tự động; xóa dữ liệu trang của trình duyệt, thay đổi trình duyệt, thay đổi thiết bị, hoặc chuyển sang cơ sở dữ liệu/bảng khác sẽ dẫn đến việc thấy các bản ghi lịch sử khác nhau.

1. Chuyển sang **Lịch sử Truy vấn** trong khu vực kết quả.
2. Xem trạng thái thực thi, thời gian, cơ sở dữ liệu, bảng, SQLsố hàng trả về, và thời gian đã trôi qua trong các bản ghi lịch sử.
3. Để thực thi SQL một câu lệnh một lần nữa, nhấp **Chèn vào Trình chỉnh sửa và Thực thi** trong cột thao tác của bản ghi đó.
4. Nếu bạn chỉ cần tái sử dụng câu lệnh, nhấp **Sao chép SQL**.

Mô tả Trường Lịch sử Truy vấn:

| Trường | Mô tả |
| --- | --- |
| Trạng thái | Có SQL thực thi thành công hay không; nếu thất bại, khắc phục sự cố kết hợp với thông báo lỗi. |
| Thời gian | Thời gian thực thi của truy vấn hiện tại. |
| Cơ sở dữ liệu | Cơ sở dữ liệu được chọn trong khi SQL thực thi. |
| Bảng | Bảng liên quan trong khi SQL thực thi. |
| SQL | Câu lệnh truy vấn thực tế đã được thực thi. |
| Trả về số hàng | Số hàng dữ liệu được truy vấn này trả về. |
| Thời gian thực thi | SQL Thời gian thực thi và có thể được sử dụng để xác định nguy cơ truy vấn chậm. |
| Thao tác | Hỗ trợ chèn lại và thực thi, hoặc sao chép SQL. |

Khi khắc phục sự cố với lịch sử truy vấn, nên tập trung vào: 

| Tình huống | Gợi ý xử lý |
| --- | --- |
| Trạng thái thất bại | Đầu tiên, kiểm tra xem liệu SQL cú pháp, bảng cơ sở dữ liệu tồn tại, và các trường có đúng không. |
| Mất nhiều thời gian | Thêm điều kiện lọc, hoặc trước tiên kiểm tra cấu trúc bảng và các trường chỉ mục. |
| Trả về quá nhiều hàng | Thêm điều kiện 'WHERE' và 'LIMIT'. |
| Kết quả nhiều truy vấn không nhất quán | Xác nhận xem cơ sở dữ liệu, bảng, hoặc môi trường đã được chuyển đổi hay chưa. |

> Lịch sử truy vấn được sử dụng để hỗ trợ xem lại quá trình xử lý sự cố hiện tại. Trước khi thực hiện lại lịch sử SQL, bạn vẫn phải xác nhận SQL nội dung, cơ sở dữ liệu mục tiêu và môi trường hiện tại. 

## 8. Các Tình Huống Khắc Phục Sự Cố Thông Dụng

| Tình huống | Gợi ý vận hành |
| --- | --- |
| Xác nhận nếu hồ sơ kinh doanh tồn tại | Sử dụng ID doanh nghiệp để truy vấn chính xác. |
| Kiểm tra trạng thái công việc | Truy vấn trường trạng thái và thời gian cập nhật theo ID nhiệm vụ. |
| Khắc phục cấu hình không hiệu quả | Truy vấn giá trị hiện tại và thời gian cập nhật trong bảng cấu hình. |
| Kiểm tra các thay đổi gần đây | Truy vấn theo thứ tự giảm dần theo trường thời gian và giới hạn số lượng mục trả về. |
| Truy vấn thông tin ứng dụng hoặc chứng chỉ | Ưu tiên sử dụng truy vấn chứng chỉ hoặc truy vấn appid trong “Thông dụng SQL”. |
| Tái sử dụng các câu lệnh khắc phục sự cố | Sao chép SQL từ “Lịch sử truy vấn”, xác minh các tham số, và thực hiện lại. |

## 9. Các lưu ý

1. Cấm truy vấn không điều kiện trên các bảng lớn trong môi trường sản xuất.
2. Nếu không chắc về SQL tác động, hãy kiểm tra trước trong môi trường rủi ro thấp.
3. Không trực tiếp sửa dữ liệu doanh nghiệp thông qua RDB công cụ trừ khi có kế hoạch thay đổi rõ ràng và được phê duyệt.
4. Các tham số chung SQL phải được điền bằng các giá trị thực của môi trường hiện tại để tránh truy vấn sai giữa các môi trường.
5. SQL trong lịch sử truy vấn có thể chứa ID nhạy cảm, xác nhận phạm vi trước khi sao chép hoặc chuyển tiếp.
6. Khi kết quả truy vấn liên quan đến thông tin nhạy cảm, không chia sẻ toàn bộ ảnh chụp màn hình hoặc dữ liệu dạng văn bản ngoài.
