---
title : "Xác minh OPEA Chat QnA với Inferenece API"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 7.2 </b> "
---
**Ứng dụng thử nghiệm**

Bạn có thể kiểm tra việc triển khai bằng cách truy cập vào url DNS của bộ cân bằng tải mà mẫu hình thành đám mây đã tạo.

1. Tìm bộ cân bằng tải:

![VPC](/images/5.fwd/image122.png)

2. Sao chép tên DNS của bạn cho bedrock-ingress:

![VPC](/images/5.fwd/image123.png)

3. Dán vào tab trình duyệt mới để truy cập vào giao diện

Trong giao diện người dùng, bạn có thể thấy chatbot để tương tác với nó

![VPC](/images/5.fwd/image124.png)

4. Kiểm tra xem mô hình có thể cung cấp cho chúng ta câu trả lời về OPEA hay không:

![VPC](/images/5.fwd/image125.png)

![VPC](/images/5.fwd/image126.png)

Bạn có thể nhận thấy rằng phản hồi ban đầu của chatbot đã lỗi thời hoặc thiếu thông tin chi tiết cụ thể về OPEA. Điều này là do OPEA là một dự án tương đối mới và không được đưa vào tập dữ liệu được sử dụng để đào tạo mô hình ngôn ngữ. Vì hầu hết các LLM (Mô hình ngôn ngữ lớn) đều tĩnh nên chúng chỉ dựa vào dữ liệu đào tạo có sẵn và không thể tự động kết hợp các phát triển mới hoặc công nghệ mới nổi như OPEA.

5. Tải lên ngữ cảnh để cải thiện độ chính xác

Để giải quyết hạn chế này, RAG (Thế hệ tăng cường truy xuất) cho phép truy xuất ngữ cảnh theo thời gian thực. Giao diện người dùng ChatQnA bao gồm một biểu tượng tải lên, cho phép bạn thêm ngữ cảnh có liên quan.

**Cách thức hoạt động:**

1. Khi bạn tải lên một tài liệu hoặc liên kết, nó sẽ được gửi đến dịch vụ vi mô DataPrep.

2. DataPrep xử lý nội dung và tạo nhúng.

3. Sau đó, dữ liệu đã xử lý được lưu trữ trong Cơ sở dữ liệu Vector để truy xuất.

Bằng cách tải lên các tài liệu hoặc liên kết đã cập nhật, bạn mở rộng cơ sở kiến ​​thức của chatbot, đảm bảo chatbot cung cấp các phản hồi có liên quan, chính xác và cập nhật hơn.

![VPC](/images/5.fwd/image127.png)

Việc triển khai cho phép bạn tải lên tệp hoặc trang web. Đối với trường hợp này, hãy sử dụng trang web OPEA:

+ Nhấp vào biểu tượng tải lên để mở bảng điều khiển bên phải

+ Nhấp vào Dán liên kết

+ Sao chép/dán văn bản https://opea-project.github.io/latest/introduction/index.html vào hộp nhập

+ Nhấp vào Xác nhận để bắt đầu quá trình lập chỉ mục
Khi quá trình lập chỉ mục hoàn tất, bạn sẽ thấy một biểu tượng được thêm vào bên dưới hộp văn bản, có nhãn là https://opea-project.github.io/latest/introduction/index.html

6. Hỏi ứng dụng sau khi cung cấp ngữ cảnh:

Hỏi lại "OPEA là gì?" để xem câu trả lời đã cập nhật.

![VPC](/images/2.prerequisite/image129.png)

Lần này, bot trò chuyện phản hồi chính xác dựa trên dữ liệu mà nó thêm vào lời nhắc từ nguồn mới, trang web OPEA.

**Kết luận**

Trong nhiệm vụ này, bạn đã triển khai thành công một chatbot hỗ trợ RAG bằng Amazon Bedrock. Bằng cách tải lên ngữ cảnh có liên quan, bạn đã cho phép mô hình cập nhật và tinh chỉnh phản hồi của mình một cách động dựa trên thông tin mới. Quy trình này chứng minh cách tích hợp RAG tăng cường khả năng thích ứng theo thời gian thực, cho phép hệ thống liên tục cải thiện độ chính xác và tính liên quan của mình trong khi tận dụng sức mạnh của Amazon Bedrock.