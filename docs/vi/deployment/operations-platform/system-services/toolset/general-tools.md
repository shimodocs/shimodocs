# Công cụ Chung

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Tổng quan trang

Trang Công cụ Chung bao gồm 7 chức năng được sử dụng thường xuyên: JSON định dạng, chuyển đổi định dạng, JWT giải mã, chuyển đổi dấu thời gian, kiểm tra thời gian máy, phân tích Mã QR, và mã hóa/giải mã Base64.

1. Nhấp vào bất kỳ thẻ chức năng nào để vào trang thao tác.
2. Sau khi vào, bạn có thể chuyển trực tiếp sang các công cụ khác từ danh sách chức năng bên trái.
3. Nhấp 'Quay lại Menu Chức năng' để trở về trang chủ thẻ.

## 2. JSON Định dạng

Chức năng này được sử dụng để định dạng, nén và xác thực JSON nội dung.

1. Nhấp vào "JSON Định dạng."
2. Nhập nội dung cần xử lý vào "Input" JSON”, ví dụ:

   ```json
   {"name":"MDP","enabled":true,"items":[1,2]}
   ```

3. Nhấp 'Định dạng', và thụt lề JSON sẽ được tạo ở bên phải.
4. Nhấn vào "Nén", và một phiên bản gọn JSON không có khoảng trắng và xuống dòng sẽ được tạo ra bên phải.
5. Nhấn 'Sao chép' để sao chép kết quả đã xử lý.
6. Nhấn 'Xóa' để loại bỏ nội dung đầu vào và đầu ra.

Kết quả thử nghiệm: Chuỗi, giá trị boolean và mảng đều được giữ nguyên đúng, và các chức năng định dạng và nén hoạt động chính xác.

## 3. Chuyển đổi Định dạng

Chức năng này hỗ trợ chuyển đổi và định dạng giữa JSON, YAMLvà TOML định dạng.

1. Nhấp vào "Chuyển đổi Định dạng."
2. Chọn định dạng nội dung đầu vào trong "Định dạng Nguồn."
3. Chọn định dạng đầu ra mong muốn trong "Định dạng Đích."
4. Nhập nội dung cần chuyển đổi ở bên trái.
5. Nhấp vào "Chuyển đổi," và kết quả sẽ được hiển thị ở bên phải.
6. Nhấp vào "Đảo Ngược Định dạng" để hoán đổi định dạng nguồn và định dạng đích.
7. Nhấp vào "Định dạng" để điều chỉnh thụt lề và bố cục của nội dung hiện tại.
8. Nhấp vào "Sao chép" để sao chép kết quả đầu ra.

Lần này chúng tôi sử dụng JSON để chuyển đổi sang YAML, nhập:

```json
{"name":"MDP","ports":[80,443],"enabled":true}
```

Kết quả chuyển đổi: 

```yaml
name: MDP
ports:
  - 80
  - 443
enabled: true
```

Kết quả đo được: Các trường, mảng và giá trị boolean được chuyển đổi chính xác.

## 4. JWT Giải mã

Tính năng này được sử dụng để phân tích Header, Payload và Signature của một JWT Token.

1. Nhấp vào "JWT Giải mã.
2. Dán JWT Token vào hộp nhập.
3. Nhấp vào "Giải mã."
4. Xem thuật toán chữ ký và loại Token trong Header.
5. Xem thông tin như người dùng, vai trò và thời gian hết hạn trong Payload.
6. Xem nội dung thô của Signature.
7. Nhấn nút sao chép trong mỗi phần để sao chép kết quả phân tích.
8. Nhấn "Xóa" để xóa Token hiện tại và kết quả phân tích.

Kết quả kiểm tra: Token kiểm tra đã phân tích thành công các trường như `HS256`, `JWT`, người dùng, vai trò và thời gian hết hạn.

> JWT Giải mã chỉ để xem cấu trúc Token và không thể thay thế việc kiểm tra tính hợp lệ chữ ký phía máy chủ.

## 5. Chuyển đổi dấu thời gian 

Tính năng này hỗ trợ chuyển đổi hai chiều giữa dấu thời gian Unix và ngày-giờ. 

### 5.1 Chuyển đổi dấu thời gian sang Ngày/Giờ 

1. Nhấn "Chuyển đổi dấu thời gian". 
2. Nhập dấu thời gian 10 chữ số giây hoặc 13 chữ số mili-giây vào "Dấu thời gian (giây hoặc mili-giây)". 
3. Nhấn "Chuyển đổi" ở trên cùng. 
4. Xem ngày và giờ trong "Kết quả chuyển đổi." 
5. Nhấn nút sao chép bên cạnh kết quả để sao chép nội dung. 

### 5.2 Ngày Giờ sang Dấu Thời gian 

1. Nhập 'YYYY-MM-DD HH:mm:ss' hoặc ISO định dạng thời gian vào trường "Ngày Giờ". 
2. Nhấn "Chuyển đổi" bên dưới. 
3. Xem dấu thời gian Unix trong "Kết quả chuyển đổi (giây)". 
4. Nhấn "Thời gian hiện tại" để nhanh chóng điền dấu thời gian và ngày hiện tại. 
5. Nhấn "Xóa" để xóa tất cả nội dung. 

Kết quả kiểm tra: '1704067200' đã chuyển đổi thành công sang ngày-giờ, và ngày-giờ cũng có thể chuyển đổi chính xác sang dấu thời gian cấp giây. 

> Khi kiểm tra dữ liệu qua các múi giờ, trước tiên cần làm rõ liệu thời gian kinh doanh sử dụng UTC hay múi giờ địa phương. 

## 6. Kiểm tra thời gian máy

Chức năng này được sử dụng để kiểm tra thời gian của tất cả Pods với `app=ws-gateway` trong NAMESPACE hiện tại và làm nổi bật các instance có sai lệch thời gian hơn 30 giây.

1. Nhấn "Kiểm tra thời gian máy".
2. Nhấp vào "Làm mới" ở góc trên bên phải.
3. Kiểm tra nhãn hiện tại NAMESPACE và nhãn truy vấn.
4. Xem thời gian tham chiếu do hệ thống tính toán, đó là thời gian trung vị của tất cả các Pod.
5. Xem nút nơi mỗi Pod được đặt, dấu thời gian Unix và thời gian có thể đọc được.
6. Kiểm tra "Chênh lệch so với tham chiếu" và "Trạng thái."
7. Nếu độ lệch vượt quá 30 giây, kiểm tra NTP/Chrony của nút, thời gian máy ảo và cài đặt múi giờ.

Kết quả kiểm tra: 1 `ws-gateway` Pod trả về, với độ lệch so với thời gian tham chiếu là `0s` và trạng thái "Bình thường."

## 7. Phân tích Mã QR

Tính năng này được sử dụng để tải lên hình ảnh mã QR và trích xuất văn bản, liên kết hoặc nội dung khác bên trong.

1. Nhấp vào "Phân tích Mã QR."
2. Nhấp vào "Chọn Tệp."
3. Chọn một hình ảnh mã QR rõ ràng từ thiết bị cục bộ của bạn.
4. Sau khi trang hiển thị bản xem trước hình ảnh, kiểm tra "Kết quả Phân tích" bên dưới.
5. So sánh kết quả với nội dung dự kiến của mã QR để xác nhận tính nhất quán.
6. Nhấp vào "Sao chép" để sao chép nội dung đã phân tích.
7. Nhấp vào "Xóa" để xóa hình ảnh và kết quả phân tích.

Kết quả kiểm tra: Mã QR thử nghiệm có thể được tải lên, xem trước và phân tích chính xác.

## 8. Mã hóa và Giải mã Base64

Tính năng này được sử dụng để chuyển đổi hai chiều giữa văn bản thuần và nội dung Base64.

### 8.1 Mã hóa Base64

1. Nhấp vào "Mã hóa và Giải mã Base64."
2. Nhập văn bản thuần bên trái.
3. Nhấp vào "Mã hóa."
4. Xem kết quả mã hóa Base64 ở bên phải.

### 8.2 Giải mã Base64

1. Nhập nội dung Base64 bên trái.
2. Nhấp vào "Giải mã."
3. Xem văn bản khôi phục ở bên phải.
4. Nhấn "Sao chép" để sao chép kết quả.
5. Nhấn "Xóa" để xóa đầu vào và đầu ra.

Kết quả kiểm tra:

```text
MDPTEST → TURQ5rWL6K+V
TURQ5rWL6K+V → MDPTEST
```

Tiếng Trung UTF-8 nội dung có thể chuyển đổi qua lại bình thường.

