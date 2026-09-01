# Kiểm tra Tương thích

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Tổng quan trang

Trang kiểm tra khả năng tương thích được sử dụng để kiểm tra cấu hình lưu trữ đối tượng, kết nối, khả năng tải lên và hiệu suất tải lên. Trang được chia thành:

1. Cấu hình lưu trữ;
2. Kiểm tra khả năng tương thích;
3. Kiểm tra hiệu suất.

## 2. Cấu hình lưu trữ 

### 2.1 Mô tả mục cấu hình 

| Mục Cấu hình | Chức năng |
| --- | --- |
| Khóa truy cập | Định danh nhận dạng truy cập lưu trữ đối tượng, tức là AK |
| Khóa bí mật | Khóa truy cập đi kèm với AK, tức là SK |
| Điểm cuối (Endpoint) | Địa chỉ Dịch vụ Lưu trữ Đối tượng |
| Tên Bucket | Bucket mục tiêu sẽ được kiểm tra |
| Khu vực | Khu vực vị trí Bucket lưu trữ |
| Public Endpoint | Tên miền công cộng được trình duyệt sử dụng để truy cập lưu trữ đối tượng, tùy chọn |
| Path Style | Sử dụng định dạng 'endpoint/bucket/object' để truy cập các đối tượng, các dịch vụ như MinIO thường cần được bật |

### 2.2 Điền cấu hình 
1. Nhấp vào "Điền Bucket phụ kiện" hoặc "Điền Bucket nội dung" khi cần. 
2. Hệ thống tự động điền các cấu hình như AK, SK, Endpoint, Bucket, Region, v.v., tương ứng với môi trường hiện tại. 
3. Nếu bạn không sử dụng tự động điền, bạn cũng có thể điền thủ công tất cả các cấu hình. 
4. Kiểm tra xem endpoint có chứa giao thức và cổng chính xác hay không. 
5. Kiểm tra xem Tên Bucket có phải là bucket cần phát hiện lần này hay không. 
6. Kiểm tra xem Vùng có khớp với vùng thực tế của bộ lưu trữ đối tượng hay không.
7. Nếu bạn cần trình duyệt truy cập trực tiếp bộ lưu trữ đối tượng, hãy điền Endpoint Công khai.
8. Quyết định có bật Kiểu Đường dẫn hay không tùy thuộc vào loại bộ lưu trữ đối tượng.

Sau khi tự động điền bucket Đính kèm lần này:

- Bucket: `shimo-attachments`;
- Endpoint: được hệ thống điền tự động;
- Vùng: `cn-north-1`;
- Endpoint Công khai: được hệ thống điền tự động;
- Kiểu Đường dẫn: đã bật.

Sau khi nhấp vào "Điền bucket Nội dung", Bucket có thể tự động chuyển sang `file-contents`.

> Khóa bí mật là thông tin nhạy cảm; không hiển thị nó dưới dạng văn bản thuần túy trong ảnh chụp màn hình, trò chuyện hoặc vé.

## 3. Kiểm tra Tương thích

Bài kiểm tra tương thích sẽ lần lượt kiểm tra tải lên từ backend, tải lên trực tiếp từ trình duyệt và tải lên đa phần.

### 3.1 Bắt đầu Kiểm tra

1. Hoàn tất cấu hình lưu trữ.
2. Nhấp vào tab "Kiểm tra Tương thích".
3. Xác nhận lại rằng cấu hình Bucket, Endpoint, Region, Public Endpoint và Path Style là chính xác.
4. Nhấp vào "Bắt đầu Kiểm tra Tương thích."
5. Chờ cho đến khi trang hiển thị "Kiểm tra Tương thích Hoàn thành."
6. Kiểm tra xem trạng thái tổng kết trên trang có phải là "Tất cả Đã thành công" không.
7. Kiểm tra trạng thái, thời gian và thông tin kết quả của ba bài kiểm tra một cách riêng biệt.

### 3.2 Kiểm tra Tải lên Backend

Bài kiểm tra này dùng để xác minh kết nối mạng và quyền ghi từ dịch vụ backend đến bộ lưu trữ đối tượng.

1. Backend tạo một tệp văn bản kiểm tra.
2. Backend ghi tệp vào Bucket mục tiêu.
3. Trang hiển thị thời gian tải lên và đường dẫn của đối tượng thử nghiệm.
4. Trạng thái thành công màu xanh lá cho thấy mạng backend và quyền ghi là bình thường.

Kết quả hiện tại: Tải lên thành công, thời gian `157 ms`.

### 3.3 Kiểm tra Tải lên Trực tiếp từ Trình duyệt

Kiểm tra này được sử dụng để xác minh liên kết cho trình duyệt tải trực tiếp lên bộ nhớ đối tượng thông qua PostPolicy.

1. Trang yêu cầu PostPolicy cần thiết cho tải lên trực tiếp từ backend.
2. Trình duyệt sử dụng Điểm đầu công khai để tải tệp trực tiếp lên bộ nhớ đối tượng.
3. Trang kiểm tra mã HTTP trạng thái trả về từ bộ nhớ đối tượng.
4. HTTP 204 cho biết tải lên trực tiếp từ trình duyệt thành công.

Kết quả lần này: Tải lên trực tiếp từ trình duyệt thành công, mất `61 ms`, mã trạng thái `204`.

### 3.4 Kiểm tra Tải lên Nhiều Phần

Kiểm tra này được sử dụng để xác minh quá trình hoàn chỉnh của việc tải lên nhiều phần cho tệp lớn.

1. Khởi tạo nhiệm vụ tải lên nhiều phần.
2. Chia tệp thử nghiệm thành nhiều phần.
3. Tải từng phần theo thứ tự.
4. Gọi giao diện hợp nhất để hoàn tất việc tạo đối tượng.
5. Trang hiển thị `multipart upload succeeded`, cho thấy toàn bộ quá trình thành công.

Kết quả lần này: Tải lên nhiều phần thành công, mất `1746 ms`.

### 3.5 Mô tả các Đối tượng Thử nghiệm

Kiểm tra tương thích sẽ thực hiện ghi thực tế vào Bucket mục tiêu, và đường dẫn của các đối tượng thử nghiệm backend thường giống như:

```text
compatibility-tests/<RANDOM UUID>.txt
```

1. Xác nhận rằng Bucket mục tiêu là đúng trước khi chạy kiểm tra.
2. Không chạy kiểm tra bừa bãi trên Bucket sản xuất sai.
3. Sau khi kiểm tra, bạn có thể kiểm tra và dọn dẹp các đối tượng thử nghiệm theo chính sách dọn dẹp của môi trường.

## 4. Kiểm tra Hiệu năng

Kiểm thử hiệu năng được sử dụng để đo thời gian tải lên và băng thông đối với các kích thước tệp khác nhau.

### 4.1 Cấu hình Kiểm thử Hiệu năng

1. Nhấp vào tab "Kiểm thử Hiệu năng".
2. Chọn chế độ kiểm thử; trang mặc định ở "Trình duyệt Trực tiếp".
3. Chọn Loại Nội dung; bạn có thể sử dụng `application/octet-stream` mặc định.
4. Chọn kích thước tệp bạn muốn kiểm thử.
5. Trang hỗ trợ các kích thước 1, 5, 8, 10, 12, 15, 20 và 25 MB.
6. Trong môi trường kiểm thử, bạn có thể chọn 1 MB trước để xác thực nhanh.
7. Đối với so sánh hiệu năng chính thức, chọn nhiều kích thước tệp để kiểm thử.

### 4.2 Bắt đầu Kiểm thử Hiệu năng

1. Xác nhận rằng cấu hình lưu trữ là đúng.
2. Xác nhận rằng ít nhất một kích thước tệp đã được chọn.
3. Nhấp 'Bắt đầu Kiểm thử Hiệu năng'.
4. Chờ tất cả các tệp tải lên hoàn tất.
5. Kiểm tra băng thông trung bình, thời gian ngắn nhất và thời gian dài nhất.
6. Kiểm tra trạng thái, thời gian đã trôi qua, băng thông và thông báo lỗi cho từng kích thước tệp.
7. Nếu xảy ra lỗi, trước tiên kiểm tra mạng trình duyệt, Điểm cuối Công khai, cấu hình cross-origin và trạng thái lưu trữ đối tượng.

### 4.3 Kết quả của Kiểm thử này

Kiểm thử này chỉ sử dụng tệp 1 MB để thực hiện kiểm thử tải lên trực tiếp nhẹ ở frontend:

| Chỉ số | Kết quả |
| --- | ---: |
| Kích thước Tệp | 1,0 MB |
| Trạng thái | Thành công |
| Thời gian | 874 ms |
| Băng thông | 1,14 MB/s |
| Thông báo Lỗi | Không có |

Kết quả thực tế: Tải lên thành công, và trang có thể tạo các chỉ số thời gian đã trôi qua và băng thông đúng.

> Kết quả hiệu suất có thể bị ảnh hưởng bởi mạng trình duyệt, tải của cụm, liên kết proxy và tải lưu trữ đối tượng. Một lần kiểm tra chỉ có thể xác minh tính khả dụng chức năng; việc chấp nhận hiệu suất chính thức nên được kiểm tra lặp đi lặp lại trong cùng một môi trường và thống kê cho P50, P95, và tỷ lệ thất bại nên được ghi lại.

## 5. Lưu ý
1. Xác nhận Bucket mục tiêu trước khi thực hiện kiểm tra để tránh ghi nhầm tệp thử nghiệm vào bucket lưu trữ sai.
2. Không hiển thị Secret Key dưới dạng văn bản thuần túy trong tài liệu hoặc ảnh chụp màn hình.
3. Tải lên trực tiếp từ trình duyệt phụ thuộc vào Public Endpoint và cấu hình cross-origin.
4. S3Các dịch vụ tương thích như MinIO thường yêu cầu bật Path Style.
5. Kiểm tra hiệu suất tạo ra lưu lượng mạng và ghi lưu trữ thực sự; đánh giá tác động môi trường trước khi kiểm tra các tệp lớn.
6. Việc chấp nhận hiệu suất chính thức nên được thực hiện nhiều vòng; kết quả kiểm tra trên một trình duyệt không đủ.
