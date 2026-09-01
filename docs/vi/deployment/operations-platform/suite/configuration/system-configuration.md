# Cấu hình hệ thống

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Hướng dẫn

Sổ tay này giới thiệu tính năng "Cấu hình Hệ thống" của ShimoDocs Suite, phù hợp cho quản trị viên hệ thống và những người triển khai sử dụng tính năng này lần đầu. Bạn có thể làm theo các bước trong tài liệu này để tìm các mục cấu hình, chỉnh sửa cấu hình, xác minh xem các thay đổi có hiệu lực hay không, và khôi phục cài đặt gốc nếu cần.

> [!TIP]
>
> Nếu bạn không chắc về ý nghĩa hoặc tác động của một mục cấu hình, vui lòng liên hệ ShimoDocs hỗ trợ kỹ thuật để xác nhận trước khi thực hiện bất kỳ thay đổi nào.

**Quy tắc phạm vi quan trọng nhất** Khi để trống ID Doanh nghiệp, truy vấn và sửa đổi sẽ áp dụng cho cấu hình toàn cầu; khi chọn ID Doanh nghiệp, truy vấn và sửa đổi sẽ áp dụng cho cấu hình của doanh nghiệp được chọn. Sửa đổi cấu hình toàn cầu có thể ảnh hưởng đến nhiều doanh nghiệp, vì vậy vui lòng xác nhận lại phạm vi cấu hình trước khi lưu.

### 1.1 Lối truy cập

Admin Backend > ShimoDocs Suite > Quản lý Cấu hình > Cấu hình Hệ thống

### 1.2 Chuẩn bị Trước Khi Sử Dụng

- Xác nhận rằng tài khoản đăng nhập có quyền xem và chỉnh sửa ShimoDocs Suite cấu hình hệ thống.
- Trước tiên xác nhận xem phạm vi mục tiêu là toàn cầu hay một doanh nghiệp cụ thể, và lấy đúng ID doanh nghiệp.
- Xác nhận tên khóa cấu hình từ yêu cầu cấu hình hoặc Phụ lục A. Tên khóa cấu hình là định danh duy nhất; vui lòng không đoán chỉ dựa trên tên bằng tiếng Trung.
- Ghi lại nguồn, trạng thái và giá trị hiệu lực trước khi sửa đổi; đối với các cấu hình quan trọng, cũng chuẩn bị giá trị phục hồi.
- Các cấu hình toàn cầu có ảnh hưởng lớn nên được sửa đổi trong thời gian ít hoạt động kinh doanh, và thông báo trước cho các nhân sự liên quan.

## 2. Phạm vi và ưu tiên cấu hình

Cấu hình hệ thống hỗ trợ cả phạm vi toàn cầu và doanh nghiệp. Trước khi chỉnh sửa, cần kiểm tra trường ID doanh nghiệp và thông báo về phạm vi ở cuối trang.

| **Khu vực chức năng** | **Phạm vi Toàn cầu** | **Phạm vi Doanh nghiệp** | **Dấu hiệu Nhận diện Trang** |
| --- | --- | --- | --- |
| Cấu hình Hệ thống | Để trống ID doanh nghiệp | Chọn ID doanh nghiệp | Thông báo ở cuối trang “Ghi đè phiên bản toàn cầu” hoặc “Kết quả hiệu lực cuối cùng của doanh nghiệp” |

*Hình 1  Vị trí chọn Mã Doanh nghiệp trong cấu hình hệ thống*

### 2.1 Cấu hình Toàn cầu

- Giữ cho Mã Doanh nghiệp không được chọn.
- Trang hiển thị rằng truy vấn và thay đổi hiện tại là cho 'Ghi đè Phiên bản Toàn cầu.'
- Các giá trị toàn cầu là các giá trị cơ sở được sử dụng khi không có ghi đè của doanh nghiệp; việc sửa đổi chúng có thể ảnh hưởng đến nhiều doanh nghiệp.
- Trước khi lưu, kiểm tra ít nhất một lần nữa để đảm bảo trường Mã Doanh nghiệp thực sự trống.

### 2.2 Cấu hình Cấp Doanh nghiệp

- Chọn doanh nghiệp mục tiêu từ danh sách Mã Doanh nghiệp.
- Trang hiển thị kết quả cuối cùng có hiệu lực sau khi gộp các giá trị mặc định với cấu hình tùy chỉnh của doanh nghiệp hiện tại.
- Cấu hình cấp doanh nghiệp chỉ ảnh hưởng đến doanh nghiệp được chọn và không thay đổi trực tiếp cấu hình của các doanh nghiệp khác.
- Khi có cấu hình cấp doanh nghiệp cho cùng một mục, giá trị hiệu lực cuối cùng của doanh nghiệp ưu tiên so với giá trị toàn cầu.

### 2.3 Ghi đè, Kế thừa và Khôi phục

- Khi doanh nghiệp hiện tại không có ghi đè, sẽ sử dụng cấu hình toàn cầu hoặc các giá trị mặc định của hệ thống.
- Các thao tác như 'Khôi phục Mặc định Hệ thống' hoặc xóa ghi đè hiện tại thường có nghĩa là loại bỏ ghi đè ở phạm vi hiện tại và kế thừa lại giá trị cấp trên.
- Các thông báo trên trang như 'Mặc định Hệ thống,' 'Ghi đè Phiên bản Toàn cầu,' và 'Kết quả Hiệu lực Cuối cùng của Doanh nghiệp' có thể được dùng để xác định giá trị hiện tại đến từ lớp nào.
- Trước khi thực hiện khôi phục hoặc xóa, ghi lại giá trị hiện tại và xác nhận rằng kết quả kế thừa đáp ứng kỳ vọng.

**Cảnh báo rủi ro** Không lưu trực tiếp nếu chưa xác nhận phạm vi doanh nghiệp. Nếu ID Doanh nghiệp để trống, thao tác có thể ghi đè toàn cầu và ảnh hưởng đến nhiều doanh nghiệp.

## 3. Cấu hình hệ thống

Cấu hình hệ thống được sử dụng để xem và điều chỉnh các chức năng chung, hạn mức và các tham số vận hành của ShimoDocs Suite.

### 3.1 Phương pháp tìm kiếm thứ nhất: Tìm kiếm chính xác

- ID Doanh nghiệp: Để trống cho toàn cục; chọn một ID doanh nghiệp để cấu hình ở cấp doanh nghiệp.
- Tiêu chí tìm kiếm: chọn loại, loại giá trị và ngày kết thúc hiệu lực theo nhu cầu; giữ "Tất cả" nếu không chắc chắn.
- Tên Khóa: nhập tên khóa cấu hình; một khóa trên mỗi dòng hoặc dùng dấu phẩy để tách nhiều khóa.
- Nhấn "Tìm kiếm" để xác nhận tên, khóa, nguồn, trạng thái và giá trị hiện tại trong kết quả.
- Nhấn "Chỉnh sửa" ở bên phải của hàng mục tiêu để mở popup sửa đổi.

*Hình 2  Khu vực Tìm kiếm Chính xác Cấu hình Hệ thống*

*Hình 3  Kết quả đơn sau tìm kiếm chính xác theo tên khóa cấu hình*

**Gợi ý Phạm vi** Khi xuất hiện "Khi không có doanh nghiệp được chọn, truy vấn và chỉnh sửa hiện tại là ghi đè phiên bản toàn cầu" ở cuối trang, điều đó cho thấy phạm vi hiện tại là toàn cục. Sau khi chọn một doanh nghiệp, trang hiển thị kết quả có hiệu lực cuối cùng kết hợp giá trị mặc định với cấu hình tùy chỉnh của doanh nghiệp hiện tại.

### 3.2 Phương pháp tìm kiếm thứ hai: Xác định trực tiếp trong danh sách

- Giữ phạm vi doanh nghiệp và điều kiện lọc chính xác, và sử dụng cuộn trang để duyệt danh sách.
- Xác nhận cấu hình mục tiêu bằng "Tên" hoặc "Tên Khóa", không chỉ dựa vào giá trị hiện tại để đánh giá.
- Xem loại, ngày hiệu lực kết thúc, giá trị hiện tại, nguồn và trạng thái trong cùng một hàng.
- Nhấp vào "Chỉnh sửa" ở bên phải. Nếu bạn không thấy cột thao tác, cuộn bảng theo chiều ngang sang bên phải hoặc phóng to khung trình duyệt.

### 3.3 Chỉnh sửa các loại cấu hình hệ thống khác nhau

#### 3.3.1 Loại Khóa-Giá Trị

Phần trên cùng của cửa sổ chỉnh sửa hiển thị các siêu dữ liệu chỉ đọc, bao gồm tên khóa, tên, mô tả, loại và ngày kết thúc hiệu lực. Khi được bật, điền giá trị vào các ô nhập như "Giá Trị Chuỗi" và lưu. Nếu chuỗi mang JSON, URL, đường dẫn, hoặc danh sách, định dạng gốc nên được giữ nguyên.

*Hình 4  Cửa sổ popup chỉnh sửa loại Khóa-Giá Trị Cấu Hình Hệ Thống*

#### 3.3.2 Loại Hạn Ngạch

Cửa sổ popup hạn ngạch thường bao gồm Trạng thái, Giá trị Tối thiểu, Giá trị Tối đa và công tắc "Không Kiểm tra". Sau khi bật cấu hình, điền phạm vi theo yêu cầu kinh doanh; bật "Không Kiểm tra" nghĩa là hệ thống không thực hiện kiểm tra giới hạn theo phạm vi nhập. Giá trị phải phù hợp với đơn vị trong popup, chẳng hạn như "cái", "MB", v.v.

*Hình 5 Cửa sổ popup chỉnh sửa loại Hạn Ngạch của Cấu Hình Hệ Thống*

#### 3.3.3 Các Loại Chức Năng

Loại chức năng chủ yếu dựa trên công tắc trạng thái. Bật nó có nghĩa là mục cấu hình được bật trong phạm vi hiện tại; tắt nó có nghĩa là mục đó bị vô hiệu hóa hoặc chưa được bật. Một số khóa có ngữ nghĩa ngược và nên được xác định dựa theo tên và mô tả của mục cấu hình. Ví dụ, các khóa có tên chứa 'unsupport' hoặc 'disable' có thể đại diện cho 'không được hỗ trợ' hoặc 'bị vô hiệu hóa' khi chuyển sang bật.

### 3.4 Lưu, Xóa và Khôi phục

- Trước khi lưu, xác nhận lại phạm vi doanh nghiệp, tên khóa, loại, đơn vị và các giá trị đã chỉnh sửa.
- Sau khi lưu, tìm kiếm lại cùng một mục cấu hình để xác nhận rằng nguồn, trạng thái và giá trị hiệu lực đã thay đổi.
- Khi có ghi đè trong phạm vi hiện tại, thao tác 'Xóa' có thể khả dụng; sau khi xóa ghi đè, nó sẽ trở về giá trị kế thừa từ cấp trước.
- Khi cần khôi phục, trước tiên hãy viết lại giá trị đã ghi ban đầu, hoặc xóa ghi đè hiện tại sau khi xác nhận quan hệ kế thừa.
- Không hiểu 'xóa' là xóa mục cấu hình chính; xóa trên trang thường chỉ áp dụng cho bản ghi ghi đè trong phạm vi hiện tại.

## 4. Xác minh hiệu lực và Khôi phục

### 4.1 Xác minh Sau Khi Lưu

- Trong trang cấu hình hệ thống, truy vấn lại cùng phạm vi doanh nghiệp và cùng mục cấu hình để xác nhận nguồn, trạng thái và giá trị hiệu lực.
- Đi đến trang chức năng thực tế sử dụng cấu hình này để kiểm tra hiệu năng của chức năng, thay vì chỉ xem phía backend cấu hình.
- Đối với cấu hình toàn cầu, ít nhất một công ty không có cấu hình cấp doanh nghiệp nên được kiểm tra ngẫu nhiên; cấu hình cấp doanh nghiệp chỉ được xác minh cho công ty mục tiêu.
- Đối với tài khoản, quyền hạn hoặc các cài đặt liên quan đến bộ nhớ đệm, làm mới trang, đăng nhập lại hoặc chờ bộ nhớ đệm cập nhật nếu cần.
- Ghi lại thời gian sửa đổi, người vận hành, phạm vi doanh nghiệp, tên khóa, giá trị trước và sau khi sửa đổi, và kết quả xác minh.

### 4.2 Khôi phục

- Có giá trị gốc xác định: chỉnh sửa lại và ghi lại giá trị gốc.
- Chỉ cần loại bỏ ghi đè phạm vi hiện tại: sử dụng 'Khôi phục mặc định hệ thống' hoặc xóa ghi đè hiện tại.
- Sau khi hoàn tác, truy vấn lại giá trị nguồn và giá trị hiệu lực, và vào lại trang kinh doanh để xác minh.
- Nếu thay đổi toàn cầu gây ra bất thường rộng rãi, ưu tiên khôi phục phạm vi toàn cầu, sau đó điều tra sự khác biệt phạm vi giữa các doanh nghiệp riêng lẻ.

**Lưu ý quan trọng** Xóa trên trang thường áp dụng cho các bản ghi ghi đè trong phạm vi hiện tại; mục cấu hình vẫn tồn tại. Cần xác nhận rằng giá trị kế thừa đáp ứng mong đợi trước khi xóa.

## 5. Câu hỏi thường gặp

| **Câu hỏi** | **Cách xử lý** |
| --- | --- |
| Không tìm thấy nút chỉnh sửa hoặc hành động | Khi bảng rộng, cuộn ngang sang bên phải; bạn cũng có thể mở rộng khu vực hiển thị của trình duyệt. |
| Không có kết quả cho tìm kiếm chính xác | Kiểm tra chữ hoa/chữ thường và dấu gạch dưới của tên khóa; xác nhận phạm vi ID doanh nghiệp; xóa các bộ lọc kiểu, loại giá trị hoặc ngày kết thúc hiệu lực quá chặt chẽ. |
| Sau khi lưu, trang kinh doanh không có thay đổi | Kiểm tra xem phạm vi doanh nghiệp sai có được chọn hay không, nguồn có bị ghi đè bởi doanh nghiệp hay không, có cần làm mới hoặc đăng nhập lại hay không, và xác nhận xem các mục cấu hình có áp dụng cho chức năng hiện tại hay không. |
| Nút khôi phục mặc định hệ thống không khả dụng | Phạm vi hiện tại không có ghi đè, hiện đang sử dụng giá trị kế thừa hoặc giá trị mặc định của hệ thống. |
| JSON hoặc URL Lỗi Cấu hình | Giữ hợp lệ JSON hoặc URL Định dạng, không bỏ qua dấu ngoặc kép, dấu phẩy hoặc giao thức; xác minh trước trong doanh nghiệp thử nghiệm. |
| Giá trị hiệu quả cuối cùng của doanh nghiệp khác với giá trị toàn cục | Doanh nghiệp hiện tại có thể có ghi đè. Kiểm tra nguồn và bản ghi ghi đè để xác định giữ khác biệt của doanh nghiệp hay khôi phục kế thừa. |

## Phụ lục A: Chỉ mục mục cấu hình hệ thống

Chỉ mục dưới đây chỉ liệt kê các mục cấu hình hệ thống có thể được truy vấn và sửa đổi trên trang hiện tại; phạm vi hiển thị cụ thể phụ thuộc vào phiên bản triển khai hiện tại và hiển thị thực tế của trang.

| **Khóa Cấu hình** | **Tên mục cấu hình** | **Loại/Quy tắc** | **Hỗ trợ Trang** |
| --- | --- | --- | --- |
| cho phép_nhóm_quản trị_lấy_được mời_người dùng_mật khẩu | Quản trị doanh nghiệp lấy ban đầu PASSWORD của người dùng được mời | Chuỗi rỗng | Có thể cấu hình trên trang |
| tự động_đăng nhập_bật_không_quyền_trang | Truy cập ẩn danh không có quyền sẽ chuyển hướng đến trang đăng nhập | Chuỗi rỗng | Có thể cấu hình trên trang |
| lô_xóa_tệp_số lượng_giới hạn | Số lượng tệp tối đa cho xóa lô | 0–500 | Có thể cấu hình trên trang |
| lô_tải xuống_tệp | Số lượng tệp tối đa cho tải xuống lô đơn | 0–500 | Có thể cấu hình trên trang |
| lô_tải xuống_kích thước | Tổng kích thước tối đa cho tải xuống lô đơn | 0–21474836480 | Có thể cấu hình trên trang |
| lô_di chuyển_tệp_số lượng_giới hạn | Số lượng tệp tối đa cho di chuyển lô | 0–500 | Có thể cấu hình trên trang |
| thương hiệu | Tên thương hiệu Front-end | Chuỗi rỗng | Có thể cấu hình trên trang |
| thay đổi_thư mục_đồng tác giả | Hợp tác thư mục | Chuỗi rỗng | Có thể cấu hình trên trang |
| phân loại_dấu_cấu hình_giới hạn | Số lượng chính sách hạ cấp tối đa | 0–30 | Có thể cấu hình trên trang |
| phân loại_dấu_giới hạn | Số lượng nhãn phân loại tối đa | 0–20 | Có thể cấu hình trên trang |
| phân loại_dấu_quy tắc_giới hạn | Số lượng quy tắc nhãn phân loại tối đa | 0–30 | Có thể cấu hình trên trang |
| đám mây_nhóm_không gian_tải xuống_tệp_kích thước | Dung lượng tối đa của một tệp tải xuống (MB) | 0–3072 | Có thể cấu hình trên trang |
| đám mây_nhóm_không gian_tải lên_tệp_kích thước | Giới hạn dung lượng tải lên tệp của không gian nhóm | 0–300 | Có thể cấu hình trên trang |
| ngày_giải nén_tệp_số lượng_giới hạn | Số lượng tệp tối đa có thể giải nén mỗi ngày | 0–2000 | Có thể cấu hình trên trang |
| mặc định_ảnh đại diện | Ảnh đại diện mặc định URL | Đường dẫn | Có thể cấu hình trên trang |
| mặc định_doanh nghiệp_thùng rác_hạn mức | Hạn mức thùng rác doanh nghiệp mặc định | 0–0 | Có thể cấu hình trên trang |
| mặc định_không gian_hạn mức | Hạn mức không gian nhóm mặc định | 0–107374182400 | Có thể cấu hình trên trang |
| mặc định_nhóm_người dùng_hạn mức | Giới hạn dung lượng mặc định cho thành viên doanh nghiệp | 0–0 | Có thể cấu hình trên trang |
| mặc định_người dùng_tệp_thẻ | Thẻ mặc định cho tệp người dùng | JSON mảng | Có thể cấu hình trên trang |
| mặc định_người dùng_hạn mức | Hạn mức không gian cá nhân mặc định trong nhóm (Máy tính của tôi) | 0–107374182400 | Có thể cấu hình trên trang |
| phòng ban_số lượng_giới hạn | Số lượng phòng ban tối đa có thể tạo trong doanh nghiệp | 0–500 | Có thể cấu hình trên trang |
| phòng ban_độ sâu_giới hạn | Số cấp phòng ban lồng nhau tối đa | 0–20 | Có thể cấu hình trên trang |
| vô hiệu hóa_theo lô_tải xuống | Vô hiệu hóa tải xuống theo lô | Chuỗi rỗng | Có thể cấu hình trên trang |
| vô hiệu hóa_doanh nghiệp_thùng rác | Ẩn thùng rác doanh nghiệp | Chuỗi rỗng | Có thể cấu hình trên trang |
| hiển thị_IP_vị trí | Hiển thị vị trí IP | Chuỗi rỗng | Có thể cấu hình trên trang |
| ổ đĩa_trình chỉnh sửa_về_thương hiệu_hiển thị | Hiển thị thông tin thương hiệu trên ShimoDocs Suite trang Giới thiệu của trình soạn thảo | Chuỗi rỗng | Có thể cấu hình trên trang |
| ổ đĩa_trình chỉnh sửa_về_mục nhập_hiển thị | Hiển thị mục “Giới thiệu” trong ShimoDocs Suite trình soạn thảo | Chuỗi rỗng | Có thể cấu hình trên trang |
| ổ đĩa_trình chỉnh sửa_chính thức_trang web_mục nhập_hiển thị | ShimoDocs Suite Hiển thị mục Trang web chính thức của Trình soạn thảo | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_liên kết_báo cáo | Báo cáo liên kết bên ngoài | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_người ngoài | Cộng tác viên bên ngoài | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_PC_hệ thống_chủ đề | bật_PC_hệ thống_chủ đề | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_rdoc_MD_hình ảnh_xuất_tùy chọn | bật_rdoc_MD_hình ảnh_xuất_tùy chọn | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_rủi ro | Nhận dạng rủi ro | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_chia sẻ_hết hạn_thời gian | Thời gian hết hạn liên kết chia sẻ | Chuỗi rỗng | Có thể cấu hình trên Trang |
| bật_chia sẻ_mật khẩu | Mật khẩu chia sẻ | Chuỗi rỗng | Có thể cấu hình trên Trang |
| tệp_cộng tác viên_giới hạn | Số lượng tối đa cộng tác viên trên mỗi tệp | 0–100 | Có thể cấu hình trên Trang |
| thư mục_con_số lượng_giới hạn | Số lượng tối đa tệp ở cùng cấp | 0–2000 | Có thể cấu hình trên Trang |
| miễn phí_người dùng_tạo_giới hạn | Giới hạn số lượng mẫu mà người dùng miễn phí có thể tạo | 0–5 | Có thể cấu hình trên Trang |
| giao diện_thời gian chạy_tính năng | Danh sách mục cấu hình thời gian chạy giao diện | JSON mảng | Có thể cấu hình trên trang |
| nhập_người dùng_hàng_giới hạn | Số lượng người dùng tối đa có thể nhập cùng lúc | 0–500 | Có thể cấu hình trên trang |
| mời_di động_giới hạn_hết hạn | Cửa sổ thời gian hết hạn cho số lượng lời mời cộng tác trên tệp gửi qua điện thoại di động | 0–3600 | Có thể cấu hình trên trang |
| mời_di động_giới hạn_tối đa | Giới hạn số lượng lời mời cộng tác trên tệp qua điện thoại di động | 0–20 | Có thể cấu hình trên trang |
| là_mở_vai trò_áp dụng | Ứng dụng quyền tệp | Chuỗi rỗng | Có thể cấu hình trên trang |
| đăng nhập_thiết bị_giới hạn | Số lượng thiết bị đăng nhập đồng thời tối đa cho mỗi tài khoản | 0–0 | Có thể cấu hình trên trang |
| tối đa_người tạo_nhóm_mỗi_tài khoản | Số lượng doanh nghiệp tối đa có thể tạo trên mỗi tài khoản | 0–3 | Có thể cấu hình trên trang |
| tối đa_thư mục_độ sâu | Độ sâu phân cấp thư mục tối đa | 0–50 | Có thể cấu hình trên trang |
| tối đa_tham gia_nhóm_mỗi_tài khoản | Số lượng doanh nghiệp tối đa mà một tài khoản có thể tham gia | 0–100 | Có thể cấu hình trên trang |
| tối đa_thùng rác_danh sách_kích thước | Số bản ghi trả về bởi giao diện danh sách thùng rác | 0–500 | Có thể cấu hình trên trang |
| nhiều phần_tải lên_bật | Tải lên nhiều phần | Chuỗi số | Có thể cấu hình trên trang |
| một lần_giải nén_tệp_số lượng_giới hạn | Số lượng tệp tối đa mỗi lần giải nén | 0–500 | Có thể cấu hình trên trang |
| chỉ_chủ sở hữu_có thể_xóa | Chỉ chủ sở hữu mới có thể xóa | Chuỗi rỗng | Có thể cấu hình trên trang |
| cao cấp_người dùng_tạo_giới hạn | Số lượng mẫu tối đa mà người dùng có thể tạo | 0–50 | Có thể cấu hình trên trang |
| riêng tư_triển khai_trang_biểu tượng | Cấu hình biểu tượng trang | Chuỗi rỗng | Có thể cấu hình trên trang |
| công khai_chia sẻ | Chia sẻ công khai | Chuỗi rỗng | Có thể cấu hình trên trang |
| rag_tìm kiếm_quy tắc | RAG quy tắc tìm kiếm | JSON đối tượng | Có thể cấu hình trên trang |
| sdkCheckpointCacheTTL | Thời gian lưu bộ nhớ đệm cấu hình Trình chỉnh sửa | 0–600 | Có thể cấu hình trên trang |
| sdk_điểm kiểm tra_danh sách trắng | Danh sách trắng cấu hình Trình chỉnh sửa | JSON đối tượng | Có thể cấu hình trên trang |
| tìm kiếm_trí tuệ nhân tạo_bật | tìm kiếm_trí tuệ nhân tạo_bật | Chuỗi rỗng | Có thể cấu hình trên trang |
| chia sẻ_mật khẩu_độ dài | Độ dài mật khẩu chia sẻ | 0–6 | Có thể cấu hình trên trang |
| đơn_tệp_tải lên_kích thước_giới hạn | Kích thước tối đa của một tệp tải lên (GB) | 0–1 | Có thể cấu hình trên trang |
| đơn_tải lên_tệp_số lượng_giới hạn | Tải lên theo lô | Chuỗi rỗng | Có thể cấu hình trên Trang |
| nhóm_thay đổi | Thay đổi Nhóm | Chuỗi rỗng | Có thể cấu hình trên Trang |
| nhóm_vai trò_quản lý | Quản lý Vai trò | Chuỗi rỗng | Có thể cấu hình trên Trang |
| chủ đề_màu sắc | Màu chủ đề Giao diện Người dùng | Chuỗi rỗng | Có thể cấu hình trên Trang |
| chủ đề_màu sắc_nút | Màu chủ đề Nút | HEX Giá trị Màu | Có thể cấu hình trên Trang |
| giao diện_bán kính_cấu hình | Cấu hình Bán kính Viền Giao diện Người dùng | Chuỗi rỗng | Có thể cấu hình trên Trang |
| tải lên_theo lô_tối đa | Số lượng tệp tối đa mỗi lần tải lên | 0–500 | Có thể cấu hình trên Trang |

## Phụ lục B: Thuật ngữ và Tương ứng Trường Trang

| **Thuật ngữ** | **Ý nghĩa** |
| --- | --- |
| Khóa Cấu hình / Tên Khóa | Khóa duy nhất của mục cấu hình, ví dụ batch_tải xuống_tệp. |
| ID Doanh nghiệp | Định danh doanh nghiệp. Chọn nó để nhập phạm vi cấu hình cấp doanh nghiệp. |
| Cấu hình Toàn cầu | Phạm vi mặc định được truy vấn và thay đổi khi để trống ID Doanh nghiệp. |
| Cấu hình Cấp Doanh nghiệp | Ghi đè chỉ có hiệu lực cho doanh nghiệp được chọn. |
| Mặc định Hệ thống | Nếu không có ghi đè tùy chỉnh trong phạm vi hiện tại, giá trị mặc định tích hợp sẽ được sử dụng. |
| Ghi đè Phiên bản Toàn cầu | Mục cấu hình hiện tại có cài đặt tùy chỉnh ở cấp toàn cầu. |
| Kết quả Hiệu lực Doanh nghiệp | Kết quả thực tế sau khi hợp nhất giá trị mặc định doanh nghiệp với ghi đè doanh nghiệp. |
| Khóa-Giá trị | Một tham số giá trị đơn được lưu trữ dưới dạng chuỗi, có thể chứa văn bản, URL, đường dẫn, hoặc JSON. |
| Hạn ngạch | Một phạm vi số bao gồm giá trị tối thiểu, tối đa hoặc một công tắc giới hạn. |
| Chức năng | Một tham số thuộc loại công tắc hoặc trạng thái. |
| Không kiểm tra | Không thực hiện các kiểm tra xác thực dựa trên giới hạn trên đã chỉ định. |
