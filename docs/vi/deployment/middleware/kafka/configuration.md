# Kafka Cấu hình

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Tài liệu này nhằm hướng dẫn nhân viên triển khai, vận hành hoặc tích hợp hoàn thành ShimoDocs tích hợp với phần mềm trung gian tin nhắn bên ngoài Kafka từng bước, cho các kịch bản như xử lý tác vụ không đồng bộ, thông báo tin nhắn, đồng bộ dữ liệu và gửi nhật ký kiểm toán. 


## 1. Xác nhận trước khi vận hành 

## Kafka Yêu cầu cho phiên bản 


| Phần mềm trung gian | Phiên bản được đề xuất | Cho dưới 3000 người dùng | Cho hơn 3000 người dùng | 
| --- | --- | --- | --- | 
| Kafka | Kafka 3.5 | 2C 4G 300G SSD | 4C 8G 300G SSD | 


## Yêu cầu cấu hình 
- Broker >= 3 
- Yếu tố sao chép: số lượng bản sao mặc định là 3, trong môi trường sản xuất bắt buộc phải có ≥ 3 để đảm bảo tính khả dụng cao 
- Giữ tin nhắn: 72 giờ (có thể điều chỉnh theo nhu cầu kinh doanh) 
- Kích thước tối đa của tin nhắn đơn trên mỗi chủ đề: 10 MB 
- Xác thực: hỗ trợ SASL truy cập mã hóa (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512) 





## Kết nối mạng 

Các cổng để truy cập Kafka các instance từ K8s cụm kinh doanh phải được mở 

```js
telnet IP 9092
```



## Các yêu cầu khác
- Mạng nội bộ RTT được khuyến nghị < 5ms; giữa các trung tâm dữ liệu/khu vực được khuyến nghị < 20ms.
- Băng thông phải đáp ứng thông lượng cao nhất để tránh sự tắc nghẽn tin nhắn do mạng bị bão hòa.
- Đảm bảo rằng Kafka Thời gian của Broker và ShimoDocs máy chủ ứng dụng được đồng bộ hóa (NTP), vì sai lệch thời gian có thể ảnh hưởng đến việc sắp xếp thứ tự tin nhắn và TTL tính toán.
