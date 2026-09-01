# Nâng cấp hệ thống

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan chức năng

Nâng cấp hệ thống được sử dụng để tải lên và áp dụng gói cài đặt mới. MDP Trước khi nâng cấp chính thức, hệ thống sẽ tự động kiểm tra gói nâng cấp và môi trường hiện tại, và hiển thị các thay đổi về cấu hình và dịch vụ liên quan đến việc nâng cấp này, giúp bạn hoàn tất việc nâng cấp phiên bản hoặc bảo trì định kỳ.

Trang cũng lưu giữ lịch sử nâng cấp, giúp dễ dàng xem các bản ghi nâng cấp trước đây, trạng thái thực hiện và các nhật ký liên quan.

**Lưu ý**: Các nâng cấp phiên bản lớn có thể cập nhật sơ đồ cơ sở dữ liệu. Việc nâng cấp liên quan đến thay đổi cấu hình, khởi động lại dịch vụ và thay đổi giao diện chức năng, có thể ảnh hưởng đến trải nghiệm người dùng. Nên thực hiện vào giờ thấp điểm.

## Vào Trang

Sau khi đăng nhập vào quản trị backend, chọn **Nâng cấp hệ thống** trong điều hướng bên trái để vào trang.

Nâng cấp hệ thống chỉ dành cho quản trị viên. Nếu bạn không thấy menu này, vui lòng liên hệ với quản trị viên hệ thống để xác nhận quyền của tài khoản hiện tại.

## Chuẩn Bị Trước Khi Nâng Cấp

Trước khi bắt đầu nâng cấp, nên xác nhận các mục sau:

- Sử dụng gói nâng cấp do chính thức cung cấp phù hợp với loại sản phẩm hiện tại và phương pháp triển khai.
- Gói nâng cấp đang ở `.tar.gz` định dạng. Vui lòng không trích xuất hoặc chỉnh sửa các tệp bên trong gói.
- Nên thực hiện nâng cấp trong giờ cao điểm ngoài kinh doanh hoặc cửa sổ bảo trì.
- Xác nhận rằng dịch vụ hiện tại đang chạy bình thường và thông báo trước cho người dùng liên quan.
- Nếu việc nâng cấp liên quan đến thay đổi License, vui lòng chuẩn bị nội dung License mới trước.

## Các bước nâng cấp

### 1. Tải gói nâng cấp lên

Nhấp vào khu vực tải lên trên trang nâng cấp hệ thống hoặc kéo gói nâng cấp vào khu vực tải lên. Sau khi tải lên hoàn tất, hệ thống sẽ tự động phân tích và kiểm tra gói nâng cấp.

Việc kiểm tra chủ yếu bao gồm:

- Định dạng và tính toàn vẹn nội dung của gói cài đặt.
- Liệu chữ ký của gói cài đặt có hợp lệ không.
- Phiên bản của gói nâng cấp và kế hoạch nâng cấp.
- Liệu loại sản phẩm và kiến trúc triển khai có khớp không.
- Liệu license hiện tại có hỗ trợ nâng cấp này không.

Kết quả kiểm tra được chia thành các trạng thái sau:

- **Đỗ**: Kiểm tra bình thường và bạn có thể tiếp tục.
- **Thay đổi**: Có những thay đổi dự kiến trong nâng cấp này; vui lòng xác nhận nội dung trước khi tiếp tục.
- **Không khớp**: Có các vấn đề ngăn cản việc nâng cấp; bạn cần thay thế gói nâng cấp hoặc xử lý cấu hình liên quan trước khi tải lên lại.

### 2. Nhập License

Nếu hệ thống xác định rằng nâng cấp này yêu cầu License cập nhật, trang sẽ hiển thị các sản phẩm cần cập nhật và thông tin như mã máy chủ hiện tại.

Sau khi dán nội dung License mới, nhấp **Xác minh và Lưu tạm thời**. Việc nâng cấp chỉ có thể tiếp tục khi Giấy phép đã được xác minh thành công. Giấy phép được lưu tạm thời sẽ có hiệu lực tự động sau khi cập nhật được áp dụng thành công. Để tham khảo Giấy phép, xem [Quản lý Giấy phép]

Nếu trang hiển thị rằng Giấy phép không cần được cập nhật, bạn có thể tiếp tục trực tiếp sang bước tiếp theo.

### 3. Xác nhận nội dung gói nâng cấp

Trang sẽ hiển thị các tệp cấu hình và tài nguyên dịch vụ trong gói nâng cấp. Bạn có thể chọn các tệp cụ thể để xem nội dung của chúng và xác nhận rằng gói nâng cấp phù hợp với mục tiêu nâng cấp hiện tại.

### 4. Xác nhận các thay đổi

Hệ thống sẽ so sánh môi trường hiện tại với gói nâng cấp và hiển thị các tài nguyên sẽ được thêm, sửa đổi, xóa hoặc khởi động lại trong lần nâng cấp này.

Vui lòng chú ý đặc biệt để xác nhận: 

- Có việc xóa tài nguyên nào không mong muốn không. 
- Có dịch vụ quan trọng nào cần được khởi động lại không. 
- Các thay đổi trong tệp cấu hình có như mong đợi không. 

### 5. Áp dụng Cập nhật 

Sau khi xác nhận các thông tin trên là chính xác, nhấn **Xác nhận để Bắt đầu Cập nhật**. Hệ thống sẽ tạo một bản sao lưu trước khi nâng cấp và bắt đầu áp dụng gói cập nhật. 

Trong quá trình nâng cấp, trang sẽ liên tục hiển thị nhật ký thực thi, bao gồm thông tin như cập nhật tài nguyên, khởi động lại dịch vụ, và kiểm tra trạng thái đang chạy. Khi một số thành phần khởi động lại, trang quản lý có thể tạm thời không truy cập được. Vui lòng chờ một lát rồi mở lại trang để kiểm tra tiến trình. 

Nếu việc thực thi thất bại, bạn có thể xử lý các vấn đề theo nhật ký và sau đó nhấn **Thực hiện lại**. 

### 6. Hoàn tất Nâng cấp 

Sau khi nhiệm vụ nâng cấp thực hiện thành công, nhấp **Hoàn tất** để kết thúc quá trình nâng cấp này. 

Trang hoàn tất nâng cấp sẽ hiển thị tên và phiên bản của gói nâng cấp và cung cấp các thao tác sau: 

- **Xem Nhật Ký Thực Thi**: Xem toàn bộ quá trình nâng cấp này. 
- **Khôi phục về Phiên bản Trước Nâng cấp**: Vào bản sao lưu trước khi nâng cấp và làm theo hướng dẫn trên trang để khôi phục. 
- **Quay lại Cập nhật Ứng dụng**: Quay lại trang chủ nâng cấp hệ thống. 

## Lịch Sử Nâng cấp 

Phía dưới trang chủ nâng cấp hệ thống sẽ hiển thị lịch sử các lần nâng cấp, bao gồm tên gói nâng cấp, phiên bản, thời gian tạo, và trạng thái thực hiện. 

Nhấp vào bản ghi nâng cấp để nhập lại quá trình tương ứng và xem tiến trình nâng cấp hoặc kết quả thực thi lịch sử.

## Tình huống phổ biến

- **Xác minh gói nâng cấp thất bại**: Vui lòng xác nhận nguồn gốc gói nâng cấp, tính toàn vẹn của tệp, loại sản phẩm và kiến trúc triển khai là chính xác.
- **Chênh lệch phiên bản**: Vui lòng kiểm tra phiên bản hệ thống hiện tại và phiên bản gói nâng cấp để đảm bảo đang sử dụng đúng lộ trình nâng cấp.
- **Cần cập nhật giấy phép**: Lấy giấy phép mới tương thích với phiên bản mục tiêu và môi trường chạy hiện tại, xác thực và lưu tạm thời trước khi tiếp tục.
- **Trang quá trình nâng cấp tạm thời không khả dụng**: Dịch vụ MDP có thể đang được cập nhật hoặc khởi động lại, vui lòng đợi một chút và làm mới trang.
- **Nhiệm vụ nâng cấp thất bại**: Kiểm tra nhật ký thực thi để xác định nguyên nhân; sau khi giải quyết vấn đề, sử dụng **Thực hiện lại**.
- **Bất thường dịch vụ sau khi nâng cấp**: Trước tiên kiểm tra nhật ký thực thi và trạng thái dịch vụ; nếu cần phục hồi, bạn có thể quay lại bằng cách sử dụng bản chụp trước khi nâng cấp.

> Nâng cấp hệ thống sẽ thay đổi cấu hình dịch vụ và có thể kích hoạt khởi động lại dịch vụ. Vui lòng chỉ tiến hành sau khi xác nhận gói nâng cấp và các thay đổi là chính xác.
