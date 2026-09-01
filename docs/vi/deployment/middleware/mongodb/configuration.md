# MongoDB Cấu hình

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Bài viết này nhằm hướng dẫn nhân viên triển khai, vận hành hoặc tích hợp hoàn thành việc tích hợp ShimoDocs với bên ngoài MongoDB từng bước một

## 1. Xác nhận Trước Khi Thao Tác

## MongoDB Yêu cầu Phiên bản


| Phần mềm trung gian | Phiên bản được đề xuất | Dưới 3000 Người dùng | Trên 3000 Người dùng |
| --- | --- | --- | --- |
| MongoDB | MongoDB 4.4 | 2C 8G 100G SSD | 4C 16G 100G SSD |


## Yêu cầu Cấu hình Cluster
- Hỗ trợ các cụm sao lưu (replica set) có độ khả dụng cao, ít nhất 3 nút là bắt buộc trong môi trường sản xuất
- Khuyến nghị bật SCRAM-SHA-256 xác thực





## Kết nối Mạng

Các cổng cho K8s các cụm kinh doanh truy cập MongoDB phải được mở

```js
telnet IP 27017
```

## Xác thực và Phân quyền
- Trong môi trường sản xuất, khuyến nghị thực thi SCRAM-SHA-256 xác thực.


## Các yêu cầu khác
- Mạng nội bộ P99 độ trễ đọc < 5ms, độ trễ ghi < 10ms
- Ổ đĩa IOPS phải đáp ứng yêu cầu ghi đỉnh; SSD là bắt buộc
- Đồng bộ hóa đồng hồ: MongoDB các nút cụm và ShimoDocs các máy chủ ứng dụng phải được NTP đồng bộ hóa
- Sao lưu đầy đủ định kỳ và sao lưu Oplog liên tục
