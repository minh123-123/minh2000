---
title : "Khám phá OpenSearch (Tùy chọn)"
date :  "2025-02-21" 
weight : 3
chapter : false
pre : " <b> 7.3 </b> "
---
**Khám phá RAG và tương tác với UI**

Bây giờ tất cả các dịch vụ đã hoạt động, hãy cùng khám phá UI do triển khai cung cấp.

Để truy cập Giao diện người dùng ChatQnA Bedrock, hãy mở trình duyệt web và điều hướng đến DNS của ChatQnA Bedrock Load Balancer:

👉 http://bedrock-ingress-xxxxxxx.us-east-2.elb.amazonaws.com (Thay thế bằng URL DNS Bedrock Ingress thực tế của bạn).

Khi đã vào bên trong UI, bạn có thể tương tác với chatbot, kiểm tra phản hồi của chatbot và trải nghiệm cách chatbot xử lý các truy vấn bằng cách sử dụng chức năng truy xuất do RAG cung cấp.

![VPC](/images/5.fwd/image124.png)

Bây giờ khi bạn gửi lời nhắc đến chatbot, phản hồi sẽ đến từ Claude Haiku của Anthropic thông qua Amazon Bedrock.
