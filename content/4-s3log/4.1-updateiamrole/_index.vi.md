---
title : "Thiết lập tích hợp Bedrock"
date :  "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 7.1 </b> "
---
**Amazon Bedrock là gì?**
Amazon Bedrock là dịch vụ được quản lý hoàn toàn, cung cấp quyền truy cập vào nhiều mô hình nền tảng hiệu suất cao (FM) từ các công ty hàng đầu trong ngành như AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI và chính Amazon. Thông qua một API duy nhất, Bedrock cho phép các nhà phát triển xây dựng các ứng dụng AI tạo sinh đồng thời đảm bảo tính bảo mật, quyền riêng tư và các hoạt động AI có trách nhiệm.

Các nhà phát triển có thể tương tác với Amazon Bedrock thông qua AWS Software Development Kit (SDK) hoặc AWS Command Line Interface (CLI). Bedrock cũng cung cấp các tính năng gốc cho phép người dùng tạo cơ sở kiến ​​thức RAG (Retrieval-Augmented Generation), quy trình làm việc của agentic và lan can. Việc tích hợp Bedrock với OPEA mở rộng quyền truy cập vào nhiều mô hình nền tảng hơn đồng thời tận dụng các khả năng tiên tiến của Bedrock cùng với OPEA.

**Kiến trúc thay đổi như thế nào?**

Các tích hợp trong tương lai với OPEA sẽ mở khóa toàn bộ tiềm năng của các khả năng của Amazon Bedrock, bao gồm các mô hình Titan Embedding. Tuy nhiên, đối với mô-đun này, trọng tâm chỉ tập trung vào LLM.

Nhờ kiến ​​trúc mô-đun và có thể hoán đổi của OPEA, hầu hết các thành phần từ thiết lập ChatQnA mặc định (Mô-đun 1) vẫn không thay đổi. Dịch vụ TGI (Hugging Face) hiện được thay thế bằng một vùng chứa Bedrock, tích hợp liền mạch vào triển khai hiện có.

**Thành phần kiến ​​trúc được cập nhật:**

+ chatqna-bedrock
- Trong triển khai này, khi người dùng gửi tin nhắn qua Giao diện người dùng ChatQnA, tin nhắn đó sẽ được định tuyến đến vùng chứa Bedrock ở phía sau, nơi giao tiếp với Amazon Bedrock để truy xuất và trả về phản hồi.

Tích hợp này duy trì kiến ​​trúc ChatQnA đồng thời cải tiến kiến ​​trúc này bằng các khả năng LLM mạnh mẽ của Amazon Bedrock, đảm bảo khả năng mở rộng, hiệu quả và triển khai liền mạch.

{{% notice info %}}
Chúng tôi đã sử dụng không gian tên Kubernetes của bedrock để tách các nhóm và dịch vụ liên quan đến triển khai Bedrock. Khi bạn sử dụng kubectl và các lệnh Kubernetes khác trong các ví dụ bên dưới, hãy đảm bảo đủ điều kiện cho lệnh bằng -n bedrock.
{{% /notice %}}

**Triển khai ChatQnA bằng Amazon Bedrock LLM**

Đối với phòng thí nghiệm này, chúng tôi đã tạo một tập lệnh thay đổi với triển khai song song đầy đủ của ví dụ ChatQnA trong cùng một cụm Kubernetes mà bạn đã sử dụng. Lệnh sau sẽ triển khai các pod vào cụm trong không gian tên "bedrock" giống hệt với các pod ChatQnA ban đầu, ngoại trừ các mô hình Bedrock thay vì TGI.

aws cloudformation execute-change-set --change-set-name bedrock-change-set --stack-name OpeaBedrockStack

**Kích hoạt Mô hình**

Mô-đun này hoạt động với hầu hết mọi LLM tạo văn bản được Bedrock hỗ trợ, nhưng vì mục đích của phòng thí nghiệm này, chúng tôi đã sử dụng mô hình Anthropic Claude Haiku 3. Vì vậy, trong khi bạn đang chờ bộ thay đổi triển khai, hãy kích hoạt mô hình của chúng ta trong bảng điều khiển Bedrock:

1. Chuyển sang vùng us-west-2, bạn có thể thử nghiệm trên các vùng khác nhưng thường thì us-west có nhiều khả năng hơn:

![VPC](/images/5.fwd/image116.png)

2. Truy cập Amazon Bedrock:

![VPC](/images/5.fwd/image117.png)

3. Truy cập tab truy cập mô hình:

![VPC](/images/5.fwd/image118.png)

4. Ở đầu màn hình, nhấp vào nút có nội dung Sửa đổi quyền truy cập mô hình

![VPC](/images/5.fwd/image119.png)

5. Chọn Claude 3 Haiku

![VPC](/images/5.fwd/image120.png)

{{% notice info %}}
Có thể mất một hoặc hai phút để cấp quyền truy cập, nhưng đừng lo lắng sẽ không mất nhiều thời gian hơn thế nữa.
{{% /notice %}}

6. Sau khi bạn xác nhận rằng quyền truy cập mô hình đã được cấp, hãy chuyển trở lại vùng us-east-2 nơi cụm EKS của bạn nằm.

**Xác nhận triển khai**

Bây giờ hãy xác nhận rằng triển khai Bedrock của chúng ta đã hoàn tất. Bạn có thể theo dõi trạng thái của các pod Bedrock bằng lệnh kubectl:

...để có đầu ra như thế này:

![VPC](/images/5.fwd/image121.png)

{{% notice info %}}
Có thể mất vài phút để Bedrock khởi tạo hoàn toàn và khả dụng. Chỉ tiếp tục khi bạn thấy pod chatqna-bedrock-deployment ở trạng thái Đang chạy.
{{% /notice %}}

Bây giờ bạn có thể sử dụng Amazon Bedrock trong môi trường của mình.