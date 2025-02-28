---
title : "Khám phá triển khai OPEA ChatQnA"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 3.2. </b> "
---
### Khám phá triển khai dịch vụ vi mô OPEA
Bây giờ, chúng ta hãy cùng tìm hiểu sâu hơn về triển khai OPEA ChatQnA RAG. Là bản thiết kế dựa trên dịch vụ vi mô, bản thiết kế này được thiết kế để có khả năng mở rộng, phục hồi và linh hoạt. Trong nhiệm vụ này, bạn sẽ khám phá từng dịch vụ vi mô để hiểu vai trò của chúng trong toàn bộ hệ thống. Bằng cách kiểm tra các thành phần này, bạn sẽ hiểu sâu hơn về cách chúng tương tác và đóng góp vào chức năng của ứng dụng.

Kiến trúc này mang lại một số lợi thế chính:

+ **Khả năng mở rộng** – Mỗi dịch vụ vi mô có thể mở rộng độc lập dựa trên nhu cầu, đảm bảo hiệu suất và sử dụng tài nguyên tối ưu.

+ **Cách ly lỗi** – Nếu một dịch vụ gặp sự cố, nó sẽ không làm gián đoạn toàn bộ hệ thống, giúp tăng cường độ tin cậy.

+ **Bảo trì và cập nhật hiệu quả** – Các dịch vụ vi mô cho phép cập nhật nhanh chóng và dễ dàng thích ứng với nhu cầu kinh doanh và nhu cầu của người dùng đang thay đổi.

#### Kiến trúc dịch vụ vi mô OPEA
Các triển khai OPEA được xây dựng xung quanh ba thành phần chính:

![VPC](/images/2/image025.png)

+ **Megaservice** – Hoạt động như một đơn vị điều phối cho tất cả các dịch vụ vi mô, quản lý quy trình làm việc và đảm bảo tương tác liền mạch giữa các thành phần. Điều này rất cần thiết để phối hợp một ứng dụng đầu cuối với nhiều bộ phận chuyển động. Bạn có thể tìm thêm thông tin chi tiết trong tài liệu OPEA.

+ **Gateway** – Hoạt động như điểm vào cho người dùng, định tuyến các yêu cầu đến đến các dịch vụ vi mô phù hợp trong kiến ​​trúc dịch vụ vi mô. Đảm bảo kết nối liền mạch giữa người dùng bên ngoài và các thành phần bên trong.

+ **Microservice** – Đây là các thành phần chức năng riêng lẻ của ứng dụng, xử lý các tác vụ như nhúng, truy xuất, xử lý LLM và tương tác cơ sở dữ liệu vector.
Truy cập các dịch vụ vi mô

Trước khi bắt đầu khám phá, hãy lưu ý rằng chỉ có dịch vụ cổng và giao diện người dùng được hiển thị bên ngoài. Trong tác vụ này, bạn sẽ trực tiếp truy cập từng dịch vụ vi mô nội bộ cho mục đích thử nghiệm, sử dụng cổng Nginx để định tuyến hiệu quả các yêu cầu đến các dịch vụ nội bộ này.

{{% notice info %}}
Bạn sẽ cần ghi chú tất cả các pod đã triển khai.
{{% /notice %}}

kubectl get svc liệt kê tất cả các dịch vụ trong cụm Kubernetes, hiển thị tên, loại, IP cụm và cổng được hiển thị của chúng. Lệnh này cung cấp tổng quan về cách các ứng dụng được hiển thị để truy cập nội bộ hoặc bên ngoài.

Chạy lệnh sau trên CloudShell của bạn:

![VPC](/images/2/image026.png)

Bạn sẽ thấy đầu ra tương tự như sau:

![VPC](/images/2/image027.png)

Lệnh kubectl get svc được sử dụng để liệt kê các dịch vụ đang chạy trong cụm Kubernetes. Các dịch vụ hoạt động như các điểm vào cho phép giao tiếp giữa các thành phần khác nhau của ứng dụng của bạn. Mỗi dịch vụ có một tên duy nhất (ví dụ: chatqna hoặc chatqna-ui), giúp xác định vai trò của dịch vụ đó trong hệ thống.

Các dịch vụ Kubernetes có thể được hiển thị theo nhiều cách khác nhau:

+ **ClusterIP** – Chỉ có thể truy cập trong cụm, cho phép các thành phần nội bộ giao tiếp an toàn.

+ **NodePort** – Hiển thị dịch vụ bên ngoài thông qua một cổng cụ thể trên mỗi nút, giúp dịch vụ có thể truy cập được bên ngoài cụm.
Cluster-IP là địa chỉ nội bộ được các dịch vụ khác sử dụng để tiếp cận ứng dụng. Nếu dịch vụ có thể truy cập được từ bên ngoài cụm, External-IP sẽ được hiển thị. Tuy nhiên, trong trường hợp này, các dịch vụ này hoàn toàn là nội bộ.

Cột Cổng cho biết dịch vụ lắng nghe trên cổng mạng nào. Ví dụ:

+ chatqna có thể đang chạy trên cổng 8888/TCP, xử lý giao tiếp nội bộ.

+ chatqna-nginx có thể được cấu hình với 80:30144/TCP, trong đó lưu lượng từ cổng 80 được chuyển tiếp đến 30144 cho mục đích định tuyến.
Cuối cùng, cột Tuổi hiển thị thời gian dịch vụ đã chạy—ví dụ: 12 giờ cho tất cả các dịch vụ được liệt kê trong kịch bản này.

Bây giờ, chúng ta hãy khám phá kiến ​​trúc chi tiết.

#### Bước 1: Megaservice (Orchestrator) (POD:chatqna:8888)
Megaservice đóng gói toàn bộ logic cho ứng dụng ChatQnA RAG. Dịch vụ siêu nhỏ này có nhiệm vụ xử lý các yêu cầu đến và thực hiện tất cả các hoạt động nội bộ cần thiết để tạo ra các phản hồi phù hợp.

Dịch vụ này không được hiển thị trực tiếp, nhưng bạn có thể truy cập trực tiếp từ LoadBalancer, dịch vụ này sẽ chuyển tiếp yêu cầu.

+ Tìm bộ cân bằng tải

![VPC](/images/2/image028.png)

+ Nhấp vào chatqna-Ingress

![VPC](/images/2/image029.png)

+ Lưu ý Tên DNS. Như đã đề cập, đây là URL công khai có thể truy cập bên ngoài.

![VPC](/images/2/image030.png)

Bạn sẽ sử dụng lệnh curl để gửi yêu cầu đến các điểm cuối API, kiểm tra từng dịch vụ vi mô riêng lẻ. Mục tiêu là đặt một câu hỏi, chẳng hạn như "Doanh thu của Nike vào năm 2023 là bao nhiêu?" và xác minh rằng API phản hồi chính xác. Bước này đảm bảo rằng tất cả các dịch vụ vi mô trong hệ thống đang hoạt động như mong đợi.

![VPC](/images/2/image031.png)

Nếu mọi thứ hoạt động bình thường, bạn sẽ nhận được phản hồi xác nhận rằng quy trình làm việc Retrieval-Augmented Generation (RAG) đang hoạt động.

Tuy nhiên, bạn có thể nhận thấy rằng mô hình không thể cung cấp câu trả lời chính xác. Điều này xảy ra vì nó thiếu ngữ cảnh cần thiết và dựa vào thông tin lỗi thời. Nếu không có quyền truy cập vào dữ liệu hiện tại và có liên quan, mô hình không thể tạo ra phản hồi chính xác.
Trong các bước tiếp theo, bạn sẽ cải thiện hệ thống bằng RAG, cho phép mô hình truy xuất thông tin mới nhất, có liên quan theo ngữ cảnh. Điều này sẽ đảm bảo rằng mô hình cung cấp câu trả lời chính xác và có ý nghĩa hơn.

Bây giờ, chúng ta hãy khám phá từng dịch vụ siêu nhỏ một cách chi tiết để hiểu vai trò của nó và cách nó góp phần cải thiện khả năng trả lời câu hỏi chính xác của mô hình.

#### Bước 2: Dịch vụ siêu nhỏ
Mỗi dịch vụ siêu nhỏ tuân theo logic sau để thực hiện một tác vụ trong luồng RAG:

![VPC](/images/2/image032.png)

Trong luồng, bạn có thể quan sát các dịch vụ siêu nhỏ và chúng ta có thể chia luồng RAG thành hai bước:

+ **Nhắc trước**: Bước này bao gồm việc chuẩn bị cơ sở kiến ​​thức (KB) bằng cách tải lên các tài liệu có liên quan và đảm bảo rằng thông tin được sắp xếp để truy xuất hiệu quả.

+ **Nhắc nhở**: Bước này tập trung vào việc truy xuất dữ liệu có liên quan từ cơ sở kiến ​​thức và sử dụng dữ liệu đó để tạo ra câu trả lời chính xác cho câu hỏi của người dùng.

#### **Nhắc nhở trước**

Trong bước này, logic là bắt đầu từ một tài liệu (Nike's revenue PDF) và thực hiện tiền xử lý cần thiết để chuẩn bị lưu trữ trong cơ sở dữ liệu. Như đã trình bày, quy trình này chủ yếu liên quan đến 3 dịch vụ siêu nhỏ: chuẩn bị dữ liệu, nhúng và lưu trữ vectơ. Hãy cùng khám phá từng dịch vụ siêu nhỏ

![VPC](/images/2/image033.png)

#### **Nhúng dịch vụ siêu nhỏ (POD: chatqna-tei:80)**
Nhúng là biểu diễn số của một đối tượng—chẳng hạn như từ, cụm từ hoặc tài liệu—trong không gian vectơ liên tục. Trong xử lý ngôn ngữ tự nhiên (NLP), nhúng biến đổi các từ, câu hoặc phân đoạn văn bản thành vectơ—các tập hợp số nắm bắt ý nghĩa, mối quan hệ và ý nghĩa theo ngữ cảnh của chúng. Sự chuyển đổi này cho phép các mô hình máy học xử lý và hiểu văn bản hiệu quả hơn.

Ví dụ, nhúng từ biểu diễn các từ dưới dạng các điểm trong không gian vectơ, trong đó các từ có nghĩa tương tự như "vua" và "nữ hoàng" được đặt gần nhau hơn. Mô hình nhúng nắm bắt các mối quan hệ này thông qua phép tính vectơ.

Trong quá trình đào tạo, nếu mô hình thường xuyên gặp "vua" khi liên kết với "đàn ông" và "nữ hoàng" khi liên kết với "phụ nữ", mô hình sẽ học được rằng "vua" và "nữ hoàng" có mối quan hệ tương tự với "đàn ông" và "phụ nữ". Điều này cho phép mô hình định vị các từ theo cách phản ánh các mối quan hệ có ý nghĩa, chẳng hạn như liên kết giới tính, trong ngôn ngữ.

![VPC](/images/2/image034.png)

Nhúng: Một thành phần chính của RAG
Nhúng đóng vai trò quan trọng trong Thế hệ tăng cường truy xuất (RAG) bằng cách tăng cường khả năng xử lý và truy xuất thông tin có liên quan của mô hình. Chúng cung cấp một số lợi thế chính:

+ **Ghi lại ý nghĩa** – Các nhúng thể hiện mối quan hệ ngữ nghĩa giữa các từ, cho phép các mô hình RAG hiểu ngữ cảnh, sắc thái và cấu trúc ngôn ngữ sâu hơn. Điều này cải thiện khả năng tạo ra các phản hồi có liên quan và mạch lạc.

+ **Giảm chiều** – Bằng cách chuyển đổi dữ liệu văn bản phức tạp thành các vectơ có kích thước cố định, nhúng giúp xử lý dữ liệu hiệu quả hơn và có khả năng mở rộng, cải thiện hiệu suất của hệ thống.

+ **Nâng cao hiệu suất mô hình** – Bằng cách tận dụng các điểm tương đồng về mặt ngữ nghĩa, nhúng cho phép truy xuất thông tin chính xác hơn, tinh chỉnh chất lượng phản hồi được tạo ra và giúp mô hình khái quát hóa tốt hơn trên nhiều truy vấn khác nhau.

OPEA cung cấp nhiều tùy chọn để chạy các dịch vụ nhúng vi mô, như được nêu chi tiết trong tài liệu nhúng OPEA. Trong trường hợp này, ChatQnA sử dụng dịch vụ nhúng vi mô Hugging Face TEI, chạy mô hình nhúng BAAI/bge-large-en-v1.5 cục bộ.

Vì một số dịch vụ không được hiển thị bên ngoài, bạn sẽ sử dụng pod Nginx để tương tác với chúng thông qua curl. Để thực hiện việc này, mỗi dịch vụ sẽ được truy cập bằng tên DNS nội bộ của dịch vụ đó.

1. Truy cập vào ngnix POD (sao chép toàn bộ tên pod NGNIX của bạn từ kubectl get pods và THAY THẾ chatqna-nginx-xxxxxxxx trên lệnh bên dưới)

![VPC](/images/2/image035.png)

Dấu nhắc lệnh của bạn bây giờ sẽ chỉ ra rằng bạn đang ở bên trong vùng chứa, phản ánh sự thay đổi trong môi trường:

![VPC](/images/2/image036.png)

Khi đã vào bên trong, bây giờ bạn sẽ có quyền truy cập trực tiếp vào các pod bên trong.

2. Nhận nhúng từ Microservice nhúng cho cụm từ "Học sâu là gì?":

![VPC](/images/2/image037.png)

Câu trả lời sẽ là biểu diễn vectơ của cụm từ "Học sâu là gì?". Dịch vụ này trả về nhúng vectơ cho các đầu vào từ REST API.

![VPC](/images/2/image038.png)

### **Dịch vụ cơ sở dữ liệu vectơ (POD: chatqna-redis-vector-db:80)**
Dịch vụ cơ sở dữ liệu vectơ đóng vai trò quan trọng trong ứng dụng Retrieval-Augmented Generation (RAG) bằng cách lưu trữ và truy xuất các nhúng. Điều này rất cần thiết cho các ứng dụng như ChatQnA, nơi thông tin có liên quan cần được truy xuất hiệu quả dựa trên truy vấn của người dùng.

#### **Sử dụng Redis làm Cơ sở dữ liệu vectơ**
Trong tác vụ này, Redis được sử dụng làm cơ sở dữ liệu vectơ. Tuy nhiên, OPEA hỗ trợ nhiều phương án thay thế, có thể tìm thấy trong kho lưu trữ vectơ OPEA.

Cơ sở dữ liệu vectơ (VDB) được thiết kế riêng để lưu trữ và quản lý các vectơ có chiều cao, biểu diễn các từ, câu hoặc hình ảnh dưới dạng số. Trong AI và học máy, các vectơ này—còn được gọi là nhúng—ghi lại ý nghĩa và mối quan hệ giữa các điểm dữ liệu, cho phép xử lý và truy xuất hiệu quả.

#### **Dịch vụ vi mô chuẩn bị dữ liệu (POD: chatqna-data-prep:6007)**
Dịch vụ vi mô chuẩn bị dữ liệu (Dataprep) chịu trách nhiệm định dạng và xử lý trước dữ liệu để có thể chuyển đổi thành nhúng và lưu trữ trong cơ sở dữ liệu vector. Điều này đảm bảo dữ liệu sạch, có cấu trúc và sẵn sàng để truy xuất hiệu quả.

#### **Chức năng chính của dịch vụ vi mô chuẩn bị dữ liệu**
+ Nhận dữ liệu thô (ví dụ: tài liệu hoặc báo cáo).

+ Xử lý và chia nhỏ dữ liệu thành các phân đoạn nhỏ hơn.

+ Gửi dữ liệu đã xử lý đến Dịch vụ vi mô nhúng để vector hóa.

+ Lưu trữ các nhúng kết quả trong Cơ sở dữ liệu vector.

Vì các cơ sở dữ liệu vector khác nhau có các yêu cầu định dạng dữ liệu riêng, nên Dịch vụ vi mô chuẩn bị dữ liệu đảm bảo khả năng tương thích với cơ sở dữ liệu đã chọn.

#### **Kiểm tra các dịch vụ vi mô**
Để xác minh chức năng của hệ thống và giúp mô hình trả lời câu hỏi ban đầu—
"Doanh thu của Nike năm 2023 là bao nhiêu?"—bạn sẽ cần tải lên tệp ngữ cảnh có liên quan (báo cáo doanh thu) để có thể xử lý.

Để thực hiện việc này, hãy tải xuống mẫu báo cáo doanh thu của Nike vào pod Nginx bằng lệnh bên dưới. (Nếu bạn không còn đăng nhập vào Nginx pod, hãy đảm bảo đăng nhập lại trước khi tiếp tục.)

Thực hiện lệnh sau để tải xuống báo cáo doanh thu Nike mẫu vào nginx pod (nếu bạn không còn đăng nhập vào NGinx pod, hãy đảm bảo sử dụng lệnh trên để đăng nhập lại):

1. Tải xuống tài liệu vào microservice:

![VPC](/images/2/image039.png)

2. Cung cấp tài liệu cho cơ sở kiến ​​thức (Vectord) (Sẽ mất khoảng 30 giây):

![VPC](/images/2/image040.png)

Sau khi chạy lệnh trước đó, bạn sẽ nhận được thông báo xác nhận như bên dưới. Lệnh này đã cập nhật cơ sở kiến ​​thức bằng cách tải lên tệp cục bộ để xử lý.

![VPC](/images/2/image041.png)

API microservice chuẩn bị dữ liệu có thể truy xuất thông tin về danh sách các tệp được lưu trữ trong cơ sở dữ liệu vector.

3. Kiểm tra xem tài liệu đã được tải lên chưa:

![VPC](/images/2/image042.png)

Sau khi chạy lệnh trước, bạn sẽ nhận được thông báo xác nhận.

![VPC](/images/2/image043.png)

Xin chúc mừng! Bạn đã chuẩn bị thành công cơ sở kiến ​​thức của mình. Bây giờ bạn sẽ khám phá các dịch vụ vi mô liên quan đến xử lý nhanh chóng.

#### Bước 3: Nhắc nhở
Sau khi cơ sở kiến ​​thức được thiết lập, bạn có thể bắt đầu tương tác với ứng dụng bằng cách đặt các câu hỏi theo ngữ cảnh cụ thể. Retrieval-Augmented Generation (RAG) đảm bảo rằng các phản hồi vừa chính xác vừa dựa trên dữ liệu có liên quan.

Quy trình bắt đầu bằng việc ứng dụng truy xuất thông tin có liên quan nhất từ ​​cơ sở kiến ​​thức để trả lời truy vấn của người dùng. Bước này đảm bảo rằng Mô hình ngôn ngữ lớn (LLM) có quyền truy cập vào ngữ cảnh chính xác và cập nhật để tạo ra phản hồi có thông tin.

Tiếp theo, thông tin đã truy xuất được kết hợp với lời nhắc nhập của người dùng và gửi đến LLM. Lời nhắc được làm giàu này nâng cao khả năng của mô hình trong việc cung cấp các câu trả lời không chỉ dựa trên kiến ​​thức đã được đào tạo trước mà còn được hỗ trợ bởi dữ liệu bên ngoài trong thế giới thực.

Cuối cùng, bạn sẽ thấy cách LLM xử lý lời nhắc được làm giàu này để tạo ra phản hồi mạch lạc và chính xác theo ngữ cảnh. Bằng cách tận dụng RAG, ứng dụng cung cấp các câu trả lời có liên quan cao, dựa trên thông tin mới nhất từ ​​cơ sở kiến ​​thức.

Các dịch vụ vi mô liên quan đến giai đoạn này bao gồm:

- Nhúng
- Cơ sở dữ liệu vectơ
- Trình thu thập
- Xếp hạng lại
- LLM

![VPC](/images/2/image044.png)

#### **Dịch vụ vi mô thu thập (POD: chatqna-retriever-usvc:7000)**
Dịch vụ vi mô thu thập chịu trách nhiệm định vị thông tin có liên quan nhất trong cơ sở kiến ​​thức và trả về các tài liệu khớp chặt chẽ với truy vấn của người dùng. Nó tương tác với nhiều hệ thống phụ trợ lưu trữ kiến ​​thức và cung cấp API để truy xuất dữ liệu khớp nhất.

Các cơ sở kiến ​​thức khác nhau sử dụng các phương pháp truy xuất khác nhau:

Cơ sở dữ liệu vectơ sử dụng phương pháp khớp tương tự vectơ giữa câu hỏi của người dùng và nhúng tài liệu được lưu trữ.
Cơ sở dữ liệu đồ thị tận dụng vị trí đồ thị để tìm thông tin liên quan.
Cơ sở dữ liệu quan hệ dựa vào phương pháp khớp chuỗi và biểu thức chính quy để định vị văn bản có liên quan.
Trong nhiệm vụ này, bạn sẽ sử dụng Redis làm cơ sở dữ liệu vectơ và truy xuất thông tin thông qua trình thu thập Redis.

Vì việc truy xuất vectơ dựa vào nhúng, trước tiên bạn cần tạo nhúng cho câu hỏi:
"Doanh thu của Nike năm 2023 là bao nhiêu?"

Thao tác này sẽ cho phép trình thu thập tìm kiếm trong cơ sở kiến ​​thức để tìm tài liệu có liên quan nhất—chẳng hạn như báo cáo doanh thu của Nike mà bạn đã tải lên ở bước trước.

Để tạo nhúng, hãy sử dụng dịch vụ vi mô chatqna-tei. (Đảm bảo bạn đã đăng nhập vào pod Nginx trước khi tiếp tục.)

1. Tạo nhúng và lưu cục bộ (embed_question):

![VPC](/images/2/image045.png)

Bạn sẽ nhận được thông tin chi tiết về tác vụ viết:

![VPC](/images/2/image046.png)

2. Kiểm tra xem nhúng của bạn đã được lưu chưa:

**echo $embed_question**

Bạn sẽ có thể thấy các vectơ mà dịch vụ vi mô nhúng tạo ra. Bây giờ bạn có thể sử dụng dịch vụ vi mô trình thu thập để có được thông tin tương tự nhất từ ​​cơ sở kiến ​​thức của mình.

3. Lấy và lưu các vectơ tương tự từ embed_question ban đầu tại địa phương similar_docs:

**similar_docs=$(curl chatqna-retriever-usvc:7000/v1/retrieval -X POST -d "{\"text\":\"test\",\"embedding\":${embed_question}}" -H 'Content-Type: application/json')**

Bằng cách xem kết quả đầu ra trước đó, bạn có thể thấy các đoạn tương tự nhất (TOP_3) từ tài liệu Báo cáo doanh thu của Nike và câu hỏi **"Doanh thu của Nike năm 2023 là bao nhiêu?"**.

**echo $similar_docs**

{{% notice info %}}
Kết quả đầu ra sau đây đã được định dạng để dễ đọc hơn. Kết quả của bạn sẽ được trình bày dưới dạng văn bản thuần túy và có thể hơi khác một chút do thuật toán tìm kiếm tương tự. Tuy nhiên, bạn có thể kiểm tra lại xem các tài liệu đã truy xuất có liên quan đến truy vấn ban đầu của bạn không.
{{% /notice %}}

Ứng dụng sẽ sử dụng thông tin đó làm ngữ cảnh để nhắc LLM, nhưng vẫn còn một bước nữa mà bạn cần thực hiện để tinh chỉnh và kiểm tra chất lượng của các tài liệu đã truy xuất đó: trình xếp hạng lại.

#### **Vi dịch vụ xếp hạng lại (POD: chatqna-teirerank:80)**
Vi dịch vụ xếp hạng lại đóng vai trò quan trọng trong tìm kiếm ngữ nghĩa, tận dụng các mô hình xếp hạng lại để tăng cường tính liên quan của các kết quả đã truy xuất. Khi được cung cấp truy vấn của người dùng và một tập hợp các tài liệu, vi dịch vụ này sẽ sắp xếp lại các tài liệu dựa trên mức độ tương đồng về mặt ngữ nghĩa của chúng với truy vấn, đảm bảo rằng các kết quả có liên quan nhất sẽ xuất hiện đầu tiên.

Xếp hạng lại đặc biệt có giá trị trong các hệ thống truy xuất văn bản, trong đó các tài liệu ban đầu được truy xuất bằng cách sử dụng:

+ Nhúng dày đặc, nắm bắt ý nghĩa ngữ nghĩa sâu sắc.
+ Tìm kiếm từ vựng thưa thớt, dựa trên việc khớp dựa trên từ khóa.

Mặc dù các phương pháp truy xuất này có hiệu quả, nhưng mô hình xếp hạng lại sẽ tinh chỉnh các kết quả bằng cách tối ưu hóa thứ tự của các tài liệu đã truy xuất. Bước này cải thiện đáng kể độ chính xác, đảm bảo đầu ra cuối cùng có liên quan hơn, chính xác hơn và phù hợp hơn về mặt ngữ cảnh với truy vấn của người dùng.

![VPC](/images/2/image047.png)

OPEA có nhiều tùy chọn để xếp hạng lại. Đối với phòng thí nghiệm này, bạn sẽ sử dụng Hugging Face TEI để xếp hạng lại. Đây là dịch vụ vi mô chatqna-teirerank trong cụm của bạn.

Trình xếp hạng lại sẽ sử dụng similar_docs từ giai đoạn trước và so sánh với câu hỏi Doanh thu Nike năm 2023 là bao nhiêu? để kiểm tra chất lượng của các tài liệu đã truy xuất.

Trích xuất 3 đoạn văn bản đã truy xuất và lưu chúng trong một biến mới để xếp hạng lại:

1. Cài đặt các phụ thuộc jq để định dạng similar_docs

**echo -e "deb http://deb.debian.org/debian bookworm main contrib non-free\ndeb http://security.debian.org/debian-security bookworm-security main contrib non-free\ndeb http://deb.debian.org/debian bookworm-updates main contrib non-free" > /etc/apt/sources.list && apt update && apt install -y jq**

2. Trích xuất và định dạng các văn bản thành một mảng chuỗi JSON hợp lệ

**texts=$(echo "$similar_docs" | jq -r '[.retrieved_docs[].text | @json]')**

3. Gửi yêu cầu đến dịch vụ vi mô với truy vấn và các văn bản đã định dạng:

**curl -X POST chatqna-teirerank:80/rerank \
-d "{\"query\":\"Doanh thu của Nike năm 2023 là bao nhiêu?\", \"texts\": $texts}" \
-H 'Content-Type: application/json'**

**Response:**

{{% notice info %}}
Đầu ra sau đây đã được định dạng để dễ đọc hơn. Kết quả của bạn được hiển thị ở dạng văn bản thuần túy và có thể thay đổi đôi chút do thuật toán tìm kiếm tương đồng. Các tài liệu được truy xuất được xếp hạng theo mức độ tương đồng với truy vấn của bạn, với chỉ mục được xếp hạng cao nhất thể hiện sự khớp có liên quan nhất. Bạn có thể xác nhận rằng tài liệu được xếp hạng cao nhất tương ứng với tài liệu phù hợp nhất với truy vấn của bạn.
{{% /notice %}}

![VPC](/images/2/image048.png)

Máy chủ phản hồi bằng một mảng JSON chứa các đối tượng có hai trường: chỉ mục và điểm. Điều này cho biết cách các đoạn trích được xếp hạng dựa trên mức độ liên quan của chúng với truy vấn: {"index":2,"score":0.9972289} có nghĩa là văn bản đầu tiên (chỉ mục 0) có điểm liên quan cao khoảng 0,7982. {"index":0,"score":0.9776342},{"index":3,"score":0.9296986},{"index":1,"score":0.84730965} cho biết các đoạn trích khác (chỉ mục 3, 1 và 2) có điểm thấp hơn nhiều.

Như bạn có thể thấy từ similar_doc, id=2 có thông tin bên dưới, trong đó nó CHÍNH XÁC đề cập đến doanh thu năm 2023!

![VPC](/images/2/image049.png)

Chỉ đoạn trích đầu tiên sẽ được sử dụng để nhắc LLM.

#### **LLM Microservice (POD: chatqna-tgi:80)**
Cốt lõi của ứng dụng RAG (Retrieval-Augmented Generation) là Mô hình ngôn ngữ lớn (LLM), đóng vai trò quan trọng trong việc tạo ra phản hồi. Bằng cách tận dụng RAG, hệ thống nâng cao hiệu suất của LLM, đảm bảo phản hồi chính xác, có liên quan và nhận thức được ngữ cảnh.

Các loại LLM
LLM thường được chia thành hai loại chính, mỗi loại có điểm mạnh và điểm yếu riêng:

+ Mô hình nguồn đóng
Các mô hình độc quyền này được phát triển bởi các công ty công nghệ lớn như Amazon Web Services (AWS), OpenAI và Google. Chúng được đào tạo trên các tập dữ liệu mở rộng và được tối ưu hóa để có đầu ra chất lượng cao, đáng tin cậy. Tuy nhiên, chúng đi kèm với một số hạn chế nhất định:

- Tùy chỉnh hạn chế: Người dùng có quyền kiểm soát tối thiểu đối với việc tinh chỉnh.
- Chi phí cao hơn: Quyền truy cập thường bị giới hạn và có thể tốn kém.
- Mối quan ngại về chủ quyền dữ liệu: Quyền truy cập API có thể hạn chế việc sử dụng trong các ứng dụng yêu cầu quản trị dữ liệu chặt chẽ.

+ Mô hình nguồn mở
Có sẵn miễn phí để sử dụng và sửa đổi, LLM nguồn mở cung cấp tính linh hoạt và khả năng kiểm soát cao hơn. Chúng cho phép người dùng tùy chỉnh và tinh chỉnh các mô hình theo nhu cầu cụ thể. Chạy các mô hình nguồn mở cục bộ hoặc trên cơ sở hạ tầng đám mây riêng đảm bảo quyền riêng tư dữ liệu và hiệu quả chi phí tốt hơn. Tuy nhiên, chúng yêu cầu:

- Chuyên môn kỹ thuật: Việc triển khai và tối ưu hóa các mô hình nguồn mở có thể phức tạp.
- Tài nguyên tính toán: Để đạt được hiệu suất tương đương với các mô hình đóng thường đòi hỏi phần cứng mạnh mẽ.
Tích hợp linh hoạt với OPEA: Kiến trúc dịch vụ vi mô này hỗ trợ cả mô hình đóng và mô hình nguồn mở, cung cấp tính linh hoạt để lựa chọn mô hình phù hợp nhất cho ứng dụng của bạn. Trong ví dụ này, mô hình TGI (Suy luận tạo văn bản) từ Hugging Face được sử dụng.

Kiểm tra dịch vụ vi mô LLM: Để xác minh chức năng của nó, bạn có thể trực tiếp nhắc TGI LLM bằng một câu hỏi mẫu:
"Doanh thu của Nike vào năm 2023 là bao nhiêu?"

Bài kiểm tra này sẽ chứng minh mô hình có thể truy xuất và tạo phản hồi có thông tin tốt như thế nào dựa trên cơ sở kiến ​​thức đã tải.

4. Trực tiếp nhắc nhở TGI(LLM) Microservice:

![VPC](/images/2/image050.png)

Mô hình sẽ cung cấp cho bạn câu trả lời cho lời nhắc như sau:

**"generated_text":" Doanh thu của Nike năm 2023 chưa được báo cáo vì vẫn đang trong quý 4. Năm tài chính đầy đủ trước đó—tức là năm 2022—đã mang lại 48,9 tỷ đô la doanh thu cho công ty đồ thể thao đa quốc gia của Mỹ. Họ xử lý việc thiết kế, phát triển, sản xuất và tiếp thị/bán hàng trên toàn thế giới của một danh mục sản phẩm đa dạng. Từ khi thành lập vào năm 1964 với tên gọi Blue Ribbon Sports, công ty đã được đổi tên thành Nike, Inc., vào năm 1978. Logo Jumpman (ví dụ), bao gồm Michael