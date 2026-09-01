# Quản lý giấy phép

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

> [!TIP]
>
> Quản lý giấy phép được sử dụng để lấy mã máy chủ cho ShimoDocs Suite, viết và phát hành giấy phép, cũng như xem trạng thái hiện tại của giấy phép, phạm vi giấy phép và thông tin máy đang chạy. Đối với kích hoạt lần đầu, 
> gia hạn giấy phép khi hết hạn, hoặc khi các nút máy chủ thay đổi, bạn có thể cập nhật ShimoDocs Suite giấy phép qua trang này.

> Sau khi viết giấy phép, bạn phải kiểm tra kết quả xác minh trước khi phát hành giấy phép. Chỉ việc viết hoặc lưu tạm thời không làm giấy phép có hiệu lực chính thức.
- Nếu bạn là khách hàng trả phí, vui lòng gửi mã máy đã sao chép đến ShimoDocs đội ngũ hỗ trợ kỹ thuật. Chúng tôi sẽ tạo Giấy phép tương ứng dựa trên mã máy và gửi thông tin giấy phép cho bạn.
- Nếu bạn muốn dùng thử miễn phí, vui lòng truy cập **ShimoDocs Đơn Xin Giấy Phép Trang Web Chính Thức**: https://shimo.net/license để điền thông tin đơn và dán mã máy.
- Sau khi gửi, hệ thống sẽ tự động tạo Giấy phép dùng thử. Vui lòng sao chép và hoàn tất việc viết trong vòng 10 phút sau khi nó được tạo. Nếu hết hạn, vui lòng nộp lại đơn.
## 1. Truy cập Quản lý Giấy phép

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **ShimoDocs Suite** ở phía trên.
3. Chọn **Quản lý giấy phép** trong thanh điều hướng bên trái.

Trang bao gồm các khu vực để kích hoạt giấy phép, thông tin giấy phép, so sánh mã máy và thông tin máy.

## 2. Nộp đơn xin ShimoDocs Suite Giấy phép

Trước khi nộp đơn xin cấp quyền, vui lòng trước hết đảm bảo rằng ShimoDocs Suite các nút máy chủ ổn định và sẽ không có nút nào được thêm, gỡ bỏ hoặc thay thế tạm thời. 

### 1. Sao chép tất cả mã máy 

1. Trong khu vực 'Kích hoạt cấp phép', nhấp vào 'Sao chép tất cả mã máy chỉ với một cú nhấp'. 
2. Xác nhận rằng trang hiển thị thông báo sao chép thành công. 
3. Lưu các mã máy vào tài liệu ứng dụng cấp phép. 

### 2. Nộp thông tin ứng dụng cấp phép

Nộp tất cả mã máy và yêu cầu cấp phép cho ShimoDocs nhân viên dịch vụ để tạo ShimoDocs Suite giấy phép.

Được khuyên xác nhận các thông tin sau khi nộp đơn:

| Thông tin | Mô tả |
| --- | --- |
| Sản phẩm được cấp phép | ShimoDocs Suite. |
| Tên giấy phép và công ty | Tên giấy phép và công ty sẽ hiển thị trên giấy phép. |
| Số lượng chỗ ngồi | Số lượng chỗ ngồi tối đa có thể sử dụng giấy phép. |
| Thời gian sử dụng | Thời gian bắt đầu và hết hạn của giấy phép sản phẩm. |
| Thời gian dịch vụ hậu mãi | Thời gian bắt đầu và hết hạn của dịch vụ hậu mãi. |
| Bộ công cụ khả dụng | Các loại tài liệu hoặc bộ tính năng cần được kích hoạt. |
| Khả năng AI | Có cần bật các tính năng AI không. |
| Mã máy | Tất cả mã máy của máy chủ hiện đang chạy ShimoDocs Suite. |

## 3. Ghi và xác minh quyền hạn

Sau khi nhận giấy phép mới, vui lòng thực hiện các bước sau để ghi nó vào hệ thống.

### 1. Ghi giấy phép

1. Nhấp vào 'Ghi quyền hạn' trong khu vực 'Kích hoạt cấp phép'.
2. Dán toàn bộ nội dung giấy phép nhận được vào hộp nhập.
3. Đảm bảo nội dung giấy phép đầy đủ, không thiếu ký tự hoặc thừa khoảng trắng.
4. Nhấp 'Lưu tạm thời'.

### 2. Kiểm tra kết quả xác minh giấy phép

Sau khi lưu tạm thời, hệ thống sẽ xác minh giấy phép mới và hiển thị các kết quả sau:

| Kết quả xác minh | Mô tả | Hành động đề xuất |
| --- | --- | --- |
| Không khớp | Thông tin khóa của giấy phép mới không khớp với môi trường hiện tại. | Kiểm tra mã máy, loại ứng dụng, phiên bản và phạm vi cấp phép. Sau khi xác nhận chúng đúng, hãy lấy lại giấy phép. |
| Đã thay đổi | Một số thông tin trong giấy phép mới khác với giấy phép hiện tại. | Kiểm tra giá trị gốc, giá trị mới và kết quả xác định trên trang để xác nhận rằng các thay đổi khớp với ứng dụng này. |
| Đã qua | Mục xác minh tương ứng đã được phê duyệt. | Tiếp tục kiểm tra các thông tin cấp phép khác. |

Nếu có bất kỳ mục 'Đã thay đổi' nào trên trang, và các thay đổi tuân thủ với ứng dụng cấp phép này, nhấp vào 'Xác nhận Lưu tạm thời.'

> Nếu xảy ra 'không khớp' hoặc 'đã thay đổi' không mong muốn, vui lòng dừng phát hành trước và kiểm tra lại thông tin ứng dụng và môi trường hoạt động hiện tại.

## 4. Phát hành Giấy phép

Sau khi hoàn tất xác minh và lưu tạm thời, trang sẽ hiển thị thông tin giấy phép sẽ được phát hành.

### Kiểm tra trước khi phát hành

Hãy kiểm tra cẩn thận các mục sau:

- Tên giấy phép.
- Hạn chế số lượng người dùng.
- Thời gian bắt đầu và thời gian hết hạn.
- ID ứng dụng.
- Mã máy chủ.

Sau khi xác nhận thông tin là đúng, nhấp vào 'Phát hành Giấy phép.'

Sau khi phát hành hoàn tất, hãy làm mới trang để xem trạng thái và thông tin cấp phép cập nhật.

## 5. Xem Thông tin Cấp phép

Phần 'Thông Tin Ủy Quyền' hiển thị nội dung giấy phép đang hoạt động hiện tại.

### Mô tả các Trường Thông Tin Ủy Quyền

| Trường | Mô tả |
| --- | --- |
| Tên Ủy Quyền | Tên của ủy quyền cho giấy phép hiện tại. |
| Công Ty Được Ủy Quyền | Công ty mà giấy phép thuộc về. |
| Phiên Bản Sản Phẩm | Phiên bản sản phẩm áp dụng cho ủy quyền hiện tại. |
| Phiên Bản Phát Hành | Loại phát hành của ủy quyền hiện tại. |
| Loại Ứng Dụng | Loại sản phẩm áp dụng cho giấy phép hiện tại. |
| Số Lượng Người Thuê | Số lượng người thuê tối đa được phép theo giấy phép hiện tại. |
| Số lượng chỗ ngồi | Số Lượng Chỗ Ngồi Tối Đa |
| Thời Gian Sử Dụng Sản Phẩm | Ngày bắt đầu và ngày hết hạn của việc cấp phép sản phẩm. |
| Thời gian dịch vụ hậu mãi | Ngày bắt đầu và ngày hết hạn của dịch vụ sau bán hàng. |
| Số lượng Máy | Số lượng máy chủ đã được liên kết trong giấy phép. |
| Khả năng AI | Liệu giấy phép hiện tại có bật khả năng AI hay không. |
| Các Gói có sẵn | Các loại tài liệu hoặc gói được phép bởi giấy phép hiện tại. |

## 6. Kiểm tra Kết quả So sánh Mã Máy

Bên dưới thông tin cấp phép, kết quả so sánh giữa mã máy trong giấy phép và các máy chủ đang chạy hiện tại sẽ được hiển thị.

| Kết quả | Mô tả |
| --- | --- |
| Máy trong Giấy chứng nhận | Số lượng máy chủ đã được liên kết trong giấy phép hiện tại. |
| Máy Thực tế | Số lượng máy chủ đang chạy được hệ thống hiện tại phát hiện. |
| Khớp | Mã máy trong giấy phép khớp với máy chủ hiện tại. |
| Không khớp | Hiện tại có một máy chủ chưa được liên kết với giấy phép, hoặc máy trong giấy phép đã thay đổi. |
| Thiếu | Máy được liên kết trong giấy phép hiện không được hệ thống phát hiện. |

Nếu số lượng máy trong chứng chỉ không khớp với số lượng máy thực tế, hoặc nếu trang hiển thị 'Không khớp' hoặc 'Thiếu', vui lòng nhấp vào 'Sao chép Mã Máy' và nộp lại đơn xin giấy phép sử dụng mã máy hiện tại.

> Nếu có thông báo đếm ngược chỉ ra mã máy không khớp xuất hiện ở đầu trang, vui lòng hoàn tất xác minh thông tin máy chủ hoặc thay thế giấy phép trong thời gian thông báo để đảm bảo quyền sử dụng liên tục.

## 7. Xem Thông Tin Máy

Phần 'Thông Tin Máy' hiển thị các nút máy chủ đang chạy hiện tại.

### Mô Tả Trường Thông Tin Máy

| Trường | Mô tả |
| --- | --- |
| Máy chủ | Tên hoặc địa chỉ của nút máy chủ hiện tại. |
| Hệ điều hành | Hệ điều hành hiện được máy chủ sử dụng. |
| CPU | Mẫu bộ xử lý của máy chủ. |
| Số lõi | Số lượng CPU lõi của máy chủ. |
| Bộ nhớ | Lượng bộ nhớ hiện đang được máy chủ nhận diện. |
| Kiến trúc | Kiến trúc hệ thống của máy chủ. |
| Mã máy | Mã định danh duy nhất của máy chủ dùng để tạo và liên kết giấy phép. |

## 8. Kiểm tra Kết quả Cập nhật Quyền

Sau khi hoàn tất việc phát hành giấy phép, nên kiểm tra theo thứ tự sau:

1. Làm mới trang quản lý quyền để xác nhận trạng thái quyền bình thường.
2. Xác minh tên quyền, số lượng chỗ ngồi, thời gian sử dụng, các bộ sẵn có và các khả năng AI.
3. Xác nhận rằng số máy trong chứng chỉ khớp với số máy thực tế.
4. Xác nhận rằng không có kết quả "không khớp" hoặc "thiếu" bất ngờ trong so sánh mã máy.
5. Đăng nhập vào ShimoDocs Suite để xác minh rằng các loại tài liệu và chức năng được cấp phép có thể sử dụng bình thường.

> Sau khi thay thế, thêm hoặc xóa các nút máy chủ, nên quay lại trang này ngay để kiểm tra kết quả so sánh mã máy. Nếu mã máy thay đổi, cần phải nộp đơn lại và phát hành giấy phép sử dụng tất cả các mã máy mới.
