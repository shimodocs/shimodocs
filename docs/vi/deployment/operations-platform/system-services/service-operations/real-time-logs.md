# Nhật ký thời gian thực

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan tính năng

Nhật ký thời gian thực được sử dụng để xem nhật ký hoạt động của các dịch vụ trong một Kubernetes cụm trực tuyến, cho phép bạn nhanh chóng xác định các bất thường của dịch vụ, thất bại yêu cầu, và trì hoãn thực thi.

Các trường hợp sử dụng chính:
- Nhanh chóng lọc các nhật ký được kích hoạt theo thời gian thực
- Một giải pháp nhẹ khi hệ thống ghi nhật ký đầy đủ chưa được triển khai

Lưu ý: Nhật ký thời gian thực được lấy thông qua Kubernetes API, và dữ liệu nhật ký có thể bị ảnh hưởng bởi Kubernetes cập nhật theo vòng.

## Truy cập Trang

Sau khi đăng nhập vào bảng điều khiển quản lý, chọn **Nhật ký thời gian thực** từ điều hướng bên trái để vào trang.

Nhật ký thời gian thực chỉ được hỗ trợ trong chế độ triển khai. Kubernetes Nếu bạn không thấy menu này, vui lòng liên hệ với quản trị viên hệ thống của bạn để xác nhận chế độ triển khai và quyền truy cập tài khoản hiện tại của bạn.

## Truy vấn Nhật ký

Khuyến nghị thực hiện theo các bước sau:

1. Chọn **Cụm** và **NAMESPACE** bạn muốn truy vấn.
2. Chọn mục nhật ký, hỗ trợ Deployment, StatefulSet hoặc Pod, và có thể chọn nhiều mục cùng một lúc.
3. Chọn phạm vi nhật ký, có thể truy vấn 100 dòng gần nhất, 1000 dòng, 5000 dòng hoặc nhật ký từ 1 phút đến 24 giờ trước.
4. Để thu hẹp kết quả truy vấn, bạn có thể điền các điều kiện lọc theo từng dòng.
5. Nhấp **Bắt đầu** và trang sẽ tải nhật ký trong phạm vi đã chọn và liên tục hiển thị các nhật ký mới được tạo.

Nhấp **Dừng** để kết thúc việc kéo thời gian thực. Khi truy vấn được khởi động lại, các nhật ký trên trang hiện tại sẽ bị xóa và kết quả truy vấn mới sẽ được tải.

## Lọc Nhật ký

Lọc theo từng dòng không phân biệt chữ hoa chữ thường đối với chữ cái tiếng Anh. Nhấn Enter sau khi nhập các điều kiện để áp dụng chúng. Các cách sử dụng phổ biến như sau:

```text
error
error AND timeout
error OR warning
error NOT health
error AND (timeout OR deadline)
"connection refused"
```

- `AND`: Bao gồm nhiều điều kiện cùng lúc. 
- `OR`: Bao gồm bất kỳ điều kiện nào. 
- `NOT`: Loại trừ nội dung đã chỉ định. 
- `()`: Kết hợp nhiều điều kiện lọc. 
- `""`: Tìm kiếm cụm từ đầy đủ có chứa khoảng trắng. 

Bạn có thể nhấn nút trợ giúp ở bên phải hộp nhập để xem ví dụ cú pháp đầy đủ. Bạn cũng có thể sử dụng **Truy vấn Thường gặp** để nhanh chóng điền các mục nhật ký và điều kiện lọc đã được cài sẵn. 

## Xem Nhật ký 

Danh sách nhật ký hiển thị thời gian nhật ký, POD_NAME, và nội dung nhật ký. 

- Nhấn vào POD_NAME để sao chép tên đầy đủ. 
- Khi nội dung dài, bạn có thể mở rộng để xem đầy đủ nhật ký. 
- Nhật ký trong JSON định dạng có thể được mở rộng thành nội dung đã định dạng và hỗ trợ sao chép chỉ với một cú nhấp. 
- Khi có nhiều nhật ký, trang sẽ tự động phân trang, và bạn có thể nhanh chóng nhảy lên đầu hoặc cuối bằng các nút ở đầu danh sách. 

## Phân bố khối lượng nhật ký 

Biểu đồ phân bố khối lượng nhật ký trên trang hiển thị số lượng nhật ký theo các khoảng thời gian khác nhau và hiển thị tổng số dòng nhật ký và số dòng khớp sau khi lọc. 

Bạn có thể kéo để chọn phạm vi thời gian trên biểu đồ phân bố, và danh sách nhật ký sẽ chỉ hiển thị nội dung trong phạm vi thời gian đó, phù hợp để nhanh chóng tập trung vào các thời kỳ có đột biến hoặc bất thường của nhật ký. 

## Các thao tác trên trang 

- **Bắt đầu**: Kéo nhật ký dựa trên điều kiện hiện tại và liên tục nhận nhật ký mới. 
- **Dừng**: Dừng nhận nhật ký mới; các nhật ký đã tải sẽ vẫn còn trên trang.
- **Xóa**: Xóa các nhật ký đang hiển thị; nếu việc lấy dữ liệu theo thời gian thực tiếp tục, các nhật ký mới sẽ tiếp tục xuất hiện.

## Tình huống phổ biến

- **Chưa có nhật ký**: Vui lòng đảm bảo dịch vụ mục tiêu đang chạy và thử mở rộng phạm vi thời gian của nhật ký.
- **Chưa chọn mục tiêu**: Vui lòng chọn ít nhất một Deployment, StatefulSet, hoặc Pod.
- **Quá nhiều mục tiêu**: Một truy vấn hỗ trợ tối đa 20 Pod thực tế; vui lòng giảm lựa chọn và thử lại.
- **Điều kiện lọc không hợp lệ**: Vui lòng kiểm tra xem `AND`, `OR`, `NOT`, dấu ngoặc, hoặc dấu ngoặc kép đã đầy đủ chưa.
- **Việc lấy nhật ký bị gián đoạn**: Điều này có thể do dịch vụ khởi động lại, thay đổi mạng, hoặc thiếu quyền. Bạn có thể nhấp **Bắt đầu** lại.

> Trang giữ lại tới 500.000 dòng nhật ký. Khi vượt quá giới hạn, các nhật ký cũ sẽ tự động bị xóa.

## Giao diện vận hành mẫu

Hình dưới đây hiển thị vùng chọn khối lượng công việc, lọc từ khóa và xem nhật ký theo thời gian thực.

Nhấp **Chọn Cluster & NAMESPACE** để chuyển sang cluster mục tiêu và NAMESPACE, sau đó tiếp tục chọn các khối lượng công việc bạn muốn xem.

