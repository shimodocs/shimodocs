# Trung tâm cấu hình

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng 

Trung tâm Cấu hình được sử dụng để xem và chỉnh sửa cấu hình ứng dụng của các dịch vụ khác nhau. Trang hiển thị cả cấu hình mẫu của nhà máy và cấu hình hiện đang hoạt động, giúp dễ dàng hiểu sự khác biệt về cấu hình và phát hành các chỉnh sửa một cách có kiểm soát. 

Sau khi cấu hình được xuất bản, hệ thống sẽ lưu các sửa đổi này và có thể tự động khởi động lại các dịch vụ liên quan dựa trên lựa chọn của bạn để áp dụng cấu hình mới. 

## Truy cập Trang 

Sau khi đăng nhập vào hệ thống quản lý, chọn **Trung tâm cấu hình** trong điều hướng bên trái để truy cập trang. 

Trung tâm Cấu hình chỉ khả dụng cho quản trị viên. Nếu bạn không thấy menu này, vui lòng liên hệ với quản trị viên hệ thống để xác nhận quyền của tài khoản. 

## Mô tả Trang 

Trang chủ yếu được chia thành ba khu vực: 

- **Danh sách Ứng dụng và Tệp**: Hiển thị các tệp có thể cấu hình theo ứng dụng, có hỗ trợ tìm kiếm theo tên ứng dụng. 
- **Cấu hình Mẫu Nhà máy**: Hiển thị cấu hình gốc được cung cấp trong gói cài đặt, chỉ để xem và tham khảo. 
- **Cấu hình đang Hoạt động**: Hiển thị cấu hình hiện đang được môi trường sử dụng, có thể chỉnh sửa trực tiếp. 

Các tệp cấu hình thường nằm trong JSON, YAML, hoặc TOML định dạng. Vui lòng duy trì cú pháp tệp và cấu trúc dữ liệu đúng. 

## Chỉnh sửa và Xuất bản Cấu hình 

Khuyến nghị làm theo các bước dưới đây: 

1. Ở bên trái, chọn ứng dụng và tệp cấu hình bạn cần chỉnh sửa.
2. Tham khảo mẫu của nhà máy và chỉnh sửa nội dung cấu hình trong **Khu vực Cấu hình Hiệu lực.** 
3. Sau khi chỉnh sửa, trang sẽ hiển thị trạng thái **Đã chỉnh sửa nhưng chưa xuất bản**.
4. Nhấp **Đã chỉnh sửa nhưng chưa xuất bản**, hoặc sử dụng `Ctrl S` (Windows) / `Command S` (macOS) để mở cửa sổ xác nhận.
5. Kiểm tra đường dẫn trường, loại thay đổi và giá trị mới sẽ được xuất bản.
6. Chọn có bật hay không **Khởi động lại các dịch vụ liên quan sau khi xuất bản cấu hình<br>** theo nhu cầu.<br>
7. Nhấp **Xuất bản Cấu hình<br>** để hoàn tất việc sửa đổi.<br>

Nếu có lỗi định dạng trong nội dung cấu hình, hệ thống sẽ thông báo lỗi và ngăn chặn việc xuất bản. Vui lòng sửa lại và thử lại.<br>

## Xác nhận Thay đổi

Cửa sổ xác nhận trước khi xuất bản sẽ hiển thị các khác biệt cho sửa đổi này:

- **Đường dẫn**: Đường dẫn cấu hình đã thay đổi.
- **Thao tác**: Loại thay đổi, chẳng hạn như thêm, sửa hoặc xóa.
- **Giá trị**: Giá trị cấu hình sau khi thay đổi.

Khuyến nghị xác nhận từng sự khác biệt để tránh vô tình xóa cấu hình hoặc sửa đổi các tham số dịch vụ không chính xác.

## Khởi động lại dịch vụ

Một số cấu hình chỉ có hiệu lực sau khi dịch vụ được khởi động lại. Theo mặc định, trang cho phép **Khởi động lại các dịch vụ liên quan sau khi xuất bản cấu hình<br>**, và sau khi xuất bản thành công, các dịch vụ liên quan đến ứng dụng sẽ tự động được khởi động lại.

Nếu tùy chọn này bị tắt, cấu hình vẫn sẽ được xuất bản, nhưng các dịch vụ liên quan có thể cần được khởi động lại thủ công sau để áp dụng các thiết lập mới.

Trong quá trình khởi động lại dịch vụ, các chức năng liên quan có thể gặp biến động ngắn; khuyến nghị thực hiện các thay đổi cấu hình quan trọng vào giờ làm việc ngoài giờ cao điểm.

## Tình huống phổ biến

- **Không tìm thấy ứng dụng**: Vui lòng xóa tiêu chí tìm kiếm hoặc xác nhận rằng ứng dụng mục tiêu đã được triển khai chính xác.
- **Không thể tải tệp cấu hình**: Vui lòng kiểm tra trạng thái dịch vụ và quyền tài khoản hiện tại trước khi thử lại.
- **Lỗi định dạng cấu hình**: Vui lòng kiểm tra thụt lề, dấu ngoặc, dấu nháy và định dạng trường trong JSON, YAML, hoặc TOML.
- **Không có thay đổi để xuất bản**: Nội dung cấu hình thực tế không có thay đổi hiệu quả, không cần xuất bản.
- **Các thay đổi không có hiệu lực sau khi xuất bản**: Vui lòng xác nhận xem các dịch vụ liên quan đã được khởi động lại chưa, nếu cần, hãy khởi động lại thủ công và kiểm tra lại.
- **Phát hành thất bại**: Vui lòng kiểm tra nội dung cấu hình hoặc trạng thái dịch vụ theo hướng dẫn trên trang, và xuất bản lại sau khi đã xử lý. 

> Các thay đổi cấu hình có thể ảnh hưởng đến việc khởi động dịch vụ và các chức năng nghiệp vụ, vui lòng chỉ phát hành sau khi đã xác nhận đầy đủ các thay đổi. 

## Ví dụ về giao diện vận hành 

Hình dưới đây hiển thị các khu vực để chọn tệp cấu hình, xem nội dung cấu hình và chỉnh sửa. 

