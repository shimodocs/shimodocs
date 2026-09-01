# Lập kế hoạch tài nguyên

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

## 1. Mục đích Tài liệu

Tài liệu này được sử dụng để hướng dẫn lập kế hoạch tài nguyên máy chủ và phần mềm trung gian trong các kịch bản triển khai riêng, để các kỹ sư triển khai, kỹ sư vận hành và nhân viên hỗ trợ kỹ thuật trước bán hàng tham khảo.

Nội dung của tài liệu dựa trên kế hoạch năng lực dự án lịch sử, cấu hình mẫu, và cơ sở phần mềm trung gian, và có thể được sử dụng cho ước lượng trước bán hàng, ứng dụng tài nguyên, triển khai thực hiện, và đánh giá mở rộng sau này.

## 2. Phạm vi và Hướng dẫn

### 2.1 Phạm vi

Tài liệu này áp dụng cho việc lập kế hoạch sơ bộ các nút ứng dụng và tài nguyên phần mềm trung gian cho các quy mô người dùng khác nhau trong các kịch bản triển khai riêng.

### 2.2 Hướng dẫn

* Các cấu hình trong tài liệu này đều là các cấu hình được khuyến nghị, dùng cho đánh giá năng lực dự án giai đoạn đầu và lập kế hoạch tài nguyên.

* Tài nguyên nút ứng dụng và tài nguyên phần mềm trung gian nên được tính toán riêng biệt; không khuyến nghị lập kế hoạch kết hợp.

* Trong các kịch bản người dùng quy mô lớn, tài nguyên phần mềm trung gian cần được hiệu chỉnh thêm dựa trên tải công việc cao điểm, mô hình đồng thời, kết quả kiểm tra áp lực năng lực và dữ liệu giám sát sản xuất.

* Trong môi trường sản xuất chính thức, khuyến nghị dự phòng khả năng mở rộng và ưu tiên xây dựng khả năng sẵn có cao.

* Nếu sử dụng máy chủ kiến trúc nội địa, CPU khuyến nghị ước tính tổng tài nguyên là gấp đôi cấu hình tiêu chuẩn.

## 3. Nguyên tắc lập kế hoạch

### 3.1 Nguyên tắc triển khai Ứng dụng và Phần mềm trung gian

* Đối với các kịch bản có ít hơn 10.000 người dùng, có thể đánh giá việc triển khai một số phần mềm trung gian trong K8s cụm dựa trên tình hình dự án thực tế.

* Đối với các kịch bản có từ 10.000 người dùng trở lên, khuyến nghị triển khai các nút ứng dụng và phần mềm trung gian hoàn toàn riêng biệt.

* Các phần mềm trung gian cốt lõi như cơ sở dữ liệu, bộ nhớ đệm, hàng đợi tin nhắn và dịch vụ tìm kiếm được khuyến nghị triển khai với kiến trúc sẵn sàng cao là ưu tiên.

* Khi điều kiện cho phép, khuyến nghị ưu tiên sử dụng các dịch vụ phần mềm trung gian quản lý công khai trưởng thành trên đám mây để cải thiện độ ổn định và khả năng bảo trì.

### 3.2 Nguyên tắc lập kế hoạch Lưu trữ Đối tượng

* Ưu tiên sử dụng các dịch vụ lưu trữ đối tượng công khai trên đám mây, chẳng hạn như Alibaba Cloud OSS, Huawei Cloud OBS, Tencent Cloud COS, AWS S3.

* Nếu sử dụng triển khai riêng tư của lưu trữ đối tượng, SSD phải sử dụng ổ đĩa, và hiệu suất, độ ổn định, và khả năng vận hành sau khi tăng dung lượng phải được đánh giá cẩn thận.

* Nếu doanh nghiệp liên quan đến việc tải lên, tải xuống, xem trước lượng lớn tệp, hoặc các tình huống cộng tác nhiều người dùng chỉnh sửa bảng tính lớn, khuyến nghị ưu tiên sử dụng dịch vụ lưu trữ đối tượng độc lập.

## 4. Kế hoạch Nút Ứng dụng

### 4.1 Phân loại Thông số Nút Ứng dụng

#### Thông số A

* Thông số khuyến nghị: `24C / 48G / >=500G SSD * N`

* Phạm vi áp dụng: dưới 10.000 người dùng

* Các tính năng áp dụng: 

   * Có thể hỗ trợ các tình huống doanh nghiệp nhỏ đến vừa 

   * Middleware có thể được triển khai trong K8s môi trường tùy thuộc vào dự án 

   * Một nút đơn chịu tải cao; khi một nút gặp sự cố, phạm vi ảnh hưởng tương đối lớn 

#### Thông số B 

* Thông số Khuyến nghị: `16C / 32G / >=300G SSD * N` 

* Phạm vi áp dụng: 10.000 người dùng trở lên 

* Các tính năng áp dụng: 

   * Phù hợp cho các tình huống triển khai quy mô lớn, độ sẵn sàng cao 

   * Phải sử dụng middleware độc lập 

   * Sử dụng cách tiếp cận nhiều nút với thông số nhỏ, cung cấp phân bổ tải cân bằng hơn và mở rộng linh hoạt hơn 

   * Khi một nút được bảo trì hoặc gặp sự cố, tác động tổng thể đến doanh nghiệp nhỏ hơn 






### 4.2 Tiêu chí Tính toán Nút Ứng dụng 

Dựa trên các ví dụ dự án hiện có và quy tắc tính toán dung lượng, các nút ứng dụng được khuyến nghị ước tính bằng công thức sau: 

`Number of nodes = Number of users × 0.03 ÷ 160` 

Có thể hiểu đơn giản là: 

`Number of nodes ≈ Number of users ÷ 5300`

Trong đó:

* Hệ số người dùng đồng thời được ước tính là `0.03`.

* Dung lượng của một `16C / 32G` nút xấp xỉ `150 ~ 180 QPS`.

* Khuyến nghị sử dụng `160 QPS/node` như cơ sở tính toán.

* Kết quả tính toán được khuyến nghị làm tròn lên, với dung lượng bổ sung dự phòng cho việc mở rộng.

### 4.3 Bảng Cấu Hình Khuyến Nghị cho Các Nút Ứng Dụng

| Quy mô người dùng (Người) | Thông số kỹ thuật của nút | Số lượng khuyến nghị | Gợi ý triển khai |
|:----|:----|:----|:----|
|500|24C / 48G / 500G SSD|1 đơn vị|Có thể triển khai trên một máy; để đảm bảo khả năng cao, khuyến nghị triển khai ít nhất 3 máy chủ|
|3000|24C / 48G / 500G SSD|3 đơn vị|Chế độ cụm, triển khai khả năng cao (ngưỡng thông số kỹ thuật tối thiểu cho triển khai cụm)|
|10,000|24C / 48G / 500G SSD|3 đơn vị|Chế độ cụm, triển khai khả năng cao; việc sử dụng phần mềm trung gian bên ngoài có thể được đánh giá theo nhu cầu dự án|
|30,000|16C / 32G / 300G SSD|5 đơn vị|Chế độ cụm, triển khai khả năng cao, sử dụng phần mềm trung gian độc lập|
|50,000|16C / 32G / 300G SSD|10 đơn vị|Chế độ cụm, triển khai khả năng cao, sử dụng phần mềm trung gian độc lập|
|100,000|16C / 32G / 300G SSD|18 ~ 20 đơn vị|Khuyến nghị bắt đầu với 18 đơn vị và dự trữ dung lượng để mở rộng, sử dụng phần mềm trung gian độc lập|
|200,000|16C / 32G / 300G SSD|38 ~ 40 đơn vị|Khuyến nghị xây dựng và triển khai theo các giai đoạn|
|300,000|16C / 32G / 300G SSD|56 ~ 60 đơn vị|Khuyến nghị xây dựng và triển khai theo các giai đoạn|
|500,000|16C / 32G / 300G SSD|94 ~ 100 đơn vị|Khuyến nghị lập kế hoạch một nhóm tài nguyên độc lập và xây dựng, triển khai theo giai đoạn|
|700,000|16C / 32G / 300G SSD|132 ~ 140 đơn vị|Khuyến nghị lập kế hoạch một nhóm tài nguyên độc lập và xây dựng, triển khai theo giai đoạn|

### 4.4 Kết luận về lên kế hoạch các nút ứng dụng

* Người dùng dưới 10.000 được khuyến nghị sử dụng Thông số A

* Người dùng từ 10.000 trở lên được khuyến nghị sử dụng Thông số B

* Đối với quy mô người dùng 100.000, có thể bắt đầu với 18 đơn vị theo mốc mẫu, các quy mô khác được ước tính theo công thức thống nhất và làm tròn lên

* Đối với các dự án có tăng trưởng liên tục, khuyến nghị tiến hành chiến lược mở rộng theo giai đoạn để tránh đầu tư một lần quá mức

## 5. Lên kế hoạch phần mềm trung gian

### 5.1 Nguyên tắc phân loại phần mềm trung gian

Kế hoạch tài nguyên phần mềm trung gian hiện tại được thực hiện theo hai cấp độ cơ bản:

* `Users below 3,000`: Sử dụng cấu hình cơ bản quy mô nhỏ.

* `3000 users and above`: Sử dụng cấu hình cơ bản quy mô lớn. 

Đối với các kịch bản quy mô lớn như 10.000, 30.000, 50.000, 100.000, 200.000, 300.000, 500.000, 700.000 người dùng, đề xuất bắt đầu đồng đều với cấu hình cơ bản 'từ 3000 người dùng trở lên', và mở rộng linh hoạt theo sự phát triển của doanh nghiệp. 

### 5.2 Bảng tiêu chuẩn phần mềm trung gian 

|Phần mềm trung gian|Phiên bản Khuyến nghị|Dưới 3000 Người dùng|3000 Người dùng trở lên|Yêu cầu Tính sẵn sàng Cao| 
|:----|:----|:----|:----|:----| 
|MySQL|MySQL 8.0|4C / 8G / 200G SSD|8C / 16G / 200G SSD|Tính sẵn sàng cao với cơ chế dự phòng Master-slave<br>Bộ ký tự: utf8mb4<br>Múi giờ: Châu Á/Thượng Hải hoặc UTC<br>Kết nối: tối đa_kết nối ≥ 1000| 
|MongoDB|MongoDB 4.4|2C / 8G / 100G SSD|4C / 16G / 100G SSD|Cụm tính sẵn sàng cao theo chế độ Replica set| 
|Redis|Redis 6.2.21|2C / 4G / 100G SSD|2C / 8G / 100G SSD|Tính sẵn sàng cao Master-slave/sentinel, bảo lưu dữ liệu; Không hỗ trợ chế độ Cluster; Số lượng DB ≥ 64| 
|Kafka|Kafka 3.5|2C / 4G / 300G SSD|4C / 8G / 300G SSD|Số lượng broker >= 3, hệ số nhân bản mặc định 3<br>Giữ tin nhắn: 72 giờ (có thể điều chỉnh theo nhu cầu kinh doanh)<br>Kích thước tin nhắn tối đa trên mỗi Chủ đề: 10 MB<br>Xác thực: Hỗ trợ SASL truy cập mã hóa (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512)|
|Elasticsearch|ES 8.18.5|2C / 4G / 200G SSD|4C / 8G / 200G SSD|Số lượng nút >= 3<br>Cài đặt bắt buộc:<br>analysis-ik (tách từ tiếng Trung),<br>analysis-pinyin (tách Pinyin)|
|Lưu trữ Đối tượng|S3 tương thích giao thức|Tương thích với S3|Tương thích với S3 giao thức|Ưu tiên đám mây công cộng, phải hỗ trợ HTTPS truy cập từ bên ngoài|

Lưu ý: 

* Các thông số kỹ thuật middleware nêu trên cần được mở rộng theo tải thực tế





## 6. Đề xuất triển khai và vận hành & bảo trì

### 6.1 Đề xuất triển khai

* MySQL, MongoDB, Redis, Kafka, Elasticsearch Được khuyến nghị triển khai ở chế độ cụm có khả năng sẵn sàng cao.

* Nếu điều kiện cho phép, khuyến nghị ưu tiên sử dụng cơ sở dữ liệu và dịch vụ middleware được quản lý trên đám mây công cộng để cải thiện tính ổn định và khả năng bảo trì.

* Với các kịch bản người dùng có 10.000 người trở lên, khuyến nghị triển khai các node ứng dụng và middleware riêng biệt. 

* Đối với Kafka, khuyến nghị sử dụng một instance riêng để tránh chia sẻ tài nguyên với các doanh nghiệp khác. 

### 6.2 Đề xuất triển khai lưu trữ đối tượng 

* Khuyến nghị ưu tiên sử dụng sản phẩm lưu trữ đối tượng trên đám mây công cộng. 

* Nếu sử dụng lưu trữ đối tượng riêng, SSD phải sử dụng ổ đĩa. 

* Nếu không gian nhóm liên quan đến nhiều kịch bản tải lên, tải xuống hoặc xem trước các tệp lớn, dung lượng, thông lượng và băng thông của lưu trữ đối tượng nên là các yếu tố đánh giá chính. 

### 6.3 Cân nhắc khi mở rộng 

Trong các kịch bản kinh doanh sau, khuyến nghị ưu tiên đánh giá và bổ sung tài nguyên middleware: 

* Số lượng lớn tệp đính kèm được tải lên, tải xuống hoặc xem trước 

* Tìm kiếm toàn văn tần suất cao 

* Tích tụ tin nhắn hoặc các tác vụ không đồng bộ tập trung 

* Ghi hàng loạt và phân tích thống kê trong các thời kỳ cao điểm 

* Tăng trưởng liên tục khối lượng nhật ký 

Các chỉ số chính cần tập trung bao gồm: 

* Cơ sở dữ liệu: CPU, bộ nhớ, IO đĩa 

* Redis: số lượng kết nối, tỉ lệ truy cập, sử dụng băng thông 

* Kafka: số lượng broker, tích tụ tin nhắn, dung lượng đĩa 

* Elasticsearch: Số lượng nút, kích thước chỉ mục, dung lượng lưu trữ 

* Lưu trữ đối tượng: hiệu suất đọc/ghi, thông lượng yêu cầu, dung lượng, băng thông 






## 7. Kết luận 

* Đối với các kịch bản quy mô nhỏ (dưới 10.000 người dùng), khuyến nghị sử dụng cấu hình nút ứng dụng Thông số A, và đánh giá xem có cần triển khai một số phần mềm trung gian trong cụm dựa trên tình hình dự án. 

* Đối với các kịch bản quy mô trung và lớn (10.000 người dùng trở lên), khuyến nghị sử dụng cấu hình nút ứng dụng Thông số B, kết hợp với phần mềm trung gian độc lập và kiến trúc khả dụng cao. 
* Phần mềm trung gian được khuyến nghị cấu hình dựa trên hai mốc: "dưới 3000 người dùng" và "từ 3000 người dùng trở lên". Đối với các dự án quy mô lớn, việc mở rộng liên tục dựa trên kiểm tra căng thẳng và dữ liệu giám sát. 
* Trước khi triển khai chính thức, việc xác nhận kế hoạch tài nguyên, kiểm tra khả năng tương thích và kiểm tra sức chịu tải dung lượng nên được thực hiện đồng thời để tránh sự khác biệt giữa thông số triển khai và phạm vi được hỗ trợ thực tế. 

* Nếu sử dụng một sản phẩm trong nước CPU máy chủ kiến trúc, khuyến nghị ước lượng tài nguyên gấp đôi thông số tiêu chuẩn. 

* Hướng dẫn này dành cho việc lựa chọn trước khi cài đặt và không thay thế cho kiểm tra tải trực tiếp tại chỗ hoặc triển khai cuối cùng.
