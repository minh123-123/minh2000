---
title : "Triển khai Guardrails"
date :  "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 4.1. </b> "
---
### Tại sao cần có lan can bảo vệ?
Khi AI ngày càng được nhúng vào các ứng dụng, việc đảm bảo kết quả an toàn, có đạo đức và đáng tin cậy là điều cần thiết. Lan can bảo vệ trong các hệ thống AI—đặc biệt là những hệ thống tương tác với người dùng hoặc đưa ra quyết định tự chủ—giúp điều chỉnh phản hồi, ngăn chặn hành vi không mong muốn và căn chỉnh đầu ra theo các tiêu chuẩn được xác định trước. Nếu không có lan can bảo vệ, các mô hình AI có thể tạo ra nội dung thiên vị hoặc không an toàn, đưa ra quyết định không phù hợp hoặc xử lý sai dữ liệu nhạy cảm.

Việc triển khai lan can bảo vệ cho phép bạn:

+ **Tăng cường sự an toàn của người dùng** – Bằng cách lọc phản hồi, bạn giảm thiểu rủi ro tạo ra nội dung có hại hoặc không phù hợp.

+ **Đảm bảo tuân thủ quy định** – Khi các quy định về quyền riêng tư và bảo mật phát triển, lan can bảo vệ giúp duy trì sự tuân thủ các tiêu chuẩn pháp lý và đạo đức.

+ **Cải thiện độ chính xác và độ tin cậy** – Lan can bảo vệ tinh chỉnh đầu ra của AI, giảm tỷ lệ lỗi và đảm bảo hiệu suất nhất quán và đáng tin cậy hơn.

Bằng cách tích hợp lan can bảo vệ, bạn có thể khai thác sức mạnh của AI đồng thời giảm thiểu rủi ro, tạo ra trải nghiệm người dùng an toàn và có trách nhiệm hơn.

### Kiến trúc OPEA phát triển như thế nào?
Với kiến ​​trúc linh hoạt của OPEA, hầu hết các thành phần cốt lõi từ mô-đun ChatQnA mặc định (Mô-đun 1) vẫn không thay đổi. Tuy nhiên, việc giới thiệu các rào chắn bảo vệ đòi hỏi phải thêm hai thành phần tích hợp liền mạch vào quá trình triển khai:

1. chatqna-tgi-guardrails – Dịch vụ siêu nhỏ này chạy máy chủ TGI bằng mô hình meta-llama/Meta-Llama-Guard-2-8B, hoạt động như một bộ lọc an toàn thời gian thực, đảm bảo rằng tất cả các truy vấn đều tuân thủ các giao thức bảo mật đã xác định.

2. chatqna-guardrails-usvc – Dịch vụ siêu nhỏ này hoạt động như Trình phân tích điểm cuối hoạt động (OPEA), đánh giá các truy vấn của người dùng và xác định bất kỳ truy vấn nào yêu cầu nội dung có khả năng không an toàn.

### Triển khai rào chắn bảo vệ ChatQnA

Kubernetes cho phép cô lập các môi trường phát triển thông qua việc sử dụng nhiều không gian tên. Vì các pod cho đường ống ChatQnA hiện đang được triển khai trong không gian tên mặc định, bạn sẽ triển khai các guardrails trong một không gian tên guardrails riêng biệt để tránh bất kỳ sự gián đoạn nào.

Nếu bạn đã đăng xuất khỏi CloudShell, hãy nhấp vào cửa sổ CloudShell để khởi động lại shell hoặc nhấp vào biểu tượng trong AWS Console để mở CloudShell mới

1. Truy cập CloudShell của bạn và triển khai mẫu ChatQnA-Guardrails ClourFormation vào EKS Cluster của bạn

![VPC](/images/4.s3/image060.png)

{{% notice info %}}
Có thể tìm thấy manifest cho ChatQnA-Guardrails trong kho lưu trữ ChatQnA GenAIExamples và hướng dẫn triển khai thủ công có thể tìm thấy tại đây. Các hướng dẫn bạn đang sử dụng trong hội thảo này sử dụng các mẫu AWS CloudFormation do gói AWS Marketplace EKS tạo ra.
{{% /notice %}}

2. Xác minh không gian tên mới đã được tạo

![VPC](/images/4.s3/image061.png)

Bạn sẽ thấy không gian tên guardrails

![VPC](/images/4.s3/image062.png)

3. Kiểm tra các pod trên không gian tên guardrails. Sẽ mất vài phút để tải xuống các mô hình và để tất cả các dịch vụ hoạt động.
Chạy lệnh sau để kiểm tra xem tất cả các dịch vụ có đang chạy không:

![VPC](/images/4.s3/image063.png)

![VPC](/images/4.s3/image064.png)

Chờ cho đến khi đầu ra hiển thị rằng tất cả các dịch vụ bao gồm chatqna-tgi-guardrails và chatqna-guardrails-usvc đang chạy (1/1).

4. Xác minh việc triển khai đã hoàn tất bằng cách xác minh bộ cân bằng tải mới trên bảng điều khiển quản lý của bạn

{{% notice info %}}
Chờ 5 phút để bộ cân bằng tải được tạo.
{{% /notice %}}

![VPC](/images/4.s3/image065.png)

![VPC](/images/4.s3/image066.png)

Bằng cách kiểm tra xem cả bộ cân bằng tải và pod đều đang chạy hay không, chúng ta có thể xác nhận rằng việc triển khai đã sẵn sàng và bắt đầu kiểm tra hành vi.