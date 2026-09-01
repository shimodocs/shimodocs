# Triển khai với MongoDB

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giới thiệu cách vô hiệu hóa MongoDB trong ShimoDocs và cấu hình của khách hàng MongoDB tích hợp sẵn như một cơ sở dữ liệu tài liệu của bên thứ ba. MongoDB Sau khi cấu hình, trình cài đặt sẽ kiểm tra MongoDB kết nối mạng và các quyền như kết nối và xác thực. Khi kiểm tra xong, triển khai có thể tiếp tục. 

# 1. Chuẩn bị trước khi cấu hình 
Trước khi bắt đầu, vui lòng xác nhận: 
- MongoDB Máy chủ đã được cài đặt và đang chạy bình thường. 
- Nút triển khai có thể truy cập host và cổng của MongoDB Máy chủ. 
- Thông tin kết nối và PASSWORD cho việc xác thực với MongoDB Máy chủ đã được chuẩn bị. 

> [!TIP] 
> 
> IP, cổng và tài khoản trong bài viết này chỉ là ví dụ. Vui lòng sử dụng thông tin môi trường thực tế để cấu hình và không ghi lại thông tin thực PASSWORD trong tài liệu công khai hoặc ảnh chụp màn hình. 
> 

# 2. Vào Cấu Hình Nâng Cao 
Trong bước "Cấu hình" của trình cài đặt, sau khi hoàn thành cấu hình mạng, môi trường mục tiêu và thông tin nút, mở rộng "Cấu hình nâng cao" ở cuối trang. 

# 3. Hủy cài đặt MongoDB
Trong khu vực 'Dịch vụ Middleware', bỏ chọn MongoDB

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp MongoDB, và sẽ sử dụng MongoDB chuẩn bị bên ngoài thay vì. Đối với các phần mềm trung gian khác, việc có sử dụng dịch vụ có sẵn hay không nên được chọn theo kế hoạch triển khai thực tế.

# 4. Mở cấu hình phần mềm trung gian của bên thứ ba
Trong khu vực "Middleware Bên Thứ Ba", nhấp vào "Cấu hình".

# 5. Cấu hình MongoDB Cơ sở dữ liệu Tài liệu
1. ChọnMongoDB Cơ sở dữ liệu Tài liệu" bên trái.
2. Bật "Sử dụng Hàng đợi Tin nhắn Bên Thứ Ba". MongoDB Cơ sở dữ liệu Tài liệu.
3. Nhập Host, Cổng, USERNAME, PASSWORD, Ghi đè Chuỗi Kết nối
4. ec41748c2ae1f83f1a73bfefaa8128d

> [!WARNING]
>
> Chú ý: Nếu một bên thứ ba MongoDB tạo một tài khoản riêng cho ứng dụng và tuân theo 'nguyên tắc quyền tối thiểu,' nơi một tài khoản chỉ có quyền truy cập vào cơ sở dữ liệu cụ thể, cần phải gán một người dùng và PASSWORD cho mỗi cơ sở dữ liệu kinh doanh

# 6. Xác nhận kết quả kiểm tra
Trình cài đặt sẽ kiểm tra những điều sau:
- đăng nhập: Tài khoản có thể được xác thực bình thường
- Khả năng kết nối: Môi trường triển khai có thể truy cập MongoDB
- quyền: Tài khoản có quyền cho kết nối, xác thực, thực thi lệnh, v.v.

Sau khi tất cả các mục kiểm tra hiện 'Thành công', đóng cửa sổ cấu hình và quay lại trang 'Cấu hình' của trình cài đặt.

Nếu có bất kỳ lỗi nào, vui lòng kiểm tra theo các hướng dẫn trên trang:
- Liệu host và port đã được điền chính xác.
- Liên kết mạng giữa node triển khai và MongoDB Máy chủ đã được kết nối.
- Liệu USERNAME và PASSWORD là chính xác.
- Tài khoản có các quyền cần thiết (kết nối và xác thực, quyền lệnh, v.v.) hay không.

# 7. Tiếp tục khởi tạo triển khai
Sau khi quay lại trang 'Cấu hình', đảm bảo MongoDB vẫn chưa được kiểm tra, sau đó nhấp 'Khởi tạo Triển khai' để tiếp tục hoàn thành tổng quan triển khai, kiểm tra và các bước thực thi.

> [!TIP]
>
> Trước khi khởi tạo triển khai, vui lòng xác nhận một lần nữa rằng MongoDB cấu hình đã được lưu và tất cả các mục kiểm tra đã được thông qua.
