# Cài đặt Nâng cao

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Cài đặt nâng cao được sử dụng để quản lý tùy chỉnh của hệ thống `pd-config` trực tiếp thông qua YAML. Điều này phù hợp để xử lý các tham số nâng cao không có trên trang cài đặt tiêu chuẩn hoặc để cấu hình hàng loạt.

Hệ thống sẽ hợp nhất các cấu hình tùy chỉnh với các cấu hình mặc định của nhà sản xuất. Các giá trị tùy chỉnh cùng đường dẫn sẽ ghi đè các giá trị mặc định, trong khi các cấu hình không được điền sẽ tiếp tục sử dụng các giá trị mặc định của nhà sản xuất.

## Truy cập Trang

Sau khi đăng nhập vào quản trị backend, chọn **Cài đặt Nâng cao** trong điều hướng bên trái để truy cập trang.

Cài đặt Nâng cao chỉ có sẵn cho quản trị viên. Trang này có thể ảnh hưởng đến toàn bộ hệ thống, nên được vận hành bởi những nhân sự quen thuộc với MDP cấu trúc cấu hình.

## Mô tả Trang

Trang được chia thành hai phần:

- **Cấu hình mặc định của nhà sản xuất (Factory Default pd-config)**: Cấu hình mặc định do gói cài đặt cung cấp, chỉ đọc.
- **Cấu hình tùy chỉnh (Custom pd-config)**: Cấu hình tùy chỉnh của khách hàng đang được lưu, có thể chỉnh sửa.

Cấu hình tùy chỉnh không cần sao chép toàn bộ nội dung mặc định; thường chỉ giữ lại các mục cấu hình cần ghi đè hoặc thêm mới.

## Chỉnh sửa và Xuất bản Cấu hình

Khuyến nghị thực hiện theo các bước sau:

1. Nhấp **Làm mới (Refresh)** để đảm bảo tải cấu hình tùy chỉnh mới nhất.
2. So sánh với cấu hình mặc định của nhà sản xuất bên trái, và chỉnh sửa YAML nội dung bên phải.
3. Nhấp **Xuất bản (Publish)**.
4. Kiểm tra nội dung được thêm, xóa và chỉnh sửa trong cửa sổ xác nhận khác biệt.
5. Sử dụng các nút khác biệt trước/tiếp theo để kiểm tra các thay đổi từng mục một.
6. Sau khi xác nhận không có lỗi, nhấn **Xác nhận Xuất bản**.

Sau khi xuất bản thành công, hệ thống sẽ tạo một tác vụ ứng dụng cấu hình và mở nhật ký tác vụ trong một cửa sổ mới. Tùy thuộc vào các thay đổi và cài đặt hệ thống, các dịch vụ liên quan có thể tự động khởi động lại.

## Lịch sử Cấu hình

Nhấp **Lịch sử** để xem các cấu hình tùy chỉnh đã xuất bản trước đây, bao gồm ID bản ghi, thời gian tạo, và MD5.

- Nhấp **Xem** để xem đầy đủ YAML của một phiên bản lịch sử.
- Sau khi chọn hai bản ghi, bạn có thể thực hiện so sánh sự khác biệt.

Trang hiện tại không cung cấp nút khôi phục một lần nhấp. Để khôi phục các cấu hình lịch sử, vui lòng kiểm tra phiên bản tương ứng, xác minh nội dung, sao chép thủ công vào khu vực chỉnh sửa và xuất bản lại.

## Ghi chú

- YAML cú pháp phải được giữ đúng; chú ý khoảng trắng, dấu hai chấm và kiểu dữ liệu.
- Không tự ý xóa các mục cấu hình mà bạn không hiểu.
- Trước khi xuất bản, hãy kiểm tra kỹ các khác biệt để tránh ghi đè các thay đổi vừa được các quản trị viên khác gửi.
- Các thay đổi quan trọng nên được thực hiện trong giờ thấp điểm, và cấu hình gốc nên được ghi lại trước.
- Sau khi xuất bản, kiểm tra nhật ký tác vụ để đảm bảo việc áp dụng cấu hình và kiểm tra trạng thái dịch vụ đã hoàn tất.

## Tình huống phổ biến

- **Xuất bản Thất bại**: Vui lòng kiểm tra YAML định dạng, tên trường, và kiểu giá trị cấu hình.
- **Khởi động lại Dịch vụ Sau Khi Xuất bản**: Thay đổi cấu hình có thể yêu cầu khởi động lại các dịch vụ liên quan, điều này là bình thường.
- **Trang tạm thời không truy cập được Sau Khi Xuất bản**: MDP hoặc các dịch vụ liên quan có thể đang khởi động lại; vui lòng làm mới sau khi đợi một lát.
- **Cấu hình Không Đạt Hiệu quả Mong đợi**: Vui lòng xác nhận đường dẫn cấu hình có đúng không và kiểm tra kết quả hợp nhất cuối cùng cùng nhật ký tác vụ.
- **Sửa đổi cấu hình sai**: Tìm phiên bản đúng trong lịch sử, sao chép nội dung và đăng lại.

> Cài đặt nâng cao sẽ ảnh hưởng đến cấu hình cấp hệ thống và hoạt động dịch vụ, không đăng trực tiếp các cấu hình chưa được kiểm chứng lên môi trường sản xuất.

## Ví dụ giao diện thao tác

Hình dưới đây hiển thị giao diện so sánh chỉnh sửa giữa cấu hình mặc định của nhà máy và cấu hình tùy chỉnh.

