# Chặn Hợp tác

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Mô tả Chức năng

Khi có tồn đọng trong Kafka, và xác nhận rằng tồn đọng bất thường được gây ra bởi một tệp nhất định, bạn có thể sử dụng chức năng cấm này để cấm chỉnh sửa tệp đó, từ đó giải quyết vấn đề tồn đọng Kafka 

## Minh họa Sử dụng

1. Chọn chặn hợp tác 

2. Nhập guid của tệp, nhấn mạnh: điều này đề cập đến guid bên trong ShimoDocs, không phải id tệp của khách hàng 

Nhập ShimoDocs tệp GUID và nhấn 'Thêm vào Chặn'; tệp sẽ bị cấm chỉnh sửa trong vòng 3 phút. 

Nhấn nút 'Bỏ chặn' để khôi phục chức năng chỉnh sửa của tệp 

### Cách lấy GUID 

1. Mở công cụ phát triển trình duyệt 

2. Lọc các yêu cầu pull 

3. Trong yêu cầu, chuỗi 16 ký tự từ rp3OMYnMrdcQJZkm là GUID 

### Cách xác định hiệu quả của việc chặn

Tài liệu không thể lưu thành công; sau khi chỉnh sửa tệp, một cửa sổ bật lên ngoại tuyến xuất hiện sau 2 phút, và dữ liệu bị mất sau khi trang được làm mới.

### Khi nào nên bỏ chặn

Không khuyến nghị bỏ cấm. Nói chung, điều này là do tệp quá lớn khiến máy chủ không thể hỗ trợ chỉnh sửa. Sau khi bị cấm, tệp trở thành chỉ đọc. Khuyến nghị sao chép thủ công nội dung tệp sang tệp mới.
