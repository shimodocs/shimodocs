# Cấu hình AI

[← ShimoDocs Suite Tài liệu triển khai](../../README.md)

Cấu hình AI được dùng để kết nối ShimoDocs Suite với các mô hình cơ sở, mô hình hình ảnh, tìm kiếm trực tuyến và các dịch vụ Embedding. Sau khi hoàn tất cấu hình, các chức năng trong ShimoDocs Suite như hội thoại AI, tạo nội dung, xử lý hình ảnh và truy xuất kiến thức có thể truy cập các dịch vụ tương ứng. 

## 1. Hiểu Bốn Loại Khả Năng Trước Khi Cấu Hình 

Mục đích của bốn loại cấu hình là khác nhau, và bạn không nhất thiết phải cấu hình tất cả. Vui lòng chọn dựa trên ShimoDocs Suite các tính năng mà bạn dự định bật. 

| Loại Cấu Hình | Mục đích | Cần Cấu Hình | 
| --- | --- | --- |
| Mô Hình Cơ Sở | Xử lý hội thoại, viết lách, tóm tắt, viết lại, hỏi đáp và các tác vụ văn bản hoặc đa phương tiện khác | Thông thường cần thiết khi sử dụng các tính năng AI |
| Mô Hình Hình Ảnh | Tạo hoặc chỉnh sửa hình ảnh | Chỉ cần khi sử dụng các tính năng tạo hoặc chỉnh sửa hình ảnh |
| Tìm Kiếm Trực Tuyến | Truy xuất thông tin từ các dịch vụ tìm kiếm bên ngoài để bổ sung tham chiếu của mô hình | Chỉ cần khi sử dụng các tính năng truy xuất trực tuyến |
| Embedding | Chuyển đổi văn bản thành vector cho truy xuất cơ sở tri thức, tìm kiếm ngữ nghĩa và các tính năng tương tự | Chỉ cần khi sử dụng các tính năng truy xuất tri thức hoặc tìm kiếm vector |

> [!TIP]
>
> Tìm kiếm trực tuyến thường là một dịch vụ tìm kiếm độc lập và không giống như các khả năng trực tuyến được tích hợp trong mô hình cơ sở. 

## 2. Truy cập Cấu Hình AI 

1. Đăng nhập vào **MDP Nền tảng Vận hành**. 
2. Ở trên cùng, chọn **ShimoDocs Suite**.
3. Trong thanh bên trái, chọn **Quản lý người thuê**.
4. Tìm thẻ **Cấu hình AI** .
5. Nhấp vào thẻ để vào trang "Cấu Hình Mô Hình AI và Tìm Kiếm".

## 3. Trước tiên, chọn nhà cung cấp dịch vụ mô hình để kết nối

Vui lòng xác nhận trước dịch vụ mô hình mà bạn dự định sử dụng, sau đó chuyển đến mục tương ứng để cấu hình.

| Loại Mô Hình | Nhà Cung Cấp Dịch Vụ |
| --- | --- |
| Mô Hình Cơ Sở | Các nhà cung cấp tương thích với giao thức OpenAI Responses |
| Mô Hình Hình Ảnh | Các nhà cung cấp tương thích với giao thức OpenAI Image |
| Mô hình Công Cụ Tìm Kiếm Internet | Hiện chỉ hỗ trợ Volcengine |
| Mô Hình Nhúng (Embedding Model) | Các nhà cung cấp tương thích với giao thức nhúng của OpenAI |

## 4. Cấu Hình Mô Hình AI

Phần này được sử dụng để cấu hình các dịch vụ liên quan đến GPT. Vui lòng để bộ phận Kỹ Thuật xác nhận có sử dụng dịch vụ chính thức của OpenAI, Azure OpenAI, dịch vụ proxy, hay các giao diện tương thích khác, vì địa chỉ yêu cầu và ID mô hình có thể thay đổi tùy theo phương thức kết nối.

### 4.1 Mô Hình Cơ Bản

Mô hình cơ bản được sử dụng cho hội thoại, tạo nội dung, tóm tắt, viết lại và các chức năng hiểu đa phương thức.

#### Cấu Hình Nhà Cung Cấp

| Mục Cấu hình | Giá trị ví dụ | Mô tả |
| --- | --- | --- |
| Nhà Cung Cấp | Chọn OpenAI (hoặc tương thích với giao thức OpenAI Responses) | Chọn OpenAI (hoặc tương thích với giao thức OpenAI Responses) |
| Yêu Cầu URL / Cơ bản URL | https://myai.com/v1 | Chọn địa chỉ cổng AI của bạn tương thích với giao thức OpenAI Responses |
| API Khóa | sk-I••••haTO |  API Khóa được cổng AI của bạn cấp |
| Mô hình mặc định | gpt-5.5 | Mô hình tương thích với giao thức OpenAI Responses |

> [!TIP]
>
> Nhà cung cấp mô hình được cấu hình ở đây cần hỗ trợ chế độ truyền trực tuyến. ShimoDocs AI, với tư cách là khách hàng, sẽ luôn gửi `stream: true` khi yêu cầu từ nhà cung cấp mô hình. Nếu nhà cung cấp mô hình không hỗ trợ chế độ truyền trực tuyến, yêu cầu sẽ thất bại.

#### Cấu hình mô hình

| Mục Cấu hình | Giá trị ví dụ | Ghi chú phát triển |
| --- | --- | --- |
| Trạng thái | Đã bật | Cần được bật |
| ID mô hình | gpt-5.5 | ID mô hình hợp lệ |
| Tên mô hình | gpt-5.5 | Cần trùng với ID mô hình |
| Cửa sổ Ngữ cảnh | 1024000 | Điền theo điều kiện thực tế |
| Nhập văn bản | Đã bật | Cần được bật |
| Nhập hình ảnh | Đã bật | Cần được bật |

### Mô hình Hình ảnh 4.2

Các mô hình hình ảnh được sử dụng để tạo hình ảnh hoặc chỉnh sửa hình ảnh. Vui lòng điền các mô hình và khả năng mà phiên bản hiện tại thực sự hỗ trợ.

| Mục Cấu hình | Giá trị ví dụ | Ghi chú Kỹ thuật |
| --- | --- | --- |
| Trạng thái | Đã bật | Cần được bật |
| Nhà Cung Cấp | OpenAI (hoặc tương thích với giao thức hình ảnh của OpenAI) | OpenAI (hoặc tương thích với giao thức hình ảnh của OpenAI) |
| Tên mô hình | gpt-image-2 | Cần tương thích với giao thức hình ảnh của OpenAI |
| Yêu Cầu URL / Cơ bản URL | https://myai.com/v1 | Chọn địa chỉ cổng AI của bạn tương thích với giao thức Phản hồi của OpenAI |
| API Khóa | sk-I••••haTO |  API Khóa được cổng AI của bạn cấp |
| Tính năng | tạo-hình-ảnh, chỉnh-sửa-hình-ảnh | Giữ mặc định tạo-hình-ảnh, chỉnh-sửa-hình-ảnh |

> [!TIP]
>
> Hiện tại chỉ hỗ trợ Hình ảnh OpenAI API giao thức

### Mô hình Tìm kiếm Internet 4.3

Tìm kiếm mạng hiện tại chỉ hỗ trợ cấu hình Volcengine.

| Mục Cấu hình | Giá trị ví dụ | Ghi chú Kỹ thuật |
| --- | --- | --- |
| Trạng thái | Đã bật | Bật theo nhu cầu thực tế. Nếu bật, bạn cần hoàn thành giá trị của tất cả các mục khác trong nhóm cấu hình hiện tại |
| Nhà Cung Cấp Dịch Vụ | Volcengine | Hiện chỉ hỗ trợ Volcengine |
| API Điểm cuối (Endpoint) | https://open.feedcoopapi.com/search_api/web_search | Địa chỉ tìm kiếm mạng mặc định của Volcengine |
| API Khóa | mCmh•••••••• | Lấy từ nhà cung cấp dịch vụ tìm kiếm mạng |
| Cài đặt thời gian chờ | 120s | Nếu một yêu cầu tìm kiếm mạng đơn lẻ vượt quá thời gian này, nó sẽ thất bại. Khuyến nghị giữ ở 120s |

### Mô hình Nhúng 4.4

Các mô hình nhúng được sử dụng để truy xuất cơ sở tri thức và tìm kiếm ngữ nghĩa. ID mô hình và kích thước phải khớp với đầu ra vector thực tế.

| Mục Cấu hình | Giá trị ví dụ | Ghi chú phát triển |
| --- | --- | --- |
| Nhà Cung Cấp Dịch Vụ | OpenAI (hoặc mô hình nhúng tương thích với OpenAI) | OpenAI (hoặc mô hình nhúng tương thích với OpenAI) |
| Cơ bản URL | https://myai.com/v1 | Chọn địa chỉ cổng AI của bạn tương thích với giao thức Phản hồi OpenAI |
| API Khóa | ak-•••••••• | Lấy từ nhà cung cấp mô hình nhúng |
| Mô Hình Nhúng (Embedding Model) | qwen3-embedding:4b | ID mô hình |
| Kích thước | Giá trị nguyên | Kích thước liên quan đến mô hình nhúng; bạn có thể tham khảo nhà cung cấp về các tham số kích thước |

| Các mục xác nhận phát triển | Nội dung |
| --- | --- |
| Các mô hình nhúng được hỗ trợ | OpenAI (hoặc mô hình nhúng tương thích với OpenAI) |
| Kích thước vector được đề xuất | Liên quan đến mô hình nhúng |
| Có cần xây dựng lại dữ liệu vector cho các kích thước khác nhau không | Có |

> [!TIP]
>
> Hiện tại chỉ hỗ trợ OpenAI Embedding API giao thức

### 4.5 GPT Kiểm tra hoàn thành cấu hình

| Mục kiểm tra | Kết quả mong đợi | Kết quả thực tế |
| --- | --- | --- |
| Cuộc trò chuyện mô hình cơ bản | Nhập một câu hỏi đơn giản trong phiên AI | Mô hình trả về kết quả tương ứng |
| Xử lý văn bản dài | Xuất văn bản dài | Mô hình trả về kết quả tương ứng dựa trên nội dung văn bản dài |
| Nhập hoặc xử lý hình ảnh | Nhập một hình ảnh để nhận dạng | Có thể trả về nội dung đã nhận dạng |
| Tìm kiếm trên Internet | Nhắc AI lấy thông tin vé máy bay hoặc vé tàu | Có thể trả về kết quả vé máy bay hoặc vé tàu |
| Vector hóa nhúng | Sử dụng từ khóa để tìm kiếm AI trên toàn trang | Có thể trả về nội dung khớp mong đợi |

## 5. Ý nghĩa kinh doanh của từng mục cấu hình

Phần này cung cấp giải thích thống nhất về mục đích của từng mục cấu hình trên trang. Khi cấu hình lần đầu, bạn có thể điền theo mẫu của nhà cung cấp đã đề cập trước đó, sau đó quay lại phần này để xác nhận xem từng trường có đáp ứng yêu cầu kinh doanh thực tế hay không.

### 5.1 Các mục cấu hình nhà cung cấp mô hình cơ bản

| Mục Cấu hình | Ý nghĩa kinh doanh | Ảnh hưởng phổ biến khi nhập sai | Bắt buộc |
| --- | --- | --- | --- |
| Nhà Cung Cấp | Chỉ cho hệ thống phương pháp thích ứng mô hình nào sẽ được sử dụng. Ngay cả khi hai dịch vụ tương thích với các giao diện tương tự, tùy chọn nhà cung cấp cũng có thể xác định định dạng yêu cầu, phương pháp xác thực và cách kết quả được phân tích. | Có thể không lưu được, định dạng yêu cầu không khớp hoặc phản hồi không thể phân tích. | Có |
| Yêu Cầu URL / Cơ bản URL | Địa chỉ nhập liệu ShimoDocs Suite truy cập khi gửi yêu cầu đến dịch vụ mô hình. | Không thể kết nối với mô hình nếu địa chỉ không đúng; giao diện có thể xuất hiện không tồn tại nếu cấp độ đường dẫn sai. | Có |
| API Khóa | Chứng chỉ được dịch vụ mô hình sử dụng để xác định người gọi và xác minh quyền. | Thường xuất hiện thông báo thất bại xác thực nếu sai, hết hạn hoặc quyền không đủ. | Có |
| Mô hình mặc định | Mô hình mà hệ thống ưu tiên gọi khi các chức năng kinh doanh không cụ thể xác định mô hình. | Một số chức năng AI có thể không khả dụng nếu không được thiết lập hoặc thiết lập cho mô hình không khả dụng. | Có |

### 5.2 Các mục cấu hình mô hình cơ bản

| Mục Cấu hình | Ý nghĩa kinh doanh | Ảnh hưởng phổ biến khi nhập sai | Bắt buộc |
| --- | --- | --- | --- |
| Trạng thái | Kiểm soát việc mô hình có được phép gọi bởi ShimoDocs Suite. Sau khi đóng, cấu hình vẫn có thể được giữ, nhưng doanh nghiệp thường không thể tiếp tục sử dụng mô hình. | Ngay cả khi cấu hình mô hình đúng, nếu trạng thái là đã đóng, doanh nghiệp vẫn có thể hiển thị mô hình là không khả dụng. | Có |
| ID mô hình | Tên mô hình hoặc định danh duy nhất được giao diện dịch vụ mô hình nhận diện. | Thường xuất hiện thông báo mô hình không tồn tại nếu không khớp với tên máy chủ. | Có |
| Tên mô hình | Tên hiển thị cho quản trị viên trên Nền tảng Vận hành để giúp phân biệt các mô hình khác nhau. | Nếu tên trùng lặp hoặc không rõ ràng, dễ chọn phải mô hình sai; việc có tham gia vào yêu cầu thực tế hay không được Kỹ thuật xác nhận. | Có |
| Cửa sổ Ngữ cảnh | Tổng lượng thông tin mà một mô hình có thể xử lý trong một yêu cầu duy nhất, thường ảnh hưởng đến độ dài của văn bản nhập, lịch sử hội thoại và không gian đầu ra. | Thiết lập lớn hơn khả năng thực tế của mô hình có thể gây ra lỗi yêu cầu; thiết lập quá nhỏ có thể dẫn đến việc nội dung bị cắt ngắn hoặc không thể gửi. | Có |
| Nhập văn bản | Chỉ ra xem mô hình có thể chấp nhận nội dung văn bản hay không. | Nếu thiết lập sai thành tắt, các chức năng liên quan đến văn bản có thể không thể chọn hoặc gọi mô hình này. | Có |
| Nhập hình ảnh | Chỉ ra xem mô hình cơ bản có thể hiểu hình ảnh do người dùng tải lên hay không; đây là khả năng đầu vào đa phương tiện và không giống như việc tạo ra hình ảnh. | Kích hoạt nó cho một mô hình không hỗ trợ hình ảnh có thể gây ra lỗi yêu cầu; nếu tắt, chức năng hiểu hình ảnh sẽ không khả dụng. | Có |

### 5.3 Tùy chọn Cấu hình Mô hình Hình ảnh

| Mục Cấu hình | Ý nghĩa kinh doanh | Hiệu quả Thường gặp của Việc Thiết lập Sai | Bắt buộc |
| --- | --- | --- | --- |
| Trạng thái | Kiểm soát xem mô hình hình ảnh có thể được gọi bởi các chức năng tạo hoặc chỉnh sửa hình ảnh hay không. | Nếu trạng thái là tắt, các chức năng hình ảnh liên quan không thể sử dụng mô hình. | Có |
| Nhà Cung Cấp Dịch Vụ | Xác định phương pháp thích ứng giao diện được sử dụng cho các yêu cầu hình ảnh. | Chọn sai có thể dẫn đến các tham số yêu cầu hoặc định dạng trả về không tương thích. | Có |
| Tên Mô hình / ID Mô hình | Chỉ định mô hình hình ảnh thực tế sẽ được gọi. Liệu trường này là tên hiển thị hay ID yêu cầu cần được xác nhận bởi Bộ phận Kỹ thuật. | Nếu tên không trùng khớp với máy chủ, điều đó có thể chỉ ra rằng mô hình không tồn tại. | Có |
| Cơ bản URL | Địa chỉ dịch vụ mà các yêu cầu tạo hoặc chỉnh sửa hình ảnh được gửi tới. | Nếu địa chỉ hoặc đường dẫn sai, dịch vụ hình ảnh không thể được gọi. | Có |
| API Khóa | Thông tin xác thực được sử dụng để gọi dịch vụ hình ảnh. | Lỗi, hết hạn hoặc thiếu quyền sẽ gây thất bại trong xác thực. | Có |
| Tính năng | Khai báo các khả năng hình ảnh được mô hình hỗ trợ, chẳng hạn như tạo hình ảnh, chỉnh sửa hình ảnh, v.v. | Nếu một khả năng không được mô hình hỗ trợ được cấu hình, mục kinh doanh có thể hiển thị nhưng cuộc gọi sẽ thất bại. | Có |

Lưu ý: Hiện tại, chỉ hỗ trợ giao thức Hình ảnh của OpenAI API 

### 5.4 Cấu hình Tìm kiếm Internet

| Mục Cấu hình | Ý nghĩa kinh doanh | Ảnh hưởng chung nếu sai | Bắt buộc |
| --- | --- | --- | --- |
| Trạng thái | Điều khiển việc ShimoDocs Suite có thể gọi dịch vụ tìm kiếm hiện tại hay không. | Khi trạng thái tắt, mô hình vẫn có thể khả dụng, nhưng không thể lấy kết quả tìm kiếm trên internet. | Không |
| Nhà Cung Cấp Dịch Vụ | Chỉ định loại dịch vụ tìm kiếm sẽ sử dụng và phương pháp điều chỉnh giao diện của nó. | Nếu chọn sai, yêu cầu và phân tích kết quả có thể không tương thích. | Không |
| Địa chỉ giao diện | Điểm cuối dịch vụ được truy cập khi khởi tạo yêu cầu tìm kiếm. | Nếu địa chỉ sai, chức năng internet có thể hết thời gian chờ hoặc không kết nối được. | Không |
| API Khóa | Chứng chỉ xác thực được dịch vụ tìm kiếm sử dụng. | Nếu sai hoặc không đủ quyền, yêu cầu tìm kiếm sẽ bị từ chối. | Không |
| Cài đặt thời gian chờ | Thời gian tối đa chờ cho một tìm kiếm; nếu vượt quá, hệ thống sẽ ngừng chờ và coi là thất bại hoặc không có kết quả. | Cài đặt quá ngắn sẽ gây ra tình trạng hết thời gian chờ thường xuyên; cài đặt quá dài làm tăng thời gian chờ của người dùng. | Không |

### 5.5 Cấu hình Nhúng

Các mô hình nhúng không nhất thiết phải được bật, nhưng nếu không bật, nội dung tài liệu không thể được vector hóa, do đó hệ thống không thể xử lý các câu hỏi liên quan đến cơ sở tri thức của người dùng.

| Mục Cấu hình | Ý nghĩa kinh doanh | Hậu quả chung khi điền sai | Có bắt buộc không |
| --- | --- | --- | --- |
| Cơ bản URL | Địa chỉ dịch vụ được gửi đến yêu cầu vectơ hóa văn bản. Nếu địa chỉ không chính xác, dữ liệu vector không thể được tạo hoặc cập nhật. Không |
| API Khóa | Thông tin xác thực được sử dụng bởi dịch vụ Embedding. Vectơ hóa thất bại do lỗi, hết hạn, hoặc thiếu quyền. Không |
| Mô Hình Nhúng (Embedding Model) | ID mô hình thực sự chịu trách nhiệm chuyển văn bản thành vectơ. Vectơ không thể được tạo khi mô hình không tồn tại hoặc không khớp. Không |
| Kích thước | Độ dài vectơ cuối cùng được tạo bởi mỗi dòng văn bản phải khớp với đầu ra thực tế của mô hình và cấu hình lưu trữ vectơ. | Nếu các chiều không nhất quán, việc ghi hoặc truy xuất thường không khả thi; Sau khi thay đổi chiều, bạn có thể cần phải tạo lại các vectơ hiện có. Không |

Lưu ý: Hiện tại, chỉ có OpenAI Embedding API giao thức 

## 6. Trình tự cấu hình được khuyến nghị 

Để giảm trùng lặp, nên cấu hình theo thứ tự sau: 

1. Trước tiên, xác nhận những tính năng AI nào cần được kích hoạt trong ShimoDocs Suite. 
2. Chọn một mô hình cơ sở đáp ứng yêu cầu giao thức. 
3. Cấu hình Nhà cung cấp và thêm ít nhất một mô hình cơ sở. 
4. Đặt mô hình khả dụng đã được xác thực làm mô hình mặc định. 
5. Cấu hình các mô hình hình ảnh theo nhu cầu kinh doanh. 
6. Cấu hình tìm kiếm qua mạng theo nhu cầu kinh doanh. 
7. Nếu sử dụng cơ sở tri thức hoặc tìm kiếm ngữ nghĩa, hãy cấu hình Embedding. 
8. Sau khi lưu, kiểm tra từng khả năng riêng biệt; không đánh giá thành công cấu hình chỉ dựa vào hiển thị "Bật" trên trang. 

## 7. Quy tắc cấu hình hiệu quả 
| Các vấn đề cần xác nhận bởi Kỹ thuật | Nội dung |
| --- | --- |
| Có hiệu lực ngay sau khi lưu cấu hình không | Không có hiệu lực ngay lập tức; bạn cần đợi 1-2 phút |
| Bạn có cần khởi động lại dịch vụ không | Không cần khởi động lại dịch vụ |
| Cấu hình mới có hiệu lực trên trang đã mở không | Bạn cần làm mới trang hiện tại |
| Lựa chọn ưu tiên giữa nhiều mô hình | Không được hỗ trợ |
| Nó có tự động chuyển khi mô hình mặc định không khả dụng không? | Không được hỗ trợ |

## 8. Xử lý sự cố phổ biến 

| Hiện tượng | Nguyên nhân phổ biến | Phương pháp xử lý sự cố |
| --- | --- | --- |
| Dịch vụ mô hình kết nối gặp sự cố | Địa chỉ yêu cầu, mạng, chứng chỉ hoặc cấu hình cổng có sự bất thường | Kiểm tra địa chỉ dịch vụ, DNS, cổng, chứng chỉ và chính sách tường lửa | 
| Thông báo thất bại xác thực | API Lỗi khóa, hết hạn hoặc quyền hạn không đủ | Xác nhận rằng API Khóa là chính xác và có quyền truy cập vào mô hình hoặc dịch vụ mục tiêu | 
| Mô hình Prompt Không Tồn Tại | ID Mô hình Không Khớp với Tên Máy chủ | Xác nhận đầy đủ ID Mẫu và kiểm tra chữ hoa chữ thường cũng như hậu tố phiên bản | 
| Văn bản có sẵn nhưng hình ảnh không có sẵn | Mẫu không hỗ trợ nhập hình ảnh, hoặc công tắc nhập hình ảnh chưa được bật | Kiểm tra khả năng của mẫu và công tắc nhập | 
| Mục tính năng hình ảnh tồn tại nhưng không thể gọi được | Các tính năng không nhất quán với khả năng thực tế của mô hình | Xác minh khả năng tạo và chỉnh sửa được hỗ trợ bởi mô hình hình ảnh | 
| Các lần hết thời gian tìm kiếm trực tuyến thường xuyên | Dịch vụ tìm kiếm chậm, mạng không ổn định hoặc cài đặt thời gian chờ quá ngắn | Kiểm tra độ trễ mạng, hiệu suất dịch vụ và cài đặt thời gian chờ | 
| Lỗi ghi nhúng | Kích thước đầu ra không nhất quán với cấu hình lưu trữ vector | Xác minh kích thước đầu ra thực tế của mô hình và cấu hình lưu trữ | 

## Hỏi & Đáp

1. Làm thế nào để xác minh cấu hình có hiệu quả không?

Sau khi hoàn tất cấu hình, bạn có thể vào thanh bên của trình chỉnh sửa để mở một phiên AI nhằm xác minh xem chức năng có hoạt động hay không:

- Các tin nhắn nên được trả lời bình thường
- Nếu một mô hình hình ảnh được cấu hình, bạn có thể gửi một lệnh như 'Tạo một hình ảnh Xxx' và quan sát xem lệnh có thực hiện đúng không
- Nếu tìm kiếm trực tuyến được cấu hình, bạn có thể gửi một lệnh như 'Tìm kiếm trực tuyến thời tiết hôm nay ở Bắc Kinh' và quan sát xem kết quả có đúng như mong đợi không

2. Có hỗ trợ giao diện /chat/completions không?

Hiện tại không hỗ trợ. Hiện chỉ hỗ trợ giao thức OpenAI Responses API Người ta biết rằng các API chính thức như Deepseek / Xiaomi-Mimo cung cấp hỗ trợ cho giao thức này. Các giải pháp triển khai cục bộ như vLLM và Ollama cũng hỗ trợ giao thức Responses.
