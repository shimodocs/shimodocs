# Khả năng cao Kubernetes Triển khai

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

## 1. Tình huống Áp dụng 

> [!TIP] 
> 
> K8s Triển khai cụm phù hợp cho môi trường sản xuất. So với triển khai trên máy đơn, triển khai cụm phù hợp hơn cho vận hành lâu dài, mở rộng và các kịch bản sẵn sàng cao. 

- Đối với môi trường sản xuất, khuyến nghị sử dụng `3 master   N worker`. 
- Ít nhất, chuẩn bị 3 máy chủ, tất cả 3 làm master. Worker có thể ban đầu tái sử dụng các node master và sau đó tăng số worker theo quy mô. 

## 2. Chuẩn bị Trước Khi Triển khai 

### 2.1 Chuẩn bị Thông tin Sau 

| Thông tin | Ví dụ | Mô tả | 
| --- | --- | --- | 
| Môi trường Mạng | Trực tuyến / Ngoại tuyến | Chọn Trực tuyến nếu hỗ trợ truy cập mạng công cộng; chọn Ngoại tuyến cho mạng nội bộ hoặc môi trường không kết nối | 
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Chọn 1 máy làm node cài đặt để bắt đầu trang web | 
| Kinh doanh NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | Tối thiểu 3 máy chủ | 
| Người dùng để Thực thi | `root` | Các lệnh cài đặt nên được thực thi với `root` | 
| Giao thức Truy cập | HTTP / HTTPS | HTTPS Được khuyến nghị cho môi trường sản xuất | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | Địa chỉ để người dùng truy cập ShimoDocs Suite |
| Thư mục dữ liệu | `/data` | Nên giữ nhất quán trên tất cả các nút |
| Công cụ cài đặt | `mdp-installer-${Arch}` | Trình cài đặt do ShimoDocs, `${Arch}` phân biệt các kiến trúc chip khác nhau, giá trị của nó có thể là amd64 cho kiến trúc x86 hoặc arm64 cho kiến trúc arm |
| Gói cài đặt sản phẩm | ShimoDocs Suite gói cài đặt | Sử dụng tên tệp thực tế được cung cấp |
| Gói hình ảnh ngoại tuyến | `*.tar.gz` | Chỉ cần thiết cho cài đặt ngoại tuyến |
| Phần mềm trung gian bên ngoài | Có / Không | Nếu có phần mềm trung gian bên ngoài, chuẩn bị địa chỉ, cổng, tài khoản, PASSWORD trước |

### 2.2 Yêu cầu tối thiểu của máy chủ

| Mục | Yêu cầu |
| --- | --- |
| Số lượng máy chủ | 3 hoặc nhiều hơn |
| Vai trò được khuyến nghị | `3 master   N worker` |
| CPU mỗi nút | 16 lõi hoặc nhiều hơn |
| Bộ nhớ mỗi nút | 32 GB hoặc nhiều hơn |
| Đĩa hệ thống | Root `/` phân vùng 100 GB hoặc nhiều hơn |
| Đĩa dữ liệu | Gắn kết riêng `/data`, không gian khả dụng 300 GB hoặc nhiều hơn |
| Cài đặt ngoại tuyến | Nên dự trữ thêm 100 GB hoặc nhiều hơn trên đĩa dữ liệu của nút cài đặt |

Lưu ý:

- Không phân vùng `/root`, `/var`, hoặc `/tmp` riêng. 
- Không đặt dữ liệu trên đĩa hệ thống; đặt tất cả vào `/data`. 
- Thời gian trên tất cả các nút phải được đồng bộ hóa. 
- Các nút cài đặt phải có thể truy cập các nút khác thông qua SSH. 

Có thể thực hiện trên mỗi máy chủ: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Xác nhận rằng các nút khác có thể truy cập từ nút cài đặt: 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

Nếu đăng nhập thất bại, trước tiên hãy kiểm tra SSH, PASSWORD, tường lửa hoặc cài đặt nhóm bảo mật trước khi tiếp tục cài đặt.

## 3. Tải lên công cụ cài đặt và gói cài đặt
> [!TIP]
>
> - Hãy chắc chắn chỉnh sửa tên tệp trong các lệnh theo tình huống thực tế. Ví dụ, tên gói cài đặt trong môi trường kiến trúc x86 là mdp-installer-amd64.
> - Chọn phương pháp tải lên phù hợp dựa trên tình huống thực tế. Bài viết này sử dụng dòng lệnh scp làm ví dụ, nhưng các công cụ đồ họa khác cũng có thể được sử dụng để tải lên. SSH 

Thực hiện lệnh sau trên máy tính cục bộ của bạn để chuyển trình cài đặt đến nút cài đặt:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

Cài đặt ngoại tuyến vẫn yêu cầu tải lên gói hình ảnh ngoại tuyến: 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

Đăng nhập vào nút cài đặt: 

```bash
ssh root@<INSTALL_NODE_IP>
```

Cấp quyền thực thi cho trình cài đặt:

```bash
chmod +x /root/mdp-installer-amd64
```

Khởi chạy trang web của trình cài đặt: 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

Truy cập bằng trình duyệt: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. Cài đặt qua Trang Web

### 4.1 Tải lên Gói Cài đặt Sản phẩm

1. Mở `http://<INSTALL_NODE_IP>:18080`.
2. Tải lên ShimoDocs Suite gói cài đặt.
3. Sau khi tải xong, nhấp vào `Continue`.

### 4.2 Cấu hình ACCESS_DOMAIN

Nhập ShimoDocs Suite địa chỉ truy cập:

| Mục Cấu hình | Cách điền |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 Xác nhận Cấu hình Cơ bản

| Mục Cấu hình | Cách điền |
| --- | --- |
| NODE_IP | Điền IP master / worker NODE_một cách lần lượt |
| SSH Cổng | Thường `22` |
| SSH PASSWORD | `root` người dùng PASSWORD |
| Loại nút | `master`, `worker`, nút cài đặt |
| Thư mục dữ liệu | `/data` |

Các bước thao tác:

1. Thêm INSTALL_NODE_IP.
2. Thêm địa chỉ IP của từng nút master/worker.
3. Phân vai trò nút cho từng máy chủ.
4. Kiểm tra khả năng kết nối từ nút cài đặt đến từng nút.
5. Điền vào danh mục dữ liệu và phân đoạn mạng container.

Các điểm chính cần xác nhận trong quá trình cấu hình:

- Giao thức truy cập và ACCESS_DOMAIN được điền chính xác.
- Pod CIDR và Dịch vụ CIDR không xung đột với mạng hiện có, mạng văn phòng, VPN, hoặc IDC các phân đoạn mạng.
- Thư mục dữ liệu sử dụng `/data` hoặc thư mục của đĩa dữ liệu thực tế đã lên kế hoạch.
- Phương pháp cài đặt trực tuyến/ngoại tuyến phù hợp với môi trường mạng hiện tại.
- Cài đặt ngoại tuyến yêu cầu tải lên gói hình ảnh cơ sở ngoại tuyến và gói hình ảnh ứng dụng. Mặc định là cài đặt trực tuyến, và cần đảm bảo rằng cụm có quyền truy cập mạng công cộng.

### 4.4 Triển khai ban đầu

Sau khi cấu hình hoàn tất, nhấp Khởi tạo Triển khai. Trang sẽ hiển thị tổng quan về triển khai này; vui lòng chú ý đặc biệt đến:

- Phiên bản gói sản phẩm.
- Triển khai NODE_IP.
- SSH người dùng và cổng.
- ACCESS_DOMAIN và giao thức.
- Thư mục dữ liệu.
- Chế độ cài đặt trực tuyến hoặc ngoại tuyến.
- Lựa chọn phần mềm trung gian.

Tiếp tục sau khi xác nhận không có lỗi.

### 4.5 Kiểm tra Môi trường Hệ thống

Trình cài đặt sẽ tự động kiểm tra môi trường máy chủ.

Tiếp tục triển khai sau khi kiểm tra thành công. Nếu xảy ra bất kỳ lỗi nào, vui lòng làm theo hướng dẫn trên trang để xử lý và sau đó kiểm tra lại. Các hướng xử lý phổ biến bao gồm:

- Không đủ dung lượng đĩa: dọn dẹp không gian hoặc mở rộng đĩa dữ liệu.
- Cổng không khả dụng: giải phóng cổng hoặc điều chỉnh việc sử dụng cổng.
- SSH Kết nối thất bại: kiểm tra tài khoản, PASSWORD, khóa riêng, cổng và nhóm bảo mật.
- Ngoại lệ đồng bộ thời gian: cấu hình NTP hoặc hiệu chỉnh thời gian máy chủ.
- Thiếu các lệnh cơ bản: Cài đặt các lệnh thiếu theo bản phân phối hệ thống.

### 4.6 Bắt đầu Triển khai

Sau khi kiểm tra môi trường vượt qua, nhấp vào Bắt đầu Triển khai.

Bạn có thể xem nhật ký thực thi của từng thành phần trong quá trình triển khai. Trong quá trình cài đặt, vui lòng đảm bảo: 

- Quá trình cài đặt vẫn đang chạy. 
- Trình duyệt có thể giao tiếp với nút cài đặt qua mạng. 
- Máy chủ không được khởi động lại. 
- Không di chuyển hoặc xóa gói cài đặt, gói hình ảnh ngoại tuyến hoặc thư mục dữ liệu. 

### 4.7 Chờ Quá Trình Cài Đặt Hoàn Thành

Quá trình cài đặt yêu cầu một khoảng thời gian chờ, và thời gian chính xác phụ thuộc vào hiệu năng máy chủ, môi trường mạng và tốc độ tải xuống hình ảnh.

Khi trang hiển thị rằng tất cả các nhiệm vụ đã được thực hiện thành công và không có thành phần nào thất bại, điều đó cho thấy việc triển khai đã hoàn tất.

### 4.8 Xác nhận Kết Quả Cài Đặt

Sau khi cài đặt hoàn tất, trình cài đặt sẽ hiển thị trang hoàn tất triển khai và thông tin điểm truy cập. Vui lòng xác nhận trước rằng không có nhiệm vụ nào thất bại trên trang trước khi tiếp tục truy cập hệ thống kinh doanh và MDP Nền tảng Vận hành.

Truy cập địa chỉ kinh doanh: 

```text
http://<ACCESS_DOMAIN>/
```

Nếu HTTPS được cấu hình trong quá trình cài đặt, vui lòng truy cập: 

```text
https://<ACCESS_DOMAIN>/
```

Sau khi đăng nhập bằng tài khoản mặc định hoặc tài khoản quản trị, vui lòng thay đổi ngay PASSWORD mật khẩu ban đầu.

Truy cập MDP Nền tảng Vận hành:

```text
http://<ACCESS_DOMAIN>/mdp/
```

Nếu bạn cần sửa đổi MDP quản trị viên PASSWORD, bạn có thể thực hiện lệnh sau trên nút triển khai để sửa đổi hoặc đặt lại PASSWORD.
Vui lòng thay thế {password} bằng mật khẩu phức tạp mới PASSWORD theo các yêu cầu bảo mật thực tế.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 5. Kiểm tra sau khi cài đặt

### 5.1 Kiểm tra K8s Trạng thái nút

Thực hiện trên nút triển khai:

```bash
kubectl get node
```

Trạng thái nút nên là `Ready`. 

Tiếp tục kiểm tra dịch vụ: 

```bash
kubectl get pod -A
```

Các trạng thái bình thường thường là: 

- `Running`: Dịch vụ đang chạy. 
- `Completed`: Nhiệm vụ đã hoàn tất thực hiện. 

Nếu bạn gặp các trạng thái như `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`, trước tiên hãy kiểm tra nhật ký Pod tương ứng và xử lý chúng. 

### 5.2 Kiểm tra quyền truy cập 

Truy cập ShimoDocs Suite truy cập qua trình duyệt: 

```text
http://<ACCESS_DOMAIN>/
```

Nếu HTTPS được cấu hình, vui lòng truy cập: 

```text
https://<ACCESS_DOMAIN>/
```

Xác nhận rằng trang đăng nhập có thể mở bình thường. 

### 5.3 Kiểm tra Backend Quản lý và License 

Xác nhận các mục sau: 

- Backend quản lý có thể truy cập được. 
- Quản trị viên có thể đăng nhập. 
- Trang License có thể mở được. 
- Thông tin máy có thể xem được. 
- License có thể được đăng ký hoặc cập nhật theo quy trình ủy quyền. 

### 5.4 Kiểm tra Chức năng Kinh doanh 

Sau khi đăng nhập bằng tài khoản thử nghiệm hoặc tài khoản do quản trị viên tạo, ít nhất hãy xác minh: 

- Có thể tạo tài liệu, bảng tính, bài thuyết trình. 
- Tài liệu có thể chỉnh sửa, lưu hoặc làm mới, và nội dung vẫn tồn tại. 
- Hợp tác chỉnh sửa đa người dùng khả dụng. 
- Nhập và xuất file bình thường. 
- Các chức năng cốt lõi như tìm kiếm, không gian nhóm, liên hệ, v.v. khả dụng. 

Sau khi đăng nhập tài khoản thử nghiệm mặc định lần đầu tiên, vui lòng cập nhật PASSWORD ngay lập tức. 
Tài khoản PASSWORD là tài khoản triển khai và phân phối PASSWORD! 

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 Dừng Quy Trình Cài Đặt

Sau khi việc triển khai hoàn tất và được chấp nhận, dịch vụ web cài đặt có thể bị dừng
Dừng trang web cài đặt:
Lệnh để dừng trình cài đặt: 

```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

Nếu trình cài đặt được khởi chạy trong nền bằng `nohup`, bạn cũng có thể kiểm tra nhật ký: 

```bash
tail -f /root/nohup.out
```

## 6. Xử Lý Các Vấn Đề Thường Gặp

### 6.1 Trình Duyệt Không Thể Mở Trang Cài Đặt

Kiểm tra những điều sau:

- Quy trình cài đặt có còn đang chạy không.
- Cổng `18080` có bị tường lửa hoặc nhóm bảo mật chặn không.
- Địa chỉ IP mà trình duyệt truy cập có phải là INSTALL_NODE_IP.

Bạn có thể thực hiện trên máy chủ:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 Kiểm Tra Môi Trường Thất Bại

Xử lý từng mục theo hướng dẫn trên trang. Sau khi xử lý, quay lại trang cài đặt và chạy lại kiểm tra môi trường.

Các kiểm tra ưu tiên:

- Liệu CPU, bộ nhớ và đĩa có đáp ứng yêu cầu không.
- Liệu `/data` có phải là ổ dữ liệu độc lập không.
- Liệu thời gian máy chủ có được đồng bộ hóa không.
- Liệu SSH người dùng có quyền triển khai.

### 6.3 Kéo Hình Ảnh Cài Đặt Ngoại Tuyến Thất Bại

Hướng kiểm tra:

- Liệu gói hình ảnh ngoại tuyến đã được tải lên nút triển khai chưa.
- Liệu gói hình ảnh ngoại tuyến cơ bản và gói hình ảnh ngoại tuyến sản phẩm có đầy đủ không.
- Liệu phiên bản gói hình ảnh có khớp với gói cài đặt sản phẩm không.
- Liệu địa chỉ kho hình ảnh riêng, tài khoản, và PASSWORD được điền chính xác.

### 6.4 Pod Còn Ở Trạng Thái Bất Thường Trong Thời Gian Dài

Đầu tiên, kiểm tra Pod bất thường:

```bash
kubectl get pod -A
```

Kiểm tra lại nhật ký: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Xử lý các vấn đề với hình ảnh, cấu hình, tài nguyên hoặc phụ thuộc dựa trên nhật ký.

## 7. Giữ lại Tài liệu sau khi Cài đặt

Sau khi triển khai, khuyến nghị giữ lại các tài liệu sau để thuận tiện cho việc bảo trì, nâng cấp và xử lý sự cố sau này:

- INSTALL_NODE_IP, ACCESS_DOMAIN, và giao thức truy cập.
- Tên tệp cài đặt và phiên bản.
- Tên tệp gói cài đặt sản phẩm và phiên bản.
- Tên tệp gói hình ảnh ngoại tuyến và phiên bản.
- Ảnh chụp màn hình cấu hình chính của trang web.
- `kubectl get node` kiểm tra kết quả.
- `kubectl get pod -A` kiểm tra kết quả.
- Hồ sơ cấp phép giấy phép.
- Hồ sơ chấp nhận chức năng kinh doanh.
- Các vấn đề gặp phải trong quá trình triển khai và kết quả xử lý.
