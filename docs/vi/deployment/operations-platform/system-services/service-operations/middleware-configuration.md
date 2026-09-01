# Cấu hình phần mềm trung gian

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Tổng quan 

Cấu hình Middleware là trang trong MDP Nền tảng Vận hành tích hợp với các hệ thống lưu trữ và middleware khác nhau trong môi trường khách hàng, quản lý tập trung thông tin kết nối cho các thành phần như S3 lưu trữ đối tượng, Redis, hàng đợi tin nhắn Kafka, cơ sở dữ liệu quan hệ MySQL, cơ sở dữ liệu tài liệu MongoDB, và tìm kiếm toàn văn Elasticsearch. Các thay đổi cấu hình được phát hành đến môi trường khách hàng thông qua các tác vụ không đồng bộ, với hiển thị tiến trình thử nghiệm và phát hành theo thời gian thực trong quá trình thay đổi. 

Các khả năng chính: 

- Cấu hình thông tin kết nối cho mỗi middleware (Endpoint, Access Key, USERNAMEPASSWORD, vv) 
- Chuyển đổi giữa các nhà cung cấp khác nhau (S3 và OSS, AWS / MinIO / Tencent Cloud COS / Huawei Cloud OBS, MySQL và DM Dameng) 
- Theo dõi giá trị thay đổi trong biểu mẫu; chỉ các trường đã chỉnh sửa mới được gửi 
- Mỗi phần cấu hình có thể được kiểm tra độc lập; chỉ cho phép xuất bản sau khi xác minh thành công 
- Xuất bản một lần nhấp: gửi hàng loạt tất cả các cấu hình trường chưa hoàn thành và thực thi không đồng bộ 

### 1.1 Người dùng áp dụng 

| Vai trò           | Hoạt động thông thường                                     | 
| -------------- | ---------------------------------------------------- | 
| Kỹ sư triển khai | Điền thông tin kết nối middleware trong quá trình triển khai ban đầu | 
| Nhân viên trực vận hành | Thay thế chứng chỉ, thay đổi Endpoint, kiểm tra kết nối | 
| Phản ứng khẩn cấp | Chuyển middleware dự phòng và sửa đổi cấu hình thời gian chờ |

### 1.2 Các hoạt động không khuyến nghị trong module này 

Chuyển đổi nhà cung cấp (như S3 → OSS) là các thay đổi liên quan đến việc di chuyển dữ liệu hạ nguồn quy mô lớn và nên được xử lý theo quy trình thay đổi. Điều chỉnh hàng loạt thông tin kết nối trên nhiều môi trường không được bao phủ bởi module này; bạn cần vào từng môi trường riêng để cấu hình trang. Kế hoạch năng lực middleware và cảnh báo giám sát không có trên trang này; vui lòng sử dụng module quản lý cụm và cấu hình cảnh báo. 

---

## 2. Giải thích chi tiết từng cấu hình middleware 

### 2.1 S3 Lưu trữ Đối tượng 

**Các bước thao tác**: Vào 'Cấu hình Middleware' từ menu bên trái, mặc định ở phần này → Cuộn xuống để xem ba phần cấu hình theo thứ tự: Công khai S3 cài đặt phiên bản, Chỉnh sửa Hợp tác S3 cài đặt phiên bản, và cài đặt Bucket. 

#### 2.1.1 Công khai S3 và Chỉnh sửa Cộng tác S3

**Các bước thao tác**: Đầu tiên điền vào phần công khai S3 cài đặt phiên bản, sau đó điền vào cài đặt chỉnh sửa cộng tác S3 cài đặt phiên bản, và cuối cùng điền vào cài đặt Bucket. Sau khi chỉnh sửa bất kỳ trường nào, nhấn "Kiểm tra kết nối" ở phía dưới.
Các trường biểu mẫu trong cả hai phần đều nhất quán:
| Trường               | Mô tả                                                              | Bắt buộc |
| ----------------- | ---------------------------------------------------------------------- | ---- |
| Loại lưu trữ           | Chọn từ Dropdown 'S3 (Lưu trữ đối tượng)' hoặc 'OSS (Alibaba Cloud)'                             | Có   |
| Loại phụ               | Tải động dựa trên loại lưu trữ: Đối với S3, các tùy chọn bao gồm AWS / Tencent Cloud COS / Huawei Cloud OBS / MinIO / Khác; đối với OSS, chỉ có Alibaba Cloud OSS là khả dụng | Có    |
| ID Khóa Truy cập     | Định danh thông tin xác thực                                                        | Có   |
| Bí mật Khóa Truy cập | Khóa thông tin xác thực, ô nhập PASSWORD được che giấu | Có |
| Khu vực | Ví dụ `cn-north-1` | Có |
| ForcePathStyle | Checkbox, có bật truy cập kiểu đường dẫn hay không | Không |
| SSL | Checkbox, có bật HTTPS | Không |
| Điểm cuối (Endpoint) | Địa chỉ truy cập dịch vụ nội bộ | Có |
| Địa chỉ truy cập công khai | Địa chỉ truy cập bên người dùng | Có |
| Quy tắc thay thế địa chỉ | Regex hoặc chuỗi dùng để ánh xạ địa chỉ nội bộ sang địa chỉ công khai | Có |

#### 2.1.2 Cài đặt Bucket 

**Các bước thao tác**: Tất cả các Bucket trả về bởi máy chủ sẽ được hiển thị từng cái một, và bạn có thể điền vào CDN tên miền theo nhu cầu. 

| Trường       | Mô tả                  |
| --------- | ------------------------- |
| Tên Bucket | Tên của Bucket         |
| Tiền tố      | Tiền tố đường dẫn lưu trữ đối tượng  |
| CDN Tên miền  | CDN Tên miền tăng tốc     |
| Bật CDN Xác thực | Checkbox, khi bật, hai mục "Loại xác thực" và "Khóa xác thực" sẽ được thêm vào |

> Sau khi bật CDN xác thực, yêu cầu loại xác thực và khóa xác thực tương ứng. 

### 2.2 Redis
**Các bước**: Sử dụng điều hướng nhanh bên phải để nhấp vào Redis biểu tượng để cuộn tới mục này → chọn một chế độ → điền địa chỉ và PASSWORD → nhấp "Kiểm tra kết nối". 

Mô tả Trường:

| Trường     | Mô tả                        |
| -----     | -----------------------------    |
| Chế độ      | Standalone hoặc Sentinel          |
| Địa chỉ   | Địa chỉ kết nối ở chế độ standalone, ví dụ, `redis-sentinel-master-ss:6379` |
| Tên Master | Yêu cầu trong chế độ Sentinel, ví dụ, `mymaster` |
| Danh sách Địa chỉ | Nhiều địa chỉ trong chế độ Sentinel, có thể thêm / xóa động |
| PASSWORD  | Bắt buộc                          |

Chuyển đổi chế độ sẽ tự động đặt lại các trường Địa chỉ, Tên Master và Danh sách Địa chỉ.

### 2.3 Kafka
**Các bước vận hành**: Nhấp vào Kafka biểu tượng trong điều hướng nhanh bên phải để cuộn đến phần này → Điền địa chỉ Broker → Nếu SASL được bật, mở rộng các SASL trường con → Nhấp vào "Kiểm tra Kết nối".

Mô tả Trường:

| Trường         | Mô tả                                         |
| ---------- | ------------------------------------------------ |
| Địa chỉ Broker | Mảng, có thể thêm / xóa động        |
| Tiền tố Chủ đề    | Tiền tố tự động thêm vào tất cả các Chủ đề         |
| Bật SASL Xác thực | Công tắc, bật nó sẽ thêm ba SASL cấu hình |
| Cơ chế Xác thực | PLAIN / SCRAM-SHA-256 / SCRAM-SHA-512 (sau khi bật SASL) |
| USERNAME / PASSWORD   | SASL thông tin đăng nhập (sau khi bật SASL)           |

### 2.4 MySQL (Cơ sở dữ liệu quan hệ)
**Các bước thao tác**: Trong điều hướng nhanh bên phải, nhấp vào RDB biểu tượng và cuộn đến phần này → chọn MySQL hoặc DM Dameng → điền host, cổng, USERNAMEPASSWORD → nhấp vào "Kiểm tra Kết nối".

Mô tả Trường:

| Trường      | Mô tả        |
| -------- | ---------------- |
| Loại Cơ sở dữ liệu | MySQL hoặc DM (Dameng) |
| Địa chỉ Host  | Ví dụ `mysql-master` |
| Cổng          | 3306           |
| USERNAME / PASSWORD | Thông tin đăng nhập      |

> Mục “RDB Cơ sở dữ liệu quan hệ” trong menu bên phải và tiêu đề trang tương ứng với MySQL phần cấu hình.

### 2.5 MongoDB
**Các bước thao tác**: Nhấp vào MongoDB biểu tượng trong điều hướng nhanh bên phải để cuộn đến phần này → Điền chuỗi kết nối → Điền từng thông tin đăng nhập cơ sở dữ liệu theo cấu hình máy chủ → Nhấp vào "Kiểm tra Kết nối".

Mô tả Trường: 

| Trường           | Mô tả                       |
| ------------- | -------------------------------- |
| Chuỗi Kết nối | Ví dụ `mongo-master:27017` |
| Mỗi Cơ sở dữ liệu USERNAME / PASSWORD | Điền từng cơ sở dữ liệu theo cấu hình máy chủ |

### 2.6 Elasticsearch
**Các bước:** Sử dụng điều hướng nhanh bên phải để nhấn vào Elasticsearch biểu tượng và cuộn đến phần này → Điền địa chỉ máy chủ và cổng → Nếu bật xác thực, điền vào USERNAME và PASSWORD → Nhấn 'Kiểm tra kết nối'.

Mô tả Trường: 

| Trường     | Mô tả       | Bắt buộc |
| ----      | --------------  | ----    |
| Địa chỉ máy chủ | ví dụ, `elasticsearch` | Có      |
| Cổng        | 9200             | Có      |
| USERNAME    | Thông tin xác thực ES   | Không       |
| PASSWORD    | Thông tin xác thực ES   | Không       |

---

## 3. Các thao tác phổ biến 

### 3.1 Cập nhật thông tin xác thực (ví dụ, Xoay Khóa Truy cập) 

1. Đi tới cấu hình phần mềm trung gian 
2. Thay thế ID Khóa Truy cập và Bí mật Khóa Truy cập trong S3 thẻ công khai 
3. Nhấp vào "Kiểm tra kết nối" và chờ thông báo màu xanh "Kiểm tra kết nối thành công" 
4. Lặp lại kiểm tra kết nối cho các phần đã chỉnh sửa khác 
5. Nhấp vào "Xuất bản Cấu hình" ở dưới cùng 
6. Hệ thống sẽ nhắc rằng một tác vụ không đồng bộ đã được tạo và chuyển hướng đến tab nhật ký tác vụ 

### 3.2 Chuyển Nhà cung cấp Middleware 

1. Đi tới cấu hình phần mềm trung gian 
2. Trong thẻ tương ứng, chỉnh sửa "Loại / Tiểu loại Lưu trữ" cũng như Endpoint, Access Key, quy tắc thay thế địa chỉ của nhà cung cấp mới, v.v. 
3. Sau khi chỉnh sửa, nhấp "Kiểm tra Kết nối" để xác minh 
4. Nhấp "Xuất bản Cấu hình" 

> Việc chuyển nhà cung cấp liên quan đến việc tải lại pool kết nối cho các dịch vụ hạ nguồn, vì vậy vui lòng tránh giờ cao điểm; sau khi chuyển, nên giám sát nhật ký ứng dụng trong 5 đến 10 phút. 

### 3.3 Bật Kafka SASL 

1. Đi tới cấu hình middleware và xác định vị trí Kafka phần 
2. Bật công tắc "Bật Xác thực" SASL và các SASL trường sẽ mở rộng 
3. Điền cơ chế xác thực, USERNAMEvà PASSWORD 
4. Nhấp "Kiểm tra Kết nối" 
5. Sau khi thành công, nhấp "Xuất bản Cấu hình" 

### 3.4 Khôi phục sau thao tác nhầm 

Trước khi nhấp vào "Xuất bản Cấu hình," trạng thái biểu mẫu được lưu trong localStorage của trình duyệt. Nó có thể được khôi phục theo cách sau: 

- Nhấp vào nút "Đặt lại tất cả" ở cuối, và tất cả các trường sẽ được khôi phục về giá trị ban đầu từ máy chủ 

### 3.5 Theo dõi Tác vụ Bất đồng bộ 

Sau khi xuất bản cấu hình thành công, hệ thống sẽ chuyển sang tab nhật ký tác vụ để hiển thị tiến độ tác vụ. Các tác vụ có thể dài hoặc ngắn, tùy thuộc vào số lượng phiên bản middleware và số lượng trường đã thay đổi. 

--- 

## 4. Các Vấn đề Thường gặp 

**Q1: Nhấp vào Redis ở điều hướng nhanh phía trên bên phải không có phản hồi.**

Bảng điều hướng nhanh bên phải chỉ cuộn đến phần tương ứng. Nếu phần đó không có trên trang hiện tại (ví dụ, bị che bởi một cửa sổ bật lên), bạn có thể cuộn trang hoặc nhấp vào Redis biểu tượng trong bảng điều hướng bên phải một lần nữa để định vị lại.

**Câu hỏi 2: Sau khi xuất bản cấu hình, trạng thái dường như không cập nhật.**

Trang sẽ tự động làm mới sau khi xuất bản. Nếu trình duyệt không tự làm mới, bạn có thể nhấn F5 thủ công để lấy cấu hình mới nhất.

**Câu hỏi 3: Số trong 'N cấu hình đã sửa đổi' không khớp với số lượng thực tế.**

Trang tính dựa trên các trường giá trị chưa lưu của biểu mẫu. Trong một số tình huống, chẳng hạn như sau khi đặt lại rồi sửa đổi, hoặc thêm/bớt các mục mảng động, điều này có thể gây ra sự không khớp trong việc đếm. Bạn có thể nhấp 'Đặt lại tất cả' và điền lại các mục.

**Câu hỏi 4: Thẻ cài đặt Bucket không tìm thấy Bucket mà tôi muốn thêm.**

Trang này hiển thị các Bucket hiện có dựa trên cấu hình phía máy chủ. Việc thêm một Bucket mới yêu cầu sửa đổi tệp cấu hình máy chủ cơ sở, không phải trang này. Nếu điều này cần thiết, vui lòng liên hệ với kỹ sư triển khai.

---

## Phụ lục A: Tham khảo Thuật ngữ

| Thuật ngữ       | Giải thích                                                                |
| -------- | ----------------------------------------------------------------- |
| Tệp Cấu hình Máy chủ | Nguồn cấu hình cuối cùng cho việc bảo trì nền tảng, được tạo bằng cách hợp nhất các giá trị mặc định của nền tảng với các giá trị được phát hành trên trang này |
| Bucket   | Các bucket lưu trữ trong S3 / OSS |
| Điểm cuối (Endpoint) | Địa chỉ dịch vụ Middleware, dùng cho truy cập nội bộ cụm |
| Địa chỉ truy cập công khai | Địa chỉ middleware hiển thị cho người dùng |
| Quy tắc thay thế địa chỉ | Ánh xạ một địa chỉ nội bộ thành regex hoặc chuỗi của một địa chỉ công khai |
| SASL     | Lớp Xác thực và Bảo mật Đơn giản, cơ chế xác thực cho các thành phần như Kafka |
| Sentinel | Một trong Rediscác kế hoạch khả dụng cao của |
| DM       | Dameng Cơ sở dữ liệu (cơ sở dữ liệu quan hệ trong nước)                                             |
| Trường Bẩn       | Các trường trong biểu mẫu đã bị chỉnh sửa và khác so với giá trị ban đầu          |
