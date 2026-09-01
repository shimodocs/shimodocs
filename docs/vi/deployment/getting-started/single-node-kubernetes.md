# Triển khai đơn nút Kubernetes Triển khai

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

## 1. Các tình huống áp dụng
- **K8s triển khai một nút**:
    - Phù hợp cho các đội nhỏ nhẹ, sử dụng quy mô nhỏ dưới 200 người, PoC, trình diễn, xác minh tính năng và thử nghiệm ngắn hạn.
- Chỉ cần 1 máy chủ, và máy chủ đồng thời đóng vai trò là nút cài đặt, K8s nút chính, và nút công việc kinh doanh.
- **Lưu ý**
    - Đối với việc ra mắt chính thức, vận hành lâu dài, hoặc mở rộng khả năng sẵn sàng cao sau này, khuyến nghị sử dụng K8s triển khai theo cụm.

## 2. Tổng quan về Quá trình Triển khai

| Bước | Những việc cần làm | Chỉ số hoàn thành |
| --- | --- | --- |
| 1. Kiểm tra Môi trường Hệ thống | Xác nhận tài nguyên máy chủ, đĩa, mạng, đồng bộ thời gian và các lệnh cơ bản | Máy chủ đáp ứng yêu cầu triển khai |
| 2. Chuẩn bị vật liệu cài đặt | Lấy bộ cài đặt và gói cài đặt sản phẩm; môi trường ngoại tuyến cũng cần chuẩn bị gói hình ảnh ngoại tuyến | Tên tệp phù hợp với CPU kiến trúc |
| 3. Tải lên vật liệu cài đặt | Tải bộ cài đặt và gói cài đặt lên nút triển khai | Các tệp đã được đặt trong thư mục chỉ định trên máy chủ |
| 4. Khởi động bộ cài đặt | Mở `mdp-installer` trang web | Trang cài đặt có thể truy cập qua trình duyệt |
| 5. Cài đặt qua Trang Web | Chọn gói phân phối, cấu hình nút, hoàn tất kiểm tra môi trường và bắt đầu triển khai | Tất cả các nhiệm vụ cài đặt đã thành công |
| 6. Kiểm tra Sau Cài đặt | Kiểm tra cụm, dịch vụ, đăng nhập, giấy phép và chức năng kinh doanh | Các chức năng cốt lõi có thể sử dụng bình thường |

## 3. Chuẩn bị Trước Khi Triển khai

### 3.1 Chuẩn bị Thông tin Máy chủ

| Thông tin | Ví dụ | Mô tả |
| --- | --- | --- |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Triển khai đơn nút K8s chỉ sử dụng 1 máy chủ |
| CPU Kiến trúc | `amd64` / `arm64` | Bộ cài đặt và gói cài đặt phải phù hợp với kiến trúc máy chủ |
| Môi trường Mạng | Trực tuyến / Ngoại tuyến | Chọn trực tuyến nếu mạng công cộng có thể truy cập; chọn ngoại tuyến cho môi trường nội bộ hoặc cô lập |
| Người Thực hiện | `root` hoặc người dùng có `sudo` quyền | Bộ cài đặt cần thực hiện các nhiệm vụ triển khai thông qua SSH |
| SSH Cổng | `22` | Nếu SSH cổng đã được thay đổi, điền cổng thực tế |
| Giao thức Truy cập | HTTP / HTTPS | HTTP có thể được sử dụng cho môi trường thử nghiệm; HTTPS được khuyến nghị cho môi trường sản xuất hoặc truy cập bên ngoài |
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` hoặc `<INSTALL_NODE_IP>` | Địa chỉ truy cập cho người dùng ShimoDocs Suite |
| Thư mục dữ liệu | `/data` | Được khuyến nghị sử dụng một đĩa dữ liệu gắn riêng |

### 3.2 Chuẩn bị tài liệu cài đặt

| Tài liệu | Tên tệp ví dụ | Mô tả |
| --- | --- | --- |
| Trình cài đặt | `mdp-installer-amd64` | Ví dụ cho `amd64` kiến trúc; thay bằng tên tệp thực tế cho các kiến trúc khác |
| Gói cài đặt sản phẩm | `co1.8.20260807.3639-drive-release..tar.gz` | Đối với triển khai một nút đơn K8s chọn gói phân phối có tên tệp không bao gồm `k3s`; tên tệp dựa theo giao hàng thực tế |
| Gói hình ảnh cơ sở ngoại tuyến | `smbase_image-amd64.tar.gz` | Chỉ cần thiết cho cài đặt ngoại tuyến |
| Gói hình ảnh sản phẩm ngoại tuyến | `offline_app_image.tar.gz` | Chỉ cần thiết cho cài đặt ngoại tuyến, phải khớp với phiên bản gói cài đặt sản phẩm |

Lưu ý:

- Tên tệp trong các lệnh cần được thay bằng tên tệp thực tế, như `mdp-installer-amd64`, `co1.8.<VERSION>-drive-release.tar.gz`.
- Gói cài đặt sản phẩm, gói hình ảnh ngoại tuyến và máy chủ CPU kiến trúc phải nhất quán.
- Trước khi cài đặt ngoại tuyến, khuyến nghị chuẩn bị sẵn gói hình ảnh cơ sở ngoại tuyến và gói hình ảnh sản phẩm ngoại tuyến cùng lúc để tránh phải thêm gói tạm thời trong quá trình triển khai.

### 3.3 Kiểm tra tài nguyên máy chủ

| Mục | Yêu cầu khuyến nghị |
| --- | --- |
| Số lượng máy chủ | 1 |
| CPU | 16 Nhân hoặc hơn |
| Bộ nhớ | 32 GB hoặc hơn |
| Đĩa hệ thống | Root `/` phân vùng 100 GB hoặc hơn |
| Đĩa dữ liệu | Gắn riêng tại `/data`, dung lượng trống trên 300 GB |
| Cài đặt ngoại tuyến | Khuyến nghị dự trữ thêm hơn 100 GB trên đĩa dữ liệu cho các gói hình ảnh và tệp trích xuất tạm thời |

Thực hiện trên máy chủ: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Xác nhận các kết quả sau: 

- CPU, bộ nhớ và đĩa đáp ứng các tiêu chuẩn triển khai. 
- `/data` đã được gắn vào một đĩa dữ liệu riêng. 
- Đồng bộ thời gian hệ thống bình thường. 
- Máy chủ có thể truy cập qua SSH đăng nhập. 
- Môi trường cài đặt trực tuyến có thể truy cập mạng công cộng; môi trường cài đặt ngoại tuyến đã chuẩn bị sẵn gói ảnh ngoại tuyến. 

### 3.4 Kiểm tra Cổng 

| Cổng | Mục đích | 
| --- | --- | 
| `22/TCP` | SSH đăng nhập và thực hiện các tác vụ cài đặt | 
| `18080/TCP` | Trang Web Trình cài đặt | 
| `80/TCP` hoặc `443/TCP` | ShimoDocs Suite lối truy cập | 

Nếu máy chủ đã bật tường lửa hoặc nhóm bảo mật, vui lòng mở các cổng trên trước. 

## 4. Tải Công cụ và Gói Cài đặt lên 

Ví dụ sau đây sử dụng `amd64` kiến trúc. Đối với các kiến trúc khác, vui lòng thay bằng tên tệp thực tế. 

### 4.1 Tải Trình cài đặt lên 

Thực thi trên máy tính cục bộ: 

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

### 4.2 Tải Gói Ảnh Ngoại tuyến lên

Bước này có thể bỏ qua đối với cài đặt trực tuyến.

Đối với cài đặt ngoại tuyến, gói ảnh ngoại tuyến cần được tải lên nút triển khai:

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

### 4.3 Đăng nhập vào máy chủ

```bash
ssh root@<INSTALL_NODE_IP>
```

### 4.4 Thêm Quyền Thực thi cho Trình cài đặt

```bash
chmod +x /root/mdp-installer-amd64
```

### 4.5 Khởi chạy Trang Web Trình cài đặt

Thực thi trên máy chủ:

```bash
cd /root
./mdp-installer-amd64 server
```

Nếu bạn muốn trình cài đặt chạy nền, bạn có thể sử dụng: 

```bash
nohup /root/mdp-installer-amd64 server > /root/mdp-installer.log 2>&1 &
```

Truy cập bằng trình duyệt: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 5. Cài đặt thông qua Trang Web

### 5.1 Chọn Gói Phân phối

Sau khi vào trang web trình cài đặt, chọn gói phân phối sản phẩm sẽ triển khai lần này.

Đối với K8s Triển khai một nút, vui lòng chọn gói phân phối có tên tệp không chứa `k3s`, ví dụ:

```text
co1.8.20260807.3639-drive-release.tar.gz
```

### 5.2 Cấu hình SSH Kết nối

Trình cài đặt sẽ đăng nhập vào nút triển khai qua SSH và thực hiện các tác vụ cài đặt. SSH cài đặt hỗ trợ hai phương thức xác thực:

- Xác thực bằng khóa riêng.
- PASSWORD Xác thực.

Khuyến nghị sử dụng `root` người dùng hoặc người dùng có `sudo` quyền hạn để thực hiện triển khai. Sau khi điền thông tin, bạn có thể thử kết nối trước để đảm bảo trình cài đặt có thể đăng nhập vào nút triển khai bình thường.

### 5.3 Xác nhận Cấu hình Cơ bản

Sau khi chọn gói phân phối, tiếp tục bước tiếp theo. Nếu không có yêu cầu đặc biệt, bạn có thể giữ cấu hình mặc định của trang; nếu môi trường triển khai đã có kế hoạch rõ ràng về tên miền, chứng chỉ, phân đoạn mạng hoặc phần mềm trung gian, hãy điền theo kế hoạch thực tế.

Các điểm chính cần xác nhận trong quá trình cấu hình: 

- Đảm bảo giao thức truy cập và ACCESS_DOMAIN được điền chính xác. 
- Pod CIDR và Dịch vụ CIDR không xung đột với mạng hiện có, mạng văn phòng, VPN, hoặc IDC phân đoạn mạng. 
- Sử dụng `/data` hoặc thư mục đĩa dữ liệu đã được lên kế hoạch thực tế cho thư mục dữ liệu. 
- Phương thức cài đặt trực tuyến / ngoại tuyến phù hợp với môi trường mạng hiện tại. 

### 5.4 Triển khai Ban đầu 

Sau khi cấu hình xong, nhấp Triển khai Ban đầu. Trang sẽ hiển thị tổng quan về triển khai này. Vui lòng tập trung kiểm tra: 

- Phiên bản gói sản phẩm. 
- Triển khai NODE_IP. 
- SSH người dùng và cổng. 
- ACCESS_DOMAIN và giao thức. 
- Thư mục dữ liệu. 
- Chế độ cài đặt trực tuyến hoặc ngoại tuyến. 
- Lựa chọn phần mềm trung gian. 

Tiếp tục sau khi xác nhận mọi thứ đều đúng. 

### 5.5 Kiểm tra Môi trường Hệ thống

Trình cài đặt sẽ tự động kiểm tra môi trường máy chủ.

Tiếp tục triển khai sau khi kiểm tra đạt. Nếu có các mục không đạt, vui lòng xử lý theo hướng dẫn trên trang và kiểm tra lại. Hướng dẫn xử lý phổ biến bao gồm: 

- Dung lượng đĩa không đủ: Dọn dẹp không gian hoặc mở rộng đĩa dữ liệu. 
- Cổng không khả dụng: Giải phóng cổng hoặc điều chỉnh sử dụng cổng. 
- SSH Kết nối thất bại: kiểm tra tài khoản, PASSWORD, khóa riêng, cổng và nhóm bảo mật. 
- Đồng bộ thời gian bất thường: Cấu hình NTP hoặc hiệu chỉnh thời gian máy chủ. 
- Thiếu các lệnh cơ bản: Cài đặt các lệnh còn thiếu theo phân phối hệ thống. 

### 5.6 Bắt đầu Triển khai 

Sau khi kiểm tra môi trường đạt, nhấp để bắt đầu triển khai. 

Trong quá trình triển khai, bạn có thể xem nhật ký thực thi của từng thành phần. Trong khi cài đặt, vui lòng đảm bảo: 

- Quá trình cài đặt vẫn đang chạy. 
- Trình duyệt có thể kết nối với mạng của nút cài đặt. 
- Không khởi động lại máy chủ. 
- Không di chuyển hoặc xóa gói cài đặt, gói hình ảnh ngoại tuyến hoặc thư mục dữ liệu. 

### 5.7 Chờ cài đặt hoàn tất

Quá trình cài đặt cần chờ một thời gian, và thời gian cụ thể phụ thuộc vào hiệu năng máy chủ, môi trường mạng và tốc độ tải xuống hình ảnh.

Khi trang hiển thị rằng tất cả các nhiệm vụ đã được thực hiện thành công và không có thành phần nào thất bại, điều đó cho thấy việc triển khai đã hoàn tất.

### 5.8 Xác nhận kết quả cài đặt

Sau khi cài đặt hoàn tất, chương trình cài đặt sẽ hiển thị trang hoàn thành triển khai và thông tin truy cập. Vui lòng xác nhận trước rằng không có tác vụ nào bị lỗi trên trang trước khi tiếp tục truy cập hệ thống kinh doanh và MDP Nền tảng Vận hành.

Truy cập địa chỉ kinh doanh: 

```text
http://<ACCESS_DOMAIN>/
```

Nếu HTTPS được cấu hình trong quá trình cài đặt, vui lòng truy cập: 

```text
https://<ACCESS_DOMAIN>/
```

Sau khi đăng nhập bằng tài khoản mặc định hoặc tài khoản quản trị, vui lòng thay đổi ngay PASSWORD mật khẩu ban đầu.

Truy cập MDP Nền tảng vận hành: 

```text
http://<ACCESS_DOMAIN>/mdp/
```

Nếu bạn cần sửa đổi MDP quản trị viên PASSWORD, bạn có thể thực hiện lệnh sau trên nút triển khai để sửa đổi hoặc đặt lại PASSWORD.
Vui lòng thay thế {password} bằng mật khẩu phức tạp mới PASSWORD theo các yêu cầu bảo mật thực tế.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 6. Kiểm tra sau cài đặt

### 6.1 Kiểm tra K8s Trạng thái nút

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

### 6.2 Kiểm tra điểm truy cập 

Truy cập ShimoDocs Suite truy cập qua trình duyệt: 

```text
http://<ACCESS_DOMAIN>/
```

Nếu HTTPS được cấu hình, vui lòng truy cập: 

```text
https://<ACCESS_DOMAIN>/
```

Xác nhận rằng trang đăng nhập có thể mở bình thường.

### 6.3 Kiểm tra Backend Quản lý và License

Xác nhận các mục sau:

- Có thể truy cập backend quản trị.
- Quản trị viên có thể đăng nhập.
- Trang License có thể được mở.
- Thông tin máy có thể xem được.
- Giấy phép có thể được xin hoặc cập nhật theo quy trình ủy quyền.

### 6.4 Kiểm tra Chức năng Kinh doanh

Sau khi đăng nhập bằng tài khoản thử nghiệm hoặc tài khoản do quản trị viên tạo, ít nhất cần xác minh:

- Bạn có thể tạo tài liệu, bảng tính và trình chiếu.
- Tài liệu có thể chỉnh sửa và lưu lại, nội dung vẫn tồn tại sau khi làm mới.
- Chỉnh sửa hợp tác đa người dùng khả dụng.
- Nhập và xuất tệp hoạt động bình thường.
- Các chức năng cốt lõi như tìm kiếm, không gian nhóm và danh bạ đều khả dụng.

Sau khi đăng nhập lần đầu bằng tài khoản thử nghiệm mặc định, vui lòng thay đổi PASSWORD mật khẩu ban đầu.
Tài khoản PASSWORD là tài khoản triển khai và bàn giao PASSWORD!
```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxx
```

### 6.5 Dừng tiến trình cài đặt

Sau khi triển khai hoàn tất và nghiệm thu đạt, dịch vụ Web của trình cài đặt có thể được dừng:
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

## 7. Khắc phục sự cố Thông thường

### 7.1 Trình duyệt không thể mở trang cài đặt

Kiểm tra những điều sau:

- Quy trình cài đặt có còn đang chạy không.
- Cổng `18080` có bị tường lửa hoặc nhóm bảo mật chặn không.
- Địa chỉ IP mà trình duyệt truy cập có phải là INSTALL_NODE_IP.

Bạn có thể thực hiện các lệnh sau trên máy chủ:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 7.2 Kiểm tra môi trường thất bại

Xử lý từng mục theo hướng dẫn trên trang. Sau khi xử lý, quay lại trang cài đặt và chạy lại kiểm tra môi trường.

Các kiểm tra ưu tiên:

- Liệu CPU, bộ nhớ và đĩa có đáp ứng yêu cầu không.
- Liệu `/data` có phải là ổ dữ liệu độc lập không.
- Liệu thời gian máy chủ có được đồng bộ hóa không.
- Liệu SSH người dùng có quyền triển khai.

### 7.3 Kéo ảnh cài đặt ngoại tuyến thất bại

Hướng kiểm tra:

- Liệu gói hình ảnh ngoại tuyến đã được tải lên nút triển khai chưa.
- Liệu gói hình ảnh ngoại tuyến cơ bản và gói hình ảnh ngoại tuyến sản phẩm có đầy đủ không.
- Liệu phiên bản gói hình ảnh có khớp với gói cài đặt sản phẩm không.
- Liệu địa chỉ kho hình ảnh riêng, tài khoản, và PASSWORD được điền chính xác.

### 7.4 Pod giữ trạng thái bất thường trong thời gian dài

Đầu tiên, kiểm tra Pod bất thường:

```bash
kubectl get pod -A
```

Kiểm tra lại nhật ký: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Xử lý các vấn đề với hình ảnh, cấu hình, tài nguyên hoặc phụ thuộc dựa trên nhật ký.

## 8. Giữ Tài liệu Sau Cài đặt

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
