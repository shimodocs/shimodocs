# Dameng V8 Yêu cầu

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Tài liệu này nhằm hướng dẫn nhân sự đang triển khai, duy trì hoặc tích hợp Dameng Cơ sở dữ liệu lần đầu tiên, để hoàn thành Dameng DM8 việc khởi tạo cơ sở dữ liệu, MySQL cấu hình chế độ tương thích, khởi động dịch vụ và kiểm tra kết nối từng bước.

Các ví dụ trong tài liệu này sử dụng kế hoạch sau:

| Mục | Giá trị ví dụ |
| --- | --- |
| Thư mục cài đặt cơ sở dữ liệu | `/opt/dmdbms` |
| Thư mục lưu trữ cơ sở dữ liệu | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| Tên phiên bản | `DBSERVER` |
| Cổng cơ sở dữ liệu | `5236` |
| Tài khoản quản trị | `SYSDBA` |
| Quản trị viên PASSWORD | `<SYSDBA_PASSWORD>` |

> Lưu ý: `<SYSDBA_PASSWORD>` và `<SYSAUDITOR_PASSWORD>` trong tài liệu là các vị trí giữ chỗ. Trong quá trình vận hành thực tế, vui lòng thay thế chúng bằng mật khẩu thực thỏa mãn PASSWORD yêu cầu về độ phức tạp.

## 1. Xác nhận trước khi vận hành

### 1. Xác nhận rằng Dameng đã được cài đặt

Thực thi trên máy chủ:

```bash
ls /opt/dmdbms/bin
```

Nếu bạn có thể thấy các tệp như `dminit`, `dmserver`, `disql`, điều đó cho thấy Dameng phần mềm đã được cài đặt.

Bạn cũng có thể kiểm tra phiên bản:

```bash
/opt/dmdbms/bin/dmserver
```

Nội dung như thế này có thể xuất hiện trong kết quả:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. Xác nhận người dùng hệ thống

Dameng thường chạy cơ sở dữ liệu bằng `dmdba` người dùng. Kiểm tra xem người dùng có tồn tại không:

```bash
id dmdba
```

Nếu không tồn tại, có thể được tạo bởi `root` người dùng:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. Chuẩn bị thư mục dữ liệu

Thực hiện bằng `root` người dùng:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

Mục đích của bước này là tạo một thư mục để lưu trữ các tệp cơ sở dữ liệu và cấp quyền cho `dmdba` người dùng.

## 2. Khởi tạo Cơ sở dữ liệu

Chuyển sang `dmdba` người dùng:

```bash
su - dmdba
```

Thực hiện lệnh khởi tạo: 

```bash
/opt/dmdbms/bin/dminit \
  PATH=/dmdata/data \
  PAGE_SIZE=32 \
  EXTENT_SIZE=32 \
  CASE_SENSITIVE=0 \
  UNICODE_FLAG=1 \
  DB_NAME=DMTEST \
  INSTANCE_NAME=DBSERVER \
  PORT_NUM=5236 \
  SYSDBA_PWD=<SYSDBA_PASSWORD> \
  SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Nếu khởi tạo thành công, bạn sẽ thấy kết quả tương tự: 

```text
create dm database success
```
Sau khi khởi tạo thành công, thư mục cơ sở dữ liệu được tạo ra: 

```text
/dmdata/data/DMTEST
```

Tệp cấu hình quan trọng trong số đó là:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. Chỉnh sửa MySQL Cấu hình tương thích

Chỉnh sửa tệp cấu hình bằng cách sử dụng `root` hoặc `dmdba` người dùng:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

Tìm và sửa đổi hai cấu hình sau: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

Nếu tệp đã có hai cấu hình này, bạn có thể chỉnh sửa trực tiếp các dòng hiện có.

Không thêm một cấu hình khác cùng tên vào cuối tệp, nếu không, có thể xảy ra trùng lặp cấu hình, khiến giá trị thực tế khác với mong đợi.

Sau khi hoàn tất sửa đổi, bạn có thể kiểm tra bằng lệnh sau:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

Mong đợi sẽ thấy:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. Đăng ký dịch vụ cơ sở dữ liệu

Chuyển trở lại `root` người dùng:

```bash
exit
```

Đăng ký dịch vụ cơ sở dữ liệu: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

Sau khi đăng ký thành công, tên dịch vụ thường là:

```text
DmServiceDBSERVER.service
```

Đặt khởi động cùng hệ thống và khởi động dịch vụ: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

Kiểm tra trạng thái dịch vụ: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Nếu bạn thấy: 

```text
Active: active (running)
```

Cho biết rằng dịch vụ cơ sở dữ liệu đã được khởi động. 

## 5. Kiểm tra xem cơ sở dữ liệu có sẵn hay không 

### 1. Kiểm tra Cổng 

Thực hiện: 

```bash
ss -lntp | grep ':5236'
```

Nếu bạn thấy `dmserver` lắng nghe trên `5236`, điều đó cho thấy cổng cơ sở dữ liệu bình thường.

### 2. Kiểm tra đăng nhập tại chỗ

Chuyển sang `dmdba` người dùng:

```bash
su - dmdba
```

Đăng nhập vào cơ sở dữ liệu: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

Thực hiện sau khi đăng nhập thành công:

```sql
select 1 as OK;
```

Nếu trả về: 

```text
OK
-----------
1
```

Cho biết kết nối cơ sở dữ liệu bình thường. 

### 3. Kiểm tra xem có phải MySQL chế độ tương thích

Thực hiện trong `disql`: 

```sql
select para_name, para_value
from v$dm_ini
where para_name in (
  'COMPATIBLE_MODE',
  'ORDER_BY_NULLS_FLAG',
  'INSTANCE_NAME',
  'PORT_NUM'
);
```

Kết quả mong đợi: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

Trong đó:

```text
COMPATIBLE_MODE = 4
```

Cho biết trạng thái chạy hiện tại của cơ sở dữ liệu đã bật MySQL chế độ tương thích. 



## Phụ lục 1, Mô tả chi tiết về các mục cấu hình 

### 1. `PATH` 

Ví dụ: 

```text
PATH=/dmdata/data
```

Ý nghĩa: 

`PATH` là thư mục gốc của các tệp cơ sở dữ liệu. Trong quá trình khởi tạo, Dameng sẽ tạo thư mục cơ sở dữ liệu dưới thư mục này.

Nếu `DB_NAME=DMTEST`, thư mục cuối cùng thường là: 

```text
/dmdata/data/DMTEST
```

Thư mục này sẽ lưu trữ các tệp dữ liệu, tệp nhật ký, tệp điều khiển và tệp `dm.ini` cấu hình.

Khuyến nghị:

- Nên đặt nó trên một ổ đĩa dữ liệu có dung lượng đủ và hiệu suất ổn định trong môi trường sản xuất.
- Không nên đặt nó trong các thư mục tạm thời, chẳng hạn như `/tmp`.
- Không di chuyển thư mục tùy tiện sau khi khởi tạo.

### 2. `DB_NAME`

Ví dụ:

```text
DB_NAME=DMTEST
```

Ý nghĩa: 

`DB_NAME` là tên của DATABASE_NAME. Nó sẽ ảnh hưởng đến tên thư mục cơ sở dữ liệu, tên tệp nhật ký, v.v. 

Ví dụ, khi `DB_NAME=DMTEST`, nó thường tạo ra: 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

Khuyến nghị:

- Sử dụng một DATABASE_NAME rõ ràng duy nhất trong suốt dự án.
- Không nên thay đổi nó sau khi khởi tạo.

### 3. `INSTANCE_NAME`

Ví dụ:

```text
INSTANCE_NAME=DBSERVER
```

Ý nghĩa: 

`INSTANCE_NAME` là tên phiên bản cơ sở dữ liệu. Nó thường được dùng để tạo tên dịch vụ khi đăng ký một dịch vụ.

Ví dụ: 

```text
INSTANCE_NAME=DBSERVER
```

Tên dịch vụ sau khi đăng ký thường là:

```text
DmServiceDBSERVER.service
```

Khuyến nghị: 

- Đối với một máy với một phiên bản, bạn có thể sử dụng `DBSERVER`.
- Khi triển khai nhiều phiên bản trên một máy, mỗi tên phiên bản phải khác nhau.

### 4. `PORT_NUM`

Ví dụ: 

```text
PORT_NUM=5236
```

Ý nghĩa: 

`PORT_NUM` là cổng lắng nghe của cơ sở dữ liệu. Ứng dụng cần truy cập cổng này khi kết nối với cơ sở dữ liệu. 

Cổng nhập trên trang chương trình phải nhất quán với cổng này: 

```text
HOST:172.17.9.84
PORT:5236
```

Khuyến nghị: 

- Cổng mặc định cho Dameng thường là `5236`. 
- Nếu có nhiều Dameng phiên bản trên cùng một máy, các cổng không được trùng lặp. 
- Sau khi thay đổi cổng, dịch vụ cơ sở dữ liệu cần được khởi động lại. 

### 5. `PAGE_SIZE` 

Ví dụ: 

```text
PAGE_SIZE=32
```

Ý nghĩa: 

`PAGE_SIZE` là kích thước trang của cơ sở dữ liệu, tính theo KB. Khi cơ sở dữ liệu đọc và ghi dữ liệu, nó tổ chức dữ liệu dựa trên các trang. 

`PAGE_SIZE=32` nghĩa là mỗi trang dữ liệu có dung lượng 32KB. 

Tác động: 

- Nó ảnh hưởng đến lưu trữ dữ liệu, lập chỉ mục và hành vi IO. 
- Không khuyến nghị thay đổi sau khi khởi tạo. 
- Nếu cần điều chỉnh, thường yêu cầu khởi tạo lại cơ sở dữ liệu và di chuyển dữ liệu. 

Khuyến nghị: 

- Nếu có SOP cho kịch bản, cấu hình theo SOP. 
- Khi không có yêu cầu đặc biệt, không thay đổi tùy ý. 

### 6. `EXTENT_SIZE` 

Ví dụ: 

```text
EXTENT_SIZE=32
```

Ý nghĩa: 

`EXTENT_SIZE` là kích thước cụm, đo bằng trang. Nó có thể được hiểu là đơn vị cơ bản của phân bổ không gian mà cơ sở dữ liệu sử dụng cùng một lúc.

Nếu: 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

Thì một cụm khoảng: 

```text
32KB * 32 = 1024KB
```

Khoảng 1MB. 

Tác động: 

- Sẽ ảnh hưởng đến độ chi tiết của phân bổ không gian tệp dữ liệu. 
- Không khuyến nghị thay đổi sau khi khởi tạo. 

### 7. `CASE_SENSITIVE` 

Ví dụ: 

```text
CASE_SENSITIVE=0
```

Ý nghĩa: 

`CASE_SENSITIVE` chỉ ra liệu tên đối tượng cơ sở dữ liệu có phân biệt chữ hoa chữ thường hay không.

Các giá trị phổ biến: 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

Ví dụ, khi không phân biệt chữ hoa chữ thường, hai tên bảng sau có thể được coi là cùng một đối tượng:

```text
user
USER
```

Tác động: 

- Sẽ ảnh hưởng đến việc nhận diện tên bảng, tên trường và tên đối tượng. 
- Đối với MySQL di chuyển hoặc MySQLkịch bản tương thích-, thường ưu tiên cấu hình là `0`. 
- Không khuyến nghị thay đổi sau khi khởi tạo. 

### 8. `UNICODE_FLAG` 

Ví dụ: 

```text
UNICODE_FLAG=1
```

Ý nghĩa: 

`UNICODE_FLAG` là cấu hình bộ ký tự.

Các giá trị phổ biến: 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` chỉ ra rằng cơ sở dữ liệu sử dụng UTFbộ ký tự -8.

Khuyến nghị:

- Khuyến nghị sử dụng UTF-8 cho hệ thống mới.
- Tương thích tốt hơn với ký tự tiếng Trung, tiếng Anh và đa ngôn ngữ.
- Không khuyến nghị thay đổi sau khi khởi tạo.

### 9. `SYSDBA_PWD`

Ví dụ:

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

Ý nghĩa: 

`SYSDBA_PWD` là PASSWORD cho `SYSDBA` tài khoản quản trị viên.

`SYSDBA` tương tự như quản trị viên siêu cấp cơ sở dữ liệu và có quyền hạn cao.

Khuyến nghị: 

- Sử dụng một PASSWORD.
- Không sử dụng mật khẩu đơn giản như `SYSDBA`, `123456`, `password`.
- PASSWORD độ dài nên ít nhất 8 ký tự và bao gồm cả chữ cái và số.
- Không viết mật khẩu thực tế PASSWORD trong tài liệu bên ngoài.

### 10. `SYSAUDITOR_PWD`

Ví dụ: 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Ý nghĩa: 

`SYSAUDITOR_PWD` là PASSWORD của `SYSAUDITOR` tài khoản quản trị kiểm toán.

`SYSAUDITOR` chủ yếu được sử dụng cho các khả năng quản lý liên quan đến kiểm toán.

Khuyến nghị:

- Sử dụng một PASSWORD khác với `SYSDBA`.
- Sử dụng một PASSWORD đáp ứng các yêu cầu về độ phức tạp.

### 11. `COMPATIBLE_MODE`

Ví dụ:

```text
COMPATIBLE_MODE = 4
```

Ý nghĩa: 

`COMPATIBLE_MODE` là cấu hình chế độ tương thích của Dameng Cơ sở dữ liệu, được sử dụng để kiểm soát cơ sở dữ liệu tương thích với loại cơ sở dữ liệu nào về mặt SQL cú pháp, các hàm và một số hành vi nhất định.

Ý nghĩa các giá trị thường gặp: 

```text
0:DEFAULT_MODE
1:SQL92
2:Oracle
3:MS SQL Server
4:MySQL
5:DM6
6:Teradata
7:PostgreSQL
8:DB2
```

Văn bản này được cấu hình như sau: 

```text
COMPATIBLE_MODE = 4
```

Chỉ thị bật MySQL chế độ tương thích. 

Chức năng: 

- Cải thiện khả năng tương thích của MySQL SQL cú pháp trong Dameng. 
- Giảm chi phí chuyển đổi khi di chuyển từ MySQL hoặc thích ứng với Dameng. 

Lưu ý: 

- Cấu hình này không có nghĩa là Dameng hỗ trợ MySQL giao thức. 
- Các chương trình vẫn cần sử dụng Dameng trình điều khiển bên trong; nếu không có tùy chọn cấu hình trình điều khiển trên trang, người dùng không cần điền riêng. 
- Cần khởi động lại dịch vụ cơ sở dữ liệu sau khi sửa đổi. 
- Việc có hiệu lực hay không cuối cùng nên dựa trên `v$dm_ini` kết quả truy vấn. 

### 12. `ORDER_BY_NULLS_FLAG` 

Ví dụ: 

```text
ORDER_BY_NULLS_FLAG = 0
```

Ý nghĩa: 

`ORDER_BY_NULLS_FLAG` được dùng để kiểm soát việc NULL các giá trị xuất hiện ở đầu hay cuối khi sắp xếp với `ORDER BY`. 

Tại sao điều này quan trọng: 

Các cơ sở dữ liệu khác nhau có thể có hành vi mặc định khác nhau khi sắp xếp các giá trị NULL. Khi di chuyển một ứng dụng từ MySQL sang Dameng, nếu kết quả sắp xếp phụ thuộc vào vị trí của NULL, tham số này có thể ảnh hưởng đến thứ tự kết quả truy vấn. 

Bài viết này được cấu hình như sau: 

```text
ORDER_BY_NULLS_FLAG = 0
```

Mục đích là để làm cho hành vi sắp xếp gần hơn với MySQL thói quen sử dụng.

Lưu ý:

- Cần khởi động lại dịch vụ cơ sở dữ liệu sau khi sửa đổi.
- Nếu doanh nghiệp SQL đã chỉ rõ `NULLS FIRST` hoặc `NULLS LAST`, hành vi được chỉ định trong SQL nên được ưu tiên.

## Phụ lục 2, Các câu hỏi thường gặp

### 1. Tại sao tôi không thể kết nối bằng MySQL một client ngay cả sau khi đã đặt MySQL chế độ tương thích?

Bởi vì MySQL chế độ tương thích chỉ ảnh hưởng đến SQL cú pháp và một số hành vi cơ sở dữ liệu, nó không thay đổi Damenggiao thức mạng của '.

Khi các ứng dụng hoặc công cụ kết nối với Dameng, vẫn cần sử dụng Dameng driver:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

không thể sử dụng: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. Làm thế nào để xác nhận rằng cấu hình thực sự có hiệu lực?

Đừng chỉ nhìn vào `dm.ini` file; nên đăng nhập vào cơ sở dữ liệu để kiểm tra trạng thái chạy:

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

Trạng thái đang chạy có hiệu lực chỉ khi thấy các kết quả sau: 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. Tại sao nó không có hiệu lực sau khi chỉnh sửa `dm.ini`?

Những lý do phổ biến:

- Dịch vụ cơ sở dữ liệu đã không được khởi động lại sau khi sửa đổi.
- Có các mục cấu hình trùng lặp trong tệp.
- Tệp đã sửa đổi không phải là `dm.ini` tệp đang được phiên bản hiện tại sử dụng.

Bạn có thể xác nhận tệp cấu hình mà phiên bản hiện tại đang sử dụng thông qua lệnh khởi động dịch vụ:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

Bạn sẽ thường thấy điều gì đó giống như sau trong kết quả đầu ra:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. Tôi nên làm gì nếu PASSWORD một lỗi phức tạp xảy ra trong quá trình khởi tạo?

Chỉ ra rằng PASSWORD quá đơn giản. Vui lòng thay đổi sang PASSWORD, ví dụ:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5 phức tạp hơn. Các tham số này có thể được thay đổi sau không? 

Không. 

Các tham số khởi tạo nói chung không được khuyến nghị thay đổi sau, ví dụ: 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

Nếu các tham số này bị cấu hình sai, thường nên khởi tạo lại cơ sở dữ liệu và di chuyển dữ liệu một lần nữa. Tham số 

'dm.ini' có thể được điều chỉnh sau, ví dụ: 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

Tuy nhiên, sau khi sửa đổi, dịch vụ cơ sở dữ liệu thường cần được khởi động lại. 

## Phụ lục 3: Danh sách kiểm tra cuối cùng 


- Thư mục dữ liệu '/dmdata/data' đã được tạo. 
- Máy chủ thư mục dữ liệu là 'dmdba:dinstall'. 
- Cơ sở dữ liệu đã được khởi tạo thành công. 
- '/dmdata/data/DMTEST/dm.ini' tồn tại. 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- Dịch vụ cơ sở dữ liệu `DmServiceDBSERVER.service` đang `active`. 
- Cổng `5236` đang được lắng nghe. 
- `SYSDBA` có thể đăng nhập vào cơ sở dữ liệu. 
- Trong `v$dm_ini`, giá trị thời gian chạy của `COMPATIBLE_MODE` đang `4`.
