---
title : "Sử dụng tài khoản của riêng bạn"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 2.2 </b> "
---

### Hướng dẫn thiết lập môi trường cho phòng thí nghiệm AWS

Nếu bạn KHÔNG thực hiện các phòng thí nghiệm này trong một sự kiện do AWS tổ chức trên Workshop Studio, bạn cần chuẩn bị môi trường trước khi bắt đầu. Lưu ý rằng các tài nguyên này sẽ phát sinh chi phí, vì vậy hãy nhớ dọn dẹp sau khi hoàn thành.

#### Bước 1: Cấu hình môi trường
Thiết lập tài khoản
Thực hiện các bước sau để cấu hình tài khoản của bạn:

1. Nhấn vào Launch Stack để khởi chạy CloudFormation với các giá trị được cấu hình sẵn trong khu vực us-east-1. 

    [Launch Stack](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/50cf2fdb-1972-4e79-814f-12d2e6fb0dab/LaunchStack.yml&stackName=LaunchStack)

Nếu muốn chạy workshop ở khu vực khác, hãy thay đổi khu vực phù hợp.
Stack sẽ thiết lập các thành phần sau:
Cụm EKS (Amazon Elastic Kubernetes Service):

-Tạo một cụm EKS mới có tên opea-eks-cluster.
-Triển khai một node trong cụm sử dụng M7i.24xlarge.

Các mẫu CloudFormation:
Cung cấp các mẫu CloudFormation để triển khai các mô-đun sau:
Module 1:ChatQnA Default

Module 2:ChatQnA with Guardrails

Module 3:ChatQnA with OpenSearch (một cơ sở dữ liệu vector mã nguồn mở)

Module 4:ChatQnA with Remote Inference (Denvr) làm mô hình LLM

Module 5:ChatQnA with Bedrock làm mô hình LLM

Cấu hình trước khi chạy Stack
Trước khi chạy stack, trên trang Quick create stack, thực hiện các bước sau:

+ **HuggingFaceToken**:Cung cấp mã token để tải xuống mô hình từ Hugging Face.
Nếu sử dụng tính năng Guardrails, đảm bảo token có quyền truy cập vào mô hình meta-llama/Meta-Llama-Guard-2-8B.

+ **ModelID**:OPEA sử dụng Text Generation Inference toolkit.
Chọn mô hình từ danh sách các mô hình được hỗ trợ trên Hugging Face và nhập Model ID.
+ **OpeaRoleArn**:Nhập ARN hoặc tên của role mà bạn đang sử dụng.

    + Nếu không chắc, hãy kiểm tra thông tin người dùng ở góc trên bên phải màn hình.
    + Nếu tên không có dấu /, sao chép toàn bộ. Nếu có dấu /, chỉ lấy phần trước dấu /.
**Ví dụ**: 
    + Nếu thấy "USER", nhập "USER". 
    + Nếu thấy "ADMIN-ROLE/USER", chỉ nhập "ADMIN-ROLE".
Lấy ARN bằng lệnh AWS CLI (nếu cần).

aws sts get-caller-identity --query 'Arn' --output text | awk -F: '{print $NF}' | (read id; if [[ $id == *"user"* ]]; then aws sts get-caller-identity --query 'Arn' --output text; else role=$(echo $id | cut -d'/' -f2); aws iam get-role --role-name $role --query 'Role.Arn' --output text; fi)

{{% notice info %}}
Đánh dấu vào hộp kiểm bên cạnh Tôi xác nhận rằng AWS CloudFormation có thể tạo tài nguyên IAM
{{% /notice %}}

Quá trình triển khai
Sau khi thiết lập các tham số, nhấp vào Tạo ngăn xếp.

![VPC](/static/images/2/image008.png)

Quá trình này sẽ khởi tạo Dự án AWS CodeBuild, dự án này sẽ đưa thư viện nguồn mở opea-demo-builder vào

Bộ công cụ phát triển đám mây AWS (CDK) sẽ tạo các mẫu CloudFormation cần thiết để định cấu hình môi trường AWS của bạn cho hội thảo.

Giám sát tiến độ triển khai
Quá trình triển khai mất khoảng 25 phút.
Mở Bảng điều khiển AWS CloudFormation và theo dõi tiến trình.

![VPC](/images/2/image009.png)

Đảm bảo rằng các ngăn xếp sau đạt trạng thái CREATE_COMPLETE:

+ Cụm EKS
+ Ứng dụng ChatQnA (triển khai mặc định)

Một số ngăn xếp có thể vẫn còn trong REVIEW_IN_PROGRESS vì chúng sẽ được triển khai trên cụm EKS sau này.

![VPC](/images/2/image010.png)

#### Bước 2: Định cấu hình quyền truy cập vào cụm EKS của bạn
Sau khi triển khai thành công các ngăn xếp CloudFormation, bạn cần đặt cấu hình môi trường cục bộ của mình để tương tác với cụm Amazon EKS bằng kubectl.

Cập nhật cấu hình Kubernetes của bạn (kubeconfig)
1. Mở AWS CloudShell

+ Bấm vào biểu tượng CloudShell trên Bảng điều khiển quản lý AWS.
+ Ngoài ra, hãy sử dụng AWS CLI cục bộ của bạn, đảm bảo kubectl và máy khách AWS CLI được cài đặt trên hệ thống của bạn.

2. Cập nhật kubeconfig

+ Chạy lệnh sau (cập nhật vùng nếu cần):
![VPC](/images/2/image011.png)

+ Nếu thành công, bạn sẽ nhận được thông báo xác nhận cho biết file cấu hình của bạn đã được cập nhật.
![VPC](/images/2/image012.png)

3. Xác minh kết nối với cụm EKS của bạn
Đảm bảo bạn có thể tương tác với cụm bằng kubectl.

#### Bước 3: Xác minh quyền truy cập cụm EKS
Sau khi cập nhật kubeconfig, hãy kiểm tra xem bạn có thể kết nối với cụm không. Nếu bạn gặp sự cố khi truy cập nhóm thông qua bảng điều khiển AWS hoặc CloudShell, hãy làm theo các bước sau:

1. Kiểm tra quyền truy cập IAM của bạn trong Bảng điều khiển EKS

    + Điều hướng đến Bảng điều khiển EKS.
![VPC](/images/2/image013.png)
    
    + Tìm ARN chính IAM của bạn trong Mục truy cập.
![VPC](/images/2/image014.png)

![VPC](/images/2/image015.png)

2. Nếu ARN của bạn không được liệt kê, hãy thêm nó theo cách thủ công
    + Bấm vào Tạo mục truy cập.
    + Nhập ARN người dùng IAM hoặc ARN vai trò của bạn.
![VPC](/images/2/image016.png)

3. Đính kèm các chính sách bắt buộc
Gán các quyền sau cho vai trò IAM của bạn:
    + Chính sách quản trị của AmazonEKS
    + Chính sách quản trị AmazonEKSCluster
![VPC](/images/2/image017.png)

4. Xác nhận quyền truy cập bằng nút niêm yết
    + Chạy lệnh sau để kiểm tra xem các nút worker có hiển thị hay không:
![VPC](/images/2/image018.png)

    + Nếu lệnh trả về danh sách các nút thì cluster của bạn đã được cấu hình thành công.
**Lưu ý**: Nếu không có nút nào xuất hiện, hãy đợi vài phút và thử lại vì quá trình cung cấp nút có thể vẫn đang được tiến hành.
![VPC](/images/2/image019.png)

#### Các bước tiếp theo: Khám phá các mô-đun hội thảo
Sau khi kết nối thành công với cụm EKS của mình, bạn đã sẵn sàng tiếp tục với bất kỳ mô-đun hội thảo nào. Chọn mô-đun phù hợp nhất với mục tiêu học tập của bạn và bắt đầu trải nghiệm thực tế với các giải pháp AWS, Kubernetes và hỗ trợ AI.
