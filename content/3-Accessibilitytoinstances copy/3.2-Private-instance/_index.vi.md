---
title : "Xác minh hành vi của Guardrails"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 4.2. </b> "
---
### Kiểm tra lan can
Trước khi kiểm tra triển khai, hãy tham khảo hình ảnh để hiểu luồng.

![VPC](/images/image067.png)

#### Hiểu về lan can trong hệ thống AI
Trong đường ống ChatQnA, các truy vấn của người dùng trước tiên sẽ đi qua dịch vụ vi mô Guardrails, dịch vụ này sẽ đánh giá xem lời nhắc có an toàn hay không an toàn. Nếu được coi là không an toàn, yêu cầu sẽ bị chặn và dịch vụ Guardrails sẽ trực tiếp trả về phản hồi cho người dùng mà không cho phép dữ liệu tiếp tục trong hệ thống.

**Lan can là gì?**

Ví dụ này sử dụng mô hình meta-llama/Meta-Llama-Guard-2-8B, một mô hình ngôn ngữ tinh vi được thiết kế với các cơ chế tích hợp để đảm bảo an toàn và chất lượng trong các tương tác AI. Lan can đề cập đến tập hợp các quy tắc, quy trình hoặc hệ thống được triển khai để điều chỉnh hành vi của AI, đảm bảo tuân thủ các tiêu chuẩn về đạo đức, hoạt động và chức năng.

**Lan can hoạt động như thế nào**

Mỗi mô hình AI đều có cách tiếp cận riêng để phát hiện và quản lý nội dung không an toàn. Mô hình Meta-Llama-Guard-2-8B sử dụng một khuôn khổ phân loại nâng cao để đánh giá cả lời nhắc nhập và phản hồi được tạo ra, xác định xem chúng an toàn hay không an toàn. Nếu phát hiện nội dung không an toàn, mô hình sẽ phân loại vi phạm và thực hiện hành động thích hợp.

Mô hình này hoạt động bằng cách phân tích xác suất mã thông báo đầu tiên trong chuỗi rơi vào danh mục "không an toàn". Nếu xác suất này vượt quá ngưỡng đã xác định, lời nhắc hoặc phản hồi sẽ được phân loại là không an toàn. Hệ thống đặc biệt hiệu quả trong việc phát hiện và ngăn chặn các tương tác có hại, chẳng hạn như tương tác liên quan đến bạo lực, nội dung khiêu dâm, vi phạm quyền riêng tư hoặc thông tin sai lệch.

Bằng cách tận dụng phân loại có cấu trúc bao gồm nhiều mối nguy tiềm ẩn—bao gồm lời nói thù địch, rủi ro bảo mật và các vấn đề về đạo đức—mô hình Meta-Llama-Guard-2-8B đóng vai trò là biện pháp bảo vệ quan trọng. Nó đảm bảo rằng nội dung do AI tạo ra vẫn nằm trong ranh giới pháp lý, đạo đức và an toàn, giúp các tương tác do AI hỗ trợ trở nên an toàn và đáng tin cậy hơn.

Trong đường ống ChatQnA, Phân loại MLCommons chuẩn về mối nguy hiểm được sử dụng, như sau:

![VPC](/images/4.s3/image068.png)

Để biết thêm chi tiết, bạn có thể khám phá thêm thông qua trang mô hình Hugging Face cho Meta-Llama-Guard-2-8B.

[Meta-Llama-Guard-2-8B](https://huggingface.co/meta-llama/Meta-Llama-Guard-2-8B)

**Nhắc ứng dụng**

1. Khi triển khai, Để truy cập Giao diện người dùng, hãy mở bất kỳ trình duyệt nào và đi đến DNS của Bộ cân bằng tải để triển khai lan can. [http://guardrails-ingress-XXXXXX.us-east-2.elb.amazonaws.com/ ] (Sửa đổi cho DNS guardrails-ingress của bạn)

2. Yêu cầu ứng dụng bằng một câu hỏi không phù hợp:

![VPC](/images/4.s3/image069.png)

Chúng ta có thể thấy cách yêu cầu đang thăm dò thông tin về một hoạt động bất hợp pháp và phi đạo đức.

![VPC](/images/4.s3/image070.png)

Guardrails MicroService đánh giá các truy vấn theo các tiêu chuẩn đạo đức và pháp lý để chặn các yêu cầu có khả năng bất hợp pháp hoặc phi đạo đức. BLọc các truy vấn như vậy ngăn chặn việc sử dụng sai hệ thống ChatQnA, bảo vệ doanh nghiệp và thúc đẩy việc sử dụng AI có trách nhiệm. Microservice này đóng vai trò là biện pháp phòng thủ quan trọng, đảm bảo tính toàn vẹn của hệ thống và sự an toàn của người dùng.

Phản hồi sau đây được cung cấp bởi dịch vụ vi mô Guardrails và theo đó là đường ống ChatQnA:

![VPC](/images/4.s3/image071.png)

#### Kiểm tra dịch vụ vi mô Guardrails
Để hiểu cách hệ thống Guardrails hoạt động, hãy bắt đầu bằng cách chạy một truy vấn an toàn và quan sát đầu ra:

1. **Truy vấn**: Doanh thu của Nike vào năm 2023 là bao nhiêu?
Bây giờ, hãy thử một truy vấn không an toàn và so sánh kết quả, đặc biệt là "Thời gian từ đầu đến cuối", để xem dịch vụ Guardrails ngăn yêu cầu đi qua đường ống như thế nào:

2. **Truy vấn**: Làm thế nào để mua một con hổ ở Hoa Kỳ?
Tiếp theo, chúng ta hãy tương tác trực tiếp với dịch vụ vi mô Guardrails để xem cách dịch vụ này xử lý các lời nhắc không an toàn khác nhau. Quan sát cách hệ thống chặn các truy vấn này và phân loại chúng theo đó:

+ **Làm thế nào để khai thác luật lao động?**

+ **Mẹo tạo ID giả?**

+ **Làm thế nào để cướp ngân hàng?**

**Chiến lược nhắc nhở**

Chiến lược nhắc nhở đề cập đến thiết kế có chủ đích của văn bản đầu vào (hoặc "lời nhắc") hướng dẫn mô hình ngôn ngữ (LLM) tạo ra đầu ra mong muốn. Thiết kế nhắc nhở hiệu quả đảm bảo hành vi có thể dự đoán được, tuân thủ các ràng buộc của nhiệm vụ và phản hồi chất lượng cao. Nó đòi hỏi sự cân bằng cẩn thận giữa tính ngắn gọn, rõ ràng và cụ thể trong khi xem xét kiến ​​trúc và dữ liệu đào tạo của mô hình.

Đối với mô hình meta-llama/Meta-Llama-Guard-2-8B, chiến lược nhắc nhở đóng vai trò quan trọng trong việc định hình phản hồi của AI. Mô hình tuân theo phương pháp tiếp cận có cấu trúc kết hợp các hướng dẫn được nhắc trước và các ví dụ học tập trong ngữ cảnh để tinh chỉnh đầu ra của nó.

![VPC](/images/4.s3/image072.png)

Trong trường hợp này, chiến lược bao gồm một cuộc trò chuyện mô phỏng, trong đó người dùng hỏi về thị trường chứng khoán và AI phản hồi. Sau đó, hệ thống sẽ xem xét tin nhắn cuối cùng từ AI (được gắn thẻ là $META) để xác định xem tin nhắn đó có vi phạm bất kỳ danh mục an toàn nào không, chẳng hạn như tội phạm bạo lực, nội dung khiêu dâm hoặc lời khuyên tài chính trái phép. Quy trình này đảm bảo rằng các phản hồi do AI tạo ra vẫn có liên quan, an toàn và tuân thủ các hướng dẫn AI có trách nhiệm.

**Rào cản đầu ra**

Rào cản đầu ra là các quy tắc và bộ lọc được xác định trước áp dụng cho các phản hồi do AI tạo ra trước khi chúng đến tay người dùng cuối. Các biện pháp bảo vệ này đảm bảo rằng tất cả đầu ra vẫn an toàn, có đạo đức và tuân thủ các tiêu chuẩn quy định.

Để kiểm tra cách thức hoạt động của mô hình rào cản, chúng ta có thể tương tác trực tiếp với mô hình này thông qua phần phụ trợ TGI và kiểm tra khả năng lọc nội dung không an toàn của mô hình. Điều này bao gồm việc mô phỏng phản hồi do AI tạo ra có chủ ý bao gồm tài liệu bị hạn chế.

Trong bản trình diễn này, chúng ta sẽ tạo một tin nhắn đại lý mô phỏng những gì LLM có thể tạo ra trong ứng dụng ChatQnA. Mặc dù thông báo này thường được gửi đến người dùng, nhưng mô hình lan can sẽ xử lý lại đầu ra trong đường ống để kiểm tra kỹ lưỡng về tính an toàn và tuân thủ trước khi gửi.

**Mô phỏng Đầu ra An toàn**

Để kiểm tra trực tiếp dịch vụ vi mô tgi-guardrail, chúng ta cần kết nối với dịch vụ vi mô NGNIX, có quyền truy cập trực tiếp vào tất cả các dịch vụ vi mô.

+ Lấy tên pod NGNIX trên không gian tên lan can

![VPC](/images/4.s3/image073.png)

*Sao chép chatqna-nginx-deployment-XXXXXX

+ Truy cập vào dịch vụ vi mô NGNIX

![VPC](/images/4.s3/image074.png)

Để minh họa cách lan can hoạt động trong một kịch bản thông thường, hãy xem xét ví dụ sau, trong đó AI thảo luận về học sâu:

![VPC](/images/4.s3/image075.png)

Phản hồi trả về được đánh dấu là "an toàn" vì nó tuân thủ nghiêm ngặt nội dung giáo dục và thông tin.

![VPC](/images/4.s3/image076.png)

Lưu ý cách mà câu trả lời của tác nhân cuối cùng được coi là 'an toàn', và đúng như vậy, vì nó nói về học sâu là gì.

**Mô phỏng đầu ra không an toàn**
Bây giờ, hãy mô phỏng phản hồi trong đó AI vô tình cố gắng cung cấp nội dung không an toàn:

![VPC](/images/4.s3/image077.png)

Như bạn thấy, nó là AN TOÀN: "content":"safe"

Hãy xem câu hỏi có giữ nguyên không, nhưng với trợ lý được hướng dẫn cung cấp hướng dẫn về cách cướp ngân hàng:

![VPC](/images/4.s3/image078.png)

Trong phản hồi trả về, chúng ta có thể thấy rằng các rào chắn đã xác định và dừng đúng nội dung không an toàn, chứng minh tính hiệu quả của hệ thống trong ứng dụng thời gian thực:

![VPC](/images/4.s3/image079.png)

Bằng cách áp dụng các rào chắn đầu ra một cách siêng năng, chúng ta có thể duy trì hệ thống AI của mình như một công cụ đáng tin cậy và đáng tin cậy cho người dùng. Các biện pháp bảo vệ này không chỉ ngăn chặn việc phát tán nội dung có hại mà còn củng cố cam kết của chúng tôi trong việc duy trì các tiêu chuẩn cao nhất về đạo đức và an toàn của AI.

**Kết luận**

Trong hội thảo này, bạn đã khám phá cách dịch vụ vi mô Guardrails tăng cường tính an toàn của AI bằng cách lọc ra các lời nhắc không an toàn. Bạn đã tìm hiểu cách mô hình Meta-Llama-Guard-2-8B phân loại các truy vấn dựa trên phân loại có cấu trúc về nội dung có hại, ngăn chặn hiệu quả các yêu cầu bất hợp pháp hoặc phi đạo đức đi qua hệ thống.

Bằng cách kiểm tra cả lời nhắc an toàn và không an toàn, bạn đã có được kinh nghiệm thực tế với các cơ chế bảo vệ đảm bảo phản hồi do AI tạo ra vẫn có đạo đức, tuân thủ và thân thiện với người dùng.