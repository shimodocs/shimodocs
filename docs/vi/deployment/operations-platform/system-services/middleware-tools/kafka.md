# Kafka Công cụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

> [!TIP]
>
> Người dùng Kafka công cụ cho phép bạn xem Kafka trạng thái cụm, chủ đề, tin nhắn, nhóm người tiêu dùng, và thông tin phân vùng thông qua Bảng điều khiển Redpanda, thường được sử dụng để khắc phục các vấn đề ghi tin nhắn, tồn đọng người tiêu dùng, và liên kết không đồng bộ.
>
> Khi trang tải thành công, Bảng điều khiển Redpanda sẽ được nhúng trong MDP.

## 1. Truy cập Kafka

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **Dịch vụ Hệ thống** ở phía trên.
3. Mở rộng **Công cụ Middleware** trong thanh điều hướng bên trái.
4. Chọn **Kafka**.
5. Chờ Bảng điều khiển Redpanda hoàn tất tải.

Nếu Bảng điều khiển chưa sẵn sàng, trang sẽ thông báo rằng nó đang khởi động hoặc khởi động thất bại và hiển thị thông tin lỗi.

## 2. Xem Tổng quan Cụm

Sau khi vào Kafka, **Tổng quan** được hiển thị mặc định.

Bạn có thể xem các thông tin sau:

| Thông tin | Mô tả |
| --- | --- |
| Trạng thái Cụm | Trạng thái chạy của cụm. |
| Dung lượng Lưu trữ Cụm | Dung lượng lưu trữ hiện tại của cụm. |
| Phiên bản Cụm | Thông tin phiên bản của cụm. |
| Brokers Trực tuyến | Số lượng broker đang trực tuyến. |
| Chủ đề | Số lượng chủ đề. |
| Bản sao | Số lượng bản sao. |
| Chi tiết Broker | ID broker, trạng thái và dung lượng. |

## 3. Xem Chủ đề

1. Trong điều hướng bên trái của Bảng điều khiển Redpanda, chọn **Chủ đề**.
2. Tìm chủ đề mục tiêu trong danh sách chủ đề.
3. Nhấp vào chủ đề để vào trang chi tiết.
4. Xem thông tin như phân vùng, tin nhắn và cấu hình của chủ đề.

Khắc phục sự cố Chủ đề Thường Tập trung vào:

| Thông tin | Mô tả |
| --- | --- |
| Phân vùng | Trạng thái phân vùng của Chủ đề. |
| Tin nhắn | Danh sách tin nhắn trong Chủ đề. |
| Cấu hình | Cấu hình Chủ đề, chẳng hạn như chính sách giữ lại. |

## 4. Xem Tin nhắn

1. Vào Chủ đề mục tiêu.
2. Mở khu vực xem tin nhắn.
3. Chọn phân vùng, vị trí hoặc phạm vi thời gian bằng các bộ lọc được cung cấp trên trang.
4. Xem Key, Value, Headers, Partition, Offset và Timestamp của tin nhắn.

> Nội dung tin nhắn có thể chứa các trường nghiệp vụ. Khi khắc phục sự cố, ưu tiên tìm theo ID nghiệp vụ, Key, Offset và timestamp.

## 5. Xem Nhóm Người tiêu dùng

1. Trong Bảng điều khiển Redpanda, chọn **Nhóm Người tiêu dùng** từ điều hướng bên trái.
2. Tìm kiếm hoặc chọn nhóm người tiêu dùng mục tiêu.
3. Nhập chi tiết nhóm người tiêu dùng.
4. Xem các Chủ đề, phân vùng, Offset Hiện tại, Offset Cuối Nhật ký và Lag liên quan của nhóm người tiêu dùng.

## 6. Xác định tồn đọng của người tiêu dùng

| Trạng thái | Mô tả |
| --- | --- |
| Lag là 0 | Nhóm người tiêu dùng hiện tại không có tồn đọng. |
| Lag liên tục tăng | Tốc độ tiêu thụ thấp hơn tốc độ sản xuất. |
| Lag không thay đổi nhưng không phải là 0 | Có thể có người tiêu dùng đã dừng, phân vùng bị tắc, hoặc lỗi tiêu thụ. |
| Lag của một phân vùng đơn lẻ rất cao | Có thể do một khóa nóng hoặc tiêu thụ bất thường trong phân vùng đó. |

## 7. Xem Các Broker

1. Trên trang Tổng quan, tìm **Chi tiết Broker**.
2. Kiểm tra ID Broker, trạng thái hoạt động và kích thước lưu trữ.
3. Nhấp **Xem** để xem chi tiết broker.

## 8. Các Tình Huống Khắc Phục Sự Cố Thông Dụng

| Tình huống | Đề Xuất Vận Hành |
| --- | --- |
| Xác nhận xem Kafka có hoạt động bình thường không | Kiểm tra Trạng Thái Cluster và các Brokers Đang Hoạt Động trong Tổng Quan. |
| Xác nhận xem các tin nhắn có đang được ghi không | Đi tới Topic và kiểm tra Tin nhắn. |
| Khắc phục sự chậm trễ của consumer | Đi tới Consumer Groups và kiểm tra Lag. |
| Tìm một tin nhắn riêng lẻ | Tìm kiếm theo Topic, Partition, Offset, Key hoặc thời gian. |
| Xác nhận cấu hình Topic | Đi tới chi tiết Topic và kiểm tra Cấu hình. |
