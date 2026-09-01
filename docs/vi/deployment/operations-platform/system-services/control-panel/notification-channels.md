# Kênh Thông báo

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Các kênh thông báo được sử dụng để quản lý tập trung cách nhận các thông báo cảnh báo hệ thống, cho phép kiểm tra trung gian và các chức năng khác gửi thông báo sự cố và hồi phục.

Các kênh hiện được hỗ trợ bao gồm WeCom, DingTalk, Feishu và Webhook tùy chỉnh.

## Truy cập Trang

Sau khi đăng nhập vào bảng điều khiển quản trị, chọn **Kênh Thông báo** trong điều hướng bên trái để truy cập trang.

Các kênh thông báo chỉ có sẵn cho quản trị viên. Nếu bạn không thấy menu này, vui lòng liên hệ với quản trị viên hệ thống để xác nhận quyền truy cập tài khoản của bạn.

## Tạo kênh thông báo mới

Nhấp **Tạo kênh**, nhập tên kênh, và chọn loại kênh:

- **WeCom**: Nhập Key Webhook của robot.
- **DingTalk**: Nhập Webhook đầy đủ URL, và tùy chọn có thể nhập Secret ký theo cấu hình robot.
- **Feishu**: Nhập Webhook đầy đủ URL, và tùy chọn có thể nhập Secret ký theo cấu hình robot.
- **Webhook tùy chỉnh**: Nhập phương thức yêu cầu URL, HTTP và mẫu body.

Xác nhận có bật kênh hay không và sau đó nhấn **Lưu**.

## Webhook tùy chỉnh

Mẫu Body của Webhook tùy chỉnh hỗ trợ các biến sau: 

```text
{{title}}
{{body}}
{{level}}
```

Ví dụ mẫu mặc định: 

```json
{"title":"{{title}}","body":"{{body}}","level":"{{level}}"}
```

Khi hệ thống gửi thông báo, nó sẽ thay thế các biến bằng tiêu đề, nội dung và mức cảnh báo thực tế. 

## Kênh Thử Nghiệm 

Sau khi lưu, nhấp **Thử Nghiệm** ở phía bên phải của kênh. Hệ thống sẽ gửi một tin nhắn thử nghiệm để xác minh xem địa chỉ Webhook, chữ ký và kết nối mạng có chính xác hay không. 

Nên thử nghiệm ngay sau khi tạo hoặc chỉnh sửa kênh, trước khi liên kết nó với kiểm tra trung gian hoặc các chức năng nghiệp vụ khác. 

## Kích hoạt, Chỉnh sửa và Xóa 

- **Bật/Tắt**: Điều chỉnh trạng thái bật khi chỉnh sửa kênh. Khi tắt, kênh sẽ không nhận thông báo nghiệp vụ. 
- **Chỉnh sửa**: Bạn có thể thay đổi tên kênh, loại kênh và cấu hình Webhook. 
- **Xóa**: Xóa các kênh không còn sử dụng. Các kênh được tham chiếu bởi kiểm tra phần mềm trung gian phải được hủy liên kết trước khi có thể xóa. 

## Tình huống phổ biến

- **Gửi thử nghiệm thất bại**: Vui lòng kiểm tra địa chỉ Webhook, Key, Secret, HTTP phương thức và quyền truy cập mạng.
- **Lưu thất bại**: Vui lòng đảm bảo tất cả các trường bắt buộc đã được điền đầy đủ và định dạng Webhook URL là chính xác.
- **Không nhận được cảnh báo kinh doanh**: Vui lòng xác nhận kênh đã được bật và đã được chọn trên trang kinh doanh tương ứng.
- **Không thể xóa kênh**: Kênh này có thể vẫn đang được sử dụng bởi kiểm tra phần mềm trung gian. Vui lòng loại bỏ liên kết và lưu cấu hình kiểm tra trước.
- **Định dạng nội dung nhận Webhook tùy chỉnh không đúng**: Vui lòng kiểm tra xem mẫu Body có đáp ứng yêu cầu của hệ thống mục tiêu hay không.

> Địa chỉ Webhook và chữ ký Bí mật là thông tin nhạy cảm. Vui lòng hạn chế truy cập và tránh chia sẻ công khai qua ảnh chụp màn hình, nhật ký hoặc công cụ trò chuyện.

## Giao diện Vận hành Mẫu

Hình bên dưới cho thấy các loại kênh và biểu mẫu cấu hình khi tạo kênh thông báo mới.

