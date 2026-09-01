# Triển khai với Kafka

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giải thích cách tắt trình cài đặt tích hợp sẵn Kafka trong ShimoDocs và cấu hình của khách hàng Kafka như một hàng đợi tin nhắn bên thứ ba. Sau khi cấu hình, trình cài đặt sẽ kiểm tra Kafkakết nối mạng của ' và quyền tạo chủ đề. Khi đã kiểm tra xong, việc triển khai có thể tiếp tục. 

# 1. Chuẩn bị trước khi cấu hình 
Trước khi bắt đầu, vui lòng xác nhận: 
- Kafka Máy chủ đã được cài đặt và đang chạy bình thường. 
- Nút triển khai có thể truy cập Kafka máy chủ và cổng của Server. 
- Chuẩn bị thông tin người dùng xác thực và PASSWORD để kết nối tới Kafka Server Topic (nếu cụm bên ngoài Kafka đã bật xác thực bảo mật). 
- Tài khoản đã xác thực phải sử dụng người dùng quản trị và có quyền tạo, xóa, ủy quyền, và đọc và ghi Topics (nếu cụm bên ngoài Kafka đã bật xác thực bảo mật). 

> [!TIP]
>
> IP, cổng và tài khoản trong bài viết này chỉ là ví dụ. Vui lòng cấu hình sử dụng thông tin môi trường thực tế của bạn; không ghi lại thông tin thật PASSWORD trong tài liệu hoặc ảnh chụp màn hình bên ngoài. 
>

# 2. Vào Cấu Hình Nâng Cao 
Trong bước "Cấu hình" của trình cài đặt, sau khi hoàn tất cấu hình mạng, môi trường mục tiêu, và thông tin nút, mở rộng phần "Cấu Hình Nâng Cao" ở cuối trang. 

# 3. Hủy Cài Đặt Tích Hợp Kafka
Trong khu vực 'Dịch vụ Middleware', bỏ chọn Kafka.

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp Kafka, và một bên ngoài Kafka cái đã được chuẩn bị sẽ được sử dụng sau. Đối với các middleware khác, việc có sử dụng dịch vụ tích hợp sẵn hay không nên được chọn theo kế hoạch triển khai thực tế.

# 4. Mở cấu hình phần mềm trung gian của bên thứ ba
Trong khu vực "Middleware Bên Thứ Ba", nhấp vào "Cấu hình".

# 5. Cấu hình Kafka Middleware Tin nhắn
## Kafka Máy chủ SASL Chưa bật Xác thực
1. ChọnKafka Hàng đợi Tin nhắn" ở bên trái.
2. Bật "Sử dụng Hàng đợi Tin nhắn Bên Thứ Ba". Kafka Điền vào
3. Thông tin kết nối máy chủ. Kafka Xác minh và lưu
5. ec41748c2ae1f83f1a73bfefaa8128d

## Xác thực trên SASL Máy chủ đã Kafka Máy chủ 
Nếu Kafka bật xác thực, cần được bật đồng thời trong Giao diện Web: Chỉ bật khi broker yêu cầu truy cập có xác thực nút SASL Bật 
1. cơ chế SASL xác thực 
2. Kiểm tra cơ chế 
3. Nhập USERNAME và PASSWORD 
4. Xác minh và lưu 

# 6. Xác nhận kết quả kiểm tra
Trình cài đặt sẽ kiểm tra những điều sau:
- Đăng nhập: Tài khoản có thể xác thực bình thường (nếu SASL được bật).
- Khả năng kết nối: Môi trường triển khai có thể truy cập Kafka.
- Quyền tạo Topic: Tài khoản có quyền tạo Topics, ủy quyền và đọc/ghi.

Sau khi tất cả các mục kiểm tra hiện 'Thành công', đóng cửa sổ cấu hình và quay lại trang 'Cấu hình' của trình cài đặt.

Nếu có bất kỳ lỗi nào, vui lòng kiểm tra theo các hướng dẫn trên trang:
- Liệu host và port đã được điền chính xác.
- Liên kết mạng giữa node triển khai và Kafka máy chủ có được kết nối không.
- Liệu USERNAME và PASSWORD có đúng không (Kafka Máy chủ đã bật SASL xác thực).
- Tài khoản có các quyền cần thiết không (Kafka bật xác thực, cần được bật đồng thời trong Giao diện Web: Chỉ bật khi broker yêu cầu truy cập có xác thực nút SASL xác thực được bật).

# 7. Tiếp tục khởi tạo triển khai
Sau khi quay lại trang 'Cấu hình', đảm bảo Kafka nếu không được kiểm tra, hãy nhấp vào 'Khởi tạo Triển khai' để tiếp tục với tổng quan triển khai, kiểm tra và các bước thực thi.

> [!TIP]
>
> Trước khi khởi tạo triển khai, vui lòng xác nhận một lần nữa rằng Kafka cấu hình đã được lưu và tất cả các hạng mục kiểm tra đã đạt.
