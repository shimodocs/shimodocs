# Quản lý cụm

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Tổng quan Chức năng

Mô-đun Quản lý Cụm là một bảng điều khiển trong MDP Nền tảng Vận hành giao tiếp với các cụm của khách hàng, Kubernetes nhắm vào ba tình huống: kiểm tra hàng ngày, khắc phục sự cố khẩn cấp và thay đổi tài nguyên. Mục tiêu của mô-đun này là cho phép nhân sự vận hành trực ca hoàn thành các nhiệm vụ vận hành và khắc phục sự cố phổ biến mà không cần thường xuyên chuyển sang giao diện gốc `kubectl`.

Các khả năng chính:

- Tổng quan cụm: tình trạng node, trạng thái ứng dụng đang chạy
- Quản lý tải công việc: xem, khởi động lại, thay đổi số lượng bản sao, sửa đổi tài nguyên container và xem YAML cho Deployment, StatefulSet, DaemonSet, Pod, Job và CronJob
- Quản lý Cấu hình: xem ConfigMap, HorizontalPodAutoscaler (HPA)
- Tài nguyên Mạng: xem Service, Ingress
- Chẩn đoán cấp Pod: nhật ký thời gian thực, nhật ký sự cố, K8s sự kiện, terminal web, YAML xem

### 1.1 Người dùng Áp dụng

| Vai trò        | Các Hoạt động Thường gặp                                      |
| ----------- | ---------------------------------------------------- |
| Vận hành trực ca | Xem các bất thường của node và Pod, truy vấn nhật ký, xem sự kiện |
| Hỗ trợ tại chỗ | Xem trạng thái bản sao của Deployment, phiên bản hình ảnh, Yêu cầu/Giới hạn tài nguyên         |
| Sự cố khẩn cấp | Khởi động lại Deployment hoặc DaemonSet, điều chỉnh số bản sao, điều chỉnh CPU/Bộ nhớ |
| Lập kế hoạch năng lực | Xem HPA số bản sao hiện tại và giới hạn trên/dưới                             |

### 1.2 Các thao tác không được khuyến nghị trong mô-đun này

Xóa NAMESPACE, buộc di dời Pod, chỉnh sửa Secret hoặc RBAC tài nguyên, và các thao tác nhạy cảm khác không có sẵn trong mô-đun này và cần thực hiện thông qua công cụ gốc `kubectl` hoặc công cụ thay đổi liên quan. Các thao tác hàng loạt qua nhiều cluster không khả dụng; mỗi thao tác chỉ ảnh hưởng đến cluster được chọn hiện tại và NAMESPACE. Để tải xuống nhật ký tệp lớn một lần, nên sử dụng terminal Web thay vì cửa sổ bật lên nhật ký theo luồng.

---

## 2. Truy cập và điều hướng

Menu bên trái: **Quản lý vận hành → Quản lý Cluster**.

Sau khi vào, menu **Triển khai** bên trái được chọn mặc định. Mặc định là mục đầu tiên trong cluster hiện tại, và hỗ trợ chọn cluster và NAMESPACE tùy chỉnh. NAMESPACE 

---

## 3. Workloads

### 3.1 Triển khai
**Các bước**: Tìm Deployment mục tiêu → Nhấp vào biểu tượng bút chì ở góc trên bên phải → Cửa sổ chỉnh sửa xuất hiện → Nhập giá trị mới → Xác nhận thay đổi.

Các trường hỗ trợ sửa đổi trong cửa sổ bật lên: 

- Số bản sao, giá trị tối thiểu 0, phải là số nguyên 
- CPU Yêu cầu / Giới hạn cho mỗi container, đơn vị là "core", có thể điền `1` hoặc `1000m` 
- Yêu cầu / Giới hạn bộ nhớ mỗi container, đơn vị là Mi, có thể điền vào `512` 

Sau khi gửi, một quá trình tái tạo liên tục sẽ được kích hoạt. Các trường không được liệt kê (hình ảnh, biến môi trường, probes, v.v.) sẽ không bị thay đổi. 

#### 3.1.1 Khởi động lại Deployment 
Các bước: Tìm Deployment mục tiêu → Nhấp vào biểu tượng mũi tên tròn ở góc trên bên phải → Xác nhận rằng cửa sổ popup xuất hiện → Kiểm tra cluster / NAMESPACE / tên load → Xác nhận khởi động lại. 

Cửa sổ xác nhận nêu rõ rằng "việc khởi động lại sẽ làm các Pod được xây dựng lại, và dịch vụ có thể bị gián đoạn tạm thời." Khởi động lại sẽ xây dựng lại các Pod trên tất cả các node cùng lúc. 

### 3.2 Các Pod
**Các bước thao tác**: Truy cập Pod từ menu bên trái → Phần dưới liệt kê tất cả các Pod dưới NAMESPACEhiện tại, hỗ trợ tìm kiếm theo Namespace, POD_NAME, IP của Pod, và NODE_IP. 

Điều này YAML chỉ để xem.

### 3.3 Jobs và CronJobs

#### Jobs
**Các bước**: Vào Jobs từ menu bên trái → Bảng liệt kê tất cả Jobs trong hiện tại NAMESPACE.

Có thể tìm kiếm theo Namespaces và Tên.

#### CronJobs
**Các bước**: Vào CronJobs từ menu bên trái → Bảng liệt kê tất cả CronJobs dưới hiện tại NAMESPACE.

Có thể tìm kiếm theo Namespaces và Tên. 
Nhấp **** **** để mở rộng và hiển thị bảng con của Pods tương ứng với tất cả Jobs được kích hoạt bởi CronJob này. 

### 3.4 DaemonSets 
**Các bước thao tác**: Vào DaemonSets từ menu bên trái. 

Có thể tìm kiếm theo Namespaces và tên workload.
Các thao tác được hỗ trợ:

- Chỉnh sửa: CPU / Memory có thể thay đổi, số lượng bản sao không thể thay đổi.
- Khởi động lại: Xây dựng lại Pods trên tất cả các node cùng lúc.
- YAML: Chỉ xem.

### 3.5 StatefulSets
**Các bước thao tác**: Vào StatefulSets từ menu bên trái → Chế độ xem bảng.

Việc sửa đổi số lượng bản sao, CPU/Bộ nhớ, khởi động lại hoặc danh sách Pod của StatefulSets không được hỗ trợ. Các thay đổi cần thiết nên được thực hiện bằng cách sử dụng công cụ gốc `kubectl` (xem Phụ lục B).

---

## 4. Cấu hình

### 4.1 ConfigMaps
**Các bước**: Vào ConfigMaps từ menu bên trái → bảng liệt kê tất cả ConfigMaps trong hiện tại NAMESPACE.
[Quản lý cụm] không hỗ trợ chỉnh sửa key-value. Để thay đổi, vui lòng vào Trung tâm Cấu hình.

### 4.2 HPA
**Các bước thao tác**: Vào HPA từ menu bên trái → Bảng liệt kê tất cả HPA dưới hiện tại NAMESPACE.

Chỉ xem. Để sửa đổi HPA min và max, vui lòng sử dụng công cụ gốc `kubectl`.

---

## 5. Mạng

### 5.1 Dịch vụ
**Các bước**: Sử dụng menu bên trái để vào Dịch vụ → bảng liệt kê tất cả Dịch vụ dưới hiện tại NAMESPACE.

Chỉ xem. Để thay đổi, vui lòng chỉnh sửa thông qua cấu hình toàn cục mdp. 

### 5.2 Ingress
**Các bước**: Vào Ingresses từ menu bên trái → Bảng liệt kê tất cả Ingresses trong NAMESPACE hiện tại. 

Chỉ xem, để thay đổi vui lòng chỉnh sửa thông qua cấu hình mdp toàn cục.

---

## 6. Các thao tác phổ biến

### 6.1 Khắc phục sự cố Pod

1. Sử dụng menu thả xuống phía trên để chuyển sang cụm tương ứng và NAMESPACE
2. Vào Pods trong menu bên trái
3. Lọc theo POD_NAME hoặc IP
4. Chú ý đến trường Phase ở đầu thẻ, ưu tiên `Failed` và `Pending`
5. Condition tương ứng với chỉ số sức khỏe bị mờ là điểm gặp sự cố
6. Nhấp vào biểu tượng "Events" ở cuối hàng để tìm nguyên nhân gốc rễ
7. Sử dụng "Logs" để xem đầu ra theo thời gian thực / "Crash Logs" để xem đầu ra cuối cùng của container

### 6.2 Khởi động lại Deployment

1. Vào Deployments trong menu bên trái
2. Tìm Deployment mục tiêu
3. Nhấp vào biểu tượng mũi tên tròn ở góc trên bên phải
4. Xác nhận popup bằng cách kiểm tra cụm / NAMESPACE / tên workload → xác nhận khởi động lại
5. Quan sát thanh tiến trình trạng thái các bản sao Pod ở cuối thẻ để đánh giá tiến trình tái tạo

### 6.3 Giảm số bản sao Deployment để xác thực

1. Vào Deployment tương ứng
2. Nhấp vào biểu tượng bút chì "Edit"
3. Nhập giá trị mới cho số lượng bản sao (có thể đặt 0 để gỡ lỗi) 
4. Điều chỉnh CPU / Bộ nhớ theo nhu cầu (tùy chọn) 
5. Xác nhận thay đổi và chờ cập nhật tuần tự 

Trước khi giảm số lượng bản sao, nên xác nhận với SRE đồng nghiệp xem giá trị mục tiêu có ảnh hưởng đến lưu lượng trực tuyến hay không. 

### 6.4 Chỉnh sửa ConfigMap 

Nền tảng không hỗ trợ chỉnh sửa các cặp key-value của ConfigMap trong Cluster Management - Configuration - ConfigMap. Vui lòng vào Trung tâm Cấu hình. 

--- 

## 9. Các Câu Hỏi Thường Gặp 

**Q1: Tổng quan trên cùng cho thấy tỷ lệ chạy ứng dụng không đạt 100%.** 

Điều này có nghĩa là có các Pod dưới mục hiện tại NAMESPACE những Pod không ở trạng thái Sẵn sàng (bao gồm Đang chờ, CrashLoopBackOff, Lỗi, v.v.). Vui lòng đi tới menu Pods ở bên trái, lọc theo POD_NAME hoặc IP, và kiểm tra các sự kiện và nhật ký của từng Pod không sẵn sàng. 

**Câu hỏi 2: Cửa sổ bật lên trống sau khi nhấp vào 'Chỉnh sửa Triển khai'.** 

Có ba lý do phổ biến: giật mạng, quá nhiều `managedFields` trong đối tượng tài nguyên, hoặc máy chủ API ngoại lệ. Vui lòng tắt thử lại trước; nếu nó vẫn trống, hãy liên hệ SRE và cung cấp tên cụm / namespace / workload để xử lý sự cố. 

**Câu hỏi 3: YAML nội dung pop-up rất lớn.** 

Hiện tượng bình thường. K8s Các đối tượng tài nguyên mặc định mang nhiều siêu dữ liệu và điều kiện, với nội dung chính tập trung trong `spec` phần. 

**Câu 4: Không có đầu ra trong cửa sổ log.** 

Container có thể không xuất log ra stdout/stderr, vui lòng kiểm tra chính sách xuất log của ứng dụng. Nếu container bị crash, hãy sử dụng biểu tượng "Crash Log" để lấy đầu ra của phiên bản trước. 

**Câu 5: Việc chỉnh sửa số lượng bản sao hoặc tài nguyên không có hiệu lực.** 

Nền tảng phát hành một Strategic Merge Patch, và K8s sẽ bắt đầu quá trình tái hòa hợp trong vài giây. Nếu không có thay đổi nào trong 30 giây, vui lòng quay lại giao diện gốc `kubectl describe deployment` để kiểm tra các sự kiện. 

**Q6: Không thể sửa đổi StatefulSets, ConfigMaps, HPA, Services, Ingresses.** 

Nền tảng chỉ cho phép xem các tài nguyên này. Các sửa đổi nên được thực hiện thông qua cấu hình toàn cục mdp, và chỉ hỗ trợ Services và Ingresses. 

--- 

--- 

## Phụ lục A: Key kubectl các lệnh được sử dụng trên nền tảng này 

Các lệnh sau được sử dụng để thực thi trực tiếp trên máy chủ hoặc thiết bị đầu cuối bảo trì như một con đường thay thế khi các chức năng của mô-đun này không được bao phủ. 

```bash
# View
kubectl get  statefulset <name> -n <ns>
kubectl get deployment <name> -n <ns>

# Restart STS / deployment
kubectl rollout restart statefulset/<name> -n <ns>
kubectl rollout restart deployment/<name> -n <ns>

# View the complete Ingress rule chain
kubectl describe ingress <name> -n <ns>
```

`kubectl describe deployment <name> -n <ns>` có thể được sử dụng để khắc phục sự cố quá trình đối chiếu do nền tảng phát hành sau khi chỉnh sửa.

Các biện pháp phòng ngừa:
Không nên chỉnh sửa các tài nguyên được quản lý bởi MDP chẳng hạn như deployment, configmap, ingress, sts, v.v., thông qua kubectl . Cách đúng để vận hành là sử dụng MDP cấu hình backend.

## Phụ lục B: Thuật ngữ

| Thuật ngữ               | Giải thích                                           |
| --------------- | ---------------------------------------------------- |
| Cluster         | Mục tiêu K8s cluster, được cấu hình và phát hành khi MDP bắt đầu                              |
| Namespace       | K8s NAMESPACE, được sử dụng để tách biệt business hoặc môi trường                                   |
| Workload        | Workload, thường đề cập đến Deployment, StatefulSet, DaemonSet, Job, CronJob |
| Pod             | Đơn vị lập lịch nhỏ nhất trong K8s, chứa từ 1 đến N container                              |
| HPA             | HorizontalPodAutoscaler, mở rộng ngang dựa trên metric                  |
| Request / Limit | Đặt trước / giới hạn tài nguyên container, nền tảng hỗ trợ chỉnh sửa cả hai |
| Patch           | Cập nhật một phần, nền tảng này sử dụng Strategic Merge Patch                     |
| STS             | Viết tắt của StatefulSet                                       |
| DS              | Viết tắt của DaemonSet                                         |
