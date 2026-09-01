# Quản lý người thuê

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

> [!TIP]
>
> Quản lý Người thuê được sử dụng cho việc quản lý tập trung các người thuê trong ShimoDocs Suite.
> Quản trị viên có thể xem số lượng người thuê và việc sử dụng chỗ ngồi ở đây, lấy thông tin đăng nhập tích hợp với bên thứ ba, quản lý cấu hình AI, cũng như tạo, chỉnh sửa, bật hoặc tắt người thuê.
>

## 1. Truy cập Quản lý Người thuê

1. Đăng nhập vào **MDP Nền tảng Vận hành**.
2. Chọn **ShimoDocs Suite** ở phía trên.
3. Chọn **Quản lý người thuê** trong thanh điều hướng bên trái.

## 2. Hiểu Trang Quản lý Người thuê

Thông tin tổng thể của hệ thống hiện tại được hiển thị ở đầu trang:

| Khu vực | Mô tả |
| --- | --- |
| API-KEY | Xem và sao chép `AppID` và `AppSecret` cần thiết cho tích hợp bên thứ ba. |
| Cài đặt AI | Kiểm tra xem các khả năng AI có được bật hay không và truy cập các cấu hình mô hình AI và tìm kiếm. |
| Tổng số người thuê | Số lượng người thuê đã được tạo trong hệ thống hiện tại. |
| Người thuê đã bật | Số lượng người thuê hiện đang bật. |
| Sử dụng chỗ ngồi | Số lượng chỗ ngồi đã sử dụng, tổng số chỗ ngồi trong hệ thống và tỷ lệ sử dụng. |

Danh sách người thuê bên dưới hiển thị tên người thuê, thời gian kích hoạt, quản trị viên người thuê, việc sử dụng chỗ ngồi và trạng thái hiện tại. Trong cột 'Hành động', bạn có thể chỉnh sửa hoặc tắt người thuê tương ứng.

## 3. Xem API Chìa khóa

`AppID` và `AppSecret` được sử dụng để xác thực khi ShimoDocs Suite tích hợp với hệ thống bên thứ ba.

### Các bước thao tác

1. Tìm thẻ **API-KEY** thẻ trên trang quản lý người thuê.
2. Nhấp vào biểu tượng sao chép.
3. Hệ thống sẽ sao chép môi trường hiện tại `AppID` và `AppSecret`.
4. Nhập thông tin xác thực vào cấu hình tích hợp bên thứ ba tương ứng.

> `AppSecret` là thông tin xác thực nhạy cảm. Vui lòng giữ an toàn và không ghi vào tài liệu công khai, nhật ký chat hoặc kho mã nguồn công khai.

## 4. Quản lý cấu hình AI

Thẻ cấu hình AI hiển thị trạng thái kích hoạt khả năng AI hiện tại.

Sau khi nhấp vào thẻ cấu hình AI, bạn có thể xem hoặc chỉnh sửa các nội dung sau trên trang "Cấu hình Mô hình AI và Tìm kiếm": 

### 1. Cấu hình Mô hình Cơ bản

Dùng để cấu hình các mô hình ngôn ngữ lớn (LLM) chung và các mô hình có sẵn của chúng. Trên trang này, bạn có thể xem thông tin như nhà cung cấp, khóa yêu cầu URL, API mặc định, mô hình, ID mô hình, cửa sổ ngữ cảnh và khả năng đầu vào.

### 2. Cấu hình Mô hình Hình ảnh

Dùng để cấu hình các mô hình tạo hoặc chỉnh sửa hình ảnh. Trên trang này, bạn có thể xem nhà cung cấp, tên mô hình, Khóa Cơ bản URL, API và các khả năng hình ảnh được hỗ trợ.

### 3. Cấu hình Công cụ Tìm kiếm Mạng

Dùng để cấu hình dịch vụ tìm kiếm mạng AI. Trên trang này, bạn có thể xem nhà cung cấp dịch vụ, địa chỉ giao diện, API khóa, và thời gian chờ.

### 4. Cấu hình Nhà cung cấp Embedding

Dùng để cấu hình dịch vụ vector hóa văn bản. Trên trang này, bạn có thể xem Khóa Cơ bản, URL, API Mô hình Embedding, và số chiều vector.

> Trước khi chỉnh sửa cấu hình AI, vui lòng xác nhận trước địa chỉ dịch vụ, API key, ID mô hình và các tham số khả năng đều chính xác. Sau khi sửa đổi, nên sử dụng một lượng nhỏ nội dung thử nghiệm để xác minh xem việc gọi mô hình có hoạt động đúng hay không.

### 5. Sử dụng AI trong ShimoDocs Suite
Sau khi cấu hình xong, bạn có thể sử dụng các tính năng AI trong ShimoDocs Suite.

## 5. Quản lý các khách thuê hiện có

Trong danh sách người thuê, bạn có thể xem thông tin cơ bản và việc sử dụng chỗ ngồi của từng người thuê.

### Chỉnh sửa Người thuê

1. Tìm người thuê cần điều chỉnh.
2. Nhấp vào 'Chỉnh sửa' trong cột 'Hành động'.
3. Chỉnh sửa thông tin người thuê hoặc số chỗ ngồi theo hướng dẫn trên trang.
4. Lưu các thay đổi và quay lại danh sách để xác nhận rằng thông tin đã được cập nhật.

### Vô hiệu hóa hoặc Khôi phục Người thuê

- Đối với những người thuê đang được kích hoạt, bạn có thể nhấp 'Vô hiệu hóa' trong cột 'Hành động'.
- Để khôi phục người thuê bị vô hiệu hóa, hãy kích hoạt lại nó trong các mục hành động của người thuê tương ứng.

> Việc vô hiệu hóa một người thuê sẽ ảnh hưởng đến quyền truy cập bình thường vào người thuê đó. Vui lòng xác nhận người thuê mục tiêu là chính xác trước khi tiếp tục, và lên lịch thực hiện theo nhu cầu sử dụng thực tế.

## 6. Kích hoạt Người thuê Mới

Trước khi kích hoạt người thuê, vui lòng kiểm tra trước việc sử dụng chỗ ngồi ở đầu trang để xác nhận rằng vẫn còn chỗ trống để phân bổ.

### Các bước vận hành

1. Nhấp vào 'Kích hoạt Người thuê Mới' ở góc trên bên phải của trang.
2. Nhập tên được sử dụng để nhận dạng người thuê này vào 'Tên Người thuê'.
3. Xác nhận email quản trị viên tenant do hệ thống tạo. Sau khi tenant được tạo thành công, vui lòng lưu tài khoản quản trị viên này và ban đầu PASSWORD ngay lập tức.
4. Kiểm tra "Số chỗ có thể phân bổ" để hiểu số lượng chỗ tối đa hiện có thể được phân bổ cho tenant mới.
5. Nhập số chỗ được phân bổ cho tenant này vào "Số chỗ được phân bổ cho Tenant."
6. Sau khi xác nhận thông tin chính xác, nhấp vào "Lưu."

### Mô tả trường chỗ ngồi

| Trường | Mô tả |
| --- | --- |
| Số chỗ có thể phân bổ | Số chỗ tối đa hiện có thể được hệ thống phân bổ cho tenant. |
| Số chỗ được phân bổ cho Tenant | Tổng số chỗ được phân bổ cho tenant này. Số này không thể nhỏ hơn số chỗ đã được tenant sử dụng. |
| Số chỗ đã sử dụng bởi Tenant | Số lượng thành viên doanh nghiệp đang hoạt động trong tenant này. Mỗi thành viên đang hoạt động chiếm một chỗ. |

> Cần phân bổ một số chỗ ngồi khi tạo người thuê. Số lượng chỗ ngồi có thể được điều chỉnh sau này theo mức sử dụng thực tế.

## 7. Đăng nhập lần đầu và thay đổi thông tin ban đầu PASSWORD

Sau khi người thuê được tạo thành công, vui lòng đăng nhập vào ShimoDocs Suite sử dụng tài khoản mặc định do hệ thống tạo hoặc tài khoản quản trị viên và ngay lập tức thay đổi thông tin ban đầu PASSWORD.

### 1. Mở ShimoDocs Suite

Truy cập trong trình duyệt:

```text
http://<ACCESS_DOMAIN>/
```

Nếu HTTPS Đã được cấu hình, vui lòng truy cập: 

```text
https://<ACCESS_DOMAIN>/
```

### 2. Đăng nhập vào ShimoDocs Suite

Nhập tài khoản quản trị viên và thông tin ban đầu PASSWORD được tạo khi thiết lập người thuê để hoàn tất đăng nhập.

### 3. Thay đổi thông tin ban đầu PASSWORD

Sau khi đăng nhập lần đầu, vui lòng làm theo hướng dẫn trên trang hoặc cài đặt bảo mật tài khoản để thay đổi thông tin ban đầu PASSWORD. Khi thông tin mới PASSWORD đã được thiết lập, vui lòng giữ gìn cẩn thận.

