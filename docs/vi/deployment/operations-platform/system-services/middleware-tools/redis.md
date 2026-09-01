# Redis Công cụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

> [!TIP]
>
> Redis được sử dụng trong Nền Tảng Vận Hành để xem Redis các phiên bản, DB, danh sách các Key và chi tiết Key. Nó thường được sử dụng để khắc phục sự cố cache, session, khóa phân tán, bộ đếm giới hạn tốc độ và trạng thái ngắn hạn.
>
> Trang hỗ trợ tìm kiếm theo Key hoặc tiền tố Key, và hiển thị loại Key, TTLvà giá trị hiện tại.

## 1. Truy cập Redis

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **Dịch vụ Hệ thống** ở phía trên.
3. Mở rộng **Công cụ Middleware** trong thanh điều hướng bên trái.
4. Chọn **Redis**.

Bên trái của trang là khu vực truy vấn Key, và bên phải là khu vực chi tiết Key.

## 2. Chọn Redis Phiên bản và DB

1. Trong danh sách thả xuống đầu tiên ở góc trên bên trái, chọn Redis phiên bản.
2. Trong danh sách thả xuống thứ hai, chọn DB, ví dụ `db0`.
3. Trang sẽ tải danh sách Key dựa trên phiên bản và DB hiện tại.

Nếu danh sách DB trống hoặc trang báo lỗi, vui lòng kiểm tra trước xem Redis cấu hình phiên bản có bình thường không.

## 3. Tìm Key

1. Nhập tên Key hoặc tiền tố Key vào hộp tìm kiếm.
2. Nhấn nút tìm kiếm hoặc Enter để thực hiện truy vấn.
3. Xem danh sách Key ở bên trái.
4. Nếu cần tải lại danh sách trong điều kiện hiện tại, nhấn biểu tượng làm mới.

Gợi ý hộp tìm kiếm là "Vui lòng nhập tên key, hỗ trợ tìm kiếm gần đúng." Trang sẽ hiển thị loại Key phù hợp và TTL.

## 4. Xem danh sách Key

Danh sách Key chứa các thông tin sau:

| Thông tin | Mô tả |
| --- | --- |
| Loại |  Redis loại của Khóa, chẳng hạn như `string`, `hash`, `list`, `set`, `zset`. |
| Tên Khóa | Khóa đầy đủ hiện đang được khớp. |
| TTL | Thời gian hết hạn còn lại của Khóa; trang hiển thị "vĩnh viễn" nếu Khóa hiện tại không có hạn. |

## 5. Xem Chi tiết Khóa

1. Nhấp vào Khóa mục tiêu trong danh sách Khóa bên trái.
2. Khu vực chi tiết bên phải hiển thị tên Khóa, loại, TTLvà giá trị cụ thể.
3. Để làm mới chi tiết Khóa hiện tại, nhấp vào nút làm mới trong khu vực tiêu đề chi tiết.

Các loại phương thức hiển thị khác nhau như sau:

| Loại | Phương thức hiển thị |
| --- | --- |
| `string` | Hiển thị giá trị đầy đủ trong hộp văn bản. |
| `hash` | Hiển thị trường Khóa và Giá trị trong bảng. |
| `list` / `set` | Hiển thị danh sách các phần tử trong bảng. |
| `zset` | Hiển thị Điểm số và Thành viên trong bảng. |

## 6. Sao chép Giá trị Trường

1. Tìm trường hoặc giá trị bạn muốn sao chép trong bảng chi tiết Khóa.
2. Nhấp vào nội dung tương ứng.
3. Trang sẽ sao chép nội dung đó vào bảng nhớ tạm (clipboard).

> `string` Loại được hiển thị trong hộp văn bản và có thể sao chép trực tiếp bằng cách chọn văn bản; các loại bảng hỗ trợ nhấp vào giá trị để sao chép.

## 7. Các tình huống xử lý sự cố phổ biến

| Tình huống | Hành động đề xuất |
| --- | --- |
| Xác nhận nếu bộ nhớ đệm tồn tại | Sau khi chọn instance và DB, tìm kiếm theo Khóa đầy đủ hoặc tiền tố. |
| Kiểm tra nếu bộ nhớ đệm đã hết hạn | Kiểm tra TTL trong danh sách Khóa hoặc chi tiết. |
| Xem các trường Hash | Nhấp vào Khóa để xem các trường và giá trị trong bảng bên phải. |
| Xem dữ liệu ZSet đã sắp xếp | Nhấp vào `zset` Khóa để xem Điểm số và Thành viên. |
| Xem lại trạng thái mới nhất của cùng Khóa | Nhấp vào nút làm mới trong khu vực chi tiết. |
