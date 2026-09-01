# Sửa tài liệu

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

Các triệu chứng của tệp bị hư hỏng bất thường bao gồm tài liệu không mở đúng cách, xuất hiện các thông báo lỗi khi tải và nội dung không hiển thị

Khi tài liệu không thể mở, sử dụng chức năng này để sửa tệp

# Minh họa Sử dụng

Có 2 phương pháp sửa

## Chuẩn bị

### Tham khảo nhanh cho các loại tệp

|**loạiTệp**|**Tệp URL Địa chỉ tính năng**|**Ghi chú**|
|:----|:----|:----|
|rdoc(richdoc)|/**docs**/{fileguid}|Tài liệu nhẹ|
|mosheet(modoc)|/**sheets**/{fileguid}|Bảng|
|modoc(modoc)|/**docx**/{fileguid}|Tài liệu chuyên nghiệp|

### Cảnh báo rủi ro vận hành

Sửa thất bại không gây rủi ro

## Khôi phục từ dữ liệu được mã hóa

Chỉ hỗ trợ sửa các tệp dạng bảng. Đối với các loại tệp khác, chọn [Sửa từ dữ liệu lịch sử]

Đây là phương pháp ưu tiên. Bạn có thể trực tiếp nhập tệp GUID để thực hiện sửa chữa. Điều này GUID là ShimoDocs tệp GUID.

Nguyên tắc sửa là chuyển dữ liệu tệp mã hóa trong lưu trữ đối tượng thành dữ liệu nội dung tệp không mã hóa, áp dụng cho hầu hết các tình huống.

Nếu phương pháp này không sửa được, hãy chọn phương pháp khác.

### Tệp GUID

1. Mở công cụ phát triển trình duyệt

2. Lọc các yêu cầu pull

3. Trong yêu cầu, phần rp3OMYnMrdcQJZkm, chuỗi 16 ký tự này, là guid

## Khôi phục từ dữ liệu lịch sử

Khôi phục từ hồ sơ lịch sử

1. ID tệp khách hàng

2. Loại tệp

   1. Đối với tài liệu/trình bảng/thuyết trình truyền thống, chọn modoc

   2. Đối với các tài liệu nhẹ, chọn richdoc

1. Chọn Nguồn Dữ Liệu

### ID tệp khách hàng

Nếu khách hàng sử dụng ShimoDocs cho toàn bộ trang web, đó là địa chỉ tệp của tài liệu trong trình duyệt, ví dụ, m8AZMoYMrRsYbOkb sau đây

### Cách xác định nguồn dữ liệu 

Kiểm tra cấu hình của dịch vụ svc-edit 

Mục cấu hình: history.driver 

Nếu là mysql, công tắc 'Sử dụng Nguồn Dữ Liệu Mongo' tắt 

Nếu là mongo, công tắc 'Sử dụng Nguồn Dữ Liệu Mongo' bật 

