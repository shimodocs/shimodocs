# Nhật ký dịch vụ

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan tính năng 
Tính năng nhật ký dịch vụ là một nền tảng truy xuất nhật ký giống Kibana có thể thu thập nhật ký Pod từ nhiều ShimoDocs dịch vụ và cung cấp khả năng tìm kiếm, truy vấn và phân tích tỷ lệ nhật ký. 

## Nhập và Điều hướng
Menu bên trái: Dịch vụ Hệ thống --> Vận hành Dịch vụ --> Nhật ký Dịch vụ

## SQL Chế độ
Hộp nhập hỗ trợ truy vấn cú pháp ClickHouse SQL . Sau khi nhập SQL, bạn có thể thực thi truy vấn ở chế độ ClickHouse Raw.

Như hình dưới đây, nhập

``` sql
`_raw_log_` like '%access%'
```

có thể được sử dụng để truy vấn tất cả nhật ký chứa access. 

## Lọc theo điều kiện
Như hình dưới đây, nhấp vào nút "Thêm điều kiện" để thêm điều kiện lọc mới.

## Phân tích tỷ lệ
Như hình dưới đây, nhấp vào biểu tượng bên cạnh một trường trong bản ghi hàng để mở menu thả xuống. Sau khi chọn "Giá trị hàng đầu," bạn có thể xem tỷ lệ của trường đó trong khoảng thời gian hiện tại ở góc trên bên phải.

## Mô tả trường

| Trường tích hợp sẵn | Mô tả |
| --- | --- |
| lv | Mức lỗi của nhật ký, bao gồm info, error, warn |
| container.name | CONTAINER_NAME |
| method | Phương thức trong nhật ký truy cập; gRPC in ra gRPC phương thức, HTTP in ra API đường dẫn |
| peerIP | Địa chỉ IP của peer |
| tênPeer | Tên của peer, chẳng hạn như tên dịch vụ, v.v. |
| bộPhận | Thành phần trong nhật ký truy cập, chẳng hạn như server.begin |
| chiPhí | Thời gian tiêu tốn trong nhật ký truy cập, tính bằng mili giây |

## Phân Tích Trường Hợp
### Truy Vấn Tất Cả Nhật Ký Lỗi Trong Ngày

Trong Thêm Điều Kiện, chọn trường lv và thêm lv = error như hình dưới

### Xem Nhật Ký Yêu Cầu

    1. Sử dụng `msg`='access' để xem tất cả nhật ký yêu cầu, bao gồm HTTP và gRPC
    
2. Xem HTTP yêu cầu

3. Xem gRPC yêu cầu

