# Triển khai với Redis

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giải thích cách vô hiệu hóa tính năng được tích hợp sẵn Redis trong ShimoDocs và cấu hình của khách hàng Redis như một cơ sở dữ liệu cache của bên thứ ba. Sau khi cấu hình, trình cài đặt sẽ kiểm tra Redis kết nối mạng, kết nối, xác thực, thực thi lệnh, quyền publish/subscribe, v.v. Khi các kiểm tra vượt qua, việc triển khai có thể tiếp tục. 

# 1. Chuẩn bị trước khi cấu hình 
Trước khi bắt đầu, vui lòng xác nhận: 
- Người dùng Redis Máy chủ đã được cài đặt và đang chạy bình thường. 
- Các nút triển khai có thể truy cập Redis host và cổng của Máy chủ. 
- Thông tin người dùng xác thực và PASSWORD để kết nối tới Redis Máy chủ đã được chuẩn bị. 

> [!TIP] 
> 
> IP, cổng và tài khoản trong bài viết này chỉ là ví dụ. Vui lòng sử dụng thông tin môi trường thực tế để cấu hình và không ghi PASSWORD thật trong tài liệu công khai hoặc ảnh chụp màn hình. 
> 

# 2. Vào Cấu Hình Nâng Cao 
Trong bước 'Cấu hình' của trình cài đặt, sau khi hoàn tất thiết lập thông tin mạng, môi trường mục tiêu và nút, mở rộng 'Cấu hình nâng cao' ở cuối trang. 

# 3. Hủy cài đặt tích hợp sẵn Redis
Trong phần 'Dịch vụ Middleware', bỏ chọn Redis

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp Redis, và sẽ sử dụng Redis ngoại vi đã chuẩn bị thay thế. Việc các middleware khác có sử dụng dịch vụ tích hợp sẵn hay không nên chọn theo kế hoạch triển khai thực tế.

# 4. Mở cấu hình phần mềm trung gian của bên thứ ba
Trong khu vực 'Phần mềm trung gian của bên thứ ba', nhấp vào 'Cấu hình'.

# 5. Cấu hình Redis Cache Middleware
## Redis Máy chủ một nút
1. ChọnRedis Cache' ở bên trái.
2. Bật "Sử dụng Hàng đợi Tin nhắn Bên Thứ Ba". Redis".
3. Nhấp vào 'Một nút'
4. Nhập Host, Cổng, PASSWORD
5. ec41748c2ae1f83f1a73bfefaa8128d

## Redis Máy chủ cụm Sentinel
1. Ở bên trái, chọn 'Redis Cache'.
2. Bật 'Sử dụng bên thứ ba Redis'.
3. Nhấp vào 'Cụm Sentinel'.
4. Nhập 'Tên Master, SENTINEL PASSWORD, SENTINEL Các nút'.
5. ec41748c2ae1f83f1a73bfefaa8128d

# 6. Xác nhận kết quả kiểm tra
Trình cài đặt sẽ kiểm tra những điều sau:
- đăng nhập: Tài khoản có thể được xác thực bình thường
- Khả năng kết nối: Môi trường triển khai có thể truy cập Redis
- quyền: Tài khoản có quyền kết nối, xác thực, thực thi lệnh, publish/subscribe, v.v.

Sau khi tất cả các mục kiểm tra hiện 'Thành công', đóng cửa sổ cấu hình và quay lại trang 'Cấu hình' của trình cài đặt.

Nếu có bất kỳ lỗi nào, vui lòng kiểm tra theo các hướng dẫn trên trang:
- Liệu host và port đã được điền chính xác.
- Liên kết mạng giữa node triển khai và Redis máy chủ có được kết nối không.
- Liệu USERNAME và PASSWORD là chính xác.
- Xem tài khoản có các quyền cần thiết (kết nối và xác thực, quyền lệnh, quyền publish/subscribe, v.v.) hay không.

# 7. Tiếp tục khởi tạo triển khai
Sau khi quay lại trang 'Cấu hình', đảm bảo Redis nếu không được kiểm tra, hãy nhấp vào 'Khởi tạo Triển khai' để tiếp tục với tổng quan triển khai, kiểm tra và các bước thực thi.

> [!TIP]
>
> Trước khi khởi tạo triển khai, vui lòng xác nhận một lần nữa rằng Redis Cấu hình đã được lưu và tất cả các mục kiểm tra đã được vượt qua.
