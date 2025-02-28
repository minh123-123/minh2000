---
title : "Triển khai ChatQnA"
date :  "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---
### Giới thiệu
Trong phần này, bạn sẽ triển khai bản thiết kế OPEA cho ứng dụng dựa trên RAG, **ChatQnA**, trên môi trường Amazon Elastic Kubernetes Service (EKS). Khám phá thực hành này sẽ giúp bạn hiểu rõ hơn về cách ứng dụng RAG hoạt động trong hệ sinh thái Kubernetes được quản lý, cho phép bạn phân tích các thành phần của ứng dụng và vai trò của chúng trong hệ thống.

Cấu hình triển khai có sẵn thông qua **AWS Marketplace** và ChatQnA có thể được tìm thấy trong kho lưu trữ **GenAIExamples** của OPEA.

[ChatQnA trên GitHub](https://github.com/opea-project/GenAIExamples/tree/main/ChatQnA)

Vì bạn đã thiết lập quyền truy cập vào cụm Kubernetes của mình trong phần "Thiết lập", giờ đây bạn sẽ tìm hiểu sâu hơn về môi trường đã triển khai để khám phá và tìm hiểu thêm về cấu trúc và chức năng của cụm.

#### Bước 1: Triển khai mẫu ChatQnA CloudFormation
Mở AWS CloudShell và triển khai mẫu ChatQnA CloudFormation vào cụm Amazon EKS của bạn. Thao tác này sẽ khởi tạo quá trình triển khai ứng dụng RAG trong môi trường Kubernetes được quản lý của bạn.
![VPC](/images/2/image020.png)

#### Bước 2: Khám phá Tài nguyên cụm
Điều hướng đến AWS Management Console và chọn cụm EKS được chỉ định của bạn để xem lại quá trình triển khai. Mỗi cụm bao gồm các cấu hình quan trọng như phiên bản Kubernetes, thiết lập mạng và tùy chọn ghi nhật ký. Kiểm tra các thiết lập này sẽ giúp hiểu sâu hơn về cơ sở hạ tầng của ứng dụng, giúp quản lý hiệu quả và khắc phục sự cố khi cần thiết.

Xem lại Tài nguyên cụm
Nhấp vào tab Tài nguyên để xem tất cả các ứng dụng hiện đang chạy trong cụm của bạn, bao gồm ChatQnA và các dịch vụ vi mô liên quan.
![VPC](/images/2/image021.png)
![VPC](/images/2/image022.png)
Đảm bảo rằng tất cả các dịch vụ vi mô từ bản thiết kế OPEA ChatQnA được cài đặt đúng cách bằng cách liệt kê các pod đang hoạt động:
![VPC](/images/2/image023.png)
Đầu ra sẽ hiển thị tất cả các pod ở trạng thái Đang chạy (1/1), xác nhận rằng ứng dụng đã được triển khai thành công. Tại thời điểm này, bạn đã sẵn sàng để khám phá thêm về việc triển khai và quản lý tài nguyên của mình trong cụm.
![VPC](/images/2/image024.png)