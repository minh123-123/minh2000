---
title : "Tích hợp OpenSearch"
date :  "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---
**Sử dụng OpenSearch làm Cơ sở dữ liệu Vector cho OPEA**

OpenSearch là giải pháp tìm kiếm, phân tích và cơ sở dữ liệu vector mạnh mẽ. Được phát triển theo OpenSearch Software Foundation, một thành viên của Linux Foundation, giải pháp này cung cấp khả năng quản trị mở cho dự án OpenSearch trên GitHub. Amazon OpenSearch Service cung cấp giải pháp được quản lý giúp đơn giản hóa việc triển khai, mở rộng quy mô và hoạt động trên đám mây AWS. Cho dù là OpenSearch tự quản lý tại chỗ hay trên đám mây, hay sử dụng Amazon OpenSearch Service, OpenSearch đều cung cấp khả năng tìm kiếm vector hiệu suất cao để hỗ trợ tìm kiếm theo từ vựng và ngữ nghĩa nhanh, chính xác và có thể mở rộng quy mô—cho phép kết quả có liên quan hơn cho các trang web và ứng dụng AI tạo sinh.

Về bản chất, OpenSearch là một công cụ tìm kiếm. Công cụ tìm kiếm đóng vai trò quan trọng trong cuộc sống hàng ngày của chúng ta, giúp chúng ta tìm thông tin, sản phẩm, điểm đến du lịch và nhà hàng. Khi sử dụng công cụ tìm kiếm, người dùng cung cấp truy vấn văn bản, đôi khi được tinh chỉnh bằng các bộ lọc bổ sung và công cụ sẽ trả về danh sách kết quả có liên quan đến mục đích tìm kiếm của người dùng.

Là một cơ sở dữ liệu chuyên biệt, OpenSearch hoạt động cùng với các kho dữ liệu truyền thống (như cơ sở dữ liệu quan hệ) để cung cấp tìm kiếm thông lượng cao, độ trễ thấp trên khối lượng lớn văn bản phi cấu trúc, trường siêu dữ liệu có cấu trúc và nhúng vectơ. Nền tảng của OpenSearch là chỉ mục, tương tự như bảng cơ sở dữ liệu, lưu trữ tài liệu ở định dạng JSON. Mỗi tài liệu chứa các trường (khóa JSON) và các giá trị tương ứng. Người dùng có thể truy vấn tài liệu bằng văn bản, phạm vi số, ngày, dữ liệu địa lý, v.v. Khi tìm kiếm văn bản phi cấu trúc, OpenSearch xếp hạng tài liệu dựa trên điểm liên quan, ưu tiên các từ hiếm có nhiều trong tài liệu.

Những tiến bộ gần đây trong Xử lý ngôn ngữ tự nhiên (NLP) và Mô hình ngôn ngữ lớn (LLM) đã thay đổi cách chúng ta tìm kiếm và truy xuất thông tin. LLM mã hóa ngôn ngữ thành không gian vectơ nhiều chiều, cho phép tìm kiếm khớp với nghĩa thay vì từ chính xác. Chúng cũng tạo ra các phản hồi thực tế, giống con người cho các truy vấn ngôn ngữ tự nhiên.

Sự thay đổi này cho phép người dùng tương tác với các hệ thống truy xuất thông tin thông qua AI đàm thoại, tìm kiếm đa phương thức (văn bản, hình ảnh, âm thanh, video) và khám phá kiến ​​thức nâng cao.

OpenSearch đóng hai vai trò quan trọng trong các ứng dụng hỗ trợ AI:

1. Tìm kiếm ngữ nghĩa với nhúng vectơ
OpenSearch có thể lưu trữ và truy xuất các nhúng vectơ được tạo bởi các mô hình nhúng. Nó thực hiện tìm kiếm lân cận gần nhất để tìm các tài liệu có vectơ gần nhất với nhúng của truy vấn, cải thiện độ chính xác của tìm kiếm bằng cách khớp ý định thay vì từ khóa.

2. Tạo tăng cường truy xuất (RAG) cho AI tạo ra
OpenSearch có thể hoạt động như một cơ sở kiến ​​thức, truy xuất thông tin có liên quan để nâng cao phản hồi do LLM tạo ra. Bằng cách tích hợp OpenSearch vào OPEA, chúng tôi cải thiện độ chính xác và độ tin cậy của các đầu ra do AI tạo ra bằng cách dựa trên phản hồi trong dữ liệu thực tế, có thật.

![VPC](/images/4.s3/image080.png)

Nhờ khả năng hoán đổi được cung cấp bởi OPEA, hầu hết các thành phần từ [ví dụ ChatQnA] mặc định (https://github.com/opea-project/GenAIExamples/tree/main/ChatQnA)(Mô-đun 1). Tất nhiên, đối với mô-đun này, bạn sẽ cần triển khai OpenSearch. Ngoài ra, bạn sẽ sử dụng một trình thu thập và một dịch vụ vi mô chuẩn bị dữ liệu được thiết kế để hoạt động với các API truy vấn và lập chỉ mục của OpenSearch. Bạn có thể tìm thấy các thành phần này trong [thư mục thành phần của dự án OPEA GitHub](https://github.com/opea-project/GenAIComps/tree/main/comps) .

**Triển khai ChatQnA bằng OpenSearch làm cơ sở dữ liệu vector**
Đối với phòng thí nghiệm này, chúng tôi đã tạo một tập hợp thay đổi mà bạn có thể triển khai, bao gồm triển khai song song đầy đủ của ví dụ ChatQnA, trong cùng một cụm Kubernetes mà bạn đã sử dụng. Chúng tôi đã gói tất cả các thay đổi bạn cần trong một tập hợp thay đổi AWS CloudFormation mà bạn triển khai bằng lệnh bên dưới.

{{% notice info %}}
Chúng tôi đã sử dụng không gian tên Kubernetes, opensearch, để tách các pod và dịch vụ liên quan đến triển khai OpenSearch. Khi bạn sử dụng kubectl và các lệnh Kubernetes khác trong các ví dụ bên dưới, hãy đảm bảo đủ điều kiện cho lệnh bằng -n opensearch.
{{% /notice %}}

Quay lại Cloud Shell của bạn. Nếu shell đã kết thúc, hãy nhấp vào cửa sổ để mở dòng lệnh mới hoặc sử dụng biểu tượng ở đầu bảng điều khiển AWS để bắt đầu Cloud Shell mới. Sử dụng lệnh sau để triển khai bộ thay đổi OpenSearch.

![VPC](/images/4.s3/image081.png)

OpenSearch sẽ mất vài phút để triển khai. Để kiểm tra trạng thái, bạn có thể sử dụng kubectl để theo dõi trạng thái của các pod OpenSearch. Bạn có thể sử dụng lệnh

![VPC](/images/4.s3/image082.png)

để có đầu ra như thế này

![VPC](/images/4.s3/image083.png)

Chỉ tiếp tục khi bạn thấy pod opensearch-cluster-master-0 ở trạng thái Đang chạy.

**Những thay đổi đối với ChatQnA cho OpenSearch**

Phần lớn triển khai và thành phần cho ChatQnA giống hệt nhau khi bạn sử dụng OpenSearch làm back-end, so với các back-end cơ sở dữ liệu vector khác. OPEA chứa các thành phần để chuẩn bị và gửi dữ liệu đến cơ sở dữ liệu vector (chatqna-data-prep) và để truy xuất dữ liệu từ cơ sở dữ liệu vector (chatqna-retriever-usvc). Bạn có thể tìm thấy các triển khai OpenSearch cho các thành phần này trên GitHub.

Về mặt sơ đồ, giờ đây bạn đang nói chuyện với OpenSearch thông qua các thành phần dành riêng cho OpenSearch:

![VPC](/images/4.s3/image084.png)

**Hiểu về Hệ thống phân tán của OpenSearch**
OpenSearch là cơ sở dữ liệu phân tán hoạt động trên một cụm các nút, mỗi nút có thể đảm nhiệm các vai trò khác nhau để tối ưu hóa hiệu suất và khả năng mở rộng. Một nút có thể phục vụ nhiều vai trò cùng lúc, cho phép nút thực hiện nhiều chức năng khác nhau trong cụm. Một số vai trò chính của nút bao gồm:

+ **Nút dữ liệu**: Các nút này xử lý và lưu trữ dữ liệu. Chúng quản lý các yêu cầu lập chỉ mục và tìm kiếm, biến chúng thành xương sống của kiến ​​trúc phân tán của OpenSearch.

+ **Nút quản lý cụm**: Trước đây được gọi là nút chính, các nút này chịu trách nhiệm điều phối cụm. Chúng duy trì và phân phối trạng thái cụm, giám sát hoạt động của nút và đảm bảo tính ổn định của toàn bộ hệ thống. Mặc dù một nút có thể có cả vai trò dữ liệu và vai trò chính, nhưng việc tách các vai trò này thành phần cứng chuyên dụng được khuyến nghị để có hiệu suất và độ tin cậy tốt hơn.

+ **Nút ML**: Các nút này hỗ trợ khả năng học máy bằng cách chạy plugin ml-commons. Họ có thể lưu trữ và thực thi các mô hình học máy, bao gồm các mô hình ngôn ngữ lớn, cho phép xử lý dữ liệu và phân tích nâng cao.
Bằng cách tận dụng phần cứng không đồng nhất, bạn có thể chỉ định vai trò cho các nút một cách chiến lược dựa trên chức năng dự định của chúng, đảm bảo sử dụng tài nguyên hiệu quả.

OpenSearch tương tác với người dùng thông qua API RESTful, với các yêu cầu thường được định tuyến qua bộ cân bằng tải để phân phối khối lượng công việc trên các nút dữ liệu khả dụng. Khi một nút dữ liệu nhận được yêu cầu, nó hoạt động như một bộ điều phối, phân bổ các yêu cầu phụ cho các nút dữ liệu có liên quan lưu trữ dữ liệu cần thiết. Mỗi nút trong số các nút này xử lý phần yêu cầu của mình và gửi kết quả trở lại nút điều phối, nút này tổng hợp chúng thành phản hồi cuối cùng trước khi trả về cho máy khách.

**Hiểu về cách xử lý dữ liệu của OpenSearch**
Cốt lõi của cấu trúc dữ liệu của OpenSearch là chỉ mục, đóng vai trò là tập hợp hợp lý các tài liệu chứa dữ liệu có cấu trúc. Để quản lý phân phối dữ liệu hiệu quả, mỗi chỉ mục được chia thành các phân đoạn—các đơn vị lưu trữ độc lập cho phép xử lý song song.

Mỗi phân đoạn về cơ bản là một phiên bản của Apache Lucene, một thư viện tìm kiếm mạnh mẽ dựa trên Java có chức năng đọc và ghi chỉ mục tìm kiếm. Khi tạo chỉ mục, bạn sẽ xác định số lượng phân mảnh chính, xác định cách dữ liệu sẽ được phân vùng ban đầu. Khi lập chỉ mục một tài liệu, OpenSearch sẽ gán tài liệu đó cho một phân mảnh chính bằng chiến lược phân phối ngẫu nhiên, đảm bảo hiệu suất lưu trữ và truy xuất cân bằng trên toàn cụm.

![VPC](/images/4.s3/image085.png)

OpenSearch phân phối các phân mảnh trên các nút dữ liệu khả dụng trong cụm. Bạn cũng có thể đặt một hoặc nhiều bản sao cho chỉ mục. Mỗi bản sao là một bản sao hoàn chỉnh của tập hợp các phân mảnh chính. Ví dụ: nếu bạn có 5 phân mảnh chính và một bản sao, tổng cộng bạn có 10 phân mảnh. Quyết định của bạn về số lượng phân mảnh chính, số lượng bản sao và số lượng nút dữ liệu có tầm quan trọng sống còn đối với sự thành công của cụm trong việc xử lý khối lượng công việc của bạn.

Khi định cỡ cụm, sau đây là một số hướng dẫn giúp bạn đạt được thành công.

{{% notice info %}}
Tài liệu của Amazon OpenSearch Service cũng bao gồm các biện pháp thực hành tốt nhất về một số chủ đề, bao gồm cả việc định cỡ miền OpenSearch Service. Mặc dù các biện pháp thực hành tốt nhất này dành riêng cho từng dịch vụ, nhưng tài liệu nêu chi tiết các nguyên tắc đầu tiên sẽ giúp bạn định cỡ miền tự quản lý của mình.
{{% /notice %}}

### Tính toán Yêu cầu Lưu trữ cho Siêu dữ liệu và Vectơ trong OpenSearch
Để định cỡ cụm OpenSearch của bạn một cách chính xác, hãy bắt đầu bằng cách ước tính dung lượng lưu trữ cần thiết cho cả dữ liệu vectơ và siêu dữ liệu.

**Tính toán lưu trữ vectơ**
Sử dụng công thức sau để xác định dung lượng lưu trữ cần thiết cho vectơ:

Byte trên mỗi chiều
×
Số chiều
×
Số vectơ
×
(
1
+
Số bản sao
)
Byte trên mỗi chiều×Số chiều×Số vectơ×(1+Số bản sao)
Ví dụ, nếu vectơ của bạn sử dụng số dấu phẩy động (mặc định, 4 byte trên mỗi chiều), có 768 chiều và bạn có 1 triệu vectơ với 1 bản sao, yêu cầu lưu trữ vectơ của bạn là:

4
×
768
×
1
,
000
,
000
×
2
=
6
𝐺
𝐵
4×768×1.000.000×2=6GB
**Tính toán lưu trữ siêu dữ liệu**
Đối với siêu dữ liệu, hãy sử dụng công thức:

(
1
+
Số lượng bản sao
)
×
Kích thước dữ liệu nguồn (tính bằng byte)
×
1,10
(1+Số lượng bản sao)×Kích thước dữ liệu nguồn (tính bằng byte)×1,10
Ở đây, 1,10 là hệ số lạm phát tính đến sự khác biệt giữa kích thước dữ liệu nguồn thô và kích thước lưu trữ được lập chỉ mục. Nếu kích thước siêu dữ liệu của bạn là 100 GB với một bản sao, tổng dung lượng lưu trữ siêu dữ liệu cần thiết là:

2
×
100
𝐺
𝐵
×
1.10
=
220
𝐺
𝐵
2×100GB×1.10=220GB
**Yêu cầu về tổng dung lượng lưu trữ**
Cộng dung lượng lưu trữ vector và dung lượng lưu trữ siêu dữ liệu để có được tổng dung lượng lưu trữ cần thiết cho cụm OpenSearch của bạn.

### Xác minh triển khai OpenSearch
Để xác minh triển khai, bạn sẽ sử dụng chuyển tiếp cổng Kubernetes để gọi các dịch vụ vi mô khác nhau. Bạn có thể xác minh triển khai OpenSearch đang hoạt động bằng cách thực hiện yêu cầu GET đối với URL cơ sở. OpenSearch lắng nghe trên cổng 9200. Sử dụng lệnh sau để ánh xạ cổng 9200 đến cổng cục bộ 9200 của bạn. (Nếu thiết bị đầu cuối Cloud Shell của bạn đã kết thúc, hãy nhấp vào cửa sổ hoặc mở thiết bị đầu cuối mới từ bảng điều khiển AWS.)

![VPC](/images/5.fwd/image086.png)

{{% notice info %}}
Trong phần hướng dẫn này, bạn sẽ sử dụng chuyển tiếp cổng cho một số dịch vụ. Chuyển tiếp cổng mất vài giây để bắt đầu, hãy đảm bảo đợi dòng xác nhận trông như thế này: Chuyển tiếp từ 127.0.0.1:9200 -> 9200.
{{% /notice %}}

Bây giờ bạn có thể truy vấn OpenSearch trực tiếp trên localhost:9200. OpenSearch hỗ trợ giao tiếp được mã hóa thông qua Bảo mật lớp truyền tải (TLS) và được cung cấp kèm theo chứng chỉ demo không được cơ quan có thẩm quyền ký. Đối với mục đích demo, bạn sẽ sử dụng tùy chọn --insecure cho curl. Kiểm soát truy cập chi tiết của OpenSearch hỗ trợ cơ sở dữ liệu người dùng/mật khẩu nội bộ để xác thực HTTP cơ bản. Nó cũng có thể tích hợp với các nhà cung cấp danh tính Security Assertion Markup Language (SAML) để đăng nhập vào OpenSearch Dashboards (giao diện người dùng của OpenSearch). Bạn sẽ cung cấp xác thực HTTP với yêu cầu curl của mình.

Truy vấn tới / chỉ trả về tình trạng và thông tin của cụm.

![VPC](/images/5.fwd/image087.png)

Bạn sẽ nhận được kết quả như sau:

![VPC](/images/5.fwd/image088.png)

Xin chúc mừng! Bạn đã tạo triển khai được OpenSearch hỗ trợ cho ví dụ ChatQnA của OPEA!

**Làm việc với OPEA ChatQnA và OpenSearch**

Bạn có thể làm việc với từng dịch vụ vi mô trong triển khai OpenSearch theo cùng cách bạn đã làm với triển khai Redis. Trong phần này, bạn sẽ thêm một tài liệu mẫu vào OpenSearch thông qua dịch vụ chatqna-data-prep, tạo nhúng và truy vấn OpenSearch bằng nhúng đó thông qua chatqna-retriever-usvc. Khi bạn thực hiện phần này của hướng dẫn, bạn sẽ thấy rằng việc chọn OpenSearch làm DB vectơ của mình có phần minh bạch ở cấp độ chuẩn bị dữ liệu và truy xuất dữ liệu. Dịch vụ OpenSearch cung cấp một mặt tiền cho phép các thành phần OPEA khác "chỉ cần sử dụng nó".

**Tải tài liệu lên OpenSearch**
Tải xuống tài liệu pdf mẫu bằng lệnh bên dưới

curl -C - -O https://raw.githubusercontent.com/opea-project/GenAIComps/main/comps/third_parties/pathway/src/data/nke-10k-2023.pdf

Bây giờ bạn có thể sử dụng dịch vụ chatqna-data-prep để gửi tài liệu đó đến OpenSearch. Sử dụng lệnh sau để ánh xạ cổng cục bộ 6007 đến cổng dịch vụ 6007.

kubectl port-forward -n opensearch svc/chatqna-data-prep 6007:6007 &

Chờ cho đến khi bạn thấy thông báo Chuyển tiếp từ 127.0.0.1:6007 -> 6007, sau đó gửi tài liệu.

![VPC](/images/5.fwd/image089.png)

Việc chuẩn bị dữ liệu sẽ mất khoảng 30 giây để xử lý tài liệu. Khi hoàn tất, bạn sẽ thấy

{"status": 200, "message": "Data preparation succeeded"}

Để xem OPEA sử dụng OpenSearch như thế nào, bạn có thể truy vấn OpenSearch trực tiếp. Một trong những bộ API cốt lõi của OpenSearch là API Compact Aligned Text (_cat). API _cat (hầu hết các API OpenSearch đều bắt đầu bằng dấu gạch dưới, _) là một API quản trị truy xuất thông tin về cụm, chỉ mục, nút, phân đoạn và nhiều thông tin khác của bạn. Để xem các chỉ mục trong OpenSearch, hãy thực hiện lệnh sau (nếu Cloud Shell của bạn đã kết thúc, bạn sẽ cần thiết lập lại chuyển tiếp cổng. Xem hướng dẫn ở trên)

curl -XGET 'https://localhost:9200/_cat/indices?v' --insecure -u admin:strongOpea0!

Bạn sẽ thấy đầu ra như thế này:

![VPC](/images/5.fwd/image090.png)

Khi kiểm tra đầu ra của OpenSearch, bạn sẽ thấy nhiều chỉ mục hệ thống khác nhau, thường được thêm dấu chấm. Trong số đó, nhật ký kiểm tra cung cấp thông tin chi tiết về cách sử dụng API, trong khi các chỉ mục như rag-opensearch và file-keys chứa dữ liệu từ ChatQnA.

Một số chỉ mục có thể xuất hiện với trạng thái sức khỏe màu vàng, cho biết OpenSearch không thể phân bổ đầy đủ một phân đoạn. Điều này thường xảy ra khi một chỉ mục có cả phân đoạn chính và phân đoạn bản sao, nhưng chỉ có một nút khả dụng trong cụm. Vì OpenSearch thực thi rằng các phân đoạn chính và phân đoạn bản sao nằm trên các nút riêng biệt, nên bản sao vẫn chưa được chỉ định, dẫn đến trạng thái màu vàng. Để đảm bảo tính khả dụng cao và khả năng chịu lỗi, tốt nhất là cấu hình ít nhất một bản sao và triển khai nhiều nút dữ liệu.

Các số liệu lưu trữ cung cấp thêm thông tin chi tiết về cấu trúc chỉ mục. Giá trị pri.store.size biểu thị tổng kích thước trên đĩa của các phân đoạn chính, trong khi store.size bao gồm cả phân đoạn chính và phân đoạn bản sao. Khi chỉ phân bổ các phân đoạn chính, các giá trị này vẫn giống hệt nhau. Số lượng tài liệu phản ánh số lượng tài liệu OpenSearch tồn tại trong một chỉ mục. Vì hệ thống tự động phân đoạn các tài liệu lớn trước khi lập chỉ mục, nên một tài liệu duy nhất—chẳng hạn như báo cáo tài chính của Nike—có thể được chia thành nhiều phân đoạn được lập chỉ mục, trong đó tài liệu Nike trong trường hợp này bao gồm 271 phân đoạn.

Với dữ liệu được lập chỉ mục thành công, khả năng truy xuất của OpenSearch có thể được kiểm tra bằng dịch vụ vi mô thu thập. Trước khi thực hiện truy vấn, trước tiên phải tạo nhúng bằng dịch vụ nhúng. Ví dụ: để tìm kiếm doanh thu của Nike vào năm 2023, truy vấn phải được chuyển đổi thành nhúng và lưu trữ để xử lý. Sau khi tạo, cần thiết lập chuyển tiếp cổng cho dịch vụ vi mô chatqna-tei để cho phép giao tiếp liền mạch với OpenSearch.

kubectl port-forward -n opensearch svc/chatqna-tei 9800:80 &

![VPC](/images/5.fwd/image091.png)

Để xác minh rằng cuộc gọi này thành công, bạn có thể sử dụng echo $question_embedding để xem nhúng vector. Bây giờ hãy sử dụng lệnh sau để gọi dịch vụ vi mô của trình thu thập và tìm các tài liệu khớp từ OpenSearch và lưu trữ chúng trong biến bash similar_docs. Thiết lập chuyển tiếp cổng:

kubectl port-forward -n opensearch svc/chatqna-retriever-usvc 9801:7000 &

Sau khi shell xác nhận rằng nó đang chuyển tiếp, hãy chạy lệnh truy xuất

![VPC](/images/5.fwd/image092.png)

Một lần nữa, bạn có thể xác minh việc truy xuất bằng lệnh echo $similar_docs | jq .. Bây giờ bạn có thể khám phá trình xếp hạng lại, liên hệ trực tiếp với tài liệu tương tự và so sánh với câu hỏi Doanh thu của Nike năm 2023 là bao nhiêu?. Dịch vụ chatqna-teirerank mong đợi một mảng các khối văn bản. Thực hiện các lệnh sau để định dạng lại $similar_docs và lưu kết quả vào tệp cục bộ rerank.json

texts=$(echo "$similar_docs" | jq -r '[.retrieved_docs[].text | @json]')
echo "{\"query\":\"Doanh thu của Nike năm 2023 là bao nhiêu?\", \"texts\": $texts}" | jq -c . > rerank.json

Bây giờ hãy thiết lập chuyển tiếp cổng cho dịch vụ chatqna-teirerank.

kubectl port-forward -n opensearch svc/chatqna-teirerank 9802:80 &

Sau khi shell phản hồi rằng nó đang chuyển tiếp, hãy thực hiện lệnh sau để xem kết quả xếp hạng lại

curl -X POST localhost:9802/rerank \
-d @rerank.json\
-H 'Content-Type: application/json'

Bạn sẽ thấy đầu ra như thế này. Trong trường hợp này, mục được truy xuất nhiều nhất vẫn có điểm số tốt nhất sau khi xếp hạng lại, tiếp theo là mục thứ ba, thứ nhất và thứ hai.

[{"index":0,"score":0.9984302},{"index":3,"score":0.9972289},{"index":1,"score":0.9776342},{"index":2,"score":0.84730965}]

OPEA sẽ sử dụng kết quả đầu tiên làm ngữ cảnh cho dịch vụ chatqna-tgi. Bây giờ bạn đã liên hệ với từng dịch vụ vi mô và thấy dữ liệu và truy vấn được chuyển đổi như thế nào trong quá trình xử lý truy vấn của OPEA.

Như một bài kiểm tra cuối cùng, bạn có thể gửi truy vấn đến bộ cân bằng tải để xem kết quả. Sử dụng lệnh sau để lấy địa chỉ của bộ cân bằng tải:

kubectl get ingress -n opensearch

Bạn sẽ thấy kết quả như thế này

opensearch-ingress alb * opensearch-ingress-156457628.us-east-2.elb.amazonaws.com 80 46h

Sao chép địa chỉ của bộ cân bằng tải và dán vào lệnh bên dưới để xem phản hồi của ChatQnA cho truy vấn "Doanh thu của Nike năm 2023 là bao nhiêu?"

curl http://[TÊN DNS INGRESS CỦA BẠN]/v1/chatqna -H "Content-Type: application/json" -d '{"messages": "Doanh thu của Nike năm 2023 là bao nhiêu?"}'

Bạn sẽ thấy văn bản phát trực tuyến có câu trả lời: "Trong năm tài chính 2023, NIKE, Inc. Doanh thu là 51,2 tỷ đô la."

Để kiểm tra cuối cùng, hãy sao chép-dán URL ingress vào trình duyệt của bạn, tại đó bạn có thể thử truy vấn từ UI.

**Kết luận**

Trong nhiệm vụ này, OpenSearch được triển khai dưới dạng cơ sở dữ liệu vector và tích hợp của nó với OPEA đã được thử nghiệm. Các kết nối trực tiếp đã được thực hiện với các dịch vụ vi mô chuẩn bị dữ liệu, nhúng và truy xuất để hiểu sâu hơn về cách OpenSearch tương tác với các thành phần này. Ngoài ra, API truy vấn của OpenSearch đã được khám phá để kiểm tra khả năng truy xuất tài liệu của nó. Để khám phá thêm, mô-đun Explore OpenSearch (Tùy chọn) cung cấp cơ hội để đào sâu hơn vào API và thử nghiệm với các loại truy vấn khác nhau.