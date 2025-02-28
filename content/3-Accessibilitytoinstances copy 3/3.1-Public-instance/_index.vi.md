---
title : "Tích hợp API suy luận (Denvr Cloud và Intel Gaudi AI Accelerator)"
date :  "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 6.1. </b> "
---
### Khi nào bạn nên sử dụng API suy luận được quản lý?

Khi các triển khai RAG (Retrieval-Augmented Generation) cấp doanh nghiệp ngày càng phức tạp, việc chuyển đổi một số thành phần nhất định sang các dịch vụ được quản lý ngày càng trở nên có lợi. Cách tiếp cận này giảm thiểu các thách thức trong triển khai, hợp lý hóa việc quản lý cơ sở hạ tầng và đảm bảo khả năng tự động mở rộng liền mạch. Hơn nữa, nó cung cấp cho các nhà phát triển một cách hiệu quả và dễ tiếp cận hơn để tích hợp các Mô hình ngôn ngữ lớn (LLM).

### API suy luận của Denvr Cloud được kích hoạt như thế nào?

OPEA LLM và các dịch vụ vi mô nhúng chạy trên Intel Gaudi2 AI Accelerators, do Denvr Cloud lưu trữ. Các API này được bảo mật bằng các dịch vụ xác thực OPEA và được tối ưu hóa bằng cân bằng tải để có hiệu suất cao nhất. Trong khi triển khai ban đầu được OPEA xây dựng, Denvr Cloud đã tinh chỉnh và cải tiến hệ thống hơn nữa để đáp ứng các yêu cầu cụ thể về hiệu suất và khả năng mở rộng.

Tham khảo kiến ​​trúc để biết thông tin triển khai:

![VPC](/images/5.fwd/image101.png)

### Kiến trúc OPEA phát triển như thế nào?

Thiết kế mô-đun của OPEA cho phép hoán đổi thành phần liền mạch, cho phép hầu hết các thành phần từ ví dụ ChatQnA mặc định (Mô-đun 1) được tích hợp dễ dàng vào các triển khai mới. Kiến trúc được cập nhật vẫn tương tự như thiết lập ChatQnA ban đầu mà không có rào cản nhưng bao gồm một thành phần bổ sung:

**chatqna-llm-uservice**
chatqna-llm-uservice hoạt động như một dịch vụ bao bọc cho API suy luận, hỗ trợ các điểm cuối tương thích với vLLM và OpenAI. Dịch vụ này tạo điều kiện kết nối với API suy luận và đảm bảo cấu hình phù hợp.

### Triển khai ChatQnA với API suy luận

Đối với phòng thí nghiệm này, chúng tôi đã giới thiệu một tập hợp thay đổi cho phép triển khai song song hoàn toàn ví dụ ChatQnA trong cùng một cụm Kubernetes mà bạn đã sử dụng. Chạy lệnh sau sẽ triển khai các pod trong không gian tên "remote-inference", phản ánh thiết lập ChatQnA ban đầu nhưng tận dụng các mô hình suy luận từ xa LLM thay vì TGI.

1. **Truy cập API suy luận**: Để sử dụng API suy luận, bạn sẽ cần mã thông báo API do Denvr Cloud cung cấp, chỉ có sẵn để xem trước riêng trong hội thảo. Để yêu cầu quyền truy cập, vui lòng liên hệ với Denvr Cloud theo địa chỉ vaishali@denvrdata.com.

2. **Triển khai ChatQnA bằng API suy luận Denvr Cloud (Meta Llama 70B 3.1 Instruct)**: Bây giờ bạn có thể triển khai ChatQnA bằng API suy luận Meta-Llama-3.1-70B-Instruct vào Cụm EKS của mình. Các API này được triển khai trước trên Denvr Cloud với bộ tăng tốc Intel Gaudi2 và dịch vụ vi mô OPEA LLM, đảm bảo hiệu suất và khả năng mở rộng tối ưu.

**Lưu ý quan trọng:**
Nhớ thay thế DenvrClientID và DenvrClientSecret bằng thông tin xác thực mà bạn đã nhận được trước đó từ Denvr Cloud.

{{% notice info %}}
Bản kê khai cho ChatQnA-Remote Inference có thể được tìm thấy trong kho lưu trữ ChatQnA GenAIExamples và hướng dẫn triển khai thủ công có thể được tìm thấy tại đây. Hướng dẫn tại đây sử dụng các mẫu AWS CloudFormation do gói AWS Marketplace EKS tạo ra.
{{% /notice %}}

Xác minh các dịch vụ mới đã được tạo

![VPC](/images/5.fwd/image102.png)

![VPC](/images/5.fwd/image103.png)

Kiểm tra Chat QnA trên bảng điều khiển

Truy cập vào ngnix POD (sao chép tên pod NGNIX của bạn từ kubectl get pods -n remote-inference và REPLACE chatqna-nginx-xxxxxxxx trên lệnh bên dưới)

![VPC](/images/5.fwd/image104.png)

Dấu nhắc lệnh của bạn bây giờ sẽ chỉ ra rằng bạn đang ở bên trong vùng chứa, phản ánh sự thay đổi trong môi trường:

![VPC](/images/5.fwd/image105.png)

Nhận "Học sâu là gì? Giải thích trong 20 words"*:

![VPC](/images/5.fwd/image106.png)

![VPC](/images/5.fwd/image107.png)

Xác minh việc triển khai đã hoàn tất bằng cách xác minh bộ cân bằng tải mới trên bảng điều khiển quản lý của bạn

![VPC](/images/5.fwd/image108.png)

![VPC](/images/5.fwd/image109.png)