# Tham khảo Chỉ số Giám sát

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

Tài liệu này tổ chức các chỉ số thường dùng trong hệ thống giám sát, bao gồm các nút, các container containerd, Kubernetes cụm, phần mềm trung gian và dịch vụ ứng dụng, cung cấp một tài liệu tham khảo thống nhất cho kiểm tra hàng ngày, đánh giá năng lực và xử lý sự cố. 

Tên chỉ số dựa trên các chỉ số xuất thực tế được thu thập trong Prometheus. Các phiên bản xuất khác nhau có thể có sự khác biệt nhỏ, và việc xử lý sự cố thực tế nên dựa vào kết quả truy vấn trực tuyến làm tài liệu tham khảo cuối cùng. 

## Phạm vi 

| Danh mục | Đối tượng được bao phủ | 
| --- | --- | 
| Giám sát nút | Máy chủ Linux, tài nguyên hệ thống, đĩa, mạng, tiến trình | 
| Giám sát Container | Các container chạy trên containerd, tài nguyên container Pod | 
| Kubernetes Cụm | Nút, Pod, Triển khai, StatefulSet, Job, PVC, APIServer | 
| MySQL | MySQL phiên bản, kết nối, truy vấn, bộ nhớ đệm, khóa, mạng | 
| MongoDB | MongoDB phiên bản, kết nối, thao tác, bộ nhớ, mạng, bộ đệm sao chép | 
| Redis | Redis phiên bản, client, lệnh, bộ nhớ, Keyspace, tỷ lệ trúng | 
| Kafka | Broker, Chủ đề, Phân vùng, Nhóm người tiêu dùng, Trễ, Bản sao | 
| MinIO | Nút cụm, đĩa, Bucket, S3 yêu cầu, khả năng đối tượng | 
| Elasticsearch | Tình trạng cụm, nút, phân mảnh, chỉ mục, JVM, nhóm luồng, mạng |
| Dịch vụ Ứng dụng | Máy chủ chung, cuộc gọi khách hàng, chỉnh sửa cộng tác, dịch vụ RS, thời gian chạy |

## Quy tắc Đọc Số liệu

| Loại Số liệu | Phương pháp Đọc | Cú pháp PromQL Thông dụng | Mô tả |
| --- | --- | --- | --- |
| Bộ đếm | Xem tốc độ tăng trưởng hoặc gia tăng trong một cửa sổ thời gian | `rate(x_total[5m])`, `increase(x_total[5m])` | Số lượng yêu cầu, số lượng lỗi, số lượng byte, thời gian IO thường thuộc về Bộ đếm |
| Đồng hồ đo | Xem giá trị hiện tại, trung bình, tối đa | `avg(x)`, `max(x)`, `sum(x)` | Bộ nhớ, số lượng kết nối, khả năng, giá trị trạng thái thường thuộc về Đồng hồ đo |
| Biểu đồ phân phối | Xem độ trễ theo phần trăm | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | Độ trễ yêu cầu, độ trễ xử lý, độ trễ hàng đợi thường sử dụng Biểu đồ Histogram |
| Tỷ lệ | Xem xét phần trăm | `A / B * 100` | Sử dụng, tỷ lệ lỗi, tỷ lệ trúng đều thuộc loại số liệu tỉ lệ |

Khuyến nghị không sao chép trực tiếp các số cố định làm ngưỡng. Các số liệu như CPU, bộ nhớ, ổ đĩa, số lượng kết nối, QPS, và độ trễ nên được đánh giá trong bối cảnh cao điểm kinh doanh, hoạch định năng lực, và mức nền lịch sử. Các hành vi bất thường trong tài liệu được sử dụng để nhanh chóng xác định rủi ro và không tương đương với ngưỡng cảnh báo cuối cùng.

## 1. Giám sát Dịch vụ Node

Giám sát node được sử dụng để xác định xem host có khỏe mạnh hay không, nguồn lực có đủ hay không, và liệu có nghẽn cổ chai trên đĩa hoặc mạng hay không. Các chỉ số của node chủ yếu đến từ node-exporter, kết hợp với bảng điều khiển tiến trình hệ thống để định vị ở cấp tiến trình.

### 1.1 Trạng thái Cơ bản

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Node Sống | `up` | Cho biết liệu nhà xuất khẩu hoặc mục tiêu thu thập có thể truy cập được hay không | `1` cho biết có thể thu thập, `0` cho biết không thể thu thập | Liên tục `0` cho biết có vấn đề với nút, mạng hoặc nhà xuất khẩu |
| Thời gian khởi động | `node_boot_time_seconds` | Thời gian khởi động cuối cùng của nút | Dấu thời gian Unix | Thay đổi thời gian khởi động cho biết nút đã được khởi động lại |
| Thông tin nút | `node_uname_info`, `node_os_info` | Hệ điều hành, kernel và thông tin phân phối | Thông tin nhãn | Được sử dụng để xác minh phiên bản nút, không được sử dụng trực tiếp như một chỉ số cảnh báo |

Gợi ý khắc phục sự cố: kiểm tra `up` trước, sau đó `node_boot_time_seconds`. Nếu nút không thể thu thập và thời gian khởi động gần đây đã thay đổi, ưu tiên xác nhận khởi động lại host, mạng ACL, và trạng thái tiến trình node-exporter.

### 1.2 CPU Chỉ số

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| CPU Sử dụng | `node_cpu_seconds_total` | Thời gian tích lũy mà mỗi CPU lõi dành trong các chế độ khác nhau | Tỷ lệ phần trăm | `user` và `system` vẫn cao lâu dài, công suất tính toán của node là hạn chế |
| Rảnh rỗi CPU | `node_cpu_seconds_total{mode="idle"}` | CPU thời gian nhàn rỗi | Tỷ lệ phần trăm | Thời gian nhàn rỗi luôn thấp, điều này có thể gây xếp hàng và tăng độ trễ |
| Chờ I/O | `node_cpu_seconds_total{mode="iowait"}` | Thời gian CPU chờ I/O đĩa | Tỷ lệ phần trăm | Tăng liên tục iowait thường cho thấy đĩa hoặc liên kết lưu trữ chậm hơn |
| Tải hệ thống | `node_load1`, `node_load5`, `node_load15` | Tải trung bình 1/5/15 phút | Giá trị tải | Tải liên tục cao hơn số CPU lõi cho thấy có xếp hàng tác vụ đáng kể |
| CPU Áp lực | `node_pressure_cpu_waiting_seconds_total` | Thời gian tích lũy CPU PSI chờ | Giây/giây | CPU xung đột tài nguyên là đáng kể, các tiến trình đang chờ CPU lập lịch |

Các truy vấn phổ biến:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

Gợi ý điều tra: Khi CPU sử dụng cao, trước tiên phân biệt giữa `user`, `system`và `iowait`. Cao `user` thường do áp lực tính toán kinh doanh, cao `system` có thể liên quan đến gọi hệ thống và xử lý gói mạng, và cao `iowait` cần kiểm tra thông lượng đĩa, IOPS, và độ trễ.

### 1.3 Chỉ số bộ nhớ

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Tổng bộ nhớ | `node_memory_MemTotal_bytes` | Tổng bộ nhớ vật lý của node | Byte | Dùng để tính tỷ lệ sử dụng |
| Bộ nhớ có sẵn | `node_memory_MemAvailable_bytes` | Bộ nhớ mà hệ thống có thể cấp phát cho tiến trình | Byte / Tỷ lệ phần trăm | Bộ nhớ khả dụng luôn thấp dễ dẫn đến OOM hoặc thu hồi thường xuyên |
| Bộ nhớ trống | `node_memory_MemFree_bytes` | Bộ nhớ hoàn toàn không sử dụng | Byte | Không thể chỉ dùng bộ nhớ này trong Linux để đánh giá áp lực bộ nhớ |
| Áp lực bộ nhớ | `node_pressure_memory_waiting_seconds_total` | Thời gian chờ bộ nhớ tích lũy PSI  | Giây/Giây | Tăng việc thu hồi bộ nhớ hoặc chờ cấp phát |
| OOM Số lần | `node_vmstat_oom_kill` | Số lượng hệ thống OOM giết | Số lần/Tăng thêm | Khi tăng, xác định các tiến trình bị giết và đỉnh bộ nhớ |

Các truy vấn phổ biến:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

Gợi ý điều tra: Không chỉ nên xem `MemFree` cho bộ nhớ. Khả năng sẵn có thực tế nên được đánh giá nhiều hơn bằng `MemAvailable`, kết hợp với bộ nhớ working set của container, hồ sơ tiến trình RSSvà OOM .

### 1.4 Dung lượng đĩa và Inode

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Tổng hệ thống tệp | `node_filesystem_size_bytes` | Tổng dung lượng của điểm mount | Byte | Được sử dụng để tính tỷ lệ sử dụng đĩa |
| Hệ thống tệp còn trống | `node_filesystem_avail_bytes` | Không gian có sẵn cho người dùng bình thường | Byte | Không đủ không gian có sẵn có thể gây ra lỗi ghi |
| Hệ thống tệp trống | `node_filesystem_free_bytes` | Không gian trống trong hệ thống tệp | Byte | Bao gồm không gian dự trữ của root; thường được xem xét cùng với `avail` |
| Trạng thái chỉ đọc | `node_filesystem_readonly` | Hệ thống tệp có ở chế độ chỉ đọc hay không | `0/1` | Khi `1`, việc ghi của doanh nghiệp sẽ thất bại |
| Tổng số inode | `node_filesystem_files` | Tổng số inode trong hệ thống tệp | Số lần | Cần chú ý đặc biệt trong các tình huống tệp nhỏ |
| Số inode còn lại | `node_filesystem_files_free` | Số lượng inode còn lại | Số lần/Tỷ lệ phần trăm | Khi inode cạn kiệt, không thể tạo file ngay cả khi vẫn còn dung lượng đĩa |

Các truy vấn phổ biến:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

Gợi ý điều tra: Cảnh báo dung lượng đĩa nên được kiểm tra theo điểm mount, đặc biệt đối với đĩa dữ liệu, đĩa log và thư mục runtime của container. Việc sử dụng inode cao thường đến từ số lượng lớn các tệp nhỏ, phân mảnh log hoặc các tệp tạm chưa được dọn dẹp.

### 1.5 Đĩa IOPS, Thông lượng và Độ trễ

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Đọc IOPS | `node_disk_reads_completed_total` | Số lượng yêu cầu đọc đĩa đã hoàn thành | lần/giây | Đọc IOPS gần giới hạn của thiết bị, độ trễ đọc tăng |
| Ghi IOPS | `node_disk_writes_completed_total` | Số lượng yêu cầu ghi đĩa đã hoàn thành | lần/giây | Dồn backlog ghi, log hoặc commit cơ sở dữ liệu chậm lại |
| Thông lượng đọc | `node_disk_read_bytes_total` | Tổng số byte đọc từ đĩa | Byte/giây | Thông lượng cao và iowait cao cho thấy lưu trữ đang bận |
| Thông lượng ghi | `node_disk_written_bytes_total` | Tổng số byte ghi vào đĩa | Byte/giây | Thông lượng ghi cao kéo dài có thể ảnh hưởng đến cơ sở dữ liệu và lưu trữ đối tượng |
| Thời gian đọc | `node_disk_read_time_seconds_total` | Tổng thời gian cho các yêu cầu đọc | giây/giây | Độ trễ đọc tăng |
| Thời gian ghi | `node_disk_write_time_seconds_total` | Tổng thời gian của các yêu cầu ghi | giây/giây | Tăng độ trễ ghi |
| IO bận | `node_disk_io_time_seconds_total` | Tổng thời gian đĩa xử lý IO | phần trăm | Khi gần tải đầy, ứng dụng phải chờ IO |
| Thời gian IO có trọng số | `node_disk_io_time_weighted_seconds_total` | Thời gian IO tính theo chiều dài hàng đợi | giây/giây | Sự tích tụ hàng đợi cho thấy thiết bị có hàng đợi nghiêm trọng |
| Áp lực IO | `node_pressure_io_waiting_seconds_total` | Tổng IO PSI  | giây/giây | Các tiến trình phải chờ IO lâu hơn |

Các truy vấn phổ biến:

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

Gợi ý điều tra: Không chỉ nhìn vào dung lượng đĩa khi kiểm tra sự cố. Ngay cả khi dung lượng bình thường, hiệu suất kinh doanh cũng có thể chậm lại khi IOPS, thông lượng, IO bận và iowait đồng thời tăng. Các dịch vụ IO nặng như cơ sở dữ liệu, Kafkavà MinIO nên tập trung vào độ trễ ghi và hàng đợi.

### 1.6 Chỉ số mạng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Dấu hiệu bất thường |
| --- | --- | --- | --- | --- |
| Lưu lượng đến | `node_network_receive_bytes_total` | Tổng số byte nhận bởi card mạng | Byte/giây | Tăng đột ngột lưu lượng đến, có thể do bùng nổ yêu cầu hoặc đồng bộ dữ liệu |
| Lưu lượng đi | `node_network_transmit_bytes_total` | Tổng số byte gửi bởi card mạng | Byte/giây | Tăng đột ngột lưu lượng đi, có thể do tải xuống, sao lưu hoặc sao chép |
| Lỗi nhận | `node_network_receive_errs_total` | Số tích lũy gói lỗi đã nhận | Số lượng/giây | Vấn đề về card mạng, liên kết hoặc trình điều khiển |
| Lỗi gửi đi | `node_network_transmit_errs_total` | Số tích lũy gói lỗi đã gửi | Số lượng/giây | Vấn đề liên kết hoặc sự cố hàng đợi của card mạng |
| Mất gói đến | `node_network_receive_drop_total` | Số tích lũy gói bị rớt khi nhận | Số lượng/giây | Hàng đợi kernel hoặc card mạng không thể theo kịp |
| Mất gói đi | `node_network_transmit_drop_total` | Giá trị tích lũy gói bị mất khi gửi | lần/giây | Tắc nghẽn đầu ra hoặc NIC áp lực hàng đợi |

Các truy vấn phổ biến:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

Gợi ý điều tra: Đối với các bất thường mạng, cần xem xét lưu lượng, gói lỗi và mất gói cùng nhau. Lưu lượng cao không nhất thiết báo hiệu lỗi; lưu lượng cao kèm theo gói lỗi hoặc mất gói có khả năng cao là vấn đề liên kết hoặc stack mạng của host.

### 1.7 TCP, File Handles, và áp lực hệ thống

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị / Đo lường phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Hiện tại TCP Kết nối | `node_netstat_Tcp_CurrEstab` | Số hiện tại của các kết nối đã thiết lập TCP kết nối | số lượng | Gia tăng đột ngột kết nối có thể chỉ ra đỉnh lưu lượng hoặc rò rỉ kết nối |
| TIME_WAIT | `node_sockstat_TCP_tw` | Số lượng TIME_WAIT kết nối | số lượng | Quá nhiều kết nối ngắn hạn có thể làm cạn cổng hoặc tăng áp lực kernel |
| TCP Đã phân bổ | `node_sockstat_TCP_alloc` | Số lượng socket đã phân bổ TCP socket | số lượng | Gia tăng liên tục số lượng socket cần điều tra việc giải phóng kết nối |
| TCP Đang sử dụng | `node_sockstat_TCP_inuse` | Số lượng TCP socket đang sử dụng | số lượng | Tăng áp lực kết nối |
| TCP Mồ côi | `node_sockstat_TCP_orphan` | Số lượng socket mồ côi | số lượng | Tăng bất thường có thể liên quan đến đóng kết nối bất thường |
| File Handles đang sử dụng | `node_filefd_allocated` | Số lượng file handle được hệ thống phân bổ | chiếc | Quá cao có thể ảnh hưởng đến kết nối mới và mở file |
| Giới hạn File Handle | `node_filefd_maximum` | Giới hạn file handle của hệ thống | chiếc | Dùng để tính tỷ lệ sử dụng handle |

Các truy vấn thường gặp: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

Khuyến nghị điều tra: File handle và TCP kết nối thường được xem xét cùng nhau. Khi số lượng kết nối máy chủ tăng vọt, nếu handle hệ thống gần giới hạn của chúng, ứng dụng có thể gặp lỗi chấp nhận kết nối, lỗi mở file, hoặc lỗi trong các kết nối phụ thuộc.

### 1.8 Giám sát tiến trình

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Tiến trình CPU | `process_cpu_seconds_total` | Tổng số CPU thời gian của tiến trình | giây/giây | Sử dụng cao trong thời gian dài CPU bởi một tiến trình đơn lẻ |
| Bộ nhớ vật lý | `process_resident_memory_bytes` | Tiến trình RSS bộ nhớ | Byte | Tăng liên tục RSS có thể báo hiệu rò rỉ bộ nhớ |
| Bộ nhớ ảo | `process_virtual_memory_bytes` | Bộ nhớ ảo của tiến trình | Byte | Tăng bất thường cần được đánh giá cùng với RSS |
| Handle mở | `process_open_fds` | Số lượng handle file mở của tiến trình | số lượng | Tăng liên tục có thể báo hiệu rò rỉ handle |
| Số lượng Handle tối đa | `process_max_fds` | Số lượng file handle tối đa mà tiến trình có thể mở | số lượng | Dùng để tính tỷ lệ sử dụng handle của tiến trình |
| Thời gian bắt đầu tiến trình | `process_start_time_seconds` | Thời gian bắt đầu tiến trình | Dấu thời gian Unix | Thay đổi thời gian bắt đầu báo hiệu tiến trình khởi động lại |

Khuyến nghị điều tra: Các chỉ số tiến trình được dùng để xác định dịch vụ cụ thể cho các vấn đề ở mức node. Khi node CPU cao, kiểm tra tiến trình CPU; khi áp lực bộ nhớ node cao, kiểm tra RSS; khi handle của node cao, kiểm tra `process_open_fds`. 

## 2. Giám sát containerd

Giám sát container chủ yếu đến từ kubelet/cAdvisor, phản ánh việc sử dụng tài nguyên của các container được quản lý bởi containerd. Tài liệu tiếp tục sử dụng `container_*` cách đặt tên từ các chỉ số Prometheus, nhưng môi trường chạy container thực tế là containerd. 

### 2.1 Container CPU

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| CPU Sử dụng | `container_cpu_usage_seconds_total` | Tổng số CPU thời gian sử dụng của container | lõi/giây | Tỷ lệ sử dụng gần giới hạn trong thời gian dài, có thể làm tăng độ trễ kinh doanh |
| CPU Thời gian bị hạn chế | `container_cpu_cfs_throttled_seconds_total` | Tổng thời gian CPU bị hạn chế bởi CFS | giây/giây | Đáng kể CPU hạn chế đáng kể cho thấy giới hạn quá chặt hoặc tải quá cao |
| CPU Hạn ngạch | `container_spec_cpu_quota` | Bộ chứa CPU hạn mức | giá trị hạn mức | Được dùng để xác định xem một CPU giới hạn có được đặt hay không |

Các truy vấn thường gặp: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

Đề xuất điều tra: Bộ chứa cao CPU không nhất thiết phải mở rộng. Trước tiên, kiểm tra xem nó có đang bị hạn chế không, sau đó kiểm tra xem yêu cầu/giới hạn của Pod có quá thấp không, và cuối cùng, xem xét độ trễ yêu cầu dịch vụ để xác định xem nó có thực sự ảnh hưởng đến kinh doanh hay không.

### 2.2 Bộ nhớ của container

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| RSS Bộ nhớ | `container_memory_rss` | Trang ẩn danh của container và RSS bộ nhớ | Byte | Sự tăng trưởng liên tục gần với áp lực bộ nhớ thực của tiến trình |
| Bộ nhớ đã sử dụng | `container_memory_usage_bytes` | Tổng sử dụng bộ nhớ container | Byte | Bao gồm bộ nhớ đệm, không thể xác định rò rỉ riêng |
| Bộ nhớ Working Set | `container_memory_working_set_bytes` | Bộ nhớ working set đang hoạt động của container | Byte | Tiếp cận giới hạn có thể gây OOMKilled |
| Giới hạn Bộ nhớ | `container_spec_memory_limit_bytes` | Giới hạn bộ nhớ của container | Byte | Được dùng để tính tỷ lệ sử dụng bộ nhớ |

Các truy vấn phổ biến:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

Đề xuất điều tra: Đối với rủi ro bộ nhớ trong container kinh doanh, ưu tiên xem xét working set và RSS. `usage_bytes` bị ảnh hưởng nhiều bởi page cache, phù hợp để quan sát dung lượng, nhưng không phù hợp làm cơ sở duy nhất cho OOM đánh giá.

### 2.3 Đĩa và lưu trữ tạm thời của container

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Thông lượng đọc | `container_fs_reads_bytes_total` | Tổng số byte được container đọc từ đĩa | Byte/giây | Đột biến lượng đọc có thể chỉ ra quét, nhập khẩu hoặc kéo từ nguồn cache |
| Thông lượng ghi | `container_fs_writes_bytes_total` | Tổng số byte được container ghi vào đĩa | Byte/giây | Đỉnh ghi có thể gây áp lực IO lên node |
| Đọc IOPS | `container_fs_reads_total` | Số lượng yêu cầu đọc của container | Lỗi/s | Tần suất đọc khối nhỏ cao có thể làm tăng thời gian chờ IO |
| Ghi IOPS | `container_fs_writes_total` | Số lượng yêu cầu ghi của container | Lỗi/s | Ghi quá nhiều nhật ký hoặc tệp tạm thời |
| Sử dụng hệ thống tệp | `container_fs_usage_bytes` | Sử dụng hệ thống tệp của container | Byte | Tích tụ các tệp tạm thời hoặc nhật ký |
| Giới hạn hệ thống tệp | `container_fs_limit_bytes` | Giới hạn hệ thống tệp của container | Byte | Ghi có thể thất bại khi gần đạt giới hạn |

Câu hỏi thường gặp: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

Gợi ý điều tra: Khi ghi đĩa của container bất thường, trước tiên hãy kiểm tra volume nhật ký Pod, thư mục tệp tạm thời và các tác vụ theo lô. Khi IO đĩa của node cao, các chỉ số FS của container có thể được sử dụng để xác định Pod nào đang ghi.

### 2.4 Mạng Container

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Lưu lượng vào | `container_network_receive_bytes_total` | Tổng số byte container nhận được | Byte/giây | Tăng đột ngột của lưu lượng yêu cầu hoặc lưu lượng sao chép |
| Lưu lượng ra | `container_network_transmit_bytes_total` | Tổng số byte container gửi đi | Byte/giây | Tăng lưu lượng tải xuống, đồng bộ, lấy từ nguồn gốc hoặc xuất |
| Mất gói tin đến | `container_network_receive_packets_dropped_total` | Tổng số gói tin bị loại khi nhận bởi container | lần/giây | Mất gói tin do ngăn xếp mạng hoặc áp lực nút |
| Mất gói tin đi | `container_network_transmit_packets_dropped_total` | Tổng số gói tin bị loại khi truyền bởi container | lần/giây | Tắc nghẽn xuất, NIC hàng đợi, hoặc CNI vấn đề |

Các truy vấn phổ biến:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

Gợi ý điều tra: Mạng lưới container nên được phân tích kết hợp với node NIC số liệu. Nếu mất gói tăng ở cấp Pod nhưng không có bất thường ở node, tiếp tục kiểm tra CNI, iptables và tải trên node nơi Pod đang cư trú. 

### 2.5 Luồng Container và Vòng đời

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Số lượng Luồng | `container_threads` | Số lượng luồng bên trong container | số lượng | Sự tăng liên tục của các luồng có thể cho thấy rò rỉ luồng |
| Lần cuối cùng thấy | `container_last_seen` | Lần cuối cùng cAdvisor nhìn thấy container | Dấu thời gian Unix | Không cập nhật trong thời gian dài có thể cho thấy container đã thoát hoặc có bất thường trong việc thu thập |
| Số lần khởi động lại | `kube_pod_container_status_restarts_total` | Tổng số lần container khởi động lại | số/lần tăng | Khởi động lại thường xuyên cho thấy sụp đổ, probe thất bại, hoặc OOM |
| Lý do đang chờ đợi | `kube_pod_container_status_waiting_reason` | Lý do container đang ở trạng thái chờ | giá trị nhãn | `CrashLoopBackOff`, `ImagePullBackOff`, vv, cần được xử lý |
| Trạng thái chạy | `kube_pod_container_status_running` | Container có đang chạy hay không | `0/1` | Container chính không `1` cho thấy dịch vụ không khả dụng |

Khuyến nghị điều tra: Đối với bất thường của container, đầu tiên kiểm tra lý do trạng thái, sau đó xem số lần khởi động lại và thời gian khởi động lại gần nhất. Nếu khởi động lại thường xuyên, tiếp tục điều tra bằng nhật ký ứng dụng, OOM sự kiện và cấu hình probe. 

## 3. Kubernetes Giám sát cụm

Kubernetes Giám sát được sử dụng để đánh giá sử dụng tài nguyên của cụm, sức khỏe của plane điều khiển, trạng thái bản sao workload và trạng thái đối tượng lưu trữ. Các chỉ số chính đến từ kube-state-metrics, kubelet, và APIServer. 

### 3.1 Dung lượng Node và tài nguyên có thể lập lịch

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Dung lượng Node | `kube_node_status_capacity` | Tổng dung lượng của node | CPU, bộ nhớ, số lượng Pod, vv | Được sử dụng cho kế hoạch dung lượng |
| Tài nguyên có thể lập lịch | `kube_node_status_allocatable` | Tài nguyên có thể lên lịch của node | CPU, bộ nhớ, số lượng Pod, vv | Tài nguyên có thể lập lịch không đủ sẽ khiến Pod ở trạng thái Pending |
| Điều kiện Node | `kube_node_status_condition` | Node Ready, MemoryPressure và các trạng thái khác | `0/1` | Trạng thái Ready bất thường hoặc xuất hiện Pressure cần chú ý ngay lập tức |
| Cấm lập lịch | `kube_node_spec_unschedulable` | Node có bị Cordon không | `0/1` | Khi đặt '1', node sẽ không lập lịch Pod mới |
| Thông tin nút | `kube_node_info` | Phiên bản Node, kernel, thông tin runtime container | Thông tin tag | Dùng để khắc phục sự khác biệt phiên bản |

Gợi ý khắc phục sự cố: Khi pod ở trạng thái pending, đầu tiên kiểm tra allocatable và requests, sau đó kiểm tra xem node có phải 'unschedulable' hay không, và cuối cùng kiểm tra điều kiện node có đang gặp áp lực tài nguyên. 

### 3.2 Trạng thái Pod 

| Các chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị/Phạm vi phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Thông tin Pod | `kube_pod_info` | Thông tin namespace, node của Pod, v.v. | Thông tin tag | Dùng để xác định phân bố Pod |
| Giai đoạn Pod | `kube_pod_status_phase` | Trạng thái Pending, Running, Succeeded, Failed, v.v. | `0/1` | Tăng Pending/Failed cho thấy bất thường trong lập lịch hoặc chạy |
| Pod Ready | `kube_pod_status_ready` | Pod đã sẵn sàng chưa | `0/1` | Không sẵn sàng ảnh hưởng đến khả năng phục vụ |
| Lý do Pod | `kube_pod_status_reason` | Lý do bất thường của Pod | Giá trị nhãn | Evicted, NodeLost, v.v. cần được điều tra |
| Container Khởi động lại | `kube_pod_container_status_restarts_total` | Số lần container khởi động lại | lần/tăng | Tăng khởi động lại cho thấy vấn đề về ổn định |
| Container Chờ | `kube_pod_container_status_waiting` | Container có đang ở trạng thái chờ không | `0/1` | Nếu trạng thái chờ kéo dài, Pod không thể cung cấp dịch vụ bình thường |
| Lý do đang chờ đợi | `kube_pod_container_status_waiting_reason` | Lý do trạng thái chờ | Giá trị nhãn | Không kéo được image, CrashLoop, v.v. |
| Container Kết thúc | `kube_pod_container_status_terminated` | Container có bị kết thúc không | `0/1` | Kết thúc bất ngờ nên được kiểm tra cùng với khởi động lại và log |

Các truy vấn phổ biến:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

Gợi ý điều tra: Không chỉ nhìn vào pha pod khi có bất thường. Trạng thái Ready, lý do và lý do chờ của container minh họa rõ ràng hơn vấn đề cụ thể.

### 3.3 Yêu cầu và Giới hạn Tài nguyên

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Tài nguyên yêu cầu | `kube_pod_container_resource_requests` | Yêu cầu container | CPU, Bộ nhớ | Các yêu cầu quá cao ảnh hưởng đến lịch trình, quá thấp ảnh hưởng đến tính ổn định |
| Giới hạn Tài nguyên | `kube_pod_container_resource_limits` | Giới hạn Container | CPU, Bộ nhớ | Giới hạn quá thấp có thể gây ra CPU giới hạn hoặc OOM |
| Node Allocatable | `kube_node_status_allocatable` | Tài nguyên có sẵn để lập lịch trên một node | CPU, Bộ nhớ | Được sử dụng để tính tỷ lệ phân bổ tài nguyên cụm |
| Sử dụng Container | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | Thực tế CPU và sử dụng bộ nhớ | Lõi/giây, Byte | Được sử dụng để xác định liệu các yêu cầu/giới hạn có hợp lý không |

Các truy vấn phổ biến:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

Gợi ý điều tra: Kế hoạch tài nguyên nên xem xét cả 'giá trị yêu cầu' và 'giá trị sử dụng thực tế.' Chỉ nhìn vào yêu cầu có thể đánh giá sai áp lực kinh doanh, trong khi chỉ nhìn vào sử dụng có thể bỏ qua năng lực lập lịch.

### 3.4 Bản sao Khối lượng công việc

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Bản sao Deployment | `kube_deployment_status_replicas` | Số bản sao Deployment hiện tại | đơn vị | Không nhất quán với số bản sao dự kiến |
| Bản sao Đã Cập nhật | `kube_deployment_status_replicas_updated` | Số lượng bản sao đã được cập nhật lên phiên bản mới | đơn vị | Không tăng trưởng trong thời gian dài trong quá trình phát hành |
| Bản sao Không khả dụng | `kube_deployment_status_replicas_unavailable` | Số bản sao không khả dụng | đơn vị | Dung lượng dịch vụ giảm khi lớn hơn 0 |
| Bản sao StatefulSet | `kube_statefulset_status_replicas` | Số lượng bản sao StatefulSet hiện tại | đơn vị | Bản sao bất thường trong dịch vụ stateful |
| StatefulSet Sẵn sàng | `kube_statefulset_status_replicas_ready` | Số lượng bản sao StatefulSet Sẵn sàng | đơn vị | Nếu Ready ít hơn số bản sao dự kiến, dịch vụ chưa hoàn chỉnh |

Khuyến nghị điều tra: Khi có sự cố phát hành, kiểm tra `updated` và `unavailable`. Đối với sự cố StatefulSet, chú ý đến PVC, thứ tự khởi động Pod và sự hợp node.

### 3.5 Công việc và Nhiệm vụ Theo Lô

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Công việc Đang chạy | `kube_job_status_active` | Số lượng công việc đang hoạt động hiện tại | Số lần | Hoạt động lâu dài có thể cho thấy công việc bị kẹt |
| Công việc Thất bại | `kube_job_status_failed` | Số lượng công việc thất bại | Số lần | Số lượng thất bại tăng yêu cầu kiểm tra nhật ký công việc |
| Công việc thành công | `kube_job_status_succeeded` | Số lượng công việc đã hoàn thành thành công | Số lần | Được sử dụng để xác định hoàn thành nhiệm vụ |
| Thời gian hoàn thành | `kube_job_status_completion_time` | Thời gian hoàn thành công việc | Dấu thời gian Unix | Thiếu thời gian hoàn thành có thể cho thấy công việc chưa hoàn tất |

Gợi ý điều tra: Khi các nhiệm vụ theo lô có bất thường, kiểm tra `active`, `failed`và `succeeded` cùng nhau. Chỉ xem các thất bại có thể bỏ sót các nhiệm vụ bị kẹt lâu.

### 3.6 PVC và các đối tượng lưu trữ

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| PVC Trạng thái | `kube_persistentvolumeclaim_status_phase` | PVC Đã liên kết, Đang chờ và các trạng thái khác | `0/1` | Đang chờ sẽ khiến Pod không thể gắn lưu trữ |
| PVC Dung lượng yêu cầu | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | Dung lượng lưu trữ được yêu cầu bởi PVC | Byte | Được sử dụng để lập kế hoạch dung lượng và quản lý hạn mức |

Gợi ý khắc phục sự cố: Khi dịch vụ trạng thái không thể khởi động, ngoài việc kiểm tra Pod, bạn cũng nên kiểm tra xem PVC có được liên kết, lớp lưu trữ có sẵn và liệu lưu trữ cơ sở có đủ dung lượng hay không.

### 3.7 APIServer, etcd và Control Plane

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Số lượng yêu cầu APIServer | `apiserver_request_total` | Tổng số yêu cầu APIServer cộng dồn | yêu cầu/giây | Sự tăng đột ngột các yêu cầu có thể đến từ các bộ điều khiển, kubectlhoặc các thành phần nghiệp vụ |
| Độ trễ APIServer | `apiserver_request_duration_seconds_bucket` | Các khoảng thời gian xử lý yêu cầu APIServer | P50/P95/P99 | Độ trễ tăng sẽ ảnh hưởng đến lập lịch, triển khai và đồng bộ hóa bộ điều khiển |
| Độ trễ etcd | `etcd_request_duration_seconds_bucket` | Các khoảng thời gian xử lý yêu cầu etcd | P50/P95/P99 | etcd chậm có thể làm chậm toàn bộ control plane |
| Thời gian chờ hàng đợi | `workqueue_queue_duration_seconds_bucket` | Thời gian chờ trong hàng đợi của bộ điều khiển | Thời gian phần trăm | Hàng đợi tồn đọng, đồng bộ trạng thái tài nguyên bị chậm |
| Xử lý hàng đợi | `workqueue_work_duration_seconds_bucket` | Thời gian xử lý của bộ điều khiển | Thời gian phần trăm | Bộ điều khiển xử lý chậm lại |

Các truy vấn phổ biến:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

Khuyến nghị điều tra: Các vấn đề của plane điều khiển thường biểu hiện dưới dạng triển khai chậm, cập nhật trạng thái Pod chậm và phản hồi chậm. kubectl Khi độ trễ APIServer và độ trễ etcd cùng tăng, ưu tiên kiểm tra etcd, IO đĩa và tải nút plane điều khiển.

## 4. MySQL Giám sát

MySQL Giám sát được sử dụng để quan sát khả dụng của instance, áp lực kết nối, SQL lượng yêu cầu, truy vấn chậm, cache hit, bảng tạm, chờ khóa, file handle và băng thông mạng.

### 4.1 Trạng thái Instance và Lượng Yêu cầu

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Instance Hoạt động | `up` | Mysql exporter có thể thu thập được hay không | `0/1` | Khi `0`, instance, mạng, hoặc exporter có bất thường |
| Thời gian hoạt động | `mysql_global_status_uptime` | MySQL Thời gian chạy | giây | Giảm cho thấy instance khởi động lại |
| Tổng số Truy vấn | `mysql_global_status_queries` | Số truy vấn tích lũy | lần/giây | QPS Tăng đột biến có thể chỉ ra đỉnh doanh nghiệp hoặc yêu cầu bất thường |
| Câu hỏi | `mysql_global_status_questions` | Số lệnh tích lũy do client khởi tạo | lần/giây | Cần xem cùng với số truy vấn để đánh giá áp lực yêu cầu |
| Thống kê Lệnh | `mysql_global_status_commands_total` | Số lượng tích lũy của các lệnh khác nhau | lần/giây | Có thể phân biệt các lệnh như select, insert, update, delete |

Câu hỏi thường gặp: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

Gợi ý điều tra: Khi QPS tăng, đầu tiên kiểm tra phân bố lệnh. Nếu `select` tăng cùng với các chỉ số kiểu quét, chú ý đến chỉ mục và truy vấn chậm; nếu lệnh ghi tăng, tiếp tục giám sát chờ khóa, IO đĩa, và độ trễ ghi của host.

### 4.2 Kết nối và Luồng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Kết nối Hiện tại | `mysql_global_status_threads_connected` | Số lượng luồng đang kết nối hiện tại | số lượng | Tiếp cận giới hạn có thể khiến kết nối mới thất bại |
| Luồng Hoạt động | `mysql_global_status_threads_running` | Số luồng đang thực thi hiện tại | số lượng | Tăng liên tục thường chỉ ra việc thực thi chậm hoặc đang chờ khóa SQL Thực hiện chậm hoặc chờ khóa |
| Số kết nối tối đa lịch sử | `mysql_global_status_max_used_connections` | Số lượng kết nối tối đa đã sử dụng trong lịch sử | số lượng | Đang gần đạt tối đa_kết nối cho thấy cần đánh giá lại kết nối pool |
| Số kết nối tối đa | `mysql_global_variables_max_connections` | MySQL cấu hình kết nối tối đa | số lượng | Được sử dụng để tính tỷ lệ sử dụng kết nối |
| Khách hàng bất thường | `mysql_global_status_aborted_clients` | Số lượng kết nối bất thường của khách hàng tích lũy | lần/giây | Vấn đề mạng, hết thời gian chờ hoặc lỗi phía khách hàng |
| Kết nối thất bại | `mysql_global_status_aborted_connects` | Tổng số lần kết nối thất bại | lần/giây | Lỗi xác thực, giới hạn kết nối, bất thường mạng, v.v. |

Các truy vấn phổ biến:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

Gợi ý điều tra: Số lượng kết nối cao không nhất thiết nghĩa là cơ sở dữ liệu chậm; cũng có thể do pool kết nối của ứng dụng được cấu hình không đúng. `Threads_running` việc duy trì ở mức cao trong thời gian dài là điều đáng quan ngại hơn, vì nó thường tương ứng với SQL các vấn đề về thực thi hoặc chờ khóa.

### 4.3 Truy vấn chậm, Quét và Sắp xếp

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Truy vấn chậm | `mysql_global_status_slow_queries` | Tổng số truy vấn chậm tích lũy | lần/giây | Sự tăng lên cho thấy nhiều truy vấn chậm hơn SQL |
| Quét Join đầy đủ | `mysql_global_status_select_full_join` | Số lượng join không có chỉ mục | lần/giây | Cho thấy có thể thiếu chỉ mục điều kiện join |
| Quét bảng đầy đủ | `mysql_global_status_select_scan` | Số lần quét bảng đầy đủ | lần/giây | Quét đầy đủ trên các bảng lớn có thể làm chậm instance |
| Sắp xếp kết hợp | `mysql_global_status_sort_merge_passes` | Số lần sắp xếp yêu cầu nhiều lần kết hợp | lần/giây | Bộ đệm sắp xếp không đủ hoặc quá nhiều dữ liệu cần sắp xếp |

Gợi ý điều tra: Khi số lượng truy vấn chậm tăng, kiểm tra đối chiếu với thời gian phát hành kinh doanh và SQL ghi nhật ký thay đổi. Nếu các chỉ số quét và sắp xếp tăng, thường tham khảo lại các bản ghi chậm, kế hoạch thực thi và thiết kế chỉ mục.

### 4.4 Bộ đệm InnoDB

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Kích thước bộ đệm | `mysql_global_variables_innodb_buffer_pool_size` | Cấu hình kích thước bộ đệm InnoDB | Byte | Quá nhỏ sẽ làm tăng số lần đọc đĩa |
| Trang bộ đệm | `mysql_global_status_buffer_pool_pages` | Số lượng các loại trang khác nhau trong bộ đệm | Trang | Dùng để giám sát các trang bẩn, trang trống, dữ liệu và các trang khác |
| Kích thước trang | `mysql_global_status_innodb_page_size` | Kích thước trang InnoDB | Byte | Dùng để chuyển đổi số trang thành dung lượng |

Gợi ý điều tra: Khi tỷ lệ truy cập bộ đệm kém, cơ sở dữ liệu sẽ truy cập nhiều hơn vào đĩa. Cần đánh giá cùng với thông lượng đọc đĩa và iowait của nút. IOPS

### 4.5 Bảng tạm thời, Bộ nhớ đệm bảng và Tay cầm tập tin

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Bảng tạm thời | `mysql_global_status_created_tmp_tables` | Tổng số bảng tạm thời được tạo | lần/giây | Tăng độ phức tạp truy vấn |
| Bảng tạm thời đĩa | `mysql_global_status_created_tmp_disk_tables` | Tổng số bảng tạm thời đĩa được tạo | lần/giây | Tăng áp lực IO đĩa, SQL có thể làm chậm |
| Tập tin tạm thời | `mysql_global_status_created_tmp_files` | Tổng số tập tin tạm thời được tạo | lần/giây | Tăng số tập tin tạm thời |
| Khóa bảng ngay lập tức | `mysql_global_status_table_locks_immediate` | Số lần khóa bảng được lấy ngay lập tức | lần/giây | Chỉ số tham chiếu bình thường |
| Khóa bảng chờ | `mysql_global_status_table_locks_waited` | Số lần khóa bảng bị chờ | lần/giây | Tăng cạnh tranh khóa |
| Bộ nhớ đệm bảng trúng | `mysql_global_status_table_open_cache_hits` | Số lần trúng bộ nhớ đệm mở bảng | lần/giây | Trúng thấp có thể chỉ ra việc mở bảng thường xuyên |
| Bộ nhớ đệm bảng trượt | `mysql_global_status_table_open_cache_misses` | Số lần trượt bộ nhớ đệm mở bảng | lần/giây | Cần đánh giá bộ nhớ đệm bảng |
| Bộ nhớ đệm bảng tràn | `mysql_global_status_table_open_cache_overflows` | Số lần tràn bộ nhớ đệm mở bảng | lần/giây | Cấu hình không đủ hoặc quá nhiều bảng |
| Bảng mở | `mysql_global_status_open_tables` | Số lượng bảng mở hiện tại | chiếc | Nguy cơ tăng khi gần chạm giới hạn bộ đệm |
| Cấu Hình Bộ Nhớ Đệm Bảng | `mysql_global_variables_table_open_cache` | bảng_mở_giá trị bộ nhớ đệm được cấu hình | chiếc | Dùng để tính tỷ lệ sử dụng |
| Tệp Mở | `mysql_global_status_open_files` | Số lượng tệp đang mở hiện tại | chiếc | Có thể ảnh hưởng SQL thực thi khi gần đạt giới hạn tệp |
| Giới Hạn Tệp | `mysql_global_variables_open_files_limit` | MySQL giới hạn handle tệp | chiếc | Được dùng để tính tỷ lệ sử dụng handle tệp |

Gợi Ý Khắc Phục Sự Cố: Bảng tạm thời, chờ khóa và bộ nhớ đệm bảng thường xuất hiện cùng với các truy vấn chậm. Khi các bảng tạm đĩa tăng, hãy chú ý đến IO viết nút, độ trễ đĩa, và SQL sắp xếp/nhóm.

### 4.6 Thông Lượng Mạng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Lưu lượng vào | `mysql_global_status_bytes_received` | Thời gian tích lũy MySQL byte nhận được | Byte/giây | Tăng trong phần thân yêu cầu hoặc lưu lượng ghi |
| Lưu lượng ra | `mysql_global_status_bytes_sent` | Tích lũy byte đã gửi bởi MySQL | Byte/giây | Các truy vấn lớn, quét toàn bộ bảng và xuất dữ liệu hàng loạt sẽ làm tăng lưu lượng ra ngoài |

Các truy vấn phổ biến:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

Gợi ý điều tra: Khi MySQL lưu lượng ra ngoài đột ngột tăng, thường cần chú ý đến các bộ kết quả lớn, tác vụ xuất dữ liệu và truy vấn không phân trang.

## 5. MongoDB Giám sát

MongoDB Giám sát được sử dụng để quan sát trạng thái phiên bản, số lượng kết nối, khối lượng thao tác, quét truy vấn, sử dụng bộ nhớ, thông lượng mạng và điều kiện bộ đệm sao chép.

### 5.1 Phiên bản và Kết nối

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Instance Hoạt động | `up` | Mongo exporter có thể thu thập dữ liệu hay không | `0/1` | Nếu `0`, phiên bản hoặc exporter bất thường |
| Thời gian hoạt động | `mongodb_ss_uptime` | MongoDB Thời gian chạy | giây | Giá trị nhỏ hơn cho thấy phiên bản khởi động lại |
| Số Lượng Kết Nối | `mongodb_ss_connections` | Thống kê liên quan đến kết nối hiện tại | số lượng | Số lượng kết nối bất thường cao có thể cho thấy pool kết nối hoặc đỉnh cao của hoạt động kinh doanh |

Gợi Ý Điều Tra: Khi số lượng kết nối tăng, trước tiên xác nhận xem có đỉnh cao hoạt động kinh doanh, thay đổi cấu hình pool kết nối, hoặc kết nối lại không bình thường từ client không.

### 5.2 Thao Tác và Xử Lý Tài Liệu

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Số Lượng Thao Tác | `mongodb_ss_opcounters` | Tổng số thao tác tích lũy như chèn, truy vấn, cập nhật, xóa | lần/giây | Sự gia tăng đột ngột trong một loại thao tác nhất định cho thấy sự thay đổi trong các mẫu truy cập kinh doanh |
| Xử lý Tài liệu | `mongodb_ss_metrics_document` | Tổng số tài liệu được chèn, cập nhật, xóa, trả về, v.v. | lần/giây | Nếu số lượng trả về cao hơn nhiều so với thực tế cần thiết, tập kết quả có thể quá lớn |
| Mục Chỉ mục Được Quét | `mongodb_ss_metrics_queryExecutor_scanned` | Số mục chỉ mục được quét trong quá trình truy vấn | lần/giây | Quét quá nhiều có thể cho thấy lập chỉ mục không đúng |
| Tài liệu Được Quét | `mongodb_ss_metrics_queryExecutor_scannedObjects` | Số tài liệu được quét trong quá trình truy vấn | lần/giây | Quét tài liệu cao cho thấy hiệu quả truy vấn thấp |

Các truy vấn thường gặp: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

Khuyến nghị điều tra: Một biểu hiện phổ biến của MongoDB truy vấn chậm là sự gia tăng các mục đã quét / scannedObjects. Cần phân tích kết hợp với nhật ký chậm và lượt trúng chỉ mục.

### 5.3 Bộ nhớ, Mạng và Ổ đĩa

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị/Đo lường phổ biến | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Bộ nhớ Thường trú | `mongodb_ss_mem_resident` | MongoDB bộ nhớ thường trú | MB hoặc Byte | Tăng liên tục yêu cầu kiểm tra bộ nhớ máy chủ |
| Bộ nhớ ảo | `mongodb_ss_mem_virtual` | MongoDB bộ nhớ ảo | MB hoặc Byte | Chỉ sự gia tăng không nhất thiết chỉ ra áp lực thực |
| Lưu lượng đến | `mongodb_ss_network_bytesIn` | MongoDB tổng số byte nhận được | Byte/giây | Tăng lưu lượng viết hoặc yêu cầu |
| Lưu lượng đi | `mongodb_ss_network_bytesOut` | MongoDB tổng số byte gửi đi | Byte/giây | Truy vấn lớn hoặc tác vụ xuất dữ liệu gây tăng lưu lượng ra |
| IO Đọc Máy chủ | `node_disk_reads_completed_total` | Đọc IOPS trên nút nơi MongoDB cư trú | lần/giây | Truy vấn quét gây tăng IO đọc |
| IO Ghi Máy chủ | `node_disk_writes_completed_total` | Ghi IOPS trên nút nơi MongoDB được đặt | lần/giây | Tăng áp lực ghi hoặc lưu nhật ký | 

Gợi ý khắc phục sự cố: MongoDB hiệu suất bộ nhớ và ổ đĩa nên được xem xét cùng với bộ nhớ và IO ổ đĩa của nút. Việc xem các chỉ số phiên bản cùng với đọc/ghi ổ đĩa của máy chủ giúp dễ xác định liệu MongoDB nó chậm hay các tài nguyên cơ sở chậm. 

### 5.4 Bộ Đệm Sao Chép 

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường | 
| --- | --- | --- | --- | 
| Kích Thước Bộ Đệm Sao Chép | `mongodb_ss_metrics_repl_buffer_sizeBytes` | Kích thước của bộ đệm sao chép | Byte | Tăng trưởng bộ đệm liên tục cho thấy việc tiêu thụ sao chép không kịp thời | 

Gợi ý xử lý sự cố: Bộ đệm sao chép bất thường thường liên quan đến khả năng xử lý của slave, mạng, hoặc ghi đĩa. Cần phân tích cùng với độ trễ sao chép, mạng của nút, và các chỉ số ghi đĩa. 

## 6. Redis Giám sát 

Redis Giám sát được sử dụng để quan sát độ khả dụng của instance, số lượng kết nối, xử lý lệnh, mức độ bộ nhớ, keyspace, tỷ lệ trúng, loại bỏ, và băng thông mạng. 

### 6.1 Instance và Khách Hàng 

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường | 
| --- | --- | --- | --- | --- |
| Instance Hoạt động | `up` | Có Redis Exporter có thể được thu thập | `0/1` | Khi `0`, phiên bản hoặc exporter bất thường |
| Thời gian hoạt động | `redis_uptime_in_seconds` | Redis Thời gian chạy | giây | Giảm cho thấy instance khởi động lại |
| Kết Nối Khách Hàng | `redis_connected_clients` | Số lượng kết nối khách hàng hiện tại | số lượng | Tăng đột ngột có thể cho thấy sự tăng cường kết nối hoặc cơn sốt kết nối lại |

### 6.2 Lệnh, Bộ Nhớ, và Keyspace

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Lệnh Đã Xử Lý | `redis_commands_processed_total` | Tổng số lệnh Redis đã được xử lý | lần/giây | Đột ngột QPS tăng có thể làm instance tăng CPU |
| Phân Loại Lệnh | `redis_commands_total` | Tổng số lệnh theo loại | lần/giây | Có thể xác định sự thay đổi trong các lệnh get, set, del, v.v. |
| Bộ Nhớ Sử Dụng | `redis_memory_used_bytes` | Hiện tại Redis Mức sử dụng bộ nhớ | Byte | Tiếp cận maxmemory có thể kích hoạt loại bỏ |
| Bộ Nhớ Tối Đa | `redis_memory_max_bytes` | Redis Cấu hình maxmemory | Byte | Được dùng để tính tỷ lệ sử dụng bộ nhớ |
| Số Lượng Khóa | `redis_db_keys` | Số lượng khóa trong mỗi DB | số lượng | Tăng trưởng bất thường của khóa có thể cho thấy cache không có thời hạn hoặc ghi bất thường |
| Khóa Hết Hạn | `redis_db_keys_expiring` | Số lượng khóa được thiết lập thời hạn | số lượng | Tỷ lệ thấp cần chú ý đến vòng đời cache |

Các truy vấn phổ biến:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 Tỷ Lệ Trúng, Loại Bỏ, và Mạng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Số Lần Trúng | `redis_keyspace_hits_total` | Tổng số lần trúng khóa | lần/giây | Tính tỷ lệ trúng cùng với thất bại |
| Số Lần Thất Bại | `redis_keyspace_misses_total` | Tổng số lượt bỏ lỡ khóa | lần/giây | Tăng lượt bỏ lỡ có thể dẫn đến áp lực cao hơn về nguồn gốc |
| Khóa hết hạn | `redis_expired_keys_total` | Tổng số khóa hết hạn | lần/giây | Bão hết hạn có thể gây ra CPU độ trễ |
| Khóa bị trục xuất | `redis_evicted_keys_total` | Tổng số khóa bị trục xuất | lần/giây | Sự tăng trưởng cho thấy áp lực bộ nhớ hoặc maxmemory không đủ |
| Lưu lượng vào | `redis_net_input_bytes_total` | Tổng số byte nhận được bởi Redis | Byte/giây | Tăng lưu lượng viết hoặc yêu cầu |
| Lưu lượng ra | `redis_net_output_bytes_total` | Tổng số byte gửi đi bởi Redis | Byte/giây | Lưu lượng ra cao do giá trị lớn hoặc đọc theo lô |

Các truy vấn phổ biến:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

Khuyến nghị điều tra: Đối với Redis, tập trung vào rủi ro bộ nhớ và trục xuất. Sự giảm tỷ lệ trúng sẽ chuyển áp lực sang cơ sở dữ liệu backend. Sự tăng trục xuất cho thấy cần đánh giá khả năng chứa của bộ nhớ đệm hoặc chiến lược trục xuất.

## 7. Kafka Giám sát

Kafka Giám sát được sử dụng để quan sát số lượng Brokers, trạng thái Topic/Partition, các chỉ số sản xuất và tiêu thụ, Consumer Group Lag, số lượng thành viên và trạng thái đồng bộ replica.

### 7.1 Broker, Topic và Partition

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Số lượng Brokers | `kafka_brokers` | Số lượng Brokers hiện đang hiển thị | chiếc | Sự giảm số lượng cho biết Broker không khả dụng hoặc exporter không thể truy cập |
| Partitions của Topic | `kafka_topic_partitions` | Số lượng Partition của một Topic | chiếc | Thay đổi partition ảnh hưởng đến khả năng đồng thời và năng lực tiêu thụ |
| Partition Offset hiện tại | `kafka_topic_partition_current_offset` | Offset mới nhất của Partition | Offset / tốc độ tăng trưởng | Nên tăng liên tục trong quá trình ghi sản xuất đang diễn ra |
| Partition Oldest Offset | `kafka_topic_partition_oldest_offset` | Partition Oldest Offset | Offset | Được sử dụng để quan sát phạm vi dữ liệu được giữ lại |

Câu hỏi thường gặp: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

Gợi ý điều tra: Khi tỷ lệ sản xuất bất thường, trước tiên hãy kiểm tra sự tăng trưởng offset hiện tại của topic. Nếu bộ phận kinh doanh xác nhận có các bản ghi nhưng offset không tăng, kiểm tra lỗi phía producer, trạng thái Broker và cấu hình Topic.

### 7.2 Nhóm Consumer và Độ trễ

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Offset tiêu thụ | `kafka_consumergroup_current_offset` | Offset hiện tại được nhóm Consumer tiêu thụ | Offset / tốc độ tăng trưởng | Không có sự tăng trưởng cho thấy tiêu thụ đã dừng hoặc bị kẹt |
| Độ trễ Partition | `kafka_consumergroup_lag` | Lượng tồn đọng của nhóm Consumer trên partition | số lượng | Độ trễ tăng cho thấy tiêu thụ đang bị tụt hậu so với sản xuất |
| Tổng độ trễ nhóm | `kafka_consumergroup_lag_sum` | Tổng lượng tồn đọng của nhóm Consumer | số lượng | Sự tăng liên tục của tổng độ trễ cho thấy sự chậm trễ kinh doanh đang mở rộng |
| Thành viên nhóm | `kafka_consumergroup_members` | Số lượng thành viên trong nhóm Consumer | số lượng | Giảm số lượng thành viên có thể dẫn đến giảm khả năng tiêu thụ |

Các truy vấn phổ biến:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

Gợi ý khắc phục sự cố: Chỉ số kinh doanh cốt lõi của Kafka là Lag. Khi Lag tăng, trước tiên kiểm tra xem số lượng thành viên consumer có giảm không, sau đó xem tỷ lệ tiêu thụ có giảm không, và cuối cùng kiểm tra thời gian xử lý ứng dụng, các phụ thuộc hạ nguồn và IO của Broker.

### 7.3 Bản sao và ISR

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Số lượng bản sao | `kafka_topic_partition_replicas` | Số lượng bản sao Partition | số lượng | Số bản sao ít hơn dự kiến làm giảm độ tin cậy |
| ISR Bản sao | `kafka_topic_partition_in_sync_replica` | Số lượng Partition In-Sync Replicas | số lượng | Sự giảm ISR cho thấy các bản sao bị tụt hoặc sự cố Broker |
| Leader ưu tiên | `kafka_topic_partition_leader_is_preferred` | Leader có phải là bản sao ưu tiên không | `0/1` | Mất cân bằng lâu dài có thể gây áp lực cao trên một số Broker |

Gợi ý khắc phục sự cố: Sự giảm ISR đại diện cho rủi ro độ tin cậy nhiều hơn so với Lag thông thường. Kiểm tra trạng thái Broker, mạng, độ trễ ghi đĩa và đồng bộ hóa bản sao.

## 8. MinIO Giám sát Lưu trữ Đối tượng

MinIO Giám sát được sử dụng để quan sát khả năng sẵn sàng của cụm lưu trữ đối tượng, trạng thái nút và đĩa, dung lượng Bucket, S3 yêu cầu, lỗi, lưu lượng, xử lý tiến trình và hoạt động nhiệm vụ sửa chữa. 

### 8.1 Nút và Đĩa của Cụm 

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Nút Trực tuyến | `minio_cluster_nodes_online_total` | Số lượng nút trực tuyến MinIO nút | chiếc | Giảm số lượng cho thấy nút không khả dụng |
| Nút Ngoại tuyến | `minio_cluster_nodes_offline_total` | Số lượng nút ngoại tuyến MinIO nút | chiếc | Lớn hơn 0 cần chú ý đến khả năng sẵn sàng của cụm |
| Đĩa Trực tuyến | `minio_cluster_disk_online_total` | Số lượng đĩa trực tuyến | chiếc | Giảm số lượng đĩa ảnh hưởng đến khả năng dự phòng và khả năng ghi |
| Đĩa Ngoại tuyến | `minio_cluster_disk_offline_total` | Số lượng đĩa ngoại tuyến | chiếc | Lớn hơn 0 cần khắc phục sự cố đĩa hoặc điểm gắn kết |
| Dung lượng Sử dụng được | `minio_cluster_capacity_usable_free_bytes` | Dung lượng sử dụng được của cụm | Byte | Giảm liên tục cho thấy rủi ro thiếu dung lượng |

Gợi ý Khắc phục sự cố: Đối với lưu trữ đối tượng, trước tiên hãy kiểm tra trạng thái trực tuyến của nút và đĩa. Đừng đánh giá đĩa ngoại tuyến chỉ bằng số lượng; rủi ro nên được đánh giá kết hợp với chiến lược sao lưu mã xóa. 

### 8.2 Dung lượng Bucket và Số lượng Đối tượng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Dung lượng Bucket | `bucket_usage_size` | Dung lượng đã sử dụng của bucket | Byte | Tăng dung lượng nhanh, cần đánh giá việc mở rộng |
| Số lượng Đối tượng | `bucket_objects_count` | Số lượng đối tượng trong bucket | Số lần | Quá nhiều đối tượng nhỏ làm tăng tải metadata và quá trình quét |
| Phân bố Kích thước Đối tượng | `minio_bucket_objects_size_distribution` | Phân bố kích thước đối tượng trong bucket | Thống kê theo bucket | Thay đổi trong phân bố đối tượng ảnh hưởng đến hiệu suất lưu trữ và yêu cầu |

Các truy vấn phổ biến:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

Khuyến nghị điều tra: Tăng trưởng dung lượng nên được phân tích riêng theo Bucket. Khi số lượng đối tượng tăng nhanh nhưng tăng trưởng dung lượng không rõ ràng, thường là do số lượng đối tượng nhỏ tăng. Cần chú ý đến việc dọn dẹp vòng đời và mô hình ghi dữ liệu của doanh nghiệp.

### 8.3 S3 Yêu cầu, Lỗi và Lưu lượng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| S3 Số lượng Yêu cầu | `minio_s3_requests_total` | Tổng số S3 API yêu cầu | lần/giây | Tăng đột ngột các yêu cầu, có thể là cao điểm kinh doanh hoặc thử lại |
| S3 Số lỗi | `minio_s3_requests_errors_total` | Tổng số S3 API lỗi | lần/giây | Tỷ lệ lỗi tăng ảnh hưởng đến việc đọc/ghi đối tượng |
| Lưu lượng nhận được | `minio_s3_traffic_received_bytes` | Thời gian tích lũy S3 số byte nhận được | Byte/giây | Tăng lưu lượng tải lên |
| Lưu lượng gửi đi | `minio_s3_traffic_sent_bytes` | Thời gian tích lũy S3 số byte gửi đi | Byte/giây | Tăng lưu lượng tải về hoặc truy xuất từ nguồn gốc |

Các truy vấn phổ biến:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

Khuyến nghị điều tra: Khi S3 tỷ lệ lỗi tăng, trước tiên phân tích theo API loại, sau đó kiểm tra Bucket tương ứng, trạng thái đĩa node và lưu lượng mạng.

### 8.4 Quá trình Node, Bộ xử lý Tệp và IO

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Sử dụng đĩa Node | `minio_node_disk_used_bytes` | Sử dụng đĩa của MinIO node | Byte | Mất cân bằng dung lượng của một node |
| Handle Tệp Mở | `minio_node_file_descriptor_open_total` | Số lượng handle tệp được mở bởi MinIO quá trình | Số lần | Yêu cầu có thể thất bại khi gần đạt giới hạn hệ thống |
| Gọi Hệ thống Đọc | `minio_node_syscall_read_total` | Tổng số lần gọi hệ thống đọc | Lần/giây | Tăng bất thường các lần gọi đọc |
| Gọi Hệ thống Ghi | `minio_node_syscall_write_total` | Tổng số lần gọi hệ thống ghi | Lần/giây | Tăng bất thường các lần gọi ghi |
| Bytes Đọc từ Quá trình | `minio_node_io_rchar_bytes` | Tổng số byte đọc bởi quá trình | Byte/giây | Tăng tải đọc |
| Bytes Ghi từ Quá trình | `minio_node_io_wchar_bytes` | Tổng số byte ghi bởi quá trình | Byte/giây | Tăng tải ghi |
| Số lượng goroutine | `minio_node_go_routine_total` | Số lượng goroutine trong MinIO quá trình | Số lần | Tăng trưởng liên tục có thể cho thấy tồn đọng yêu cầu hoặc rò rỉ |
| Thời gian bắt đầu | `minio_node_process_starttime_seconds` | MinIO Thời gian bắt đầu quy trình | Dấu thời gian Unix | Thay đổi cho thấy quy trình được khởi động lại |

Gợi ý điều tra: Đối với MinIO vấn đề hiệu suất, hãy xem xét S3 yêu cầu, ổ đĩa nút, IO quy trình và goroutine cùng nhau. Chỉ số lượng yêu cầu cao không nhất thiết là bất thường; tỷ lệ lỗi, độ trễ IO và trạng thái ổ đĩa ngoại tuyến là những tín hiệu rủi ro rõ ràng hơn.

### 8.5 Hoạt động Khôi phục và Sử dụng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Hoạt động Khôi phục | `minio_heal_time_last_activity_nano_seconds` | Thời gian hoạt động khôi phục cuối cùng | Dấu thời gian nanô giây | Hoạt động khôi phục dài hoặc thường xuyên cần chú ý đến sức khỏe ổ đĩa |
| Hoạt động Sử dụng | `minio_usage_last_activity_nano_seconds` | Thời gian hoạt động quét sử dụng cuối cùng | Dấu thời gian nanô giây | Các quét sử dụng bất thường có thể ảnh hưởng đến độ chính xác của thống kê dung lượng |

Gợi ý điều tra: Sau khi node hoặc ổ đĩa hồi phục bất thường, theo dõi xem các hoạt động khôi phục có tiến triển bình thường không để ngăn chặn rủi ro đối với dư thừa đối tượng còn tồn tại trong thời gian dài.

## 9. Elasticsearch Giám sát

Elasticsearch Giám sát được sử dụng để quan sát sức khỏe của cụm tìm kiếm, số lượng node, phân phối shard, các thao tác đọc/ghi chỉ mục, bộ nhớ đệm, JVM, các thread pool, ổ đĩa và mạng. Lỗi ES thường không được xác định bằng một chỉ số duy nhất; phổ biến hơn, “bất thường shard   JVM áp lực   từ chối thread pool   chỉ báo nước ổ đĩa” xuất hiện cùng nhau.

### 9.1 Sức khỏe Cụm và Node

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Sức khỏe Cụm | `elasticsearch_cluster_health_status` | Trạng thái sức khỏe cụm ES | Giá trị trạng thái | Vàng/đỏ cho thấy bất thường ở bản sao hoặc shard chính |
| Số lượng Node | `elasticsearch_cluster_health_number_of_nodes` | Số node của cụm | Số lần | Giảm số node có thể cho thấy một node ngoại tuyến |
| Số lượng Node Dữ liệu | `elasticsearch_cluster_health_number_of_data_nodes` | Số node dữ liệu trong cụm | Số lần | Giảm số node dữ liệu ảnh hưởng đến khả năng lưu trữ shard và khả năng đọc/ghi |
| Các công việc đang chờ xử lý | `elasticsearch_cluster_health_number_of_pending_tasks` | Số lượng công việc đang chờ trong cụm | Số lần | Tăng liên tục cho thấy các cập nhật trạng thái master hoặc cụm chậm |
| Shards Chính Hoạt Động | `elasticsearch_cluster_health_active_primary_shards` | Số lượng shards chính đang hoạt động | chiếc | Nguy cơ cao nếu giảm, có thể ảnh hưởng đến khả năng truy cập chỉ mục |
| Shards Hoạt Động | `elasticsearch_cluster_health_active_shards` | Tổng số shards đang hoạt động | chiếc | Giảm cho thấy shards chưa được khôi phục đầy đủ |
| Shards Đang Khởi Tạo | `elasticsearch_cluster_health_initializing_shards` | Số lượng shards đang khởi tạo | chiếc | Không giảm trong thời gian dài cho thấy phục hồi chậm |
| Shards Đang Di Chuyển | `elasticsearch_cluster_health_relocating_shards` | Số lượng shards đang di chuyển | chiếc | Quá nhiều di chuyển làm tăng áp lực mạng và đĩa |
| Shards Chưa Được Gán | `elasticsearch_cluster_health_unassigned_shards` | Số lượng shards chưa được gán | chiếc | Lớn hơn 0 cho thấy shards chưa được gán cho một node |
| Shards Chưa Gán Bị Trì Hoãn | `elasticsearch_cluster_health_delayed_unassigned_shards` | Số lượng shards chưa gán bị trì hoãn | chiếc | Đang chờ gán lại sau khi node offline |

Câu hỏi thường gặp: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

Gợi ý điều tra: ES nên kiểm tra trạng thái sức khỏe và shards chưa được gán trước tiên. Trạng thái đỏ nên ưu tiên xử lý shards chính; vàng chủ yếu do replicas chưa được gán, cũng không thể để lâu. 

### 9.2 Dung Lượng Đĩa và Hệ Thống Tệp

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị / Đo lường phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Tổng Dung Lượng Đĩa Dữ Liệu | `elasticsearch_filesystem_data_size_bytes` | Tổng dung lượng thư mục dữ liệu ES | Byte | Được sử dụng để tính tỷ lệ sử dụng đĩa |
| Dung Lượng Đĩa Dữ Liệu Có Sẵn | `elasticsearch_filesystem_data_available_bytes` | Dung lượng có sẵn của thư mục dữ liệu ES | Byte | Không đủ dung lượng có thể kích hoạt di chuyển shard hoặc giới hạn ghi |

Các truy vấn phổ biến:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

Gợi ý điều tra: ES rất nhạy cảm với việc sử dụng đĩa. Khi sử dụng đĩa quá cao, có thể xảy ra di chuyển shard, chỉ mục chỉ đọc hoặc lỗi ghi. Cần theo dõi sự tăng trưởng chỉ mục, chính sách lưu giữ và phân bố đĩa của node.

### 9.3 Tài liệu, Chỉ mục và Xóa

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Số lượng Tài liệu | `elasticsearch_indices_docs` | Số lượng tài liệu hiện tại | số lượng | Sự tăng trưởng liên tục nhanh của tài liệu yêu cầu đánh giá khả năng |
| Tài liệu đã xóa | `elasticsearch_indices_docs_deleted` | Số lượng tài liệu đã xóa | số lượng | Tỷ lệ xóa cao có thể gây áp lực hợp nhất |
| Số lần Ghi chỉ mục | `elasticsearch_indices_indexing_index_total` | Tổng số lần thực hiện thao tác chỉ mục | lần/giây | Sự gia tăng đột ngột trong ghi làm tăng CPUáp lực đĩa và làm mới |
| Thời gian Ghi chỉ mục | `elasticsearch_indices_indexing_index_time_seconds_total` | Tổng thời gian thực hiện thao tác chỉ mục | giây/giây | Sự gia tăng thời gian ghi làm chậm đường dẫn ghi |
| Số lần thao tác Xóa | `elasticsearch_indices_indexing_delete_total` | Tổng số thao tác xóa | lần/giây | Sự gia tăng đột ngột các thao tác xóa có thể gây áp lực hợp nhất phân đoạn |
| Thời lượng Thao tác Xóa | `elasticsearch_indices_indexing_delete_time_seconds_total` | Tổng thời lượng thực hiện thao tác xóa | giây/giây | Sự tăng thời lượng xóa |

Các truy vấn phổ biến:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

Khuyến nghị Giải quyết sự cố: Khi ghi chậm, đừng chỉ nhìn vào việc ghi QPS. Bạn cũng nên xem xét làm mới, hợp nhất, nhật ký giao dịch, từ chối pool luồng và IO đĩa.

### 9.4 Truy vấn và Yêu cầu Get

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Số lượng Yêu cầu Truy vấn | `elasticsearch_indices_search_query_total` | Tổng số truy vấn tìm kiếm | lần/giây | Sự gia tăng đột ngột trong truy vấn |
| Độ trễ Truy vấn | `elasticsearch_indices_search_query_time_seconds` | Tổng thời gian của các truy vấn tìm kiếm | giây/giây | Tăng độ trễ trung bình của truy vấn |
| Số lượng Yêu cầu Lấy | `elasticsearch_indices_search_fetch_total` | Tổng số trong giai đoạn tìm kiếm và lấy | lần/giây | Các bộ kết quả lớn có thể làm tăng số lần lấy |
| Độ trễ Lấy | `elasticsearch_indices_search_fetch_time_seconds` | Tổng thời gian tìm kiếm và lấy | giây/giây | Lấy chậm thường liên quan đến bộ kết quả lớn, đĩa hoặc mạng |
| Số lượng Yêu cầu Get | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Tổng số lần Get hit và miss | lần/giây | Sự gia tăng số lần thiếu có thể chỉ ra người dùng truy cập tài liệu không tồn tại |
| Thời lượng Get | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Tổng thời gian thực hiện các yêu cầu Get | giây/giây | Slow Get cho thấy áp lực đang tăng trên đường đọc |

Câu hỏi thường gặp: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

Khuyến nghị khắc phục sự cố: Các truy vấn chậm cần được phân biệt giữa truy vấn và lấy dữ liệu. Truy vấn chậm thường liên quan đến điều kiện truy vấn, cấu trúc chỉ mục và áp lực phân mảnh; việc lấy dữ liệu chậm thường xảy ra khi có nhiều trường trả về, tập kết quả lớn hoặc đọc đĩa chậm.

### 9.5 Phân mảnh, Hợp nhất, Làm mới, và Translog

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Cỡ/Đơn vị thường dùng | Triệu chứng bất thường |
| --- | --- | --- | --- | --- |
| Số lượng phân mảnh | `elasticsearch_indices_segments_count` | Số phân mảnh hiện tại | số lượng | Quá nhiều phân mảnh có thể ảnh hưởng đến truy vấn và bộ nhớ |
| Bộ nhớ Phân mảnh | `elasticsearch_indices_segments_memory_bytes` | Bộ nhớ chiếm bởi các phân mảnh | Byte | Tăng liên tục có thể gây áp lực JVM |
| Số lần Hợp nhất | `elasticsearch_indices_merges_total` | Tổng số thao tác hợp nhất | lần/giây | Hợp nhất thường xuyên cho thấy áp lực ghi hoặc xóa cao |
| Số lượng Tài liệu trong Hợp nhất | `elasticsearch_indices_merges_docs_total` | Tổng số tài liệu được xử lý bởi các lần hợp nhất | số lượng/giây | Khối lượng công việc hợp nhất tăng |
| Dung lượng Dữ liệu Hợp nhất | `elasticsearch_indices_merges_total_size_bytes_total` | Tổng dữ liệu được xử lý bởi hợp nhất | Byte/giây | Hợp nhất lớn có thể làm bão hòa IO đĩa |
| Thời lượng Hợp nhất | `elasticsearch_indices_merges_total_time_seconds_total` | Tổng thời gian tiêu tốn cho hợp nhất | Giây/giây | Hợp nhất chậm có thể ảnh hưởng đến hiệu năng ghi và truy vấn |
| Số lần Làm mới | `elasticsearch_indices_refresh_total` | Tổng số lần làm mới | lần/giây | Làm mới thường xuyên tăng tải bổ sung |
| Thời lượng Làm mới | `elasticsearch_indices_refresh_time_seconds_total` | Tổng thời gian làm mới | Giây/giây | Làm mới chậm ảnh hưởng đến khả năng quan sát gần thời gian thực |
| Số lần Ghi đệm | `elasticsearch_indices_flush_total` | Tổng số lần ghi đệm | lần/giây | Ghi đệm thường xuyên có thể liên quan đến translog và áp lực ghi |
| Thời lượng Ghi đệm | `elasticsearch_indices_flush_time_seconds` | Tổng thời gian ghi đệm | Giây/giây | Ghi đệm chậm có thể ảnh hưởng đến sự ổn định |
| Các thao tác Translog | `elasticsearch_indices_translog_operations` | Số thao tác translog hiện tại | số lượng | Tích lũy liên tục cần chú ý đến việc ghi đệm |
| Kích thước Translog | `elasticsearch_indices_translog_size_in_bytes` | Kích thước translog hiện tại | Byte | Kích thước quá lớn có thể ảnh hưởng đến thời gian khôi phục |
| Điều tiết lưu trữ | `elasticsearch_indices_store_throttle_time_seconds_total` | Tổng thời gian điều tiết lưu trữ chỉ mục | giây/giây | Tăng điều tiết, các ghi bị ảnh hưởng bởi ổ đĩa |

Gợi ý điều tra: Khi áp lực ghi cao, việc hợp nhất, làm mới, làm trống và thay đổi translog diễn ra cùng lúc. Tăng thời gian hợp nhất và điều tiết lưu trữ thường cho thấy ổ đĩa bắt đầu ảnh hưởng đến ES.

### 9.6 Bộ nhớ đệm và Cầu dao ngắt

| Chiều giám sát | Chỉ số | Ý nghĩa của chỉ số | Đơn vị/Đo lường phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Bộ nhớ đệm truy vấn | `elasticsearch_indices_query_cache_memory_size_bytes` | Bộ nhớ sử dụng bởi bộ nhớ đệm truy vấn | Byte | Sử dụng quá mức có thể làm thu hẹp JVM |
| Sự thoát của bộ nhớ đệm truy vấn | `elasticsearch_indices_query_cache_evictions` | Tổng số lần thoát của bộ nhớ đệm truy vấn | lần/giây | Thoát thường xuyên cho thấy bộ nhớ đệm không ổn định |
| Bộ nhớ fielddata | `elasticsearch_indices_fielddata_memory_size_bytes` | Bộ nhớ sử dụng bởi fielddata | Byte | Sử dụng fielddata cao có thể dễ dàng gây áp lực bộ nhớ |
| Sự thoát fielddata | `elasticsearch_indices_fielddata_evictions` | Tổng số lần thoát fielddata | lần/giây | Áp lực truy vấn hoặc tổng hợp cao |
| Sự thoát bộ nhớ đệm bộ lọc | `elasticsearch_indices_filter_cache_evictions` | Tổng số lần thoát bộ nhớ đệm bộ lọc | lần/giây | Hủy bộ nhớ đệm bộ lọc thường xuyên |
| Kích thước ước tính của cầu dao ngắt | `elasticsearch_breakers_estimated_size_bytes` | Bộ nhớ ước tính của cầu dao ngắt | Byte | Các truy vấn có thể bị từ chối khi gần đạt giới hạn |
| Giới hạn cầu dao ngắt | `elasticsearch_breakers_limit_size_bytes` | Giới hạn cầu dao ngắt | Byte | Dùng để tính tỷ lệ sử dụng cầu dao ngắt |
| Kích hoạt cầu dao ngắt | `elasticsearch_breakers_tripped` | Số lần cầu dao ngắt được kích hoạt | lần/tăng | Mô tả tăng trưởng: các yêu cầu bị chặn do rủi ro bộ nhớ |

Câu hỏi thường gặp: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

Khuyến nghị điều tra: Các truy vấn tổng hợp, sắp xếp và truy vấn script có thể dễ dàng tăng sử dụng fielddata và cầu dao ngắt. Khi cầu dao ngắt được kích hoạt, thường cần giới hạn kích thước truy vấn, tối ưu hóa ánh xạ chỉ mục, hoặc điều chỉnh phương thức truy vấn.

### 9.7 JVM, CPU, và Tải

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| JVM Bộ nhớ đã sử dụng | `elasticsearch_jvm_memory_used_bytes` | Hiện tại JVM bộ nhớ đã sử dụng | Byte | Liên tục gần giới hạn, tăng áp lực GC |
| JVM Bộ nhớ tối đa | `elasticsearch_jvm_memory_max_bytes` | Tối đa có sẵn JVM bộ nhớ | Byte | Được sử dụng để tính toán JVM sử dụng |
| JVM Bộ nhớ cam kết | `elasticsearch_jvm_memory_committed_bytes` | JVM bộ nhớ đã cam kết | Byte | Quan sát JVM phân bổ bộ nhớ |
| JVM Đỉnh của Pool Bộ nhớ | `elasticsearch_jvm_memory_pool_peak_used_bytes` | Sử dụng đỉnh của từng pool bộ nhớ | Byte | Đỉnh cao trong generation cũ cần chú ý |
| Số lần GC | `elasticsearch_jvm_gc_collection_seconds_count` | Số lần xảy ra GC | lần/giây | GC thường xuyên, độ trễ có thể dao động |
| Thời gian GC | `elasticsearch_jvm_gc_collection_seconds_sum` | Tổng thời gian GC | giây/giây | Thời gian GC cao có thể ảnh hưởng đến truy vấn và ghi |
| Tiến trình CPU | `elasticsearch_process_cpu_percent` | Quá trình ES CPU sử dụng | phần trăm | Cao kéo dài CPU có thể chỉ ra khối lượng truy vấn hoặc ghi nặng |
| Tải hệ thống | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | Tải nút 1/5/15 phút | giá trị tải | Tải cao hơn CPU lõi cho thấy xếp hàng tác vụ rõ ràng |
| Số file mở | `elasticsearch_process_open_files_count` | Số file được mở bởi quá trình ES | số lượng | Tiếp cận giới hạn hệ thống có thể ảnh hưởng đến truy cập file chỉ mục |

Các truy vấn thường gặp: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

Gợi ý điều tra: ES lớn hơn JVM bộ nhớ không phải lúc nào cũng tốt hơn. JVM sử dụng, thời gian GC, fielddata, bộ nhớ đệm truy vấn và breakers nên được giám sát cùng nhau để xác định liệu áp lực bộ nhớ có phải do truy vấn hay do sự không phù hợp giữa kích thước heap và quy mô dữ liệu.

### 9.8 Thread Pool và Mạng

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị đo/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Luồng Hoạt động | `elasticsearch_thread_pool_active_count` | Số lượng luồng hoạt động trong pool luồng | Số lần | Số lượng luồng hoạt động cao lâu dài cho thấy áp lực xử lý nặng |
| Tác vụ hoàn thành | `elasticsearch_thread_pool_completed_count` | Số lượng tích lũy của các tác vụ đã hoàn thành bởi pool luồng | Lần/Giây | Được sử dụng để quan sát thông lượng xử lý |
| Tác vụ bị từ chối | `elasticsearch_thread_pool_rejected_count` | Số lượng tích lũy của các tác vụ bị từ chối bởi pool luồng | Lần/Giây | Tăng cho thấy pool luồng hoặc hàng đợi đầy |
| Lưu lượng đến | `elasticsearch_transport_rx_size_bytes_total` | Số byte tích lũy nhận được bởi transport | Byte/giây | Tăng giao tiếp giữa các nút hoặc lưu lượng yêu cầu |
| Lưu lượng đi | `elasticsearch_transport_tx_size_bytes_total` | Tích lũy byte đã gửi bởi giao thức truyền tải | Byte/giây | Tăng lưu lượng do di chuyển shard, truy vấn hoặc sao chép |

Các truy vấn thường gặp: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

Gợi ý điều tra: Từ chối trong pool luồng là tín hiệu rủi ro kinh doanh rất trực tiếp. Đối với từ chối ghi, kiểm tra pool luồng bulk/index; đối với từ chối tìm kiếm, kiểm tra pool luồng tìm kiếm, sau đó xác định các điểm nghẽn kết hợp với CPU, JVMvà IO đĩa.

## 10. Giám sát dịch vụ ứng dụng

Giám sát ứng dụng bao gồm các yêu cầu phổ biến phía máy chủ, các lệnh gọi phụ thuộc từ phía khách hàng, tài nguyên thời gian chạy, liên kết kinh doanh chỉnh sửa hợp tác, và các tác vụ dịch vụ RS. Trọng tâm của các chỉ số ứng dụng không phải là ngưỡng tài nguyên từng cá nhân, mà là khối lượng yêu cầu, lỗi, độ trễ, và sức khỏe của các phụ thuộc.

### 10.1 Các chỉ số phổ biến phía máy chủ

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| Thời gian hoạt động của dịch vụ | `up` | Ứng dụng exporter hoặc endpoint chỉ số có thể thu thập được hay không | `0/1` | `0` có nghĩa là chỉ số không thể truy cập hoặc dịch vụ bất thường |
| Thông tin build | `ego_build_info` | Phiên bản build, nhánh của ứng dụng và các thông tin khác | Thông tin nhãn | Dùng để xác minh phiên bản phát hành |
| Số lần khởi động | `ego_server_started_total` | Tích lũy số lần máy chủ khởi động | lần/tăng | Tăng cho thấy quy trình khởi động lại hoặc phát hành |
| Yêu cầu máy chủ | `ego_server_handle_total` | Tích lũy số yêu cầu máy chủ | lần/giây | Tăng hoặc giảm đột ngột về yêu cầu cần được đánh giá kết hợp với ngữ cảnh kinh doanh |
| Thời gian tiêu thụ phía máy chủ | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | Thống kê thời gian yêu cầu phía máy chủ | P50/P95/P99 | Độ trễ tăng ảnh hưởng đến trải nghiệm người dùng | 

Các truy vấn thường gặp: 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

Gợi ý điều tra: Đối với các bất thường phía máy chủ, trước tiên hãy kiểm tra xem khối lượng yêu cầu có thay đổi hay không, sau đó xem xét các lỗi và độ trễ. Nếu độ trễ tăng nhưng tài nguyên không cao, tiếp tục kiểm tra các lệnh gọi phụ thuộc hạ nguồn và hàng đợi.

### 10.2 Các lệnh gọi phụ thuộc của client

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Độ chi tiết/Đơn vị phổ biến | Hành vi bất thường |
| --- | --- | --- | --- | --- |
| Khối lượng lệnh gọi client | `ego_client_handle_total` | Số lần ứng dụng gọi xuống hạ nguồn như một client | lần/giây | Tăng đột ngột khối lượng lệnh gọi xuống hạ nguồn, có thể làm tăng áp lực phụ thuộc |
| Độ trễ client | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | Thống kê độ trễ lệnh gọi xuống hạ nguồn | P50/P95/P99 | Hạ nguồn chậm có thể làm chậm dịch vụ hiện tại |
| Trạng thái client | `ego_client_stats_gauge` | Chỉ số kiểu trạng thái hoặc pool kết nối của client | Giá trị hiện tại | Cạn kiệt pool kết nối, kết nối nhàn rỗi bất thường, v.v. |
| Kafka Độ trễ sản xuất | `kafka_produce_duration_seconds_bucket` | Thời gian ứng dụng để sản xuất Kafka Tin nhắn | P50/P95/P99 | Độ trễ sản xuất tăng, có thể do Broker hoặc vấn đề mạng |

Các truy vấn phổ biến:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

Gợi ý điều tra: Khi một giao diện doanh nghiệp chậm, so sánh thời gian tiêu tốn ở phía máy chủ với thời gian tiêu tốn bởi các phụ thuộc client. Nếu tỷ lệ thời gian của client cao, ưu tiên kiểm tra các dịch vụ hạ nguồn, middleware hoặc mạng tương ứng.

### 10.3 Thời gian chạy và tiến trình

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Tiêu chuẩn/Đơn vị thông thường | Biểu hiện bất thường |
| --- | --- | --- | --- | --- |
| Goroutine của Go | `go_goroutines` | Số lượng goroutine trong tiến trình Go | Số lần | Tăng liên tục có thể chỉ ra bị chặn hoặc rò rỉ |
| Thời gian Go GC | `go_gc_duration_seconds` | Thời gian GC của Go | Giây/Phần trăm | Thời gian GC tăng có thể ảnh hưởng đến độ trễ |
| Bộ nhớ Heap Go | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Phân bổ và sử dụng heap của Go | Byte | Tăng liên tục cần kiểm tra rò rỉ bộ nhớ |
| Bộ nhớ hệ thống Go | `go_memstats_sys_bytes` | Bộ nhớ được Go runtime yêu cầu từ hệ thống | Byte | Quan sát cùng với RSS |
| Bộ nhớ ngăn xếp Go | `go_memstats_stack_inuse_bytes` | Sử dụng ngăn xếp Goroutine | Byte | Tăng cùng với sự tăng trưởng goroutine |
| Node.js Số lần GC | `nodejs_gc_duration_seconds_count` | Node.js Số lần GC | lần/giây | GC thường xuyên có thể chỉ ra áp lực heap |
| Node.js Thời lượng GC | `nodejs_gc_duration_seconds_sum` | Node.js Tổng thời lượng GC | giây/giây | Tăng thời lượng GC có thể ảnh hưởng đến phản hồi |
| Node.js Không gian Heap | `nodejs_heap_space_size_used_bytes` | Sử dụng từng phần Node.js không gian heap | Byte | Cần chú ý nếu gần giới hạn hoặc tăng liên tục |
| Tiến trình CPU | `process_cpu_seconds_total` | Tiến trình CPU thời gian | lõi/giây | Cao CPU sử dụng |
| Tiến trình RSS | `process_resident_memory_bytes` | Bộ nhớ vật lý của tiến trình | Byte | Tăng liên tục RSS tăng trưởng |
| Các tay cầm tiến trình | `process_open_fds` | Số lượng mô tả tệp mở trong tiến trình | số lượng | Rò rỉ tay cầm, rò rỉ kết nối |

Gợi ý điều tra: Các chỉ số runtime của Go và Node.js chủ yếu được sử dụng để giải thích độ trễ ứng dụng và sự tăng tài nguyên. Khi ứng dụng P95 tăng lên, nếu thời lượng GC tăng đồng thời, ưu tiên kiểm tra cấp phát bộ nhớ và vòng đời đối tượng.

### 10.4 Dịch vụ Chỉnh sửa Hợp tác

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Đơn vị Thường gặp | Chỉ báo Bất thường |
| --- | --- | --- | --- | --- |
| Kafka Độ trễ Consumer | `kafka_consumergroup_lag` | Tồn đọng của các Nhóm Consumer liên quan trong chỉnh sửa hợp tác | Số lần | Tăng độ trễ có thể gây chậm xử lý sự kiện |
| Thời lượng Tiến trình | `process_flow_duration_seconds_bucket` | Thời lượng của tiến trình chỉnh sửa hợp tác | P50/P95/P99 | Chậm trễ trong liên kết hợp tác tài liệu |
| Số lượng Tiến trình | `process_total` | Tổng số tiến trình được xử lý | Lần/Giây | Thay đổi bất thường về khối lượng xử lý |
| Kích thước Nội dung Tệp | `file_content_size_bytes_bucket` | Phân bố kích thước nội dung tệp | Thống kê theo nhóm | Tăng tỷ lệ tệp lớn có thể ảnh hưởng đến thời gian xử lý |
| Thời lượng Thay đổi | `handle_changeset_cost_seconds_bucket` | Thời gian cần để xử lý thay đổi | P50/P95/P99 | Tăng độ trễ đồng bộ chỉnh sửa |
| Số lần Tính toán Modoc | `modocComputeCount` | Số lượng tính toán modoc | Lần/Giây | Tăng bất thường về khối lượng tính toán |
| Lời gọi Serverless | `serverless_invocations` | Số lần gọi Serverless | Lần/Giây | Lỗi hoặc đột biến khi gọi có thể ảnh hưởng đến liên kết |

Các truy vấn phổ biến:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

Gợi ý điều tra: Đối với các liên kết chỉnh sửa hợp tác, Kafka Trễ, thời gian xử lý, thời gian bộ thay đổi và kích thước tệp nên được xem xét cùng nhau. Khi tỷ lệ tệp lớn tăng lên, sự tăng thời gian có thể là áp lực dung lượng bình thường chứ không phải sự cố tại một điểm duy nhất.

### Dịch vụ RS 10.5

| Chiều giám sát | Chỉ số | Ý nghĩa chỉ số | Phạm vi/Đơn vị phổ biến | Hiệu suất bất thường |
| --- | --- | --- | --- | --- |
| HTTP Số lượng Yêu cầu | `http_requests_total` | Số tích lũy HTTP yêu cầu | lần/giây | Tăng hoặc giảm đột ngột các yêu cầu |
| HTTP Thời lượng | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP thời lượng yêu cầu | P50/P95/P99 | Độ trễ giao diện tăng |
| gRPC Số lượng Yêu cầu | `grpc_requests_total` | Số tích lũy gRPC yêu cầu | lần/giây | gRPC ngoại lệ gọi |
| gRPC Thời lượng | `grpc_requests_duration_seconds` | gRPC thời lượng yêu cầu | P50/P95/P99 | Xử lý nội bộ hoặc hạ nguồn chậm hơn |
| Thời lượng nhiệm vụ xuất | `export_task_duration_milliseconds_count` | Số lượng và thời lượng xử lý nhiệm vụ xuất | ms/lần | Nhiệm vụ xuất chậm lại hoặc ùn ứ |
| Thời lượng nhiệm vụ nhập | `import_task_duration_milliseconds_count` | Số lượng và thời lượng xử lý nhiệm vụ nhập | ms / nhiệm vụ | Nhiệm vụ nhập chậm lại hoặc ùn ứ |
| Nhiệm vụ xuất đang tiến hành | `export_task_in_progress` | Các nhiệm vụ xuất đang thực hiện | số lượng | Nếu không giảm trong thời gian dài, cho thấy các nhiệm vụ đang bị tắc |
| Nhiệm vụ nhập đang tiến hành | `import_task_in_progress` | Các nhiệm vụ nhập đang thực hiện | số lượng | Nếu không giảm trong thời gian dài, cho thấy các nhiệm vụ đang bị tắc |
| Các chỉ số Tokio | `tokio_metrics` | Chỉ số thời gian chạy Rust Tokio | giá trị hiện tại / tỉ lệ | Hàng đợi thời gian chạy hoặc lập lịch nhiệm vụ bất thường |
| Chỉ số jemalloc | `jemalloc` | Chỉ số bộ cấp phát bộ nhớ | Byte / số lượng | Phân mảnh bộ nhớ hoặc bất thường cấp phát |
| TCP Chỉ số | `tcp` | Dịch vụ RS TCP Chỉ số liên quan đến kết nối | số lượng / tỉ lệ | Áp lực kết nối hoặc bất thường mạng |

Gợi ý điều tra: Dịch vụ RS nên kiểm tra cả các yêu cầu trực tuyến và các tác vụ chạy dài như nhập/xuất. Số lượng tác vụ đang tiến hành liên tục không giảm thường cho thấy 'các tác vụ bị kẹt' đáng tin cậy hơn so với thời gian trung bình.

## 11. Đọc số liệu và gợi ý điều tra

### 11.1 Thứ tự điều tra chung

| Bước | Mục quan sát | Mục đích |
| --- | --- | --- |
| 1 | `up`, thời gian bắt đầu, Pod Ready | Xác nhận xem dịch vụ vẫn còn hoạt động và có khởi động lại gần đây hay không |
| 2 | Khối lượng yêu cầu, tỷ lệ lỗi, P95/P99 độ trễ | Xác định xem có thực sự ảnh hưởng đến kinh doanh hay không |
| 3 | CPU, bộ nhớ, đĩa, mạng | Xác định xem có nút thắt tài nguyên hay không |
| 4 | Độ trễ phụ thuộc hạ nguồn, Kafka Độ trễ, truy vấn cơ sở dữ liệu chậm | Xác định xem nó có bị chậm do phụ thuộc hay không |
| 5 | Phiên bản phát hành, cấu hình, thay đổi lưu lượng | Xác định xem có liên quan đến thay đổi hay không |

Khi thực sự xử lý sự cố, đừng vội xem tất cả các biểu đồ trước. Trước tiên, xác nhận "có tác động đến kinh doanh hay không," sau đó tìm "nguồn gốc của tác động." Ví dụ, nếu một giao diện chậm, trước tiên xem ứng dụng P95, sau đó kiểm tra độ trễ phụ thuộc của client; nếu phụ thuộc bình thường, xem lại dịch vụ CPU, GC, bộ nhớ và hạn chế container.

### 11.2 Các kết hợp ngoại lệ phổ biến

| Triệu chứng | Hiệu suất số liệu phổ biến | Hướng điều tra ưu tiên |
| --- | --- | --- |
| Giao diện chậm | Ứng dụng P95/P99 tăng, CPU không cao | Phụ thuộc hạ nguồn, truy vấn cơ sở dữ liệu chậm, Kafka Độ trễ |
| CPU được sử dụng tối đa | `container_cpu_usage_seconds_total` cao, hạn chế cao | CPU giới hạn, giao diện nóng, tác vụ xử lý theo lô |
| Bộ nhớ OOM | Bộ nhớ làm việc gần đạt giới hạn, số lần khởi động lại tăng | Rò rỉ bộ nhớ, giới hạn quá nhỏ, xử lý đối tượng lớn |
| Đĩa chậm | iowait, IO bận, độ trễ đọc/ghi đều tăng | Cơ sở dữ liệu, Kafka, MinIO, ghi nhật ký |
| Mạng bất thường | Tăng đột biến lưu lượng đi kèm với sụt giảm/lỗi | Node NIC, CNI, liên kết, số lượng kết nối |
| Kafka Độ trễ | `kafka_consumergroup_lag` tăng liên tục | Các phiên bản Consumer, thời gian tiêu thụ, phụ thuộc hạ nguồn |
| Redis Áp lực ngược | Tỷ lệ trúng giảm, số lần bỏ lỡ tăng | Chính sách hết hạn khóa, xuyên thủng bộ nhớ đệm, dung lượng |
| MySQL Chậm | Truy vấn chậm, scan, chờ khóa tăng | SQL, chỉ mục, khóa, IO đĩa |
| MinIO Rủi ro | Đĩa ngoại tuyến, tỷ lệ lỗi, mức dung lượng tăng | Đĩa, nút, tăng Bucket, trạng thái phục hồi |
| Elasticsearch Truy vấn chậm | Thời gian truy vấn/tìm kiếm tăng, từ chối thread pool tăng | Điều kiện truy vấn, cấu trúc chỉ mục, JVM, IO đĩa |
| Elasticsearch Ghi chậm | Thời gian lập chỉ mục, thời gian hợp nhất, giới hạn lưu trữ tăng | Đỉnh ghi, làm mới, hợp nhất, mức đĩa |
