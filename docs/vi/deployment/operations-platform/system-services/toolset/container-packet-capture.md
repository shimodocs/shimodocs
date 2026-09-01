# Chụp gói trong container

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## Tổng quan tính năng 

Capture gói container được sử dụng để thu thập dữ liệu mạng từ các Pod đang chạy trong Kubernetes môi trường, giúp bạn phân tích các vấn đề như kết nối thất bại, request timeout, TCP truyền lại, và tắc nghẽn mạng. 

Sau khi capture hoàn tất, bạn có thể tải tệp PCAP xuống và kiểm tra thêm bằng các công cụ phân tích mạng như Wireshark. 

## Truy cập Trang 

Sau khi đăng nhập vào bảng điều khiển quản lý, chọn **Chụp gói trong container** trong điều hướng bên trái để vào trang. 

Capture gói container chỉ áp dụng trong Kubernetes môi trường triển khai và chỉ dành cho quản trị viên. 

## Bắt đầu Capture gói 

Nên thực hiện theo các bước sau: 

1. Tìm kiếm và định vị Pod mục tiêu trong **Danh sách Pods**. 
2. Đảm bảo trạng thái Pod là Running, sau đó nhấp **Bắt đầu Capture**. 
3. Chọn thời lượng ghi nhận: 1 phút, 5 phút, hoặc 30 phút. 
4. Chọn dung lượng tệp ghi nhận: 100 MB, 500 MB, hoặc 1 GB. 
5. Chọn điều kiện lọc nếu cần, hoặc nhập thủ công biểu thức lọc tcpdump. 
6. Xem lại lệnh ghi nhận hoàn chỉnh hiển thị trên trang. 
7. Nhấp **Bắt đầu Capture** để tạo tác vụ. 

Chỉ một tác vụ ghi nhận gói tin có thể chạy trên cùng một Pod cùng một lúc. Tác vụ sẽ tự động kết thúc khi đạt đến thời lượng đã đặt, hoặc có thể dừng thủ công. 

## Điều kiện lọc 

Thiết lập điều kiện lọc có thể giảm lưu lượng không liên quan và dung lượng tệp. Trang cung cấp một số cài đặt sẵn phổ biến, chẳng hạn như: 

- Lưu lượng trên các cổng được chỉ định. 
- gRPC lưu lượng. 
- Host và cổng được chỉ định. 
- HTTP POST yêu cầu. 
- TCP Thiết lập kết nối, truyền lại, hoặc các gói cửa sổ nhỏ. 

Bạn cũng có thể nhập thủ công bằng cú pháp tcpdump, ví dụ: 

```text
host 10.0.0.1 and port 80
```

Nếu không xác định điều kiện lọc, tác vụ có thể thu thập một lượng lớn lưu lượng mạng từ Pod.

## Quản lý các tác vụ ghi nhận gói tin

Trên **Các tác vụ ghi nhận gói tin** trên trang, bạn có thể xem ID tác vụ, Pod, trạng thái, thời gian tạo và thời gian chạy.

- **Đang chạy**: Tác vụ có thể dừng thủ công.
- **Hoàn thành**: Dịch vụ PCAP tệp có thể tải xuống.
- **Thất bại**: Bạn có thể xem nhật ký tác vụ để hiểu lý do thất bại.

Danh sách tác vụ sẽ tự động làm mới, hoặc bạn có thể nhấp **Làm mới (Refresh)** để nhận trạng thái mới nhất thủ công.

## Tải xuống và phân tích

Sau khi tác vụ hoàn tất, nhấp **Tải xuống** để nhận PCAP tệp. Chức năng tải xuống phụ thuộc vào việc hệ thống được cấu hình đúng với bộ nhớ đối tượng.

PCAP các tệp có thể chứa địa chỉ yêu cầu, dữ liệu giao thức hoặc thông tin nhạy cảm khác. Vui lòng chỉ cung cấp chúng cho nhân viên được ủy quyền và lưu trữ hoặc xóa đúng cách sau khi sử dụng.

## Tình huống phổ biến

- **Không tìm thấy Pod**: Trang chỉ hiển thị các Pod đang ở trạng thái Running trong môi trường hiện tại. Vui lòng kiểm tra trạng thái Pod và môi trường triển khai.
- **Không thể Bắt đầu Ghi gói**: Vui lòng đảm bảo rằng Pod không có nhiệm vụ ghi gói đang diễn ra, và kiểm tra Kubernetes quyền và hỗ trợ cho container tạm thời.
- **Thực hiện nhiệm vụ thất bại**: Kiểm tra nhật ký nhiệm vụ để xác minh biểu thức bộ lọc, trạng thái Pod và liệu các thành phần chụp gói có hoạt động đúng không.
- **Không thể tải tệp xuống**: Vui lòng kiểm tra cấu hình lưu trữ đối tượng và kết nối mạng.
- **Tệp chụp gói quá lớn**: Giảm thời lượng chụp và sử dụng biểu thức bộ lọc chính xác hơn.

> Bắt gói tin tiêu tốn một số tài nguyên mạng, CPUvà lưu trữ. Vui lòng tránh thực hiện các phiên bắt gói có dung lượng cao, kéo dài mà không có bộ lọc trong thời gian cao điểm.
