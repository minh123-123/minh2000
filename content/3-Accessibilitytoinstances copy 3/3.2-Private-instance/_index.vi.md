---
title : "Xác minh OPEA Chat QnA với Inferenece API"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 6.2. </b> "
---
### Kiểm tra bằng Giao diện người dùng ChatQnA
Bây giờ tất cả các dịch vụ đã hoạt động, đã đến lúc khám phá giao diện người dùng ChatQnA.

**Truy cập Giao diện người dùng**

Để mở Giao diện người dùng, hãy khởi chạy bất kỳ trình duyệt web nào và điều hướng đến URL DNS của Bộ cân bằng tải ChatQnA:
👉 http://denvr-ingress-xxxxxxx.us-east-2.elb.amazonaws.com
(Thay thế URL này bằng URL DNS denvr-ingress thực tế của bạn.)

**Tương tác với Chatbot**

Khi đã vào Giao diện người dùng, bạn có thể tương tác với chatbot và kiểm tra phản hồi của chatbot theo thời gian thực.

![VPC](/images/5.fwd/image110.png)

Để xác minh UI, hãy hỏi

![VPC](/images/5.fwd/image111.png)

![VPC](/images/5.fwd/image112.png)

**Cải thiện Phản hồi của Chatbot bằng Bối cảnh Cập nhật**

Bạn có thể nhận thấy rằng phản hồi ban đầu của chatbot đã lỗi thời, chung chung hoặc thiếu thông tin chi tiết cụ thể về Denvr Cloud. Điều này xảy ra vì Denvr Cloud không được đưa vào tập dữ liệu được sử dụng để đào tạo mô hình ngôn ngữ. Vì hầu hết các mô hình ngôn ngữ đều là tĩnh nên chúng chỉ dựa vào thông tin có sẵn tại thời điểm đào tạo.

**Thêm Bối cảnh Thông qua UI**
Để giải quyết hạn chế này, UI bao gồm một biểu tượng tải lên cho phép bạn cung cấp bối cảnh có liên quan. Khi bạn tải lên tài liệu hoặc liên kết, quy trình sau sẽ được kích hoạt:

1. Tài liệu được gửi đến dịch vụ vi mô DataPrep, nơi các nhúng được tạo.
2. Sau đó, dữ liệu đã xử lý được đưa vào Cơ sở dữ liệu Vector.

Bằng cách tải lên thông tin mới, bạn sẽ mở rộng hiệu quả cơ sở kiến ​​thức của chatbot, đảm bảo phản hồi của chatbot chính xác hơn, có liên quan hơn và cập nhật hơn.

![VPC](/images/5.fwd/image113.png)

**Tải lên tệp hoặc trang web để phản hồi nâng cao**

Việc triển khai cho phép bạn tải lên tệp hoặc trang web để cải thiện kiến ​​thức theo ngữ cảnh của chatbot. Đối với ví dụ này, hãy sử dụng trang web Denvr Datawork bằng cách làm theo các bước sau:

1. Nhấp vào biểu tượng tải lên để mở bảng điều khiển bên phải.
2. Chọn "Dán liên kết".
3. Sao chép và dán URL sau vào hộp nhập:
👉 https://www.denvrdata.com/intel
4. Nhấp vào Xác nhận để bắt đầu quá trình lập chỉ mục.
5. Sau khi lập chỉ mục hoàn tất, bạn sẽ thấy biểu tượng có nhãn https://www.denvrdata.com/intel xuất hiện bên dưới hộp văn bản, cho biết thông tin đã được thêm thành công.

![VPC](/images/5.fwd/image114.png)

Hỏi lại "Denvr là gì?" để xem câu trả lời đã cập nhật.

![VPC](/images/5.fwd/image115.png)

Lần này, chatbot phản hồi chính xác dựa trên dữ liệu mà nó đã thêm vào lời nhắc từ nguồn mới, trang web Denvr Cloud.

**Kết luận**

Trong hội thảo này, những người tham gia đã có được kinh nghiệm thực tế trong việc tích hợp các API suy luận được quản lý. Phiên thảo luận đã chứng minh rằng OPEA là một khuôn khổ cực kỳ linh hoạt, có khả năng triển khai các đường ống trên nhiều nhà cung cấp đám mây, bao gồm AWS và Denvr Cloud. Ngoài ra, phiên thảo luận còn giới thiệu cách các dịch vụ vi mô OPEA có thể được tận dụng để triển khai LLM trên Intel Gaudi2 AI Accelerators, đảm bảo triển khai mô hình AI hiệu quả và có khả năng mở rộng.