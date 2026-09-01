# Yêu cầu hệ thống

[← ShimoDocs Suite Tài liệu triển khai](README.md)

## 1. Chuẩn bị tài nguyên theo kịch bản

| Kịch bản sử dụng | Triển khai khuyến nghị | Chuẩn bị tài nguyên |
| --- | --- | --- |
| Nhóm nhẹ, nhỏ, PoC, trình diễn, xác minh tính năng | Triển khai máy chủ đơn | 1 máy chủ |
| Ra mắt chính thức, vận hành lâu dài, yêu cầu độ khả dụng cao hoặc mở rộng trong tương lai | Cụm khả dụng cao | 3 máy chủ trở lên |

- Triển khai máy chủ đơn phù hợp cho việc xác minh nhanh và sử dụng quy mô nhỏ.
- Triển khai cụm phù hợp cho việc ra mắt chính thức, vận hành lâu dài và mở rộng trong tương lai.

## 2. Yêu cầu Hệ điều hành

| Hệ điều hành | Phiên bản Hỗ trợ | Kiến trúc Hỗ trợ |
| --- | --- | --- |
| Ubuntu | 22.04, 24.04 | x86 |

Thực thi trên mỗi máy chủ:

```bash
cat /etc/os-release
uname -m
```

Kết quả xác nhận: 

- Hệ điều hành là Ubuntu 22.04 hoặc Ubuntu 24.04. 
- CPU Kiến trúc là x86. 
- Tài khoản cài đặt là `root`, hoặc có quyền quản trị hệ thống tương đương. 

Lưu ý: Lý do không còn hỗ trợ hệ thống CentOS 
- CentOS Linux 7 và 8 đã hết vòng đời, CentOS chính thức không còn cung cấp CentOS 9 và các phiên bản tiếp theo, và không còn nhận các bản cập nhật bảo mật mới, sửa lỗi lỗ hổng, hoặc vá vấn đề. 
- Các thành phần hệ thống cơ bản không thể nhận các bản vá bảo mật dài hạn, tiềm ẩn các lỗ hổng nhưng không thể khắc phục, điều này không đáp ứng yêu cầu bảo mật cho môi trường sản xuất. 
- Kernel, glibc, OpenSSL và các thành phần cơ bản khác trong CentOS 7/8 tương đối cũ và không thể đáp ứng yêu cầu của các Kubernetes thư viện chạy và phụ thuộc mới. 
- CentOS Stream có định vị phiên bản và cơ chế cập nhật khác so với CentOS Linux truyền thống, và các môi trường CentOS Stream chưa được kiểm tra đặc biệt về khả năng tương thích cũng không nằm trong phạm vi hỗ trợ chính thức. 


## 3. Yêu cầu cấu hình máy chủ 

### 3.1 Triển khai đơn nút 

- Phù hợp cho các nhóm nhỏ nhẹ, dưới 200 người. 
- PoC, kịch bản trình diễn và xác minh chức năng có thể được chuẩn bị theo tài nguyên đơn nút. 

| Dự án | Yêu cầu | 
| --- | --- | 
| Số lượng máy chủ | 1 | 
| CPU | Từ 16 lõi trở lên |
| Bộ nhớ | 32 GB hoặc nhiều hơn |
| Đĩa hệ thống | Root `/` phân vùng 100 GB hoặc nhiều hơn |
| Đĩa dữ liệu | Gắn độc lập `/data`, dung lượng khả dụng 300 GB trở lên, hỗ trợ mở rộng |

### 3.2 Triển khai Cụm

Đối với các kịch bản yêu cầu ra mắt chính thức, vận hành lâu dài, khả năng sẵn sàng cao hoặc mở rộng trong tương lai, hãy chuẩn bị tài nguyên theo yêu cầu của cụm.

| Mục | Yêu cầu |
| --- | --- |
| Số lượng máy chủ | 3 hoặc nhiều hơn |
| Vai trò được khuyến nghị | `3 master   N worker` |
| CPU mỗi nút | Từ 16 lõi trở lên |
| Bộ nhớ mỗi nút | 32 GB hoặc nhiều hơn |
| Đĩa Hệ Thống mỗi Nút | Root `/` phân vùng 100 GB hoặc nhiều hơn |
| Đĩa Dữ Liệu mỗi Nút | Gắn độc lập `/data`, dung lượng khả dụng 300 GB trở lên, hỗ trợ mở rộng |

Ghi chú Phân vùng:

- Giữ ít nhất 100 GB cho phân vùng gốc `/` phân vùng.
- Nên đặt `/root`, `/var`, `/tmp` dưới phân vùng gốc để quản lý thống nhất.
- Sử dụng một đĩa dữ liệu độc lập cho thư mục dữ liệu, được gắn tại `/data`.

## 4. Lệnh Kiểm Tra Tự Động của Máy Chủ

Thực thi trên mỗi máy chủ: 

```bash
# ============================================
# 1. View CPU architecture and core information
#    - Architecture type (x86_64/aarch64)
# ============================================
lscpu

# ============================================
# 2. Display memory and swap usage in GiB
# ============================================
free -g

# ============================================
# 3. File System Disk Space Usage
# ============================================
df -h

# ============================================
# 4. Find the executable file path
# ============================================
which iptables gzip tar

# ============================================
# 5. Display system time, time zone, and NTP synchronization status
#    Distributed clusters must have strict time synchronization, otherwise it will affect authentication and log sequencing.
# ============================================
timedatectl status
```

Danh sách kiểm tra so sánh:

| Mục kiểm tra | Điều kiện đạt |
| --- | --- |
| CPU | 16 Nhân hoặc hơn |
| Bộ nhớ | 32 GB hoặc hơn |
| Đĩa hệ thống | Root `/` Không gian khả dụng của phân vùng 100 GB trở lên |
| Đĩa dữ liệu | `/data` Đã gắn kết, không gian khả dụng 300 GB trở lên |
| Các lệnh cơ bản | `iptables`, `gzip`, `tar` có thể tìm thấy |
| Đồng bộ thời gian | Đồng bộ thời gian hệ thống bình thường |

## 5. Yêu cầu trình duyệt

| Trình duyệt | Yêu Cầu Phiên Bản |
| --- | --- |
| Chrome | 86 trở lên |
| Safari | 11 trở lên |
| Firefox | 102 trở lên |
| Edge | 84 trở lên |

Khuyến nghị ưu tiên sử dụng phiên bản Chrome hoặc Edge mới hơn để truy cập trình cài đặt và ShimoDocs Suite.

## 6. Yêu Cầu Middleware

| Thành Phần | Yêu Cầu Phiên Bản |
| --- | --- |
| Elasticsearch | 8.18.x |
| MongoDB | 4.4.x |
| Redis | 6.2.x |
| MySQL | 8.0 |
| Dameng | V8 03134284194-20240920-243574-20108 |
| Kafka | 2.7 đến 3.5 |
| Lưu trữ Đối tượng | Tương thích với S3 giao thức<br>và đảm bảo địa chỉ Endpoint của nó có thể được truy cập trực tiếp bởi trình duyệt của khách hàng từ mạng công cộng (vì ShimoDocs tải tài nguyên tĩnh của ứng dụng và các thao tác đọc/ghi tài liệu phải được hoàn thành thông qua kết nối trực tiếp của trình duyệt tới lưu trữ đối tượng). |

Lưu trữ đối tượng có thể chọn Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, AWS S3. Đối với triển khai cục bộ, sử dụng MinIO có thể được xem xét.

Nếu sử dụng phần mềm trung gian được tích hợp trong trình cài đặt, tiếp tục với các tùy chọn mặc định trên trang cài đặt. 
Nếu sử dụng phần mềm trung gian bên ngoài hiện có, chuẩn bị địa chỉ, cổng, tài khoản, PASSWORD, DATABASE_NAME hoặc tên Bucket trước khi cài đặt.

## 7. Yêu cầu Cổng

Trước khi triển khai, đảm bảo rằng máy chủ, nhóm bảo mật, tường lửa, bộ cân bằng tải và chính sách mạng đã cho phép các cổng sau.

| Cổng | Mục tiêu | Mục đích |
| --- | --- | --- |
| `18080/TCP` | Giao diện Web UI của Trình cài đặt | Truy cập trang cài đặt |
| `80/TCP` hoặc `443/TCP` | ShimoDocsSERVICE_DOMAIN | Điểm truy cập người dùng |
| `22/TCP` | Tất cả các nút triển khai | SSH Đăng nhập và phân phối nhiệm vụ cài đặt |
| `3306/TCP` | MySQL | Kết nối cơ sở dữ liệu |
| `6379/TCP` | Redis | Kết nối bộ nhớ đệm |
| `27017/TCP` | MongoDB | Kết nối cơ sở dữ liệu tài liệu |
| `9092/TCP` | Kafka | Kết nối hàng đợi tin nhắn |
| `9200/TCP` | Elasticsearch | Kết nối dịch vụ tìm kiếm |
| Theo cổng dịch vụ | S3 / OBS / OSS / COS / MinIO | Kết nối lưu trữ đối tượng |

## 8. Yêu cầu IO Ổ đĩa

Nên sử dụng SSD cho các ổ đĩa dữ liệu. Hiệu năng ổ đĩa nên đáp ứng các tiêu chuẩn sau:

| Mục | Yêu cầu |
| --- | --- |
| Đọc/ghi hỗn hợp IOPS | Trên 5000 |
| Thông lượng đọc/ghi tuần tự | Trên 150 MB/s |
| Độ trễ trung bình | Khoảng 5 ms hoặc thấp hơn |

Sau khi cài đặt `fio`, các bài kiểm tra có thể được tiến hành trên `/data`.

### 8.1 Kiểm tra Đọc/Ghi Hỗn hợp

```bash
fio --name=randrw-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=randrw \
  --rwmixread=70 \
  --bs=4k \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=4 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Chú ý đến IOPS trong kết quả; đọc/ghi hỗn hợp IOPS phải đạt trên 5000 để tiếp tục. 

### 8.2 Kiểm tra Đọc Tuần tự

```bash
fio --name=seqread-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=read \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

### 8.3 Kiểm tra Ghi Tuần tự

```bash
fio --name=seqwrite-test \
  --filename=/data/testfile \
  --size=20G \
  --rw=write \
  --bs=1M \
  --ioengine=libaio \
  --direct=1 \
  --iodepth=32 \
  --numjobs=1 \
  --runtime=300 \
  --time_based \
  --group_reporting
```

Thông lượng đọc tuần tự và ghi tuần tự đạt trên 150 MB/s có thể tiếp tục. 

Các tệp kiểm tra có thể được xóa sau khi thử nghiệm: 

```bash
rm -f /data/testfile
```

## 9. Yêu cầu Băng thông Mạng Công cộng

Ước lượng băng thông cho các tình huống truy cập mạng công cộng dựa trên số lượng người dùng:

```text
PUBLIC_NETWORK_BANDWIDTH = NUMBER_OF_USERS x 0.25 Mbps
```

Ví dụ:

| Số lượng Người dùng | Băng thông mạng công cộng được đề xuất |
| --- | --- |
| 100 Người dùng | Trên 25 Mbps |
| 200 Người dùng | Trên 50 Mbps |
| 500 Người dùng | Trên 125 Mbps |

Đối với các kịch bản truy cập mạng nội bộ, cũng nên đánh giá băng thông ra vào và cân bằng tải theo cùng một tiêu chí.

## 10. Khuyến nghị phiên bản trình duyệt của nền tảng cài đặt và vận hành

Nên sử dụng Google Chrome phiên bản 111 trở lên, tốt nhất là phiên bản ổn định mới nhất.

## 11. Danh sách kiểm tra trước triển khai

Trước khi bắt đầu cài đặt, xác nhận từng mục:

- Phiên bản hệ điều hành đáp ứng yêu cầu.
- CPUBộ nhớ, đĩa hệ thống và đĩa dữ liệu đáp ứng yêu cầu.
- `/data` Được gắn vào một đĩa dữ liệu riêng biệt.
- `iptables`, `gzip`và `tar` Đã cài đặt.
- Đồng bộ hóa thời gian hệ thống bình thường.
- Phương pháp cài đặt trực tuyến hoặc ngoại tuyến đã được xác định.
- Cổng trình cài đặt `18080` Có thể truy cập.
- Cổng truy cập doanh nghiệp `80` hoặc `443` Đang mở.
- Nếu sử dụng phần mềm trung gian bên ngoài, thông tin kết nối đã được chuẩn bị đầy đủ.
- Lưu trữ đối tượng tương thích với S3 giao thức, và quyền của bucket và tài khoản đã sẵn sàng. 
- Kiểm tra IO của đĩa dữ liệu đáp ứng yêu cầu. 
- Băng thông mạng công cộng hoặc nội bộ đáp ứng số lượng người dùng dự kiến.
