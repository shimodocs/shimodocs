# Triển khai với Dameng V8

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giải thích cách vô hiệu hóa tính năng được tích hợp sẵn MySQL trong ShimoDocs trình cài đặt và cấu hình Dameng DM8 làm cơ sở dữ liệu quan hệ bên thứ ba. Sau khi cấu hình hoàn tất, trình cài đặt sẽ kiểm tra đăng nhập cơ sở dữ liệu, kết nối mạng và quyền tạo bảng. Khi các kiểm tra được vượt qua, triển khai có thể tiếp tục.

## 1. Chuẩn bị Trước Cấu hình

Trước khi bắt đầu, vui lòng xác nhận:

- Dameng DM8 được cài đặt và chạy bình thường.
- Nút triển khai có thể truy cập host và cổng của Dameng Cơ sở dữ liệu.
- Máy chủ cơ sở dữ liệu, cổng, USERNAMEvà PASSWORD đã sẵn sàng.
- Tài khoản cơ sở dữ liệu có quyền đăng nhập, kết nối, tạo bảng và xóa bảng.
- Dameng Cơ sở dữ liệu đã hoàn tất MySQL cấu hình chế độ tương thích như yêu cầu. Để biết hướng dẫn chi tiết, vui lòng tham khảo ["Dameng Hướng dẫn Cấu hình Tích hợp Cơ sở dữ liệu](requirements.md).

> [!TIP]
>
> Địa chỉ IP, cổng và tài khoản trong bài viết này đều là ví dụ. Vui lòng sử dụng thông tin môi trường thực tế để cấu hình, và không ghi lại PASSWORD thật trong các tài liệu hoặc ảnh chụp màn hình bên ngoài.

## 2. Vào Cài đặt Nâng cao

Trong bước 'Cấu hình' của trình cài đặt, sau khi hoàn tất cấu hình mạng, môi trường mục tiêu và thông tin nút, mở rộng 'Cấu hình Nâng cao' ở cuối trang.

## 3. Bỏ chọn Cài đặt Tích hợp Sẵn MySQL

Trong phần "Dịch vụ Middleware", bỏ chọn **MySQL**.

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp sẵn MySQL, và nó sẽ sử dụng đã chuẩn bị Dameng Cơ sở dữ liệu trong tương lai. Đối với các phần mềm trung gian khác, việc sử dụng dịch vụ tích hợp sẵn cần được chọn dựa trên kế hoạch triển khai thực tế.

## 4. Mở cấu hình phần mềm trung gian của bên thứ ba

Trong khu vực 'Phần mềm trung gian của bên thứ ba', nhấp vào 'Cấu hình'.

## 5. Cấu hình Dameng Cơ sở dữ liệu

1. ChọnRDB "Cơ sở dữ liệu quan hệ" ở bên trái.
2. Bật "Sử dụng cơ sở dữ liệu quan hệ của bên thứ ba."
3. Trong mục "Ngôn ngữ truy vấn", chọn **DM (Dameng)**.
4. Điền thông tin kết nối cơ sở dữ liệu.

| Mục Cấu hình | Mô tả |
| --- | --- |
| Máy chủ | Địa chỉ IP hoặc tên máy chủ có thể truy cập của Dameng Cơ sở dữ liệu |
| Cổng | Cổng lắng nghe của Dameng Cơ sở dữ liệu, thường là 5236 theo mặc định, tùy thuộc vào cấu hình thực tế |
| USERNAME | Tài khoản được sử dụng để kết nối với cơ sở dữ liệu |
| PASSWORD |  PASSWORD tương ứng với tài khoản cơ sở dữ liệu |
| DSN | Tự động được tạo bởi trình cài đặt dựa trên thông tin trên, không cần nhập tay |

5. Sau khi xác nhận thông tin đúng, hãy nhấp vào "Xác thực và Lưu."

## 6. Xác nhận kết quả kiểm tra

Trình cài đặt sẽ kiểm tra các mục sau:

- **đăng nhập**: Tài khoản cơ sở dữ liệu có thể đăng nhập bình thường.
- **kết nối**: Môi trường triển khai có thể truy cập cơ sở dữ liệu.
- **tạo bảng quyền**: Tài khoản cơ sở dữ liệu có quyền tạo và xóa bảng.

Sau khi tất cả các kiểm tra hiển thị "Thành công", đóng cửa sổ cấu hình và quay lại trang "Cấu hình" của trình cài đặt.

Nếu có bất kỳ mục nào thất bại, vui lòng kiểm tra theo hướng dẫn trên trang:

- Liệu host và port đã được điền chính xác.
- Liệu mạng giữa nút triển khai và cơ sở dữ liệu có kết nối.
- Liệu USERNAME và PASSWORD là chính xác.
- Liệu tài khoản cơ sở dữ liệu có các quyền cần thiết.
- Liệu Dameng Dịch vụ cơ sở dữ liệu và MySQL cấu hình tương thích đã có hiệu lực bình thường.

## 7. Tiếp tục Khởi tạo Triển khai

Sau khi quay lại trang "Cấu hình", xác nhận rằng MySQL vẫn chưa được chọn, sau đó nhấp "Khởi tạo Triển khai" để tiếp tục hoàn thành tổng quan về triển khai, kiểm tra và các bước thực thi.

> [!TIP]
>
> Trước khi khởi tạo triển khai, vui lòng xác nhận lại rằng Dameng cấu hình đã được lưu và tất cả các mục xác minh đã vượt qua.
