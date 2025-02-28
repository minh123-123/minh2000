---
title : "Kiểm tra việc triển khai và xác minh quy trình làm việc RAG"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 3.3. </b> "
---
### Hiểu RAG và sử dụng UI
Bây giờ bạn đã xác minh tất cả các dịch vụ đang chạy, hãy cùng xem UI do triển khai cung cấp.

Để truy cập UI, hãy mở bất kỳ trình duyệt nào và truy cập DNS của ChatQnA Load Balancer: http://chatqna-ingress-xxxxxxx.us-east-2.elb.amazonaws.com (Sửa đổi bằng URL chatqna-ingressDNS của bạn)

Trong UI, bạn có thể thấy chatbot tương tác với nó

![VPC](/images/2/image052.png)

Để xác minh UI, hãy tiếp tục và hỏi

![VPC](/images/2/image053.png)

![VPC](/images/2/image054.png)

Câu trả lời lại đúng vì chúng ta đã lập chỉ mục cơ sở kiến ​​thức của mình ở bước trước.

Hãy thử một cái gì đó khác. Ứng dụng có thể trả lời về OPEA không:

![VPC](/images/2/image055.png)

Bạn có thể nhận thấy rằng phản hồi ban đầu của chatbot đã lỗi thời hoặc thiếu thông tin chi tiết cụ thể về OPEA. Nguyên nhân là do OPEA là một dự án tương đối mới và không được đưa vào tập dữ liệu được sử dụng để đào tạo mô hình ngôn ngữ. Vì hầu hết các mô hình ngôn ngữ đều là tĩnh—có nghĩa là chúng dựa vào dữ liệu có sẵn tại thời điểm đào tạo—nên chúng không thể tự động kết hợp các phát triển gần đây hoặc các chủ đề mới nổi như OPEA.

Tuy nhiên, RAG cung cấp giải pháp bằng cách cho phép truy xuất ngữ cảnh theo thời gian thực. Trong UI, bạn sẽ tìm thấy tùy chọn tải lên thông tin ngữ cảnh có liên quan. Khi bạn thực hiện việc này, tài liệu sẽ được gửi đến dịch vụ vi mô DataPrep, tại đó tài liệu sẽ được chuyển đổi thành nhúng và lưu trữ trong Cơ sở dữ liệu Vector.

Bằng cách tải lên tài liệu hoặc liên kết, bạn sẽ mở rộng hiệu quả cơ sở kiến ​​thức của chatbot bằng thông tin mới nhất, cải thiện tính liên quan và độ chính xác của phản hồi.

![VPC](/images/4.s3/image057.png)

Việc triển khai cho phép bạn tải lên tệp hoặc trang web. Đối với trường hợp này, hãy sử dụng trang web OPEA:

+ Nhấp vào biểu tượng tải lên để mở bảng điều khiển bên phải
+ Nhấp vào Dán liên kết
+ Sao chép/dán văn bản https://opea-project.github.io/latest/introduction/index.html vào hộp nhập
+ Nhấp vào Xác nhận để bắt đầu quá trình lập chỉ mục

Khi quá trình lập chỉ mục hoàn tất, bạn sẽ thấy một biểu tượng được thêm vào bên dưới hộp văn bản, có nhãn là https://opea-project.github.io/latest/introduction/index.html

![VPC](/images/4.s3/image058.png)

Hỏi **"OPEA là gì?"** một lần nữa để xem câu trả lời đã cập nhật.

![VPC](/images/4.s3/image059.png)

Lần này, chatbot phản hồi chính xác dựa trên dữ liệu mà nó đã thêm vào lời nhắc từ nguồn mới, trang web OPEA.

**Kết luận**

Trong nhiệm vụ này, bạn đã khám phá cấu trúc cốt lõi của ứng dụng RAG, hiểu rõ hơn về cách từng thành phần hoạt động và tương tác trong hệ thống. Từ việc truy xuất thông tin có liên quan đến việc tạo phản hồi chính xác, mọi phần đều đóng vai trò quan trọng trong quy trình làm việc RAG của OPEA—nâng cao tính liên quan của phản hồi thông qua việc truy xuất trong khi cải thiện độ chính xác với mô hình ngôn ngữ nâng cao. Buổi thực hành này cung cấp hiểu biết rõ ràng về cách OPEA tận dụng RAG để xử lý các truy vấn phức tạp một cách hiệu quả và tinh chỉnh hiệu suất mô hình thông qua tích hợp thành phần liền mạch.

Trong nhiệm vụ tiếp theo, bạn sẽ triển khai các rào cản cho chatbot. Các rào cản này rất cần thiết để phát hiện và giảm thiểu sự thiên vị, đảm bảo rằng các phản hồi do AI tạo ra vẫn có trách nhiệm, công bằng và phù hợp với các nguyên tắc AI có đạo đức.