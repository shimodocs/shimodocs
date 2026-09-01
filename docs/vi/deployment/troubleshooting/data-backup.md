# Sao lưu Dữ liệu

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

Tài liệu này giải thích phạm vi sao lưu dữ liệu, yêu cầu phục hồi, phương pháp thực hiện và các mục kiểm tra sau khi phục hồi cho ShimoDocs môi trường tư nhân hóa.

Tài liệu này bao gồm các nội dung sau:

* Phạm vi sao lưu và ranh giới trách nhiệm

* Yêu cầu sao lưu và phục hồi cơ sở dữ liệu

* Yêu cầu sao lưu và phục hồi lưu trữ đối tượng

* Các mục xác nhận trước phục hồi

* Các mục kiểm tra sau phục hồi

Tài liệu này không bao gồm các nội dung sau:

* Các bước cài đặt và triển khai ban đầu

* Kế hoạch nâng cấp và di chuyển

* Hướng dẫn công cụ phục hồi dành riêng cho nhà cung cấp phần mềm trung gian bên thứ ba

* Quy trình xử lý sự cố trong môi trường sản xuất

# 2. Phạm vi sao lưu và ranh giới trách nhiệm

## 2.1 Phạm vi Sao lưu

Dữ liệu cần được bao gồm trong phạm vi sao lưu trong ShimoDocs môi trường được tư nhân hóa bao gồm:

* MySQL dữ liệu

* MongoDB dữ liệu

* Redis dữ liệu

* Dữ liệu lưu trữ đối tượng 

* Các tệp cấu hình cài đặt và tham số môi trường 

Các thư mục dữ liệu, thư mục sao lưu và thời gian lưu trữ sao lưu được quản lý đồng bộ bởi phía khách hàng. 

## 2.2 Ranh giới Trách nhiệm 

Ranh giới của các trách nhiệm sao lưu và phục hồi như sau: 

* Phía khách hàng chịu trách nhiệm xây dựng và thực hiện các chính sách sao lưu chính thức 

* Phía khách hàng chịu trách nhiệm bảo quản các tệp sao lưu, bảo mật phương tiện và quản lý thời gian lưu trữ 

* Phía khách hàng chịu trách nhiệm cho các buổi diễn tập phục hồi, phê duyệt phục hồi và chấp nhận kết quả phục hồi 

* ShimoDocs có thể cung cấp hỗ trợ kỹ thuật và hướng dẫn hoạt động phục hồi 

Khi có sự tham gia của phần mềm trung gian bên ngoài, lưu trữ đối tượng tự xây dựng hoặc cơ sở hạ tầng do khách hàng duy trì, chiến lược sao lưu và khôi phục hoàn toàn do phía khách hàng thực hiện. 

# 3. Xác nhận trước khi thực hiện khôi phục 

Khôi phục dữ liệu là một hoạt động rủi ro cao. Các xác nhận sau phải được thực hiện trước khi tiến hành. 

## 3.1 Xác nhận mục tiêu 

Trước khi khôi phục, làm rõ các thông tin sau: 

* Môi trường mục tiêu 

* Cụm, các nút mục tiêu, NAMESPACE 

* Phạm vi dữ liệu cần khôi phục 

* Điểm thời gian khôi phục 

* Khung thời gian thực hiện 

## 3.2 Xác nhận rủi ro

Xác nhận các mục sau trước khi khôi phục:

* Khôi phục này có ghi đè dữ liệu trực tuyến hiện tại không

* Khôi phục này có yêu cầu ngừng hoạt động không

* Bản sao lưu mới nhất đã được thêm vào dữ liệu trực tuyến hiện tại chưa

* Điểm khôi phục sau khi khôi phục thất bại đã được xác định chưa

## 3.3 Xác nhận tính hợp lệ của sao lưu

Kiểm tra các mục sau trước khi khôi phục:

* Các tệp sao lưu đầy đủ và có thể đọc được

* Thời điểm sao lưu đáp ứng mục tiêu khôi phục

* Thư mục sao lưu được gắn đúng cách

* Tất cả tệp cấu hình cần thiết cho khôi phục đều đầy đủ

* Các tệp sao lưu đã vượt qua xác minh khả năng khôi phục

# 4. Chiến lược sao lưu

## 4.1 Sao lưu cơ sở dữ liệu

Tiêu chí sao lưu cơ sở dữ liệu như sau:

|Tình huống|Phương pháp thực hiện|Tần suất|Thời gian lưu trữ|Mô tả|
|:----|:----|:----|:----|:----|
|Sử dụng ShimoDocs phần mềm trung gian tích hợp sẵn|Sao lưu theo lịch hệ thống|Mỗi ngày một lần|7 ngày|Thực hiện bởi các tác vụ theo lịch trong cụm|
|Sử dụng phần mềm trung gian do khách hàng tự duy trì|Sao lưu của phía khách hàng|Một lần mỗi ngày hoặc nhiều hơn|7 ngày trở lên|Thực hiện theo chính sách của phía khách hàng|



Sao lưu cơ sở dữ liệu phải bao gồm ít nhất:

* MySQL

* MongoDB

* Redis

## 4.2 Sao lưu Object Storage

Tiêu chí sao lưu object storage như sau:

|Loại dữ liệu|Phương pháp thực hiện|Tần suất|Thời gian lưu trữ|Mô tả|
|:----|:----|:----|:----|:----|
|Dữ liệu kinh doanh lưu trữ đối tượng|Sao lưu lạnh hoặc nhân bản khôi phục thảm họa|Thực hiện theo cấp độ kinh doanh|Thực hiện theo chính sách của khách hàng|Bao gồm tài liệu đính kèm và các đối tượng tệp|
|Dữ liệu cấu hình object storage|Sao lưu cấu hình|Sao lưu đồng bộ sau khi có thay đổi|Thực hiện theo chính sách của khách hàng|Bao gồm các tham số truy cập và thông tin gắn kết|



Nhiều bản sao trong object storage là một phần của cơ chế dư thừa của cụm và không tương đương với sao lưu dữ liệu.

## 4.3 Sao lưu tệp cấu hình

Các cấu hình sau được bao gồm trong phạm vi sao lưu:

* Tham số cài đặt

* Cấu hình tên miền và giao thức

* Địa chỉ phụ thuộc bên ngoài và thông tin cổng

* Thông tin truy cập object storage 

* Các tệp cấu hình liên quan đến nghiệp vụ 

# 5. Khôi phục cơ sở dữ liệu 

Phần này áp dụng cho tất cả các khôi phục dữ liệu cho MySQL, MongoDBvà Redis. 

## 5.1 Chuẩn bị trước khi khôi phục 

Hoàn thành các bước chuẩn bị sau trước khi thực hiện khôi phục cơ sở dữ liệu: 

* Chuẩn bị thư mục khôi phục trên nút đích, ví dụ, `/data/restore` 

* Đặt dữ liệu cần khôi phục vào thư mục khôi phục 

* Xác minh rằng cấu hình middleware trong `global_config.json` tệp phù hợp với môi trường hiện tại 

* Kiểm tra nút khôi phục, điểm khôi phục, cửa sổ thực thi và thông tin phê duyệt 

## 5.2 Kiểm tra tác vụ sao lưu 

Kiểm tra các tác vụ sao lưu cơ sở dữ liệu đã lập lịch: 

```plain
kubectl get cronjob
```


Cũng ghi lại các thông tin sau:

* Tên CronJob

* Thời gian thực hiện lần cuối

* Kết quả thực hiện gần nhất

* Thư mục lưu trữ tệp sao lưu

## 5.3 Tiếp tục thực hiện

Khôi phục cơ sở dữ liệu được thực hiện thông qua một công việc một lần, và kịch bản khôi phục nằm trong ảnh sao lưu.

Các bước thực hiện như sau:

1. Chuẩn bị danh sách nhiệm vụ khôi phục `db-restore.yaml`

2. Chỉnh sửa `spec.template.spec.nodeName` tới nút nơi thư mục khôi phục được đặt

3. Chỉnh sửa `hostPath.path` tới thư mục nơi dữ liệu được khôi phục

4. Thực hiện lệnh `kubectl apply -f db-restore.yaml` để thực hiện khôi phục dữ liệu

Danh sách nhiệm vụ mẫu như sau:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    job-name: db-restore
  name: db-restore
spec:
  template:
    metadata:
      labels:
        job-name: db-restore
      name: db-restore
    spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - cd /data/pri-init/scripts/backup && sh restore_all.sh
        image: registryo.shimo.im/smbase/backup:co
        imagePullPolicy: Always
        name: db-restore
        volumeMounts:
        - name: db-config
          mountPath: /data/pri-init/scripts/global_config.json
          subPath: global_config.json
        - name: data
          mountPath: /backup
      dnsPolicy: ClusterFirst
      nodeName: master-1
      volumes:
      - name: db-config
        configMap:
          name: init-invoker
          items:
          - key: global_config.json
            path: global_config.json
      - name: data
        hostPath:
          path: /data/restore
      imagePullSecrets:
      - name: ee
      restartPolicy: Never
      schedulerName: default-scheduler
```


## 5.4 Hướng dẫn thực hiện

Sau khi nhiệm vụ khôi phục cơ sở dữ liệu được thực hiện, dữ liệu sau đây sẽ được quay lại:

* MySQL

* MongoDB

* Redis

Trong thời gian khôi phục, dữ liệu kinh doanh có thể bị ghi đè. Hoàn tất sắp xếp tắt hoàn toàn và xác minh dữ liệu trước khi thực hiện.

# 6. Khôi phục lưu trữ đối tượng

Phần này áp dụng cho MinIO và S3-khôi phục lưu trữ đối tượng tương thích.

## 6.1 Phương pháp sao lưu

Các phương pháp sao lưu phổ biến cho lưu trữ đối tượng như sau:

|Phương pháp|Kịch bản áp dụng|Mô tả|
|:----|:----|:----|
|Sao chép đồng bộ Rsync|Môi trường độc lập|Phù hợp cho sao lưu lạnh cấp thư mục|
|Snapshot đĩa|Môi trường độc lập|Phù hợp cho khôi phục nhanh trên cùng nền tảng lưu trữ|
|`mc mirror`|Môi trường độc lập hoặc cụm|Phù hợp cho sao lưu lạnh và khôi phục dữ liệu đối tượng|
|Sao chép Site / Sao chép Bucket|Môi trường cụm|Phù hợp cho sao chép khôi phục thảm họa|



## 6.2 Tiếp tục thực hiện

Các phương pháp khôi phục thường được sử dụng trong môi trường độc lập như sau:

* Khi sử dụng Rsync để sao lưu, thực hiện đồng bộ ngược để khôi phục thư mục dữ liệu

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* Khi sử dụng `mc mirror` để sao lưu, thực hiện khôi phục gương ngược

```plain
mc mirror backup-minio/ new-minio/
```


Hướng dẫn phục hồi cho môi trường cụm như sau:

* Khi có bản sao khôi phục thảm họa, thực hiện phục hồi theo kế hoạch chuyển đổi chính-dự phòng

* Khi sử dụng sao lưu lạnh, thực hiện phục hồi theo thư mục dữ liệu lưu trữ đối tượng hoặc nội dung của kho ảnh

## 6.3 Hướng dẫn thực hiện

Trước khi khôi phục lưu trữ đối tượng, cần xác nhận các vấn đề sau:

* Phạm vi bucket mục tiêu để khôi phục

* Điểm khôi phục

* Có ghi đè đối tượng trực tuyến hay không

* Đường dẫn lưu trữ mục tiêu và cấu hình quyền

* ACCESS_DOMAIN và cấu hình gateway sau khi khôi phục

# 7. Kiểm tra sau khi khôi phục

Sau khi khôi phục xong, ít nhất xác minh các nội dung sau:

* Trạng thái dịch vụ cơ sở dữ liệu bình thường

* Trạng thái dịch vụ lưu trữ đối tượng bình thường

* Quản lý được thông qua bảng điều khiển quản trị

* Người dùng đăng nhập bình thường

* Các tài liệu cốt lõi có thể được tạo, chỉnh sửa, lưu, nhập và xuất bình thường

* Điểm khôi phục dữ liệu phù hợp với mong đợi

Ghi lại các thông tin sau sau khi hoàn thành khôi phục:

* Thời gian thực hiện khôi phục

* Điểm thời gian khôi phục dữ liệu

* Người thực hiện, Người phê duyệt, Người kiểm tra

* Các vấn đề phát hiện sau khi khôi phục
