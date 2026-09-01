# Tìm kiếm Thông tin Tệp

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Truy vấn Thông Tin Tệp được sử dụng để truy vấn các bản ghi cơ bản của tệp trong hệ thống dựa trên tệp nội bộ GUID hoặc ID tệp bên phía khách hàng, giúp dễ dàng kiểm tra các định danh tệp, ứng dụng liên quan, loại tệp, trạng thái và kích thước nội dung.

Trang này chỉ đọc và sẽ không sửa đổi nội dung hoặc trạng thái tệp.

## Truy cập Trang

Sau khi đăng nhập vào quản trị backend, chọn **Truy vấn Thông Tin Tệp** trong điều hướng bên trái để truy cập trang.

## Truy vấn Tệp

Trang hỗ trợ các điều kiện truy vấn sau:

- **Tệp Nội Bộ GUID**: tệp của `history_guid`.
- **Nhà Cung Cấp Tệp Khách Hàng GUID**: phía khách hàng `provider_file_id`.
- **App ID**: tùy chọn, khuyến nghị điền cùng với Nhà Cung Cấp GUID để chỉ định ứng dụng liên quan.

Ít nhất một trong các mục Tệp Nội Bộ GUID hoặc Nhà Cung Cấp Tệp Khách Hàng GUID nên được điền, sau đó nhấn **Truy vấn**.

Nếu chỉ điền Nhà Cung Cấp GUID và không điền App ID, hệ thống sẽ trả về tất cả các bản ghi phù hợp với Nhà Cung Cấp GUID, nên có thể xuất hiện nhiều kết quả. 

### Lấy Tệp GUID 
1. Trong trường hợp ShimoDocs Suite, bạn chỉ cần sử dụng địa chỉ của bộ phần mềm trong trình duyệt như **Nhà Cung Cấp Tệp Khách Hàng GUID**. 

## Kết quả truy vấn

Kết quả truy vấn chủ yếu bao gồm:

- **id**: ID khóa chính của bản ghi tập tin.
- **app_id**: ID của ứng dụng liên quan.
- **provider_tệp_id**: ID tập tin phía khách hàng.
- **history_guid**: Tập tin lịch sử trong hệ thống nội bộ GUID.
- **created_at**: Thời gian tạo bản ghi.
- **type**: Loại tập tin, chẳng hạn như tài liệu, bảng tính, bài thuyết trình, PDF, hình ảnh hoặc video.
- **created_by**: ID người tạo.
- **status**: Giá trị trạng thái hiện tại của tập tin.
- **tệp_content_kích thước**: Kích thước nội dung tập tin, tính bằng byte.

Các loại tập tin trong kết quả sẽ hiển thị cả số loại và tên tương ứng để dễ nhận biết.

## Tình huống phổ biến

- **Nhắc nhở: Phải nhập điều kiện truy vấn**: Vui lòng điền ít nhất tập tin nội bộ GUID hoặc Provider GUID.
- **Không tìm thấy tập tin**: Vui lòng kiểm tra xem định danh có đầy đủ không, hoặc xác nhận tập tin có thuộc môi trường hiện tại hay không.
- **Trả về nhiều bản ghi**: Provider GUID có thể bị trùng lặp trên nhiều ứng dụng; vui lòng thêm App ID và tìm kiếm lại.
- **Loại tập tin hiển thị là không xác định**: Số loại của bản ghi hiện tại có thể chưa được cấu hình tên tương ứng. Bạn có thể cung cấp số này cho bộ phận hỗ trợ kỹ thuật để xác nhận.
- **Không thể xác định giá trị trạng thái**: Trường trạng thái là giá trị bản ghi cơ sở và cần phân tích thêm kết hợp với hiện tượng kinh doanh cụ thể và nhật ký.

> Các định danh tập tin thuộc dữ liệu kinh doanh; vui lòng tránh chia sẻ trực tiếp chúng trong trò chuyện công khai hoặc phiếu ngoài.
