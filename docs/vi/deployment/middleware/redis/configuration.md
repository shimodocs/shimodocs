# Redis Cấu hình

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Tài liệu này nhằm hướng dẫn người triển khai, nhân viên vận hành hoặc nhà tích hợp hoàn tất việc tích hợp ShimoDocs với bên ngoài Redis từng bước. Nó thường được sử dụng cho các kịch bản cốt lõi như quản lý phiên, khóa phân tán, bộ đếm giới hạn tốc độ và hàng đợi thông điệp.

## 1. Xác nhận trước khi vận hành

## Redis Yêu cầu Phiên bản


| Phần mềm trung gian | Phiên bản được đề xuất | Dành cho dưới 3000 người dùng | Dành cho trên 3000 người dùng |
| --- | --- | --- | --- |
| Redis | Redis 6.2.21 | 2C 4G 100G SSD | 2C 8G 100G SSD |


## Yêu cầu Cấu hình Cluster
- Hỗ trợ khả năng cao master-slave/sentinel
- Dữ liệu bền vững
- Chế độ Cluster không được hỗ trợ
- Số lượng DB phải >= 64





## Kết nối Mạng

Các cổng để kết nối K8s mạng cluster kinh doanh với Redis instance phải được mở

```js
telnet IP 6379
```

## Xác thực và Phân quyền
- Trong môi trường sản xuất, khuyến nghị bật PASSWORD xác thực (requirepass / ACL).


## Các yêu cầu khác
- Mạng nội bộ P99 độ trễ nên < 10ms
- Đồng bộ hóa đồng hồ: Redis các nút cụm và ShimoDocs các máy chủ ứng dụng phải được NTP đồng bộ hóa
- Sao lưu đầy đủ định kỳ
