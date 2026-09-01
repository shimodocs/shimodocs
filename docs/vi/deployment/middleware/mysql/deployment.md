# Triển khai với MySQL 8

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giải thích cách tắt trình cài đặt tích hợp sẵn MySQL trong ShimoDocs cài đặt và cấu hình của riêng bạn MySQL như một cơ sở dữ liệu quan hệ bên thứ ba. Sau khi cấu hình, trình cài đặt sẽ kiểm tra đăng nhập cơ sở dữ liệu, kết nối mạng và quyền tạo bảng. Khi đã kiểm tra xong, triển khai có thể tiến hành. 

# 1. Chuẩn bị trước khi cấu hình 
Trước khi bắt đầu, vui lòng xác nhận: 
- MySQL 8.0 đã được cài đặt và chạy bình thường. 
- Nút triển khai có thể truy cập MySQL máy chủ và cổng cơ sở dữ liệu. 
- Máy chủ cơ sở dữ liệu, cổng, USERNAMEvà PASSWORD đã được chuẩn bị. 
- Tài khoản cơ sở dữ liệu có quyền đăng nhập, kết nối, tạo bảng và xóa bảng. 

> [!TIP]
>
> IP, cổng và tài khoản trong bài viết này chỉ là ví dụ. Vui lòng cấu hình sử dụng thông tin môi trường thực tế của bạn; không ghi lại thông tin thật PASSWORD trong tài liệu hoặc ảnh chụp màn hình bên ngoài. 
>

# 2. Vào Cấu Hình Nâng Cao 
Trong bước "Cấu hình" của trình cài đặt, sau khi hoàn tất cấu hình mạng, môi trường mục tiêu, và thông tin nút, mở rộng phần "Cấu Hình Nâng Cao" ở cuối trang. 

# 3. Hủy cài đặt MySQL
Trong khu vực 'Dịch vụ Middleware', bỏ chọn MySQL.

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp MySQL, và một bên ngoài MySQL 8.0 đã được chuẩn bị sẽ được sử dụng sau này. Việc phần mềm trung gian khác có sử dụng dịch vụ tích hợp sẵn hay không nên được chọn theo kế hoạch triển khai thực tế.

# 4. Mở cấu hình phần mềm trung gian của bên thứ ba
Trong mục "Phần mềm trung gian bên thứ ba", nhấp vào "Cấu hình."

# 5. Cấu hình MySQL Cơ sở dữ liệu
1. Chọn 'RDB Cơ sở dữ liệu quan hệ' ở bên trái.
2. Bật 'Sử dụng cơ sở dữ liệu quan hệ bên thứ ba'.
3. Chọn Chuẩn MySQL dưới 'Phương ngữ'.
4. Điền thông tin kết nối cơ sở dữ liệu.
5. Xác minh và lưu.

# 6. Xác nhận kết quả kiểm tra
Trình cài đặt sẽ kiểm tra những điều sau:

- Đăng nhập: Tài khoản cơ sở dữ liệu có thể đăng nhập bình thường không.
- Kết nối: Môi trường triển khai có thể truy cập cơ sở dữ liệu không.
- Quyền tạo bảng: Tài khoản cơ sở dữ liệu có quyền tạo và xóa bảng không.

Sau khi tất cả các mục kiểm tra hiện 'Thành công', đóng cửa sổ cấu hình và quay lại trang 'Cấu hình' của trình cài đặt.

Nếu có bất kỳ lỗi nào, vui lòng kiểm tra theo các hướng dẫn trên trang:
- Liệu host và port đã được điền chính xác.
- Mạng giữa các nút triển khai và cơ sở dữ liệu có kết nối không.
- Liệu USERNAME và PASSWORD là chính xác.
- Liệu tài khoản cơ sở dữ liệu có các quyền cần thiết.

# 7. Tiếp tục khởi tạo triển khai
Sau khi quay lại trang 'Cấu hình', đảm bảo MySQL vẫn chưa được kiểm tra, sau đó nhấp 'Khởi tạo triển khai' để tiếp tục hoàn thành các bước tổng quan, kiểm tra và thực hiện triển khai.

> [!TIP]
>
> Trước khi khởi tạo triển khai, vui lòng xác nhận một lần nữa rằng MySQL cấu hình 8.0 đã được lưu và tất cả các mục kiểm tra đã được thông qua.
