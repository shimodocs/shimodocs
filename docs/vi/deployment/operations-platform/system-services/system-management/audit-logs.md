# Nhật ký Kiểm toán

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Nhật ký hoạt động được sử dụng để xem và theo dõi các hoạt động quản lý người dùng trong hệ thống, giúp giải quyết sự cố, kiểm toán bảo mật và theo dõi thay đổi.

Trang này chỉ đọc và không hỗ trợ sửa đổi hoặc xóa các bản ghi nhật ký.

## Truy cập Trang

Sau khi đăng nhập vào quản trị backend, chọn **Nhật ký Hoạt động** trong điều hướng bên trái để truy cập trang.

## Lọc Nhật ký

Bạn có thể truy vấn bằng cách sử dụng kết hợp các điều kiện sau:

- **Nguồn Sự kiện**: Ví dụ, Bảng Điều khiển, Trung tâm Cấu hình Ứng dụng, Bộ Cập nhật, Kubernetes Quản lý Tài nguyên, hoặc Trung tâm Quản lý Người dùng.
- **Loại Hoạt động**: Hiển thị các hoạt động tương ứng dựa trên nguồn sự kiện, chẳng hạn như cập nhật cấu hình, nâng cấp phiên bản, khởi động lại dịch vụ hoặc các hành động quản lý người dùng.
- **Người dùng Thực hiện**: Lọc các bản ghi được tạo bởi một người dùng cụ thể.

Sau khi chọn nguồn sự kiện, danh sách loại thao tác sẽ tự động điều chỉnh. Nhấp vào **Tìm kiếm** để áp dụng các điều kiện lọc, hoặc nhấp vào nút đặt lại để xóa các điều kiện.

## Xem Danh sách Nhập

Danh sách chủ yếu hiển thị:

- ID nhật ký.
- Nguồn sự kiện và loại thao tác.
- Người dùng thực hiện.
- Loại, tên và ID của đối tượng được thao tác.
- Thời gian thao tác. 

Tổng số nhật ký sẽ được hiển thị ở đầu trang, và danh sách hỗ trợ phân trang và điều chỉnh số lượng mục hiển thị trên mỗi trang. 

## Xem Chi tiết Nhật ký 

Nhấp **Chi tiết** ở phía bên phải của bản ghi để xem thông tin đầy đủ, bao gồm: 

- Tên và mã định danh nội bộ của nguồn và loại thao tác. 
- Người dùng thực hiện thao tác và ID người dùng. 
- Thời gian thao tác. 
- Loại đối tượng, ID đối tượng và tên đối tượng. 
- Siêu dữ liệu sự kiện. 

Đối với các thay đổi trong trung tâm cấu hình ứng dụng, chi tiết cũng có thể hiển thị các sửa đổi cấu hình, liệu có khởi động lại tự động sau khi phát hành hay không, và các khối lượng công việc đã được khởi động lại.

## Các trường hợp sử dụng phổ biến

- Kiểm tra ai đã thực hiện thay đổi cấu hình cụ thể.
- Xác nhận thời gian nâng cấp hệ thống, khởi động lại dịch vụ, hoặc các thao tác mở rộng.
- Theo dõi các thay đổi liên quan dựa trên tên đối tượng.
- Xác minh sự khác biệt cấu hình và kết quả thực thi bằng cách sử dụng siêu dữ liệu sự kiện.
- Điều tra thao tác sai hoặc thay đổi quản trị không mong muốn.

## Tình huống phổ biến

- **Không tìm thấy bản ghi**: Thử xóa điều kiện lọc, hoặc xác nhận xem nguồn, loại thao tác và người dùng được chọn có khớp hay không.
- **Danh sách loại thao tác trống**: Trước tiên hãy chọn nguồn sự kiện, hoặc tải lại trang để lấy danh sách mới nhất.
- **Thông tin đối tượng trống**: Một số sự kiện hệ thống có thể không liên quan đến các đối tượng cụ thể, điều này là bình thường.
- **Siêu dữ liệu không phải nội dung định dạng**: Một số sự kiện lịch sử có thể được lưu dưới dạng văn bản thuần, và trang sẽ hiển thị nội dung gốc.
- **Số lượng nhật ký không khớp với mong đợi**: Nhật ký chỉ ghi lại các thao tác đã được hệ thống kiểm tra, và có thể bị ảnh hưởng bởi chính sách lưu giữ của môi trường.

> Nhật ký thao tác có thể chứa thông tin người dùng, định danh đối tượng và thông tin thay đổi cấu hình, và chỉ nên được truy cập bởi nhân sự được ủy quyền.
