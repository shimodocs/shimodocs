# MongoDB Công cụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

> [!TIP]
>
> MongoDB được sử dụng trong Nền Tảng Vận Hành để xem MongoDB cơ sở dữ liệu, bộ sưu tập và nội dung tài liệu. Nó phù hợp để khắc phục sự cố dữ liệu dựa trên tài liệu, trạng thái trung gian, bản ghi nhiệm vụ và dữ liệu kinh doanh với cấu trúc linh hoạt.
>
> Trang hỗ trợ tìm kiếm theo cơ sở dữ liệu hoặc bộ sưu tập, và sau khi chọn một bộ sưu tập, MongoDB JSON có thể sử dụng các truy vấn điều kiện.

## 1. Truy cập MongoDB

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **Dịch vụ Hệ thống** ở phía trên.
3. Mở rộng **Công cụ Middleware** trong thanh điều hướng bên trái.
4. Chọn **MongoDB**.

Bên trái của trang hiển thị cây cơ sở dữ liệu và bộ sưu tập, trong khi bên phải hiển thị điều kiện truy vấn và kết quả truy vấn.

## 2. Tìm kiếm Cơ sở dữ liệu hoặc Bộ sưu tập

1. Nhập DATABASE_NAME hoặc từ khóa tên bộ sưu tập trong hộp tìm kiếm ở trên cùng bên trái.
2. Xem danh sách cây đã được lọc.
3. Xóa hộp tìm kiếm để khôi phục hiển thị tất cả cơ sở dữ liệu.

## 3. Mở rộng cơ sở dữ liệu và chọn một bộ sưu tập

1. Tìm cơ sở dữ liệu mục tiêu trong cây bên trái.
2. Nhấp vào biểu tượng mở rộng bên trái cơ sở dữ liệu để tải danh sách các bộ sưu tập.
3. Chọn bộ sưu tập mục tiêu.
4. Trang bên phải sẽ tự động thực hiện truy vấn một lần với điều kiện mặc định `{}`.

> Chỉ chọn cơ sở dữ liệu sẽ không thực hiện truy vấn bộ sưu tập; bạn cần chọn một bộ sưu tập cụ thể trước, và sau đó khu vực truy vấn sẽ được hiển thị bên phải.

## 4. Điền vào Điều kiện Truy vấn

1. Thông tin kết nối máy chủ. MongoDB JSON điều kiện truy vấn trong hộp nhập truy vấn bên phải.
2. Chọn số kết quả trả về, hỗ trợ `limit: 10`, `limit: 20`, `limit: 50`.
3. Nhấp **Truy vấn**.

Ví dụ Truy vấn: 

```json
{
  "_id": "task-123"
}
```

Ví dụ truy vấn theo trường:

```json
{
  "status": "running"
}
```

## 5. Xem Kết quả Truy vấn

1. Sau khi truy vấn hoàn tất, kiểm tra các tài liệu trả về trong khu vực kết quả phía bên phải.
2. Theo mặc định, kết quả được hiển thị ở JSON định dạng.
3. Nhấp **Mở rộng** để mở rộng tài liệu hiện tại.
4. Nhấp **Thu gọn** để thu gọn tài liệu hiện tại.
5. Nhấp **Sao chép** để sao chép tài liệu hiện tại JSON.

## 6. Các Tình huống Khắc phục Sự cố Thường gặp

| Tình huống | Đề xuất Vận hành |
| --- | --- |
| Xác nhận xem tài liệu có tồn tại không | Sau khi chọn bộ sưu tập, truy vấn bằng `_id` hoặc ID doanh nghiệp. |
| Kiểm tra trạng thái công việc | Truy vấn theo ID công việc và kiểm tra trường trạng thái và trường thời gian cập nhật. |
| Tìm một loại bản ghi | Sử dụng kết hợp các trường như trạng thái, loại và thời gian tạo để truy vấn. |
| Kết quả trống | Kiểm tra xem đã chọn đúng cơ sở dữ liệu, bộ sưu tập, tên trường và loại trường chưa. |
| Cần mang đi kết quả khắc phục sự cố | Nhấp **Sao chép** vào một kết quả đơn. |
