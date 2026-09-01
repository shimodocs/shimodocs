# Bắt đầu nhanh

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

> [!TIP]
>
> Bài viết này giới thiệu cách sử dụng `mdp-installer` để nhanh chóng triển khai một môi trường mới của ShimoDocs Suite.
>
> Bài viết này được viết cho **Cài đặt trực tuyến đơn node All-in-One** kịch bản, phù hợp cho việc cài đặt lần đầu, trải nghiệm sản phẩm, xác minh chức năng và thực hành quy trình triển khai. Sau khi hoàn thành bài viết này, bạn có thể nhận được ShimoDocs Suite địa chỉ truy cập doanh nghiệp và MDP địa chỉ Nền tảng Vận hành.

> Địa chỉ IP, tên gói cài đặt, VERSIONvà các thư mục trên trang chỉ mang tính minh họa. Trong quá trình triển khai thực tế, vui lòng tham khảo môi trường hiện tại và các tài liệu đã cung cấp.

## 1. Tổng quan về Quy trình Triển khai

Toàn bộ quá trình có thể được chia thành 7 bước sau:

| Bước | Hành động cần hoàn thành | Chỉ số hoàn thành |
| --- | --- | --- |
| 1. Chuẩn bị Máy chủ và Tài liệu Cài đặt | Đảm bảo máy chủ, trình cài đặt và ShimoDocs Suite gói phân phối có sẵn | Có thể đăng nhập vào máy chủ và xác định vị trí các tệp cài đặt |
| 2. Khởi động Trình cài đặt | Chạy `mdp-installer` trên nút cài đặt | Terminal hiển thị địa chỉ truy cập trình cài đặt |
| 3. Tải lên Gói Phân phối | Chọn gói ShimoDocs Suite phân phối trong trình duyệt | Trang hiển thị gói phân phối đã vượt qua kiểm tra |
| 4. Cấu hình Môi trường Triển khai | Điền tên miền hoặc IP, chế độ triển khai, thông tin đăng nhập nút và các thư mục dữ liệu | Các nút được xác minh thành công và có thể truy cập tổng quan triển khai |
| 5. Kiểm tra Môi trường | Chờ trình cài đặt kiểm tra máy chủ và môi trường triển khai | Không có lỗi nào cản trở việc cài đặt |
| 6. Bắt đầu Cài đặt | Xác nhận kết quả kiểm tra và thực hiện triển khai | Trang hiển thị rằng cài đặt đã hoàn tất |
| 7. Lưu Thông tin Giao hàng | Lưu địa chỉ truy cập và hoàn tất đăng nhập, dịch vụ và kiểm tra chức năng | Các trang kinh doanh và MDP Nền tảng Vận hành có thể truy cập |

## 2. Chuẩn bị Trước Triển khai

### 1. Chuẩn bị Nút Cài đặt

Nút cài đặt được sử dụng để chạy trình cài đặt và đóng vai trò là máy chủ mục tiêu cho triển khai một nút All-in-One.

Trước khi bắt đầu, vui lòng xác nhận:

- Một máy chủ có thể sử dụng đã được chuẩn bị và SERVER_địa chỉ IP đã được lấy.
- Máy chủ có thể truy cập qua SSH.
- Người dùng SSH là `root`, hoặc có quyền cần thiết để thực hiện các tác vụ triển khai.
- Máy chủ CPU kiến trúc phù hợp với trình cài đặt và gói phân phối, ví dụ, cả hai đều là `x86_64`.
- Máy chủ đáp ứng các yêu cầu triển khai hiện tại; nên sử dụng cài đặt tối thiểu của Ubuntu 24.04 LTS.
- Phân vùng gốc và không gian dữ liệu đáp ứng các yêu cầu triển khai hiện tại, và thư mục dữ liệu đã được xác định.
- Thời gian máy chủ và múi giờ là chính xác, và đồng bộ thời gian bình thường.
- Máy tính có trình duyệt có thể truy cập cổng `18080/TCP` của nút cài đặt.
- Máy chủ có thể truy cập Internet để tải xuống gói triển khai và tài nguyên hình ảnh trực tuyến.
- Nếu truy cập kinh doanh sử dụng tên miền, việc phân giải tên miền đã được thực hiện trước (tùy chọn).

Các yêu cầu tối thiểu của máy chủ như sau:

| Hệ điều hành | Kiến trúc | CPU | Bộ nhớ | Ổ đĩa |
| --- | --- | --- | --- | --- |
| Ubuntu 24.04 LTS | `x86_64` | 16 Lõi | 32 GB | 100 GB SSD |

Ngoài ra, vui lòng xác nhận:

- Không phân vùng `/root`, `/var`, `/tmp` riêng biệt.
- Trước khi cài đặt, không triển khai các thành phần bổ sung như Docker hoặc Kubernetes trên máy chủ có thể ảnh hưởng đến kiểm tra của trình cài đặt.
- Cổng `22/TCP` được sử dụng cho SSH, `18080/TCP` được sử dụng cho trang web cài đặt, `80/TCP` và `443/TCP` được sử dụng để truy cập kinh doanh.

> Trước khi triển khai chính thức, nên xác nhận thông số máy chủ theo độ đồng thời thực tế, kích thước tập tin và yêu cầu khả dụng; phương pháp nút đơn trong tài liệu này phù hợp cho triển khai và kiểm tra nhanh, đối với hoạt động lâu dài hoặc khả năng sẵn sàng cao, vui lòng sử dụng phương án triển khai cụm tương ứng.

### 2. Chuẩn bị Tài liệu Cài đặt

#### Lấy Trình cài đặt

Tải lên trình cài đặt do ShimoDocs cung cấp đến thư mục `/root/` của nút cài đặt. Bạn có thể chọn bất kỳ phương pháp nào sau đây:

- **Phương pháp 1: Tải lên qua SSH công cụ**. Tải lên `mdp-installer-amd64` cung cấp đến thư mục `/root/` thư mục của nút cài đặt.

#### Nhận Gói Phân Phối

Chuẩn bị ShimoDocs Suite gói phân phối cho việc triển khai này. Gói phân phối được tải lên trên trang cài đặt web, và tên file cũng như phiên bản nên tuân theo sản phẩm thực tế.

Ví dụ tên file: `co1.8.20260711.3286-drive-release.tar.gz`

Danh sách vật liệu như sau:

| File | Mô tả |
| --- | --- |
| `mdp-installer` Trình cài đặt | Chọn file tương ứng theo kiến trúc máy chủ, ví dụ, `mdp-installer-amd64`. |
| ShimoDocs Suite Gói Phân Phối | Tên file và phiên bản nên theo sản phẩm thực tế, ví dụ, `co1.8.20260711.3286-drive-release.tar.gz`. |

Nên đặt trình cài đặt và các tài liệu liên quan trong cùng một thư mục làm việc để dễ dàng truy xuất và lưu trữ sau này. Trước khi sử dụng gói phân phối, vui lòng đảm bảo các file đầy đủ và không bị hỏng do công cụ truyền tải.

## 3. Khởi chạy Trình Cài Đặt

### 1. Đăng nhập vào Nút Cài Đặt

Đăng nhập vào nút cài đặt qua SSH và điều hướng tới thư mục chứa trình cài đặt. Ví dụ:

```bash
ssh root@<INSTALL_NODE_IP>
cd /root
```

### 2. Thêm Quyền Thực Thi

Nếu trình cài đặt chưa có quyền thực thi, chạy:

```bash
chmod +x ./mdp-installer-amd64
```
Tên file trong lệnh cần được thay bằng tên trình cài đặt thực tế. 

### 3. Khởi chạy trang cài đặt web 

Chạy: 

```bash
./mdp-installer-amd64 server
```

Nếu bạn cần trình cài đặt tiếp tục chạy sau khi terminal hiện tại thoát, bạn có thể sử dụng: 

```bash
nohup ./mdp-installer-amd64 server > nohup.out 2>&1 &
```

Sau khi khởi chạy thành công, terminal sẽ hiển thị hai địa chỉ: 

- `Local`: Chỉ dành cho nút cài đặt sử dụng. 
- `Network`: Truy cập bởi các máy tính khác trên cùng mạng. 

Nếu khởi chạy ở chế độ nền, bạn có thể chạy lệnh sau để xem kết quả đầu ra của trình cài đặt: 

```bash
cat nohup.out
```

Mở `Network` địa chỉ hiển thị trong terminal trên trình duyệt, ví dụ:

```text
http://<INSTALL_NODE_IP>:18080/
```

> Trong quá trình cài đặt, vui lòng giữ cho tiến trình cài đặt đang chạy. Không đóng tiến trình cài đặt hoặc dừng dịch vụ hiện tại.

## 4. Tải lên ShimoDocs Suite Gói Phát Hành

### 1. Chọn Gói Phát Hành

Sau khi vào trang cài đặt:

1. Nhấp **Bắt đầu Triển khai** hoặc mục chọn gói phát hành trên trang.
2. Chọn ShimoDocs Suite `.tar.gz` gói phát hành sẽ được sử dụng cho lần triển khai này.
3. Chờ quá trình tải lên và xác minh file hoàn tất.

### 2. Xác nhận Kết quả Xác minh

Sau khi xác minh thành công, trang sẽ hiển thị tên gói phát hành và mục cấu hình triển khai.

Vui lòng xác nhận thông tin sau là chính xác:

- Tên gói trùng khớp với phiên bản được giao lần này.
- Gói phát hành thuộc về ShimoDocs Suite sản phẩm.
- Trang không hiển thị lỗi hỏng file, lỗi định dạng hoặc không khớp cấu trúc.

Sau khi xác nhận, nhấp **Tiếp tục** để chuyển sang cấu hình triển khai.

Nếu việc xác minh thất bại, vui lòng kiểm tra lại gói phân phối có đầy đủ, loại file đúng và gói phân phối có phù hợp với kiến trúc máy chủ không. CPU 

## 5. Cấu hình Môi trường Triển khai

### 1. Xác nhận địa chỉ mạng

Kiểm tra hostname hoặc địa chỉ IP được xác định trên trang. Địa chỉ này nên là địa chỉ node cài đặt mà người dùng có thể truy cập bình thường.

Không nhập `127.0.0.1`, và không sử dụng các địa chỉ tạm thời chỉ có thể truy cập được từ máy tính hiện tại. Khi truy cập qua tên miền, vui lòng đảm bảo rằng tên miền đã được phân giải tới mục dịch vụ chính xác.

### 2. Chọn Chế Độ Một Nút Đơn Tất Cả Trong Một

Chọn **Một Nút Đơn Tất Cả Trong Một** trong chế độ triển khai hoặc môi trường mục tiêu (tên hiển thị thực tế trên trang phụ thuộc vào phiên bản hiện tại).

Trong chế độ này, nút cài đặt vừa đảm nhận vai trò điều khiển vừa vai trò nghiệp vụ cho triển khai này, phù hợp với các môi trường nhẹ để trải nghiệm sản phẩm, kiểm tra tính năng và lập kế hoạch một nút.

### 3. Cấu Hình Nút SSH

Trình cài đặt kết nối với nút mục tiêu qua SSH và thực hiện các nhiệm vụ triển khai. Vui lòng điền:

- NODE_Địa chỉ IP.
- SSH Người dùng, thường là `root`.
- SSH Cổng, thường là `22` mặc định.
- PASSWORD hoặc thông tin xác thực bằng khóa riêng.

Sau khi điền xong, nhấn **Xác Minh** để xác nhận SSH kết nối thành công.

> SSH Thông tin đăng nhập chỉ nên được sử dụng và lưu trong môi trường kiểm soát. Không ghi PASSWORD hoặc khóa riêng trong tài liệu công khai, ảnh chụp màn hình, hoặc bản ghi trò chuyện.

### 4. Đặt Thư Mục Dữ Liệu và Các Cấu Hình Khác

Điền hoặc xác nhận các cấu hình sau theo hướng dẫn trên trang:

| Mục Cấu hình | Mô tả |
| --- | --- |
| ACCESS_DOMAIN / IP | Địa chỉ để người dùng truy cập ShimoDocs Suite; khi sử dụng IP, điền địa chỉ thực tế có thể truy cập. |
| Chế Độ Triển Khai | Chọn chế độ một nút đơn All-in-One. |
| Thư Mục Dữ Liệu Nút | Dùng để lưu trữ dữ liệu triển khai. Vui lòng đảm bảo ổ đĩa có đủ dung lượng và quyền đọc/ghi. |
| Kho Ngoại Tuyến | Hướng dẫn này dành cho cài đặt trực tuyến; giữ giá trị mặc định trên trang. |
| Phần mềm trung gian của bên thứ ba | Hướng dẫn này sử dụng triển khai mặc định; xác nhận theo yêu cầu giao hàng hiện tại nếu cần phần mềm trung gian bên ngoài. |

Nếu không có yêu cầu cấu hình đặc biệt, bạn có thể giữ các giá trị mặc định cho kho offline và phần mềm trung gian bên thứ ba. Sau khi kiểm tra, nhấn **Khởi tạo triển khai** ở dưới cùng của trang.

## 6. Xác nhận tổng quan triển khai

Tổng quan triển khai được sử dụng để kiểm tra cấu hình cài đặt trước khi kiểm tra chính thức.

Vui lòng chú ý đặc biệt các điểm sau:

- Đảm bảo phiên bản gói phát hành và tên sản phẩm là chính xác.
- ACCESS_DOMAIN hoặc IP là chính xác và không phải `127.0.0.1`.
- Chế độ triển khai là All-in-One một nút.
- NODE_IP, SSH người dùng và cổng là chính xác.
- Thư mục dữ liệu là chính xác và dung lượng đĩa đủ.
- Cấu hình kho offline và phần mềm trung gian bên thứ ba tuân thủ môi trường hiện tại.

Sau khi xác nhận không có lỗi, nhấn **Tiếp tục** để tiếp tục kiểm tra môi trường.

## 7. Thực hiện kiểm tra môi trường

Trình cài đặt sẽ kiểm tra các nút và môi trường triển khai. Quá trình kiểm tra có thể mất vài phút, vui lòng giữ trang mở.

### 1. Xem tổng quan nút

Tổng quan nút hiển thị tiến độ kiểm tra như SSH kết nối, hệ thống và hiệu suất, lưu trữ và đĩa, mạng và môi trường triển khai.

Để xem kết quả chi tiết của một kiểm tra cụ thể, nhấp vào mục kiểm tra tương ứng hoặc mục chi tiết.

### 2. Xem kết quả kiểm tra chi tiết

Kết quả chi tiết thường bao gồm:

- SSH quyền người dùng về kết nối và thực thi.
- Hệ điều hành, CPU Kiến trúc và số lượng lõi.
- Dung lượng bộ nhớ, dung lượng đĩa và quyền truy cập thư mục.
- Múi giờ và trạng thái đồng bộ thời gian.
- Mạng, tài nguyên hình ảnh và khả năng kết nối với các dịch vụ bên ngoài.
- Các dư lượng môi trường trên máy chủ có thể ảnh hưởng đến việc triển khai.

### 3. Kiểm tra trạng thái hiểu biết

| Trạng thái | Ý nghĩa | Hành động tiếp theo |
| --- | --- | --- |
| Thành công | Mục kiểm tra hiện tại đáp ứng yêu cầu triển khai | Tiếp tục chờ các mục khác hoàn tất |
| Cảnh báo | Sẽ không trực tiếp chặn việc triển khai, nhưng cần xác nhận xem có phù hợp với kế hoạch hiện tại hay không | Mở chi tiết và tiếp tục sau khi xác nhận tác động |
| Thất bại | Vấn đề hiện tại có thể ảnh hưởng đến việc cài đặt hoặc hoạt động của sản phẩm | Sửa vấn đề trước, sau đó quét lại |
| Đang tiến hành | Trình cài đặt đang thực hiện kiểm tra | Chờ kiểm tra hoàn tất, không thực hiện các hành động lặp lại |

Nếu một mục ở trạng thái 'Đang tiến hành' trong thời gian dài, bạn có thể chờ cho kiểm tra đĩa hiện tại hoặc kiểm tra từ xa hoàn tất trước khi quyết định có quét lại hay không.

### 4. Xử lý cảnh báo và thất bại

Nếu trang hiển thị cảnh báo:

1. Mở mô tả chi tiết của mục kiểm tra tương ứng
2. Xác nhận xem cảnh báo có phù hợp với kế hoạch triển khai hiện tại không
3. Nếu không chắc chắn, lưu trang và nhật ký của trình cài đặt, sau đó liên hệ với nhân viên triển khai hoặc vận hành để xác nhận

Nếu trang không hiển thị: 

1. Theo các hướng dẫn để sửa SSH, quyền, tài nguyên, đĩa, mạng hoặc vấn đề phần mềm trung gian. 
2. Nhấp **Quét lại**. 
3. Xác nhận rằng các mục bị lỗi đã biến mất. 

Sau khi đảm bảo không có lỗi nào ngăn cản triển khai và tất cả cảnh báo đã được xác nhận, nhấp **Tiếp tục**. 

## 8. Bắt đầu Cài đặt 

### 1. Xác nhận Kế hoạch Cài đặt 

Trang sẽ hiển thị kế hoạch cài đặt và các tác vụ cần thực hiện. Sau khi xác nhận chúng đúng, nhấp **Bắt đầu Triển khai**. 

Một cảnh báo "Xác nhận để Bắt đầu Cài đặt" có thể xuất hiện trên trang. Khi bắt đầu, tác vụ cài đặt sẽ tiến hành theo lịch trình; nếu bạn cần điều chỉnh cấu hình, vui lòng nhấp **Hủy** để quay lại bước trước.

### 2. Kiểm tra tiến trình Triển khai

Sau khi bắt đầu triển khai, trang sẽ hiển thị trạng thái tác vụ hiện tại, nhật ký theo thời gian thực và thời gian thực hiện. Triển khai trên một nút thường mất khoảng 10 phút, và thời gian thực tế sẽ bị ảnh hưởng bởi hiệu suất máy chủ và băng thông mạng.

Trong quá trình cài đặt, vui lòng lưu ý: 

- Không đóng quá trình cài đặt. 
- Không khởi động lại các nút cài đặt. 
- Không làm mới, quay lại hoặc gửi lại tác vụ cài đặt. 
- Nếu tác vụ thất bại, trước tiên hãy kiểm tra lỗi đầu tiên trong nhật ký tác vụ tương ứng, sau đó xử lý theo hướng dẫn. 

Khi trang hiển thị **Cài đặt Hoàn tất** hoặc vào trang **Phân phối Triển khai** điều này cho biết tác vụ cài đặt đã hoàn thành. 

## 9. Lưu thông tin Phân phối 

Trang hoàn tất cài đặt sẽ hiển thị thông tin truy cập và kiểm tra nhập cho triển khai này. Vui lòng ngay lập tức thực hiện các hành động sau: 

1. Chạy kiểm tra dịch vụ sau cài đặt và xác nhận kết quả kiểm tra. 
2. Sử dụng thông tin truy cập trên trang giao hàng triển khai, mở ShimoDocs Suite trang kinh doanh và hoàn tất xác minh đăng nhập. 
3. Ghi lại ShimoDocs Suite địa chỉ truy cập doanh nghiệp và MDP địa chỉ Nền tảng Vận hành. 
4. Lưu tài khoản ban đầu và tạm thời PASSWORDvà ngay lập tức thay đổi tài khoản ban đầu PASSWORD sau lần đăng nhập đầu tiên. 
5. Kiểm tra các nút cụm và trạng thái ứng dụng trong MDP Nền tảng Vận hành. 

> Thông tin giao hàng bao gồm địa chỉ truy cập và thông tin xác thực ban đầu. Không chụp ảnh màn hình và phân phối, không tải lên cơ sở tri thức công khai, và không gửi qua các kênh không kiểm soát. 

## 10. Kiểm tra Kết quả Triển khai 

Sau khi hoàn tất cài đặt, nên thực hiện chấp nhận theo thứ tự sau: 

### 1. Kiểm tra Dịch vụ Sau Cài đặt 

Thực hiện kiểm tra sau cài đặt trên trang hoàn tất cài đặt để xác nhận rằng các trường hợp thử nghiệm dịch vụ đạt hoặc kết quả đáp ứng mong đợi của môi trường hiện tại. 

Nếu kiểm tra thất bại hoặc chỉ đạt một phần, bạn có thể gửi lại nhiệm vụ kiểm tra trong MDP Nền tảng Vận hành. 

### 2. Kiểm tra MDP Nền tảng Vận hành

Đăng nhập vào MDP Nền tảng Vận hành, đi tới **Dịch vụ Hệ thống → Quản lý Cụm**và xác nhận rằng các nút cụm và trạng thái chạy ứng dụng bình thường.

### 3. Xác minh ShimoDocs Suite Chức năng

Đăng nhập vào ShimoDocs Suite trang frontend và kiểm tra ít nhất các chức năng sau:

- Tạo một tệp hoặc bộ kiểm thử.
- Chỉnh sửa nội dung và lưu.
- Xuất tệp.
- Nhập tệp.

Sau khi tất cả các kiểm tra trên đã hoàn tất thành công, điều đó cho thấy việc triển khai nhanh này đã xong. Nếu cần vận hành lâu dài, mở rộng hoặc khả năng sẵn sàng cao trong tương lai, vui lòng chuyển sang kế hoạch triển khai tương ứng theo quy mô thực tế, và hoàn tất cấu hình giấy phép và kinh doanh.

## 11. Các câu hỏi thường gặp

### 1. Trình duyệt không mở được trang cài đặt

Kiểm tra theo thứ tự:

- Quy trình cài đặt có còn đang chạy không.
- Địa chỉ truy cập có sử dụng IP thực của nút cài đặt hoặc tên miền có thể phân giải được không.
- Cổng `18080/TCP` đã được mở.
- Mạng giữa máy tính có trình duyệt và nút cài đặt có kết nối không.

### 2. Xác minh gói phân phối thất bại

Kiểm tra:

- Tệp đã tải lên có phải là gói phát hành hoàn chỉnh không. `.tar.gz` gói phát hành.
- Tên tệp và loại sản phẩm có nhất quán với lần giao hàng này không.
- Gói phát hành có phù hợp với máy chủ không CPU 
- Tệp có bị hỏng trong quá trình tải lên hoặc chuyển không.

### 3. SSH Xác thực thất bại

Kiểm tra:

- Liệu NODE_IP và SSH cổng có chính xác không.
- Liệu SSH người dùng, PASSWORD, hoặc khóa riêng có đúng không.
- Có SSH người dùng có các quyền cần thiết cho triển khai không?
- Tường lửa hoặc nhóm bảo mật có cho phép SSH kết nối không.

### 4. Cảnh báo trong Kiểm tra Môi trường

Các cảnh báo sẽ không trực tiếp ngăn chặn việc triển khai, nhưng bạn cần mở chi tiết để xác nhận tác động. Nếu nó liên quan đến hiệu suất đĩa, đồng bộ thời gian, dư thừa cấu hình, hoặc dịch vụ bên ngoài, trước tiên hãy xác nhận xem có phù hợp với kế hoạch triển khai hiện tại trước khi quyết định tiếp tục hay không.

### 5. Thất bại trong Kiểm tra Môi trường

Các mục thất bại cần được sửa chữa trước. Không bỏ qua kiểm tra và bắt đầu cài đặt trực tiếp. Sau khi sửa chữa, nhấp **Quét lại** để xác nhận rằng các mục thất bại đã được vượt qua.

### 6. Thất bại Nhiệm vụ Cài đặt

1. Mở nhật ký thực thi của nhiệm vụ thất bại.
2. Tìm thông báo lỗi đầu tiên xuất hiện.
3. Lưu nhật ký cài đặt, tên nhiệm vụ thất bại và thời điểm xảy ra.
4. Sau khi giải quyết các vấn đề tương ứng về mạng, đĩa, hình ảnh, phần mềm trung gian, hoặc Kubernetes vấn đề khác, tiếp tục theo phương pháp phục hồi thực tế.
