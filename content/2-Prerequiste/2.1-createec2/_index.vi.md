---
title : "Sử dụng Workshop Studio"
date : "2025-02-21" 
weight : 1 
chapter : false
pre : " <b> 2.1 </b> "
---

Nếu bạn tham gia các bài lab này trong một sự kiện do AWS tổ chức trên Workshop Studio, bạn không cần tự cấp phát tài nguyên—tất cả các tài nguyên cần thiết cho các mô-đun sẽ được thiết lập sẵn.

Những gì sẽ được thiết lập trong tài khoản AWS của bạn?
Stack sẽ tự động cấu hình các thành phần sau:

Cụm EKS:
Tạo mới cụm EKS có tên opea-eks-cluster.
Triển khai một node trong cụm sử dụng M7i.24xlarge instance để đảm bảo hiệu suất cao.
CloudFormation Templates:
Stack cũng tạo các mẫu CloudFormation cho từng mô-đun:

Mô-đun 1: ChatQnA Default

Mô-đun 2: ChatQnA với Guardrails

Mô-đun 3: ChatQnA với OpenSearch (mã nguồn mở) làm cơ sở dữ liệu vector

Mô-đun 4: ChatQnA với Bedrock làm mô hình LLM

Mô-đun 5: ChatQnA với Remote Inference (Denvr) làm mô hình LLM

Với các tài nguyên được cấu hình sẵn, bạn có thể tập trung hoàn toàn vào việc khám phá và phát triển ứng dụng GenAI mà không cần lo lắng về việc thiết lập hạ tầng.

Bước 1: Cấu hình quyền truy cập vào cụm EKS
Để tương tác với cụm EKS bằng kubectl, bạn cần cấu hình môi trường của mình để nhận diện cụm. Điều này được thực hiện bằng cách cập nhật tệp kubeconfig, tệp này lưu trữ thông tin xác thực và cấu hình truy cập cho Kubernetes.

1. Đăng nhập vào AWS Management Console:
Bắt đầu bằng cách đăng nhập vào AWS Management Console.
![VPC](/images/2/image001.png)

2. Mở Cloud Shell hoặc thiết lập môi trường cục bộ:
Trong giao diện console, nhấp vào biểu tượng Cloud Shell để mở terminal được cấu hình sẵn.
Nếu sử dụng môi trường cục bộ, hãy đảm bảo bạn đã cài đặt AWS CLI Client và kubectl trên máy cá nhân.
![VPC](/images/2/image002.png)

3. Cập nhật kubeconfig:
Chạy lệnh sau để cập nhật kubeconfig với thông tin của cụm EKS:
![VPC](/images/2/image004.png)
Bạn sẽ nhận được một đầu ra xác nhận rằng tệp cấu hình của bạn đã được cập nhật.
![VPC](/images/2/image005.png)
Giờ đây, bạn có thể sử dụng kubectl để quản lý cụm Kubernetes.
Bước 2: Kiểm tra kết nối với cụm EKS
Sau khi cập nhật kubeconfig, hãy kiểm tra xem bạn có thể kết nối với cụm hay không bằng cách liệt kê các node:
![VPC](/images/2/image006.png)
Nếu lệnh thực thi thành công, bạn sẽ thấy đầu ra hiển thị các nút được liên kết với cụm của bạn.
![VPC](/images/2/image007.png)
Bây giờ bạn đã sẵn sàng khám phá mô-đun bạn chọn và bắt đầu triển khai khối lượng công việc trên cụm EKS của mình!
