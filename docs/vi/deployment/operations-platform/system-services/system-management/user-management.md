# Quản lý Người dùng Nền tảng

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Quản lý người dùng hệ thống được sử dụng để duy trì các tài khoản người dùng trong MDP quản lý backend, bao gồm tạo người dùng, chỉnh sửa thông tin cơ bản, đặt lại PASSWORD, quản lý xác thực hai yếu tố, và xóa người dùng.

## Truy cập Trang

Sau khi đăng nhập bằng tài khoản quản trị hệ thống, chọn **Quản Lý Người Dùng Hệ Thống** trong điều hướng bên trái để truy cập trang.

Menu này chỉ có thể truy cập bởi các tài khoản quản trị hệ thống được chỉ định. Nếu bạn không thấy menu này, vui lòng liên hệ với quản trị viên hệ thống của bạn.

## Xem Người Dùng

Trang hiển thị biệt danh người dùng, TÊN NGƯỜI DÙNG, vai trò, địa chỉ email, thông tin liên hệ, thời gian đăng nhập cuối cùng và thời gian đăng ký, đồng thời cung cấp thông tin tổng quan như tổng số người dùng, người dùng hoạt động gần đây và tài khoản quản trị.

Bạn có thể xem tất cả người dùng thông qua phân trang danh sách.

## Tạo Người Dùng Mới

Nhấp **Tạo Người Dùng Mới** và điền vào các thông tin sau:

- **Biệt danh**: Bắt buộc, dùng để hiển thị trên trang.
- **USERNAME**: Bắt buộc, dùng để đăng nhập vào hệ thống.
- **Email**: Bắt buộc, phải cung cấp địa chỉ email hợp lệ.
- **Thông Tin Liên Hệ**: Tùy chọn.
- **Vai Trò**: Chọn người dùng bình thường hoặc quản trị viên.

Sau khi tạo, hệ thống sẽ tạo ban đầu PASSWORD. Vui lòng sao chép ngay lập tức và cung cấp cho người dùng thông qua phương tiện an toàn, vì PASSWORD có thể không xem được nữa khi cửa sổ này bị đóng.

### Mô Tả Vai Trò Người Dùng

- Quản Trị Viên
  - Có thể sử dụng tất cả các trang dựa trên quyền toàn cầu
    - ShimoDocs Suite
    - Trung Tâm Tài Liệu
    - Dịch vụ Hệ thống
- Người Dùng Thường
  - Phạm vi sử dụng trang dựa trên quyền toàn cầu
    - ShimoDocs Suite
    - Trung Tâm Tài Liệu
    - Dịch Vụ Hệ Thống (ẩn)

## Chỉnh Sửa Thông Tin Người Dùng

Nhấp vào nút chỉnh sửa bên phải người dùng để sửa biệt danh, email và thông tin liên hệ của họ. USERNAME không thể thay đổi trên trang này sau khi tạo.

## Đặt lại PASSWORD

Sau khi nhấn nút Đặt lại PASSWORD và xác nhận thao tác, hệ thống sẽ tạo một PASSWORDmới. PASSWORD Nguyên bản sẽ ngay lập tức trở nên không hợp lệ.

Vui lòng sao chép và lưu trữ đúng cách PASSWORD, gửi nó đến người dùng tương ứng qua một kênh đáng tin cậy, và nhắc người dùng đăng nhập và thay đổi PASSWORD càng sớm càng tốt.

## Quản lý Xác thực Hai Yếu tố

- **Bật hoặc Tắt 2FA**: Sử dụng công tắc trong hàng của người dùng và tiếp tục trong cửa sổ xác nhận.
- **Đặt lại 2FA**: Hệ thống sẽ tạo một mã QR mới và Bí mật, và thông tin xác thực ban đầu sẽ trở nên không hợp lệ.

Sau khi đặt lại, người dùng nên sử dụng các ứng dụng xác thực như Authenticator để quét lại và liên kết. Mã QR và Secret là thông tin nhạy cảm và không nên truyền qua các kênh công cộng. 

Liên kết 2FA

Thêm bằng cách quét với Authenticator và sử dụng mã 2FA 6 chữ số động cho các lần đăng nhập tiếp theo

## Xóa Người dùng

Sau khi nhấn nút xóa và xác nhận, tài khoản người dùng sẽ bị gỡ bỏ. Hành động xóa không thể hoàn tác, vì vậy hãy đảm bảo tài khoản không còn sử dụng và hoàn tất việc bàn giao dữ liệu và quyền hạn cần thiết trước khi thực hiện.

## Tình huống phổ biến

- **Không thể tạo người dùng**: Vui lòng kiểm tra xem USERNAME có bị trùng không, định dạng email có đúng không, và tất cả các trường bắt buộc đã đầy đủ chưa.
- **Người dùng không thể đăng nhập**: Xác minh rằng USERNAME và PASSWORD là đúng; nếu cần, hãy đặt lại PASSWORD.
- **Người dùng không thể hoàn tất xác thực 2FA**: Đảm bảo thời gian hệ thống chính xác, hoặc đặt lại 2FA cho người dùng và liên kết lại.
- **Menu quản lý người dùng không hiển thị**: Tài khoản hiện tại có thể không phải là tài khoản quản trị hệ thống được chỉ định.
- **Xóa người dùng vô tình**: Hành động xóa không thể hoàn tác trực tiếp; tài khoản cần được tạo lại và quyền liên quan cần được cấu hình lại.

> Thông tin xác thực được tạo khi tạo, đặt lại PASSWORD, và đặt lại 2FA nên được lưu ngay lập tức và chỉ cung cấp cho chủ tài khoản.
