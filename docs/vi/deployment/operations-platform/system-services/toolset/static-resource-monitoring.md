# Giám sát tài nguyên tĩnh

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

Giám sát tài nguyên tĩnh được sử dụng để kiểm tra JS và CSS các tài nguyên được tham chiếu bởi các trang web, giúp bạn hiểu tình trạng truy cập tài nguyên, giao thức truyền tải, cấu hình bộ nhớ đệm, và CDN sử dụng.

> Tên chức năng trên trang hệ thống là 'Phát hiện Tài nguyên Tĩnh'.

## 1. Cách Sử dụng

Đăng nhập vào nền tảng quản lý, chọn **Dịch vụ Hệ thống** ở phía trên, sau đó chọn **Bộ công cụ > Phát hiện Tài nguyên Tĩnh** trong thanh điều hướng bên trái.

Tính năng này chỉ dành cho quản trị viên. Nếu bạn không thấy mục nhập, vui lòng kiểm tra quyền tài khoản và phiên bản sản phẩm hiện tại.

1. Nhập trang đầy đủ URL, ví dụ `https://example.com/recent`.
2. Nếu trang yêu cầu đăng nhập, mở rộng 'Tiêu đề Yêu cầu Tùy chỉnh' và điền thông tin cần thiết như `Cookie` và `Authorization`.
3. Nhấn 'Bắt đầu Phát hiện' và chờ kết quả trả về.

> Các tiêu đề yêu cầu cũng sẽ được sử dụng để truy cập các tài nguyên tĩnh được tham chiếu bởi trang. Vui lòng chỉ sử dụng thông tin xác thực tạm thời và đảm bảo rằng các miền của tài nguyên đa nguồn gốc là đáng tin cậy. Địa chỉ trang, các tiêu đề yêu cầu và kết quả phát hiện gần đây nhất sẽ được lưu trong trình duyệt hiện tại.

## 2. Phạm vi phát hiện

Hệ thống sẽ xác định JS và CSS được tham chiếu trực tiếp trong trang HTML, nhưng nó không phát hiện hình ảnh, phông chữ, mã nhúng hoặc các tài nguyên được tải động sau khi thực thi script.

- Tối đa 3 JS cùng miền và CSS tệp sẽ được phát hiện cho mỗi miền;
- Tối đa 50 tài nguyên đa miền có thể được phát hiện cùng lúc;
- Các URL trùng lặp chỉ được tính một lần.

Các tài nguyên cùng miền không được phát hiện sẽ được đánh dấu là “bỏ qua lấy mẫu cùng miền,” và điều này không cho thấy tài nguyên bị lỗi.

## 3. Xem kết quả

Sau khi phát hiện xong, trang sẽ hiển thị:

- **Thông tin tóm tắt**: Số lượng HTML tài nguyên, số lượng phát hiện, số vấn đề, việc sử dụng bộ nhớ đệm, CDNvà HTTP/2;
- **Phản hồi Trang**: Mã trạng thái, giao thức, và thông tin bộ đệm của trang mục tiêu;
- **Danh sách Tài nguyên**: URL, mã trạng thái, giao thức, bộ đệm, CDN, vấn đề, và tiêu đề phản hồi của từng tài nguyên.

Danh sách tài nguyên hỗ trợ lọc theo 'Đã phát hiện', 'Tất cả', và 'Có vấn đề'. 

Hệ thống chủ yếu chỉ ra các vấn đề sau: 

- HTTP 4xx/5xx; 
- Không phát hiện bộ nhớ đệm hợp lệ; 
- HTTP/2 không được sử dụng; 
- Các tài nguyên đa nguồn gốc không hiển thị CDN đặc tính; 
- Yêu cầu quá thời gian hoặc phân giải tên miền thất bại. 

## 4. Các Vấn đề Thường gặp 

### Phát hiện Trang Thất bại 

Vui lòng kiểm tra trang URL, kết nối mạng, HTTPS chứng chỉ và trạng thái đăng nhập. Dịch vụ phát hiện không tự động sử dụng lại thông tin đăng nhập từ trình duyệt, vì vậy nếu cần, vui lòng cung cấp các header yêu cầu tùy chỉnh. 

### Tài nguyên không được nhận diện 

Vui lòng đảm bảo rằng trang trả về bình thường HTML. Các tài nguyên tải động bởi script sẽ không được nhận diện. 

### CDN Hiển thị 'Không được phát hiện' 

Kết quả này chỉ chỉ ra rằng không có CDN đặc điểm nào được phát hiện trong phản hồi và không có nghĩa là tài nguyên chắc chắn không sử dụng CDN. Vui lòng kiểm tra với CDN bảng điều khiển và kiến trúc mạng. 

## 5. Ghi chú 

- Kết quả phát hiện phản ánh những gì quan sát được từ mạng của nền tảng quản lý trong yêu cầu này và có thể khác với trải nghiệm thực tế của người dùng. 
- CDN, bộ đệm và trạng thái sự cố là kết quả được xác định tự động và chỉ để chẩn đoán hỗ trợ. 
- 'Không phát hiện vấn đề' không có nghĩa là trang đã vượt qua đầy đủ đánh giá hiệu năng, bảo mật hoặc khả năng sử dụng. 
- Sau khi trang được xuất bản, CDN được làm mới hoặc môi trường mạng thay đổi, khuyến cáo nên kiểm tra lại.
