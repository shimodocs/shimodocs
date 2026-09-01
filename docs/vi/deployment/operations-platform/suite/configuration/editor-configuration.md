# Cấu hình trình soạn thảo

[← ShimoDocs Suite Tài liệu triển khai](../../../README.md)

## 1. Hướng dẫn sử dụng

Hướng dẫn này giới thiệu tính năng "Cấu hình Trình soạn thảo" của ShimoDocs Suite, phù hợp cho quản trị viên hệ thống và người triển khai sử dụng tính năng này lần đầu. Bạn có thể làm theo các bước trong tài liệu này để tìm mục cấu hình, thay đổi công tắc hoặc hạn mức tính năng, xác minh xem chúng có hiệu lực hay không, và khôi phục cài đặt gốc nếu cần.

**Quy tắc phạm vi quan trọng nhất** Khi để trống Team ID, sẽ truy vấn và chỉnh sửa cấu hình ứng dụng mặc định; khi nhập Team ID, sẽ truy vấn và chỉnh sửa cấu hình cho nhóm tương ứng. Việc chỉnh sửa cấu hình ứng dụng mặc định có thể ảnh hưởng đến nhiều nhóm, vì vậy vui lòng xác nhận lại phạm vi cấu hình trước khi lưu.

### 1.1 Lối truy cập

Admin Backend > ShimoDocs Suite > Quản lý Cấu hình > Cấu hình Trình soạn thảo

### 1.2 Chuẩn bị trước khi sử dụng

- Xác nhận rằng tài khoản đăng nhập của bạn có quyền xem và sửa đổi ShimoDocs Suite cấu hình trình chỉnh sửa.
- Trước tiên xác nhận phạm vi mục tiêu là ứng dụng mặc định hay một nhóm cụ thể, và lấy ID Nhóm chính xác.
- Xác nhận tên các mục cấu hình từ yêu cầu cấu hình hoặc Phụ lục A. Tên mục cấu hình là định danh duy nhất; không đoán dựa trên tên tính năng bằng tiếng Trung.
- Ghi lại nguồn, giá trị hiệu lực và trạng thái hạn chế trước khi chỉnh sửa; đối với các cấu hình quan trọng, chuẩn bị giá trị khôi phục.
- Cấu hình ứng dụng mặc định có ảnh hưởng rộng; nên sửa đổi chúng trong thời gian kinh doanh thấp và thông báo trước cho những người chịu trách nhiệm liên quan.

## 2. Phạm vi và ưu tiên cấu hình

Cấu hình trình chỉnh sửa hỗ trợ hai phạm vi: Mặc định ứng dụng và Nhóm. Trước khi thực hiện thay đổi, bạn phải kiểm tra cả ID Nhóm và 'Kích thước hiện tại' ở đầu trang.

| **Khu vực chức năng** | **Phạm vi mặc định ứng dụng** | **Phạm vi nhóm** | **Chỉ báo Nhận diện Trang** |
| --- | --- | --- | --- |
| Cấu hình Trình chỉnh sửa | Để trống ID Nhóm | Nhập một số nguyên dương làm ID Nhóm | Kích thước Hiện tại hiển thị 'Mặc định Ứng dụng' hoặc 'Nhóm' |

*Hình 1 Sự tương ứng giữa ID Nhóm và Kích thước Hiện tại*

### 2.1 Phạm vi Mặc định Ứng dụng

- Giữ nguyên ID Nhóm trống.
- Phần đầu trang hiển thị “Kích thước Hiện tại: Mặc định Ứng dụng.”
- Giá trị mặc định của ứng dụng là giá trị cơ sở khi chưa thiết lập ghi đè cho nhóm; việc sửa đổi nó có thể ảnh hưởng đến nhiều nhóm.
- Trước khi lưu, hãy xác nhận lại rằng ID Nhóm thực sự đang trống để tránh ghi nhầm yêu cầu của nhóm thành cài đặt mặc định của ứng dụng.

### 2.2 Phạm vi Nhóm

- Nhập ID nguyên dương của nhóm mục tiêu vào trường ID Nhóm rồi nhấp “Truy vấn.”
- Phía trên cùng của trang hiển thị “Kích thước hiện tại: Nhóm.”
- Cấu hình cấp nhóm chỉ ảnh hưởng đến nhóm tương ứng với ID Nhóm đã nhập và không thay đổi trực tiếp cấu hình cho các nhóm khác.
- Khi một mục cấu hình có cài đặt cấp nhóm, giá trị hiệu lực của nhóm sẽ được ưu tiên hơn giá trị mặc định của ứng dụng.

### 2.3 Ghi đè, Kế thừa và Khôi phục

- Nếu nhóm hiện tại không có ghi đè tùy chỉnh, cấu hình mặc định của ứng dụng hoặc giá trị mặc định của hệ thống sẽ được sử dụng.
- “Nguồn” trong danh sách có thể giúp xác định giá trị hiện tại đến từ mặc định hệ thống, ghi đè ứng dụng hay ghi đè nhóm.
- Sau khi xóa lớp ghi đè hiện tại, cấu hình thường sẽ kế thừa lại giá trị cấp trên; xác nhận kết quả sau khi kế thừa trước khi xóa.
- Sau khi chỉnh sửa hoặc khôi phục, truy vấn lại cùng ID Nhóm và mục cấu hình để xác nhận rằng nguồn và giá trị hiệu lực đáp ứng mong đợi.

**Cảnh báo rủi ro** Không lưu trực tiếp khi chưa xác nhận kích thước hiện tại. Khi ID Nhóm trống, các thao tác sẽ được ghi vào phạm vi mặc định của ứng dụng, điều này có thể ảnh hưởng đến nhiều nhóm. 

## 3. Cấu hình Trình soạn thảo 

Cấu hình trình soạn thảo được sử dụng để xem và điều chỉnh các công tắc tính năng, hạn mức sử dụng và cấu hình có cấu trúc của ShimoDocs Suite trình chỉnh sửa. Bạn có thể lọc theo loại hoặc mở rộng "Bộ lọc nâng cao" và nhập tên mục cấu hình vào "Danh sách trắng tên" để tìm kiếm chính xác. 

### 3.1 Trường Trang 

| **Trường** | **Mô tả** | 
| --- | --- | 
| ID Ứng Dụng | Hiện tại ShimoDocs Suite Định danh ứng dụng, chỉ dùng để xác nhận ngữ cảnh. | 
| Kích thước hiện tại | "Mặc định Ứng dụng" khi ID Nhóm trống; "Nhóm" sau khi điền ID Nhóm. | 
| ID Nhóm | Định danh nhóm; chỉ chấp nhận số nguyên dương. | 
| Loại | Các tùy chọn bao gồm Tất cả, Tính năng, Hạn mức giá trị đơn, Hạn mức khoảng, hoặc JSON Cấu hình. | 
| Bộ lọc nâng cao | Mở rộng hộp nhập để nhập tên mục cấu hình. | 
| Danh sách trắng tên | Nhập tên mục cấu hình; hỗ trợ một tên trên mỗi dòng hoặc phân tách bằng dấu phẩy tiếng Anh. | 
| Nguồn | Chỉ ra giá trị hiện tại đến từ mặc định hệ thống, ghi đè ứng dụng hoặc ghi đè nhóm. | 
| Giá trị hiệu lực | Chuyển đổi thực tế, hạn mức, hoặc cấu hình cấu trúc được sử dụng trong phạm vi hiện tại. | 
| Hành động | Biểu tượng bút chì để chỉnh sửa; biểu tượng xóa để loại bỏ ghi đè lớp hiện tại. | 

*Hình 2 Khu vực truy vấn Cấu hình Trình chỉnh sửa, Danh sách kết quả và Cột hành động*

### 3.2 Tìm kiếm chính xác

1. Quyết định có điền ID Nhóm hay không dựa trên phạm vi cấu hình: để trống nếu dùng phạm vi mặc định của ứng dụng, hoặc điền đối với nhóm tương ứng.
2. Chọn "Loại" khi cần; nếu không chắc về loại, giữ "Tất cả".
3. Nhấn "Bộ lọc nâng cao" để mở rộng "Danh sách trắng tên".
4. Nhập đầy đủ tên mục cấu hình. Đối với nhiều tên, nhập mỗi tên trên một dòng hoặc phân tách bằng dấu phẩy.
5. Nhấp vào "Truy vấn" để xác minh tên, loại, nguồn và giá trị hiệu lực trong kết quả.
6. Nhấp vào biểu tượng bút chì ở phía bên phải của hàng mục tiêu để mở cửa sổ chỉnh sửa.

*Hình 3 Điền vào danh sách trắng tên sau khi nhấp 'Bộ lọc nâng cao'*

*Hình 4 Một kết quả duy nhất sau khi tìm kiếm chính xác để chỉnh sửa_giới hạn_mosheet_kích thước*

**Mẹo thao tác** Nếu bạn không thấy các biểu tượng hành động, hãy cuộn danh sách sang phải hoặc phóng to khu vực hiển thị của trình duyệt.

### 3.3 Tìm kiếm trực tiếp trong danh sách

- Trước tiên xác nhận rằng bộ lọc Nhóm ID và loại là chính xác, sau đó cuộn qua kết quả truy vấn.
- Sử dụng cả “Tên” và “Loại” để xác nhận cấu hình mục tiêu; không chỉ dựa vào giá trị hiện tại.
- Số bản ghi hiển thị trên trang có thể thay đổi tùy thuộc vào phiên bản triển khai và các mục cấu hình được ứng dụng hiện tại hỗ trợ.
- Sau khi tìm thấy hàng mục tiêu, nhấp vào biểu tượng bút chì ở phía bên phải để vào chế độ chỉnh sửa.

### 3.4 Chỉnh sửa cấu hình

#### 3.4.1 Chức năng

Loại chức năng được sử dụng để kiểm soát việc một khả năng có sẵn hay không. Sau khi mở cửa sổ chỉnh sửa, chọn trạng thái được cung cấp trên trang như “Hỗ trợ” hoặc “Ẩn” từ menu thả xuống “Giá trị hiệu lực”, sau đó nhấp “Lưu”. Một số tên mục cấu hình bao gồm ngữ nghĩa đảo ngược như không hỗ trợ hoặc vô hiệu hóa, vì vậy vui lòng đánh giá ý nghĩa thực tế dựa trên tên mục và mô tả.

*Hình 5  Cài Đặt Giá Trị Hiệu Quả của Các Mục Cấu Hình Loại Chức Năng*

#### 3.4.2 Hạn Mức Giá Trị Đơn

Hạn mức giá trị đơn thường bao gồm một công tắc "xác minh giới hạn" và một "giá trị tối đa." Khi xác minh giới hạn được bật, hệ thống sẽ kiểm tra theo giá trị tối đa; khi tắt, thường hiển thị là "không giới hạn." Giá trị tối đa phải nằm trong phạm vi cho phép của tham số và phù hợp với đơn vị trong tên tham số, chẳng hạn như MB, GB, trang, mục, hoặc ký tự.

*Hình 6 Xác Minh Hạn Mức Giá Trị Đơn và Cài Đặt Giá Trị Tối Đa*

#### 3.4.3 Hạn Mức Khoảng

- Hạn mức khoảng thường cung cấp cả giá trị tối thiểu và tối đa.
- Giá trị tối thiểu không được lớn hơn giá trị tối đa, và giá trị nhập vào nên nằm trong phạm vi cho phép được đưa ra trên trang hoặc trong phụ lục.
- Nếu trang cung cấp tùy chọn "Không Xác Minh" hoặc "Không Giới Hạn," trước tiên hãy xác nhận xem chức năng hiện tại có hỗ trợ cài đặt này không.
- Sau khi lưu, xác minh các giá trị biên trong chức năng kinh doanh thực tế để tránh chỉ kiểm tra hiển thị trong backend cấu hình.

#### 3.4.4 JSON Cấu hình

- JSON Cấu hình phải duy trì cấu trúc hợp lệ, bao gồm dấu ngoặc kép, dấu phẩy, ngoặc đơn, và loại dữ liệu chính xác.
- Lưu giá trị gốc đầy đủ trước khi thực hiện thay đổi; không chỉ ghi lại một trường.
- Khi ý nghĩa của trường không rõ ràng, không thêm, xóa hoặc đổi tên các trường một cách tùy tiện.

### 3.5 Lưu và Xóa

- Trước khi lưu, xác nhận lại kích thước hiện tại, ID Nhóm, tên mục cấu hình, loại, đơn vị và giá trị mới.
- Sau khi lưu, truy vấn lại cùng phạm vi và cùng mục cấu hình để xác nhận rằng giá trị gốc và giá trị hiệu lực đã được cập nhật.
- Biểu tượng xóa thường được sử dụng để xóa bản ghi ghi đè của phạm vi hiện tại, không phải để xóa mục cấu hình.
- Hướng dẫn này chỉ liệt kê các mục cấu hình có thể truy vấn và sửa đổi trên trang hiện tại; các mục hiển thị thực tế có thể thay đổi tùy thuộc vào phiên bản triển khai và khả năng hỗ trợ ứng dụng hiện tại.

### 3.6 Mô tả Mục Cấu Hình

Phụ lục A chỉ bao gồm các mục cấu hình của trình chỉnh sửa có thể truy vấn và sửa đổi trên trang hiện tại; phạm vi hiển thị cụ thể tuân theo phiên bản triển khai hiện tại và hiển thị thực tế trên trang.

## 4. Xác minh Hiệu lực và Khôi phục

### 4.1 Xác minh Sau Khi Lưu

- Trên trang cấu hình trình chỉnh sửa, truy vấn lại cùng Team ID và cùng mục cấu hình để xác nhận giá trị gốc, giá trị hiệu lực và trạng thái hạn chế.
- Vào trang trình chỉnh sửa hoặc hàm tính năng thực tế sử dụng cấu hình này để xác minh xem chức năng có hiển thị, hạn mức có hiệu lực hay hạn chế đã được gỡ bỏ.
- Khi áp dụng cấu hình mặc định, xác minh với ít nhất một nhóm chưa đặt cấu hình cấp nhóm; đối với cấu hình cấp nhóm, chỉ xác minh Team ID mục tiêu.
- Làm mới trang, vào lại trình chỉnh sửa, đăng nhập lại hoặc chờ cập nhật bộ nhớ đệm nếu cần.
- Ghi lại thời gian sửa đổi, người thao tác, phạm vi cấu hình, Team ID, tên mục cấu hình, giá trị trước và sau khi sửa đổi, và kết quả xác minh.

### 4.2 Khôi phục

- Nếu giá trị gốc đã được ghi lại, chỉnh sửa lại và ghi trả giá trị gốc.
- Nếu chỉ cần xóa ghi đè phạm vi hiện tại, hãy sử dụng biểu tượng xóa và xác nhận giá trị kế thừa từ cấp trên sau khi xóa.
- Sau khi phục hồi, truy vấn lại giá trị nguồn và hiệu lực, và vào trang kinh doanh để kiểm tra lại.
- Nếu việc áp dụng sửa đổi cấu hình mặc định gây ra bất thường, trước tiên hãy khôi phục giá trị mặc định, sau đó kiểm tra xem có đội nào có ghi đè độc lập không.

**Lưu ý quan trọng** Sau khi xóa ghi đè hiện tại, giá trị kế thừa có thể xuất hiện ngay trên trang. Trước khi xóa, cấu hình cấp trên phải được xác nhận đáp ứng kỳ vọng, và nên giữ lại bản ghi trạng thái trước khi sửa đổi.

## 5. Câu hỏi thường gặp

| **Vấn đề** | **Giải pháp** |
| --- | --- |
| Không tìm thấy ô nhập tên mục cấu hình | Nhấn “Bộ lọc nâng cao” để mở rộng “Danh sách trắng tên.” |
| Không tìm thấy biểu tượng chỉnh sửa hoặc xóa | Cuộn ngang danh sách tới phía bên phải xa nhất, hoặc mở rộng cửa sổ trình duyệt. |
| Tìm kiếm chính xác không trả về kết quả | Kiểm tra chữ hoa chữ thường của tên, dấu gạch dưới, ID đội và bộ lọc loại; xóa điều kiện lọc quá nghiêm ngặt và thử tìm kiếm lại. |
| Sau khi nhập ID đội, vẫn không nằm trong chiều dữ liệu đội | ID đội phải là số nguyên dương hợp lệ; sau khi nhập, nhấn “Truy vấn” lại và kiểm tra “Chiều hiện tại” ở trên cùng trang. |
| Sau khi lưu, trang kinh doanh không đổi | Kiểm tra xem có chọn nhầm phạm vi không, có bị đội ghi đè không, có cần làm mới hoặc đăng nhập lại không, và mục cấu hình có áp dụng cho chức năng hiện tại không. |
| Biểu tượng xóa không khả dụng | Phạm vi hiện tại có thể không có ghi đè tùy chỉnh và đang sử dụng giá trị mặc định của hệ thống hoặc giá trị kế thừa từ cấp cao hơn. |
| Lưu hạn mức thất bại | Kiểm tra phạm vi giá trị, đơn vị, mối quan hệ giữa giá trị tối thiểu và tối đa, và xác nhận xem “Không giới hạn” có được phép hay không. |
| JSON Lưu cấu hình thất bại | Sử dụng hợp lệ JSON; kiểm tra dấu ngoặc kép, dấu phẩy, dấu ngoặc và kiểu trường; nếu không chắc chắn, khôi phục giá trị gốc đầy đủ trước khi chỉnh sửa. |

## Phụ lục A: Chỉ mục các mục cấu hình trình chỉnh sửa

Chỉ mục sau chỉ liệt kê các mục cấu hình trình chỉnh sửa có thể tra cứu và chỉnh sửa trên trang hiện tại; phạm vi hiển thị cụ thể phụ thuộc vào phiên bản đang triển khai.

| **Tên mục cấu hình** | **Danh mục / Mô tả chức năng** | **Loại** | **Giá trị mặc định / Phạm vi tùy chọn** | **Phương pháp cấu hình** |
| --- | --- | --- | --- | --- |
| xuất_modoc_docx | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_modoc_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_modoc_pdf | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_modoc_pdf_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_modoc_wps | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_pdf_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_đơn_bảng tính_csv | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_đơn_bảng tính_pdf_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_đơn_bảng tính_xlsx | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_xlsx | Xuất / Bảng | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_mosheet_zip | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_trình bày_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_trình bày_pdf | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_trình bày_pdf_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_trình bày_pptx | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_rdoc_docx | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_rdoc_img | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_rdoc_md | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_rdoc_pdf | Xuất | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| xuất_bảng_xlsx | Xuất / Bảng Ứng dụng | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| biểu mẫu_thông báo | Chỉnh sửa Biểu mẫu / Thiết lập Cảnh báo Thông báo (Cảnh báo Phản hồi, Cập nhật Đăng ký) | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_chuyển đổi_svg | Nhập / Tải lên / Loại định dạng tệp đính kèm chuyển đổi bắt buộc | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_sơ đồ tư duy_xmind | Nhập/Tải lên / Sơ đồ tư duy | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_modoc_doc | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_modoc_docx | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_modoc_wps | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_modoc_wpt | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_mosheet_csv | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_mosheet_xls | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_mosheet_xlsm | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_mosheet_xlsx | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_trình bày_ppt | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_trình bày_pptx | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_rdoc_doc | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_rdoc_docx | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_rdoc_md | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_rdoc_txt | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_bảng_csv | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_bảng_xls | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_bảng_xlsx | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_không được hỗ trợ_tệp đính kèm_svg | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| nhập_không được hỗ trợ_tệp đính kèm_xml | Nhập/Tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| mosheet_kết hợp_các bảng | Chỉnh sửa bảng tính / Kết hợp các trang tính | Công tắc tính năng | Ẩn/Tắt | Có thể cấu hình trên Trang |
| mosheet_ngày_đề cập | Chỉnh sửa bảng tính / Nhắc nhở ngày | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| mosheet_theo dõi_chế độ | Chỉnh sửa bảng tính / Chế độ theo dõi | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| mosheet_theo dõi_lựa chọn | Chỉnh sửa bảng tính / Theo dõi lựa chọn | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| mosheet_nhập khẩu_phạm vi | Chỉnh sửa bảng tính / Tham chiếu chéo giữa các trang tính | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| mosheet_độc lập_khung nhìn | Chỉnh sửa bảng tính / Xem độc lập | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| trình bày_từ xa_trình diễn | Chỉnh sửa slide / Thuyết trình từ xa | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| xem trước_không hỗ trợ_ofd | Xem trước | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| xem trước_không hỗ trợ_pdf | Xem trước | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| xem trước_không hỗ trợ_rtf | Xem trước / Văn bản (Xem trước không hỗ trợ RTF) | Công tắc tính năng | Ẩn/Đóng | Có thể cấu hình trên Trang |
| rdoc_theo dõi_chế độ | Chỉnh sửa tài liệu / Chế độ theo dõi | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| rdoc_thông báo | Chỉnh sửa tài liệu / Cảnh báo thông báo | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| rdoc_rộng_giấy | Chỉnh sửa tài liệu / Giấy rộng | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| sdk_trình chỉnh sửa_về_thương hiệu_hiển thị | Nhập thương hiệu trình chỉnh sửa | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| sdk_trình chỉnh sửa_về_mục nhập_hiển thị | Nhập thương hiệu trình chỉnh sửa | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| sdk_trình chỉnh sửa_chính thức_trang web_mục nhập_hiển thị | Nhập thương hiệu trình chỉnh sửa | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| bảng_liên kết_tham chiếu_hoặc_công thức | Chỉnh sửa bảng ứng dụng / Trường - Tham chiếu liên kết & Công thức liên kết | Công tắc tính năng | Ẩn/Tắt | Có thể cấu hình trên Trang |
| bảng_thông báo | Chỉnh sửa bảng ứng dụng / Nhắc nhở ngày | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| bảng_tham khảo_dữ liệu | Chỉnh sửa bảng ứng dụng / Tham chiếu bảng dữ liệu (Các trang tính gộp) | Công tắc tính năng | Ẩn/Tắt | Có thể cấu hình trên Trang |
| tải lên_hình ảnh_gif | Định dạng hình ảnh tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| tải lên_hình ảnh_jpeg | Định dạng hình ảnh tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| tải lên_hình ảnh_png | Định dạng hình ảnh tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| tải lên_hình ảnh_tiff | Định dạng hình ảnh tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| tải lên_hình ảnh_webp | Định dạng hình ảnh tải lên | Công tắc tính năng | Bật | Có thể cấu hình trên Trang |
| đính kèm_giới hạn_tất cả_img_kích thước | Tham số tệp đính kèm / Kích thước tối đa cho hình ảnh đã tải lên (MB) | Hạn ngạch | Mặc định 512; 0–512 | Có thể cấu hình trên Trang |
| đính kèm_giới hạn_tất cả_kích thước | Tham số tệp đính kèm / Kích thước tối đa cho các tệp đính kèm đã tải lên (GB) | Hạn ngạch | Mặc định 2048; 0–2048 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_biểu mẫu_kích thước | Chỉnh sửa Tham số / Kích thước Dữ liệu Tối đa có thể Chỉnh sửa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_biểu mẫu_gửi | Chỉnh sửa Tham số / Số lượng Bản nộp Tối đa mỗi Mẫu | Hạn ngạch | Mặc định 50000; 0–50000 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_modoc_kích thước | Chỉnh sửa Tham số / Kích thước Dữ liệu Tối đa có thể Chỉnh sửa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_mosheet_tính_ô | Chỉnh sửa Tham số / Công thức - Tham chiếu chéo Trang - Số ô tham chiếu Tối đa | Hạn ngạch | Mặc định 1500000; 0–1500000; Chưa Xác minh | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_mosheet_tính_độ phức tạp | Chỉnh sửa Tham số / Công thức - Tham chiếu chéo Trang - Độ phức tạp của Công thức tham chiếu | Hạn ngạch | Mặc định 6000000; 0–6000000; Chưa Xác minh | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_mosheet_hàm_tham chiếu | Chỉnh sửa Tham số / Công thức - Số hàm tham chiếu chéo trang tối đa có thể nhập (đơn vị) | Hạn ngạch | Mặc định 4000; 0–4000 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_mosheet_bảng tính_ô | Chỉnh sửa Tham số / Số ô tối đa trong một bảng tính | Hạn ngạch | Mặc định 0; 0–0; Chưa xác thực | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_mosheet_bảng tính_fc | Chỉnh sửa Tham số / Số công thức tối đa có thể nhập trong một bảng tính | Hạn ngạch | Mặc định 0; 0–0; Chưa xác thực | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_mosheet_kích thước | Chỉnh sửa Tham số / Khối lượng dữ liệu tối đa có thể chỉnh sửa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_mosheet_xem | Chỉnh sửa Tham số / Số lượng chế độ xem riêng biệt tối đa mà người dùng có thể tạo trong một bảng tính (đơn vị) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_trình bày_trang | Chỉnh sửa Tham số / Số lượng slide | Hạn ngạch | Mặc định 2000; 0–2000 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_trình bày_kích thước | Chỉnh sửa Tham số / Khối lượng dữ liệu tối đa có thể chỉnh sửa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_rdoc_kích thước | Chỉnh sửa Tham số / Khối lượng dữ liệu tối đa có thể chỉnh sửa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_lịch_xem | Chỉnh sửa Tham số / Số chế độ xem lịch tối đa mỗi tệp | Hạn ngạch | Mặc định 200; 0–200 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_đếm | Chỉnh sửa Tham số / Số lượng bảng dữ liệu tối đa | Hạn ngạch | Mặc định 200; 0–200 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_gantt_xem | Chỉnh sửa Tham số / Số chế độ xem Gantt tối đa mỗi tệp | Hạn ngạch | Mặc định 200; 0–200 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_khóa_xem | Chỉnh sửa Tham số / Số chế độ xem khóa tối đa mỗi bảng dữ liệu | Hạn ngạch | Mặc định 50; 0–50 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_thủ công_phiên bản | Chỉnh sửa Tham số / Số phiên bản lưu thủ công | Hạn ngạch | Mặc định 10000; 0–10000 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_gộp_bảng_tham chiếu | Chỉnh sửa tham số / Số lượng bảng dữ liệu tối đa mà một bảng tính hợp nhất có thể tham chiếu | Hạn ngạch | Mặc định 20; 0–20 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_gộp_bảng_tóm tắt | Chỉnh sửa tham số / Số lượng bảng tính hợp nhất tối đa | Hạn ngạch | Mặc định 20; 0–20 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_cá nhân_xem | Chỉnh sửa tham số / Số lượng chế độ xem cá nhân tối đa cho một bảng dữ liệu | Hạn ngạch | Mặc định 50; 0–50 | Có thể cấu hình trên trang |
| chỉnh sửa_giới hạn_bảng_đơn_cột | Chỉnh sửa tham số / Tổng số cột của một bảng dữ liệu | Hạn ngạch | Mặc định 50; 0–50 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_bảng_đơn_hàng | Chỉnh sửa tham số / Tổng số hàng của một bảng dữ liệu | Hạn ngạch | Mặc định 20000; 0–20000 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_bảng_đơn_xem | Chỉnh sửa tham số / Số chế độ xem tối đa của một bảng dữ liệu | Hạn ngạch | Mặc định 200; 0–200 | Có thể cấu hình trên Trang |
| chỉnh sửa_giới hạn_bảng_kích thước | Chỉnh sửa tham số | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên Trang |
| xuất_giới hạn_rdoc_pixel_chiều cao | Xuất tham số / Chiều cao tối đa của hình ảnh xuất ra (px) | Hạn ngạch | Mặc định 66000; 0–66000 | Có thể cấu hình trên Trang |
| xuất_kích thước_giới hạn | Xuất tham số / Kích thước tối đa của tập tin xuất ra (GB) | Hạn ngạch | Mặc định 3072; 0–3072 | Có thể cấu hình trên Trang |
| lịch sử_giới hạn_tất cả_thời gian | Tham số lịch sử / Số ngày lưu giữ tệp lịch sử | Hạn ngạch | Mặc định 10000000000000000; 0–10000000000000000; Không xác thực | Có thể cấu hình trên Trang |
| lịch sử_giới hạn_mosheet_ô_thời gian | Tham số lịch sử / Số ngày lưu giữ lịch sử ô bảng | Hạn ngạch | Mặc định 10000000000000000; 0–10000000000000000; Không xác thực | Có thể cấu hình trên Trang |
| lịch sử_giới hạn_khôi phục_số | Tham số lịch sử / Số bản ghi lịch sử gần đây có thể khôi phục cho một tệp | Hạn ngạch | Mặc định 2000; 0–2000 | Có thể cấu hình trên trang |
| lịch sử_giới hạn_bảng_ô_thời gian | Tham số lịch sử / Số ngày lưu giữ lịch sử cho các ô bảng ứng dụng | Hạn ngạch | Mặc định 10000000000000000; 0–10000000000000000; Không xác thực | Có thể cấu hình trên trang |
| lịch sử_giới hạn_bảng_hàng_thời gian | Tham số lịch sử / Số ngày lưu giữ lịch sử động cho các hàng bảng ứng dụng | Hạn ngạch | Mặc định 10000000000000000; 0–10000000000000000; Không xác thực | Có thể cấu hình trên trang |
| lịch sử_giới hạn_phiên bản_số | Tham số lịch sử / Số phiên bản (ảnh chụp) có thể lưu/khôi phục cho một tệp | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| nhập_xuất_thời gian chờ | Tham số nhập / Thời gian nhập tối đa (phút) | Hạn ngạch | Mặc định 10; 0–10 | Có thể cấu hình trên trang |
| nhập_giới hạn_modoc_kích thước | Tham số nhập / Kích thước tệp tối đa (MB) | Hạn ngạch | Mặc định 300; 0–300 | Có thể cấu hình trên trang |
| nhập_giới hạn_modoc_từ | Tham số nhập / Số ký tự tối đa (Ký tự) | Hạn ngạch | Mặc định 2000000; 0–2000000 | Có thể cấu hình trên trang |
| nhập_giới hạn_mosheet_tất cả_bảng tính_ô | Tham số nhập / Số ô hợp lệ tối đa trong một bảng tính | Hạn ngạch | Mặc định 5,000,000; 0–5,000,000 | Có thể cấu hình trên Trang |
| nhập_giới hạn_mosheet_tất cả_xml_kích thước | Tham số nhập / Tổng kích thước của tất cả XML Tệp trong bảng tính (MB) | Hạn ngạch | Mặc định 300; 0–300 | Có thể cấu hình trên Trang |
| nhập_giới hạn_mosheet_đã chuyển đổi_kích thước | Tham số nhập / ShimoDocs Khối lượng dữ liệu (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên Trang |
| nhập_giới hạn_mosheet_đơn_bảng tính_ô | Tham số nhập / Số ô hợp lệ tối đa trong một bảng tính đơn | Hạn ngạch | Mặc định 2,000,000; 0–2,000,000 | Có thể cấu hình trên Trang |
| nhập_giới hạn_mosheet_đơn_xml_kích thước | Tham số nhập / Kích thước tối đa của một tệp XML trong bảng tính (MB) | Hạn ngạch | Mặc định 20; 0–20 | Có thể cấu hình trên Trang |
| nhập_giới hạn_mosheet_kích thước | Tham số nhập / Kích thước tệp tối đa (MB) | Hạn ngạch | Mặc định 300; 0–300 | Có thể cấu hình trên Trang |
| nhập_giới hạn_trình bày_trang | Tham số nhập / Số trang trình bày tối đa (Trang) | Hạn ngạch | Mặc định 2000; 0–2000 | Có thể cấu hình trên Trang |
| nhập_giới hạn_trình bày_kích thước | Tham số nhập / Kích thước tệp tối đa (MB) | Hạn ngạch | Mặc định 100; 0–100 | Có thể cấu hình trên trang |
| nhập_giới hạn_rdoc_kích thước | Tham số nhập / Kích thước tệp tối đa (MB) | Hạn ngạch | Mặc định 50; 0–50 | Có thể cấu hình trên trang |
| nhập_giới hạn_rdoc_từ | Tham số nhập / Số ký tự tối đa (Ký tự) | Hạn ngạch | Mặc định 300000; 0–300000 | Có thể cấu hình trên trang |
| nhập_giới hạn_bảng_đơn_cột | Tham số nhập / Số cột hiệu quả tối đa trên mỗi bảng tính (Cột) | Hạn ngạch | Mặc định 50; 0–50 | Có thể cấu hình trên trang |
| nhập_giới hạn_bảng_đơn_hàng | Tham số nhập / Số hàng hiệu quả tối đa trên mỗi bảng tính (Hàng) | Hạn ngạch | Mặc định 20000; 0–20000 | Có thể cấu hình trên trang |
| dán_giới hạn | Tham số dán / Khối lượng dữ liệu tối đa cho mỗi lần dán (MB) | Hạn ngạch | Mặc định 9; 0–9 | Có thể cấu hình trên trang |
| dán_giới hạn_modoc | Tham số dán / Số ký tự tối đa cho mỗi lần dán (Ký tự) | Hạn ngạch | Mặc định 200000; 0–200000 | Có thể cấu hình trên trang |
| dán_giới hạn_mosheet | Tham số dán / Số ô tối đa cho mỗi lần dán (Đơn vị) | Hạn ngạch | Mặc định 2000000; 0–2000000 | Có thể cấu hình trên trang |
| dán_giới hạn_trình bày | Tham số dán / Số slide tối đa có thể dán cùng một lúc | Hạn ngạch | Mặc định 200; 0–200 | Có thể cấu hình trên trang |
| dán_giới hạn_rdoc | Tham số dán / Số ký tự tối đa có thể dán cùng một lúc | Hạn ngạch | Mặc định 200000; 0–200000 | Có thể cấu hình trên trang |
| dán_giới hạn_bảng | Tham số dán / Số hàng tối đa có thể dán cùng một lúc | Hạn ngạch | Mặc định 2000; 0–2000 | Có thể cấu hình trên trang |
| xem trước_thời gian chờ | Tham số xem trước / Thời gian xem trước tối đa (phút) | Hạn ngạch | Mặc định 10; 0–10 | Có thể cấu hình trên trang |

## Phụ lục B: Thuật ngữ và Tương ứng Trường Trang

| **Thuật ngữ** | **Ý nghĩa** |
| --- | --- |
| Tên Mục Cấu Hình / Danh sách trắng Tên | Tên duy nhất của mục cấu hình, ví dụ: rdoc_thông báo, chỉnh sửa_giới hạn_mosheet_kích thước. |
| ID Nhóm | Mã định danh nhóm; nhập một số nguyên dương để áp dụng phạm vi cấu hình cấp nhóm. |
| Mặc định Ứng dụng | Phạm vi cấu hình khi ID Nhóm để trống; được nhắc đến trong hướng dẫn này là cấu hình toàn cục. |
| Cấu hình Cấp Nhóm | Ghi đè các cấu hình chỉ có hiệu lực cho ID Nhóm đã chỉ định. |
| Mặc định Hệ thống | Khi không có ghi đè ở cấp này, giá trị mặc định tích hợp sẵn của sản phẩm sẽ được sử dụng. |
| Phạm vi Ứng dụng / Phạm vi Nhóm | Các cấu hình tùy chỉnh tồn tại ở lớp hiện tại, ưu tiên hơn các giá trị ở cấp trên. |
| Công tắc tính năng | Tham số kiểu chuyển đổi hoặc trạng thái. |
| Hạn mức giá trị đơn | Một giá trị tối đa và một công tắc kiểm tra giới hạn tùy chọn. |
| Hạn mức theo phạm vi | Một tham số phạm vi bao gồm cả giá trị tối thiểu và tối đa. |
| JSON Cấu hình | Tham số có cấu trúc phải luôn hợp lệ JSON; một số mục cấu hình không được hiển thị trên trang hiện tại. |
| Không kiểm tra / Không giới hạn | Không thực hiện kiểm tra giới hạn theo giá trị tối đa nhập vào. |
