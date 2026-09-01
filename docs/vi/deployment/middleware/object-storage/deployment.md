# Triển khai với lưu trữ đối tượng

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này giải thích cách vô hiệu hóa tính năng được tích hợp sẵn MinIO trong ShimoDocs và cấu hình của khách hàng S3 lưu trữ đối tượng như lưu trữ đối tượng bên thứ ba. S3 Sau khi cấu hình hoàn tất, trình cài đặt sẽ kiểm tra S3 kết nối mạng lưu trữ đối tượng, thông tin xác thực và quyền đọc/ghi bucket. Khi các kiểm tra vượt qua, việc triển khai có thể tiếp tục. 

# 1. Chuẩn bị trước khi cấu hình 
Trước khi bắt đầu, vui lòng xác nhận: 
- S3 lưu trữ đối tượng đã được cài đặt và đang chạy bình thường. 
- K8s các nút cụm có thể truy cập máy chủ và cổng của S3 lưu trữ đối tượng. 
- Xác thực AK/SK để kết nối với S3 lưu trữ đối tượng đã sẵn sàng. 
- S3 lưu trữ phải hỗ trợ truy cập trình duyệt khách hàng. 
- Được khuyến nghị sử dụng một S3 phiên bản riêng. 
- Chuẩn S3 phải cung cấp địa chỉ truy cập giao thức cho mạng nội bộ và bên ngoài. 
- ShimoDocs chuẩn doanh nghiệp S3 các bucket nên được tạo trước. 
- S3 lưu trữ phải sử dụng SSD ổ đĩa. 

## ShimoDocs Chuẩn Doanh Nghiệp S3 Danh sách Bucket 

| Tên Bucket | Quyền Truy Cập | Nguồn được phép | Phương thức được phép | Chế độ Truy Cập | Phơi bày_tiêu đề | 
| --- | --- | --- | --- | --- | --- | 
| tự động đề cập | Riêng tư Đọc/Ghi | - | - | Mạng nội bộ |  | 
| soạn nội dung | Riêng tư Đọc/Ghi | - | - | Mạng nội bộ |  | 
| công việc-fc | Riêng tư Đọc/Ghi | - | - | Mạng nội bộ |   |
| phiên bản thay đổi tệp | Riêng tư Đọc/Ghi | - | - | Mạng nội bộ |
| tệp đã tính toán | Riêng tư Đọc/Ghi | * | GET/HEAD | Mạng nội bộ Mạng bên ngoài | 
| nội dung tệp | Riêng tư Đọc/Ghi | * | GET/HEAD | Mạng nội bộ Mạng bên ngoài | x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor |
| mẫu tệp | Riêng tư Đọc/Ghi | - | - | Mạng nội bộ Mạng bên ngoài |
| lịch sử bảng tính | Riêng tư Đọc/Ghi | - | GET/HEAD | Mạng nội bộ Mạng bên ngoài | Access-Control-Allow-Origin x-amz-meta-compressor |
| lịch sử tài liệu svc | Riêng tư Đọc/Ghi | * | GET/HEAD | Mạng nội bộ | Access-Control-Allow-Origin x-amz-meta-compressor | 
| tài sản shimo | Công khai Đọc, Riêng tư Ghi | * | GET/HEAD | Mạng nội bộ Mạng bên ngoài | 
| tệp đính kèm shimo | Riêng tư Đọc/Ghi | * | GET/POST/PUT/HEAD | Mạng nội bộ Mạng bên ngoài |  |  |  |
| hình ảnh shimo | Đọc/ghi riêng tư | * | GET/POST/PUT/HEAD | Mạng nội bộ Mạng bên ngoài |  |  |  |
| người dùng shimo | Đọc/ghi riêng tư | - | - | Mạng nội bộ Mạng bên ngoài |  |  |  |
| ảnh đại diện shimo | Công khai đọc, riêng tư ghi | * | GET | Mạng nội bộ Mạng bên ngoài  |  |  |  |
| xem trước | Đọc/ghi riêng tư | * | GET/HEAD | Mạng nội bộ Mạng bên ngoài |Accept-Ranges x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor  |  |  |
| svc-drive | Đọc/ghi riêng tư | * | GET/POST/PUT | Mạng nội bộ Mạng bên ngoài |Accept-Ranges|  |  |
| svc-table | Đọc/ghi riêng tư | * | GET/HEAD |  Mạng nội bộ Mạng bên ngoài |  |  |  |
| file-snapshots |  Đọc/ghi riêng tư|  - | - |  Mạng nội bộ Mạng bên ngoài |  |  |  |

## Hướng dẫn cấu hình Bucket
- Hiển thị_Headers được khuyến nghị để chỉ định tên header, và không sử dụng * ký hiệu, vì một số nhà cung cấp có thể không hỗ trợ * ký hiệu, ví dụ Huawei Cloud OBS, Alibaba OSS
- Tên bucket có thể được cấu hình với tiền tố theo nhu cầu của công ty để tránh trùng lặp

# 2. Vào Cài đặt Nâng cao
Trong bước 'Cấu hình' của trình cài đặt, sau khi hoàn tất cấu hình mạng, môi trường mục tiêu và thông tin nút, mở rộng 'Cấu hình Nâng cao' ở cuối trang.

# 3. Hủy cài đặt MinIO
Trong khu vực 'Dịch vụ Middleware', bỏ chọn MinIO

Sau khi bỏ chọn, trình cài đặt sẽ không còn cài đặt tích hợp MinIO, và một bên ngoài S3 object storage đã được chuẩn bị sẽ được sử dụng sau. Việc middleware khác có sử dụng dịch vụ tích hợp hay không nên được chọn theo kế hoạch triển khai thực tế.

# 4. Mở cấu hình phần mềm trung gian của bên thứ ba
Trong khu vực 'Phần mềm trung gian của bên thứ ba', nhấp vào 'Cấu hình'.

# 5. Cấu hình S3 Object Storage
1. ChọnS3 Object Storage bên trái.
2. Kích hoạt "MinIO Object Storage."
3. Đối với Main Service/Editor Interaction, nhập lần lượt: AK/SK, endpoint nội bộ, endpoint công cộng, host, cổng, SSL, và các thông tin khác
4. ec41748c2ae1f83f1a73bfefaa8128d

> [!TIP]
>
> Main Service: Thể hiện object storage được sử dụng cho các dịch vụ ngoài việc chỉnh sửa cộng tác
> Editor Interaction: Thể hiện object storage được sử dụng bởi dịch vụ chỉnh sửa cộng tác
>
> Lưu ý: main service và editor interaction có thể dùng chung một thể hiện object storage, nhưng cung cấp một thể hiện riêng cho editor interaction có thể mang lại hiệu suất chỉnh sửa cộng tác tốt hơn

## Đặt tên Bucket

> [!NOTE]
>
> Khi nhiều ứng dụng kinh doanh chia sẻ cùng S3 thể hiện, khách hàng có thể thêm tiền tố theo ShimoDocsquy tắc đặt tên bucket để giúp phân biệt các doanh nghiệp khác nhau và quản lý các bucket

# 6. Xác nhận kết quả kiểm tra
Trình cài đặt sẽ kiểm tra những điều sau:
- đăng nhập: Tài khoản có thể xác thực bình thường
- kết nối: Môi trường triển khai có thể truy cập S3 lưu trữ đối tượng
- quyền: Tài khoản có quyền kết nối, xác thực, đọc/ghi bucket, v.v.

Sau khi tất cả các mục kiểm tra hiển thị 'Thành công', đóng cửa sổ cấu hình và quay lại trang 'Cấu hình' của trình cài đặt.

Nếu có bất kỳ mục nào thất bại, vui lòng kiểm tra theo hướng dẫn trên trang:
- Liệu host và port đã được điền chính xác.
- Liên kết mạng giữa node triển khai và S3 lưu trữ đối tượng đã được kết nối.
- Liệu USERNAME và PASSWORD là chính xác. 
- Xem tài khoản có các quyền cần thiết (kết nối và xác thực, quyền đọc/ghi bucket, v.v.) không. 

# 7. Tiếp tục Khởi tạo Triển khai 
Sau khi quay lại trang 'Cấu hình', đảm bảo rằng S3 Lưu trữ Đối tượng vẫn chưa được chọn, sau đó nhấp vào 'Khởi tạo Triển khai' để tiếp tục hoàn tất tổng quan triển khai, kiểm tra và các bước thực thi. 

> [!TIP] 
> 
> Trước khi khởi tạo triển khai, vui lòng xác nhận lại rằng S3 Cấu hình Lưu trữ Đối tượng đã được lưu và tất cả các mục xác thực đã vượt qua.
