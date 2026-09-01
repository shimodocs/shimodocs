# Xử lý sự cố Cài đặt

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

> [!TIP]
>
> Các vấn đề phổ biến trong giai đoạn cài đặt thường thuộc các loại sau đây.

## 1 Đồng bộ hóa thời gian bị lệch

Triệu chứng vấn đề:

* Không thể đăng nhập

* Lỗi xác thực

* Ngoại lệ khi gọi dịch vụ

Yêu cầu xử lý:

* Đầu tiên kiểm tra độ lệch thời gian của tất cả các nút

* Sau khi sửa chữa NTP/dịch vụ đồng bộ thời gian, tiếp tục với cài đặt hoặc chấp nhận

Lệnh điều tra:

```plain
timedatectl status
date
```


## 2 Cấu hình đường dẫn đĩa dữ liệu sai

Hiện tượng:

* Sau khi cài đặt, đĩa nhanh chóng đầy

* Ghi dữ liệu thất bại

* Thư mục lưu trữ dữ liệu nằm trên ổ hệ thống

Yêu cầu xử lý:

* Thư mục lưu trữ dữ liệu phải chỉ rõ trỏ đến ổ dữ liệu

* Dữ liệu kinh doanh không được lưu trong thư mục ổ hệ thống

Lệnh khắc phục sự cố:

```plain
findmnt -n -o TARGET -T /data
df -Th|egrep -v "overlay|tmpfs"
```


## 3 Kết nối dịch vụ phụ thuộc thất bại

Hiện tượng:

* Kiểm tra dịch vụ thất bại trong quá trình cài đặt

* Kết nối tới cơ sở dữ liệu, bộ nhớ đệm, hàng đợi tin nhắn hoặc lưu trữ đối tượng thất bại

Yêu cầu xử lý:

* Đầu tiên kiểm tra xem địa chỉ, cổng, tài khoản, và PASSWORD có được nhập đúng không

* Sau đó kiểm tra kết nối mạng và chính sách bảo mật

* Cuối cùng, kiểm tra xem dịch vụ mục tiêu có khả dụng không

Lệnh khắc phục sự cố:

```plain
nc -zv <MYSQL_HOST> 3306
nc -zv <REDIS_HOST> 6379
nc -zv <MONGO_HOST> 27017
nc -zv <KAFKA_HOST> 9092
```


## 4 Gói ngoại tuyến không khớp

Hiện tượng:

* Tải mirror thất bại

* Quá trình cài đặt báo cáo dịch vụ không thể khởi động và phiên bản không khớp

* Gói cài đặt không tương ứng với gói mirror ngoại tuyến

Yêu cầu xử lý:

* Xác nhận rằng gói cài đặt, gói hình ảnh ngoại tuyến và phiên bản sản phẩm là nhất quán

* Xác nhận xem gói cài đặt có phù hợp với CPU kiến trúc không

* Xác nhận rằng vật liệu từ các dự án khác nhau hoặc các ngày khác nhau không bị trộn lẫn

## 5 Trang cài đặt không thể mở

Hiện tượng:

* Trang Web UI không thể truy cập

* Cổng 18080 không lắng nghe

* Quá trình cài đặt đã kết thúc

Lệnh khắc phục sự cố:

```plain
ps -ef | grep mdp | grep -v grep
ss -lntp | grep 18080
tail -n 100 /root/nohup.out
```


## 6. Thứ tự khắc phục sự cố khuyến nghị

Khắc phục sự cố cài đặt theo thứ tự sau:

1. Đầu tiên, xác nhận xem có phải vấn đề môi trường: hệ thống, thời gian, đĩa, cổng, mạng

2. Xác nhận lại xem có phải vấn đề cấu hình: tên miền, thư mục, địa chỉ phụ thuộc, tài khoản PASSWORD

3. Xác nhận lại xem có phải vấn đề vật liệu: gói cài đặt, gói ngoại tuyến, khả năng tương thích kiến trúc

4. Cuối cùng, kiểm tra nhật ký cài đặt và trạng thái chạy của dịch vụ

Giải thích:

* Không lặp lại việc cài đặt nếu các điều kiện tiên quyết không được đáp ứng

* Không thực thi các lệnh với lý do thất bại rõ ràng giống nhau nhiều lần

## 7. Khi nào nên dừng cài đặt

Nếu xảy ra các tình huống sau, hãy dừng cài đặt trước và chỉ tiếp tục sau khi khắc phục các vấn đề cơ bản:

* Tất cả thời gian của các nút không đồng bộ

* Ổ đĩa dữ liệu không được gắn độc lập

* Dịch vụ phụ thuộc bên ngoài không thể truy cập

* Phiên bản vật liệu ngoại tuyến không nhất quán

* Dịch vụ cài đặt không khởi động đúng cách
