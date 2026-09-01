# gRPC Công cụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

> [!TIP]
>
> Người dùng gRPC Công cụ được sử dụng để kết nối với các gRPC dịch vụ nội bộ, xem Dịch vụ và Phương thức, và khởi tạo các cuộc gọi gỡ lỗi phương thức Nhị phân.
>
> Trang hỗ trợ ba cách để chọn mục tiêu: nhập địa chỉ thủ công, chọn theo Kubernetes Dịch vụ, hoặc chọn theo Pod.

## 1. Truy cập gRPC

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **Dịch vụ Hệ thống** ở phía trên.
3. Mở rộng **Công cụ Middleware** trong thanh điều hướng bên trái.
4. Chọn **gRPC**.

Trang đầu tiên hiển thị khu vực "Mục tiêu" để chọn gRPC dịch vụ cần kết nối.

## 2. Phương pháp Chọn Mục tiêu

Trang cung cấp ba chế độ mục tiêu:

| Chế độ | Mô tả |
| --- | --- |
| Thủ công | Nhập địa chỉ gRPC thủ công, ví dụ, `svc-user:50051`. |
| Dịch vụ | Chọn mục tiêu theo Cluster, Namespace, Dịch vụ và Cổng. |
| Pod | Chọn mục tiêu theo cluster, Namespace, Pod và Cổng. |

## 3. Kết nối Thủ công 

1. Chọn **Thủ công**. 
2. Nhập gRPC địa chỉ dịch vụ trong **Ô Địa chỉ** nhập liệu. 
3. Nhấp **Kết nối**. 
4. Sau khi kết nối thành công, trang sẽ vào không gian làm việc gỡ lỗi Dịch vụ / Phương thức. 

## 4. Kết nối theo Dịch vụ

1. Chọn **Dịch vụ**.
2. Chọn cluster và Namespace mục tiêu.
3. Trong **Dịch vụ / Cổng** menu thả xuống, chọn dịch vụ và cổng mục tiêu.
4. Nếu danh sách dịch vụ không được cập nhật, nhấn **Làm mới (Refresh)**.
5. Nhấp **Kết nối**.

## 5. Kết nối theo Pod

1. Chọn **Pod**.
2. Chọn cluster và Namespace mục tiêu.
3. Trong **Pod / Cổng** menu thả xuống, chọn Pod và cổng mục tiêu.
4. Nếu danh sách Pod không được cập nhật, nhấn **Làm mới (Refresh)**.
5. Nhấp **Kết nối**.

## 6. Chọn Dịch vụ và Phương thức

Sau khi kết nối thành công, trang được chia thành danh sách Dịch vụ, danh sách Phương thức, khu vực yêu cầu và khu vực phản hồi.

1. Chọn dịch vụ mục tiêu từ danh sách Dịch vụ ở bên trái.
2. Bạn có thể sử dụng hộp tìm kiếm Dịch vụ để lọc các dịch vụ.
3. Chọn phương thức mục tiêu từ danh sách Phương thức.
4. Tùy chọn bộ lọc phương thức có thể chuyển đổi: `Unary`, `Streaming`, `All`.
5. Bạn có thể sử dụng hộp tìm kiếm Phương thức để lọc các phương thức.

> Trang hiện tại chỉ hỗ trợ gọi các phương thức Unary. Các phương thức Streaming sẽ hiển thị là không khả dụng.

## 7. Điền Tham số Yêu cầu

Khu vực yêu cầu hỗ trợ hai cách điền:

| Phương thức | Mô tả |
| --- | --- |
| Chế độ Form | Trang tạo một biểu mẫu dựa trên các trường đầu vào của phương thức. |
| JSON Chế độ | Khi **JSON Chế độ** được bật, chỉnh sửa trực tiếp toàn bộ JSON nội dung yêu cầu. |

Các bước sử dụng Chế độ Form:

1. Chọn Phương thức mục tiêu.
2. Điền các tham số yêu cầu từng trường một.
3. Sử dụng hộp thả xuống để chọn các trường liệt kê.
4. Chọn `true` hoặc `false` cho các trường boolean.
5. Sử dụng dấu phẩy như được chỉ định trên trang cho các trường lặp lại.

Các bước sử dụng JSON Chế độ:

1. Bật công tắc **JSON Chế độ** .
2. Điền toàn bộ JSON vào hộp văn bản.
3. Đảm bảo định dạng JSON là hợp lệ.

## 8. Điền Metadata

1. Mở rộng **Metadata** trong khu vực yêu cầu.
2. Điền Khóa và Giá trị.
3. Để thêm nhiều mục Metadata, nhấp vào **Thêm**.
4. Để xóa một hàng, nhấp vào biểu tượng xóa.

Metadata thường được sử dụng để truyền thông tin xác thực, ID yêu cầu, hoặc bối cảnh doanh nghiệp.

## 9. Khởi tạo cuộc gọi và xem phản hồi

1. Xác nhận mục tiêu, Dịch vụ, Phương thức, nội dung yêu cầu và Metadata.
2. Nhấp **Gọi** ở góc trên bên phải của trang.
3. Xem trạng thái, thời gian đã trôi qua, Metadata phản hồi, và phản hồi JSON trong khu vực phản hồi.
4. Nếu cuộc gọi thất bại, khu vực phản hồi sẽ hiển thị trạng thái lỗi và nội dung lỗi.

## 10. Chuyển hoặc Kết nối lại Mục tiêu

1. Nhấp **Kết nối** ở đầu trang để tải lại định nghĩa dịch vụ của mục tiêu hiện tại.
2. Nhấp **Thay đổi Mục tiêu** để quay lại trang chọn mục tiêu.
3. Sau khi chuyển mục tiêu, bạn cần kết nối lại và chọn Dịch vụ / Phương thức một lần nữa.

## 11. Các Tình huống Khắc phục Sự cố Thường gặp

| Tình huống | Gợi ý Thao tác |
| --- | --- |
| Kiểm tra xem dịch vụ có kết nối được hay không | Chọn mục tiêu và nhấp **Kết nối** để xem danh sách Dịch vụ có thể được tải hay không. |
| Tìm các phương thức giao diện | Sử dụng tìm kiếm Dịch vụ và lọc tìm kiếm Phương thức. |
| Gỡ lỗi giao diện truy vấn | Chọn một phương thức Unary, điền các tham số yêu cầu, và nhấp **Gọi**. |
| Cần truyền qua context | Mở rộng Metadata và điền Key và Value tương ứng. |
| Phản hồi trống hoặc thất bại | Kiểm tra trạng thái phản hồi, nội dung lỗi, và Metadata. |
