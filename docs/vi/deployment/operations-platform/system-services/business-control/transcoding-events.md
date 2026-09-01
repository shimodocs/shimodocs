# Tìm kiếm Sự kiện Transcoding

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan tính năng 
Chức năng truy vấn sự kiện chuyển mã được sử dụng để kiểm tra nhanh các sự kiện chuyển mã gần đây trong MDP hệ thống, giúp định vị và xử lý sự cố trong quá trình chuyển mã. 

Theo mặc định, danh sách sẽ hiển thị các sự kiện chuyển mã gần đây nhất. 

## nhiệm vụ_lấy ID
Một nhiệm vụ_ID được tạo trong quá trình nhập và xuất nhiệm vụ

Mở chế độ nhà phát triển của trình duyệt. Khi xuất, bạn có thể lấy_ID nhiệm vụ bằng cách kiểm tra giao diện này như hình dưới đây

## Truy vấn theo nhiệm vụ_id
Nhập nhiệm vụ_nhập id vào hộp nhập taskID để nhanh chóng lọc các sự kiện chuyển mã liên quan đến nhiệm vụ đó.

## Xem Liên kết
Như được hiển thị trong hình dưới, nhấp vào biểu tượng “Xem Liên kết” trong bản ghi hàng để xem tất cả các sự kiện liên quan đến nhiệm vụ_id, điều này thuận tiện cho việc phân tích quá trình hoàn chỉnh của nhiệm vụ chuyển mã này từ đầu đến cuối.

## Định vị Ngoại lệ

### gRPC Thành công, Không nhận được Callback
Nếu gRPC được gửi thành công và một phản hồi được nhận thành công, điều này chỉ ra rằng nhiệm vụ chuyển mã đã được gửi đến dịch vụ chuyển mã. Trong trường hợp này, nếu callback không được nhận kịp thời do dịch vụ chuyển mã hết thời gian chờ, cần phải kiểm tra dịch vụ chuyển mã.

### Đã nhận Callback
Nếu bạn có thể thấy rằng có một callback cho nhiệm vụ_id, thì nhìn chung được coi là thất bại chuyển mã, chẳng hạn như định dạng không tương thích hoặc các ngoại lệ khác.
