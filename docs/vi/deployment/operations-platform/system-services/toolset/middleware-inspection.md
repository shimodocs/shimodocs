# Kiểm tra phần mềm trung gian

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng 

Kiểm tra Middleware được sử dụng để kiểm tra liệu hệ thống phụ thuộc có MySQL, Redis, Elasticsearch, S3, MongoDBvà Kafka kết nối và đọc/ghi bình thường, giúp bạn phát hiện kịp thời các bất thường của dịch vụ cơ sở. 

Trang này hỗ trợ kiểm tra ngay lập tức, kiểm tra theo lịch, xu hướng sẵn có gần đây, hồ sơ lịch sử, cũng như thông báo lỗi và khôi phục. 

## Truy cập Trang 

Sau khi đăng nhập vào quản trị backend, chọn **Kiểm tra phần mềm trung gian** trong điều hướng bên trái để vào trang. 

Kiểm tra Middleware chỉ mở cho quản trị viên. Nếu bạn không thấy menu này, hãy liên hệ với quản trị viên hệ thống để xác nhận quyền tài khoản của bạn. 

## Kiểm tra ngay lập tức 

Trên **Tổng quan** trang, nhấp **Kiểm tra ngay lập tức**, và hệ thống sẽ thực hiện kiểm tra theo các đối tượng kiểm tra đã lưu. 

Kết quả kiểm tra có thể bao gồm các trạng thái sau: 

- **Bình thường**: Kết nối và thao tác kiểm tra thành công. 
- **Thất bại**: Không thể kết nối thành phần, đọc/ghi thất bại hoặc phản hồi bất thường. 
- **Bỏ qua**: Thành phần không được cấu hình trong môi trường hiện tại, hoặc điều kiện kiểm tra không đáp ứng. 

Nhấp vào kết quả thành phần cho phép bạn xem địa chỉ mục tiêu, thời gian phản hồi và chi tiết lỗi. 

## Xem xu hướng sẵn có 

Trang tổng quan hiển thị khả năng sẵn có gần đây của từng thành phần dựa trên kết quả kiểm tra lịch sử. Nó hỗ trợ xem thay đổi trạng thái trong 1 giờ, 6 giờ, 24 giờ, 3 ngày, 7 ngày, 14 ngày hoặc 30 ngày trước. 

Di chuột qua khoảng thời gian để xem thông tin như số lần kiểm tra trong khoảng thời gian đó, thời gian phản hồi trung bình và các lỗi gần đây. 

## Cấu hình Kiểm tra theo Lịch 

Trên **Lập lịch và Cảnh báo** trang, bạn có thể thiết lập: 

- **Bật Kiểm tra theo Lịch**: Khi được bật, hệ thống sẽ thực hiện tự động theo các khoảng thời gian đã đặt. 
- **Khoảng thời gian kiểm tra**: Hỗ trợ từ 1 đến 1440 phút. 
- **Số ngày lưu giữ lịch sử**: Hỗ trợ từ 7 đến 365 ngày; đặt thành `0` có nghĩa là không xóa tự động. 
- **Đối tượng kiểm tra**: Chọn phần mềm trung gian cần kiểm tra. 
- **Kênh Thông báo**: Chọn các kênh để nhận thông báo kiểm tra. 
- **Thông báo khi Thất bại**: Gửi thông báo khi trạng thái tổng thể chuyển từ bình thường sang bất thường. 
- **Thông báo khi Phục hồi**: Gửi thông báo khi trạng thái bất thường trở về bình thường. 

Các thay đổi cần được áp dụng bằng cách nhấp **Lưu**. Nếu chưa có kênh thông báo, vui lòng đi đến **Kênh Thông báo** trang để tạo và bật kênh trước.

## Xem Lịch sử Kiểm tra

Trên **Lịch sử** trang, bạn có thể xem thời gian kiểm tra, phương pháp kích hoạt, thời gian thực hiện và trạng thái cuối cùng.

Các phương pháp kích hoạt bao gồm kiểm tra thủ công và kiểm tra theo lịch. Nhấp vào một bản ghi để xem chi tiết kết quả của từng thành phần trong lần kiểm tra này. 

## Tình huống phổ biến

- **Không có bản ghi kiểm tra**: Bạn có thể trước hết nhấp **Kiểm tra Ngay**, hoặc bật kiểm tra theo lịch.
- **Thành phần hiển thị bỏ qua**: Vui lòng xác nhận rằng phần mềm trung gian tương ứng đã được cấu hình và bật trong hệ thống.
- **Kiểm tra thất bại**: Kiểm tra mạng, tài khoản, địa chỉ kết nối và trạng thái dịch vụ trung gian theo chi tiết lỗi.
- **Thông báo không nhận được**: Vui lòng xác nhận rằng kênh thông báo đã được chọn và kích hoạt, đồng thời kiểm tra các công tắc thông báo lỗi hoặc phục hồi.
- **Thông báo nhắc nhở hiển thị việc kiểm tra đang chạy**: Chỉ có thể thực hiện một tác vụ kiểm tra tại một thời điểm, vui lòng chờ tác vụ hiện tại kết thúc và thử lại.

> Các kiểm tra sẽ thực hiện kết nối nhẹ hoặc kiểm tra đọc/ghi trên dịch vụ trung gian; nên thiết lập khoảng thời gian kiểm tra hợp lý dựa trên quy mô của môi trường.
