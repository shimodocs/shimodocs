# MySQL 8 Yêu cầu

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Tài liệu này nhằm hướng dẫn nhân sự triển khai, vận hành hoặc tích hợp thực hiện ShimoDocs kết nối đến một MySQL khởi tạo cơ sở dữ liệu 8, cũng như khởi động dịch vụ và kiểm tra kết nối từng bước.


## 1. Xác nhận trước khi vận hành

## MySQL Xác nhận thông số phiên bản

| Phiên bản được đề xuất | Người dùng dưới 3000 | Người dùng trên 3000 | 
| --- | --- | --- |
| MySQL 8.0 | 4C 8G 200G SSD | 8C 16G 200G SSD | 

## MySQL Yêu cầu cấu hình và độ sẵn sàng cao
Hỗ trợ chuyển đổi độ sẵn sàng cao master-slave
Bộ ký tự: utf8mb4 
Múi giờ: Châu Á/Thượng Hải hoặc UTC 
Kết nối: tối đa_kết nối ≥ 1000
Người dùng kết nối: quyền quản trị

> [!TIP]
>
> Phải cấu hình một MySQL phiên bản riêng biệt;
> 1. Để đạt được việc cách ly lỗi, bảo mật quyền, và sao lưu phục hồi độc lập, đảm bảo vận hành ổn định và hiệu quả của hệ thống tài liệu.
> 2. Hệ thống hiện tại không hỗ trợ tên cơ sở dữ liệu tùy chỉnh và tiền tố bảng, vì vậy việc lập kế hoạch và chuẩn bị một phiên bản riêng biệt phải hoàn tất trước khi triển khai.





## Kết nối mạng 
Các cổng để kết nối mạng cụm k8s kinh doanh với MySQL phiên bản cần được mở 

```js
telnet IP 3306
```
## Xác thực người dùng
Người dùng MySQL Người dùng được cung cấp cần được xác thực khi kết nối đến MySQL máy chủ

# Giải thích: 
- Người dùng MySQL Các cấu hình trong tài liệu đều là các thiết lập khuyến nghị, dùng cho việc đánh giá dung lượng dự án giai đoạn đầu và lập kế hoạch tài nguyên, và không phải cấu hình sản xuất cuối cùng. Cấu hình thực tế cuối cùng sẽ được xác định sau đánh giá tiền bán hàng.
- Khi sử dụng các máy chủ với kiến trúc trong nước CPU khuyến nghị ước lượng tổng tài nguyên gấp đôi thông số tiêu chuẩn
- Khuyến nghị dự phòng dung lượng cho mở rộng trong môi trường sản xuất chính thức và ưu tiên triển khai độ sẵn sàng cao
