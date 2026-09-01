# Cấu hình lưu trữ đối tượng

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Tài liệu này nhằm hướng dẫn nhân sự triển khai, vận hành hoặc tích hợp thực hiện ShimoDocs kết nối với bên thứ ba bên ngoài S3 lưu trữ đối tượng từng bước.


# 1. Xác nhận trước khi vận hành

## S3 Yêu cầu lưu trữ đối tượng

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> Ưu tiên đám mây công cộng, cần hỗ trợ HTTPS truy cập từ bên ngoài



## Kết nối Mạng

Các cổng cho K8s mạng cụm kinh doanh kết nối với phiên bản lưu trữ đối tượng cần được mở

```js
telnet IP 9000
```
## Kiểm soát truy cập và Quyền hạn
- Cung cấp đầy đủ thông tin xác thực AK/SK
- Phải hỗ trợ đầy đủ các giao diện lõi như PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload



# S3 Mô tả Yêu cầu Lưu trữ
- Yêu cầu độ trễ: Trong môi trường mạng nội bộ, thời gian phản hồi trung bình của lưu trữ API được khuyến nghị < 50ms; trong môi trường mạng công cộng, được khuyến nghị < 200ms. Độ trễ cao sẽ ảnh hưởng trực tiếp đến tốc độ mở tài liệu và trải nghiệm tải lên tệp đính kèm.
- Khả năng đồng thời: phải hỗ trợ đỉnh điểm ước tính của doanh nghiệp QPS. ShimoDocs sẽ tạo ra lưu lượng bùng nổ trong quá trình cộng tác nhiều người dùng và nhập/xuất lô, do đó phía lưu trữ không được có các chính sách giới hạn tốc độ quá nghiêm ngặt.
- Khả dụng SLA: Khuyến nghị khả dụng của lưu trữ trong môi trường sản xuất ≥ 99,9%.
- Giao tiếp lưu trữ qua mạng công cộng phải được thực hiện thông qua HTTPS/TLS các kênh được mã hóa.
- Đồng bộ thời gian: dịch vụ S3 lưu trữ và ShimoDocs máy chủ ứng dụng phải đồng bộ qua NTP, nếu không S3 xác minh chữ ký sẽ thất bại.
