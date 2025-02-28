---
title : "Khám phá OpenSearch (Tùy chọn)"
date :  "2025-02-21" 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---
OpenSearch cung cấp một bộ tính năng mạnh mẽ để tìm kiếm và truy xuất dữ liệu. Phần này khám phá API truy vấn của nó để giới thiệu nhiều khả năng khác nhau.

Trong OpenSearch, một lược đồ—được gọi là ánh xạ—xác định cách dữ liệu được cấu trúc và lập chỉ mục. Ánh xạ xác định cách các trường trong tài liệu JSON được phân tích và có thể tìm kiếm được. Trong khi OpenSearch bao gồm tính năng phát hiện lược đồ tự động để triển khai nhanh chóng, thì việc xác định thủ công lược đồ trong quá trình tạo chỉ mục thường là cách tiếp cận tốt nhất. Mặc dù OPEA và dịch vụ vi mô OpenSearch đã định cấu hình ánh xạ, nhưng vẫn có thể truy xuất ánh xạ cho chỉ mục nhúng bằng lệnh sau.

curl -XGET https://localhost:9200/rag-opensearch/_mapping --insecure -u admin:strongOpea0! | jq .

Bạn sẽ thấy phản hồi như thế này:

![VPC](/images/5.fwd/image093.png)

Chỉ mục rag-opensearch bao gồm ba trường: siêu dữ liệu, văn bản và vector_field. Trường siêu dữ liệu lưu trữ JSON lồng nhau, bao gồm trường nguồn, có thể được truy cập trong các truy vấn bằng ký hiệu dấu chấm (ví dụ: metadata.source). Cả metadata.source và text đều được định nghĩa là các trường kiểu văn bản với một trường con từ khóa. Các trường văn bản trải qua quá trình phân tích cú pháp và phân tích thuật ngữ để tạo mã thông báo để khớp, trong khi các trường từ khóa được chuẩn hóa và sử dụng cho các truy vấn khớp chính xác. vector_field là trường kiểu knn_vector được thiết kế để lưu trữ các vectơ có 768 chiều. Công cụ lưu trữ được sử dụng là Thư viện không gian phi số liệu (NMSLIB), sử dụng thuật toán Hidden Navigable Small Worlds (HNSW).

**Truy vấn từ vựng**

OpenSearch là một công cụ tìm kiếm từ vựng ngoài việc là một công cụ tìm kiếm vectơ. Bạn có thể truy vấn nội dung rag bằng các truy vấn văn bản. Sử dụng truy vấn bên dưới để tìm kiếm các tài liệu OpenSearch, với thuật toán xếp hạng TF/IDF mặc định của nó (xem thêm Okapi BM25, xếp hạng dựa trên văn bản).

{{% notice info %}}
Nếu Cloud Shell của bạn đã chấm dứt, bạn có thể cần thiết lập lại chuyển tiếp cổng đến dịch vụ vi mô OpenSearch. Xem mô-đun trước để biết hướng dẫn.
{{% /notice %}}

Thực hiện lệnh sau để chạy truy vấn

![VPC](/images/5.fwd/image094.png)

Dòng đầu tiên của lệnh chạy lệnh curl, với URL chứa điểm cuối (localhost:9200, được chuyển tiếp đến dịch vụ vi mô OpenSearch) và thông số kỹ thuật API. Tại đây, yêu cầu được chuyển hướng đến chỉ mục rag-opensearch và gọi API _search. Các dòng sau xác định các tham số TLS và xác thực và sau cờ -d, nội dung yêu cầu chỉ định truy vấn.

Truy vấn này sử dụng tìm kiếm simple_query_string cho văn bản "Doanh thu năm 2023 của Nike là bao nhiêu?". Hàm simple_query_string phân tích văn bản đầu vào, chia nhỏ thành các mã thông báo riêng lẻ—về mặt kỹ thuật được gọi là các thuật ngữ—và khớp chúng với trường văn bản trong tất cả các tài liệu trong chỉ mục. Sau đó, nó sẽ chấm điểm và xếp hạng kết quả dựa trên thuật toán TF/IDF để đưa tài liệu có liên quan nhất lên đầu.

Các chỉ thị bổ sung trong truy vấn hướng dẫn OpenSearch loại trừ tất cả các trường khỏi phản hồi ("_source": false—xóa trường này sẽ trả về các giá trị tài liệu gốc), giới hạn kết quả thành một kết quả khớp duy nhất ("size": 1) và tô sáng các thuật ngữ khớp trong trường văn bản ("highlight": ...).

![VPC](/images/5.fwd/image095.png)

Phản hồi của OpenSearch bắt đầu bằng phần siêu dữ liệu bao gồm các chi tiết như thời gian xử lý truy vấn ở phía máy chủ (8 ms trong trường hợp này), liệu truy vấn có hết thời gian chờ hay không và thông tin về các phân đoạn phản hồi. Tiếp theo là phần lượt truy cập, chứa tổng số kết quả khớp, điểm liên quan cao nhất và chính các tài liệu khớp. Mỗi tài liệu bao gồm chỉ mục mà tài liệu đó thuộc về (_index), mã định danh duy nhất (_id), điểm liên quan, các trường nguồn (đã bị loại trừ trong truy vấn này) và các đoạn trích được tô sáng cho biết các thuật ngữ truy vấn khớp với tài liệu ở đâu. Các điểm nổi bật sử dụng thẻ HTML <em> để nhấn mạnh các thuật ngữ khớp.

Tuy nhiên, phản hồi không lý tưởng. Mặc dù xác định đúng các đề cập đến Nike, nhưng nó cũng bao gồm các từ không liên quan như what và is, làm giảm độ chính xác của kết quả.

Bạn có thể thử nghiệm các thuật ngữ truy vấn khác nhau bằng cách sửa đổi trường "truy vấn" (ví dụ: thay thế "doanh thu năm 2023 của nike là gì?" bằng các cụm từ khác) để quan sát cách phản hồi thay đổi. Điều chỉnh tham số "size" thành 2 hoặc nhiều hơn cho phép bạn xem nhiều kết quả cùng một lúc. Hãy thử các truy vấn như "workplace policies", "footwear apparel", "men sales" hoặc "women sales" để khám phá các đầu ra khác nhau.

Để tinh chỉnh tìm kiếm, bạn có thể chạy truy vấn k-Nearest-Neighbor (k-NN) chính xác. Trước tiên, hãy truy xuất nhúng cho truy vấn của bạn bằng dịch vụ vi mô chatqna-tei. Dịch vụ vi mô trả về một mảng các mảng, nhưng chỉ cần mảng bên trong cho truy vấn OpenSearch. Lệnh jq trích xuất phần tử đầu tiên và gán nó cho biến $embedding.

Để thực thi lệnh, bạn sẽ cần cổng được ánh xạ tới dịch vụ vi mô tei. Sử dụng lệnh ps aux | grep kubectl để kiểm tra các quy trình đang chạy và các cổng được chỉ định của chúng. Nếu bạn đã làm theo hướng dẫn đúng, dịch vụ vi mô chatqna-tei sẽ chạy trên cổng 9800.

![VPC](/images/5.fwd/image096.png)

Bạn có thể sử dụng echo $embedding để xem nhúng được tạo. Bây giờ, bạn sẽ tạo tệp cục bộ query.json với nhúng được hợp nhất vào truy vấn, sau đó chạy truy vấn k-Nearest-Neighbors (k-NN) chính xác để so sánh nhúng truy vấn với mọi tài liệu (chunk) trong chỉ mục và truy xuất các kết quả khớp gần nhất

![VPC](/images/5.fwd/image097.png)

Truy vấn này là truy vấn script_score, sử dụng một tập lệnh đã lưu để thực hiện tính toán điểm k-NN, so sánh vectơ truy vấn với mọi tài liệu trong chỉ mục. Truy vấn script_score bao gồm một truy vấn phụ, mà bạn có thể sử dụng để áp dụng bộ lọc cho các trường không phải vectơ. ChatQnA chỉ gửi khóa tệp trong trường metadata.source, do đó truy vấn này chỉ sử dụng match_all, khớp với mọi tài liệu trong chỉ mục. Phần script của truy vấn chỉ định script knn_score, với các tham số cho script biết trường nào có nhúng vectơ cho tài liệu, truyền nhúng dưới dạng query_value và chỉ định l2 là số liệu khoảng cách (space_type).

Phản hồi này là chính xác. Kết quả đầu tiên bao gồm văn bản "NIKE, Inc. Revenues were $51.2 billion in financial 2023." Không giống như các truy vấn dựa trên văn bản truyền thống, việc tô sáng không được hỗ trợ cho các trường vectơ vì chúng không chứa văn bản nguồn thô. Do đó, nội dung được truy xuất sẽ xuất hiện nguyên trạng từ trường văn bản.

Tìm kiếm k-Nearest-Neighbor (k-NN) chính xác có hiệu quả cao khi xử lý số lượng tài liệu tương đối nhỏ. Tuy nhiên, khi tập dữ liệu mở rộng, độ trễ truy vấn tăng lên đáng kể. Ngoài vài trăm nghìn tài liệu, tìm kiếm k-NN chính xác trở nên chậm một cách không thực tế. Để xử lý hiệu quả các tập dữ liệu lớn hơn, tìm kiếm lân cận gần nhất (ANN) là giải pháp thay thế tốt hơn. Lệnh bên dưới sử dụng thuật toán Hierarchical Navigable Small World (HNSW) để tìm các kết quả khớp gần nhất.

![VPC](/images/5.fwd/image098.png)

Truy vấn này là truy vấn knn, sử dụng thuật toán bạn đã chỉ định trong ánh xạ trường để xác định các lân cận gần nhất. Bạn chỉ cần truyền vào một vectơ và một giá trị cho k (số lượng lân cận cần truy xuất) và opensearch sẽ thực hiện phần còn lại.

**Tìm kiếm kết hợp với OpenSearch**

Một lần nữa, bạn có thể thấy tài liệu chính xác là kết quả đầu tiên được truy xuất. Sử dụng k-NN gần đúng, bạn có thể mở rộng cơ sở dữ liệu vectơ OpenSearch của mình lên hàng tỷ vectơ và hàng nghìn truy vấn mỗi giây.

OpenSearch hỗ trợ tìm kiếm kết hợp -- trong đó bạn chỉ định cả truy vấn từ vựng và vectơ, cùng với chiến lược chuẩn hóa và hợp nhất. OpenSearch chạy cả hai truy vấn, chuẩn hóa và hợp nhất các kết quả. Khi bạn thực hiện tìm kiếm kết hợp, bạn thiết lập một Đường ống tìm kiếm và gửi các truy vấn qua đường ống đó. Sử dụng lệnh bên dưới để sử dụng API REST của OpenSearch để thiết lập đường ống tìm kiếm.

![VPC](/images/5.fwd/image099.png)

Đường ống này sử dụng chuẩn hóa min/max để thiết lập tất cả các điểm từ vựng và vectơ trong phạm vi [0, 1]. Nó sử dụng trung bình số học để kết hợp các điểm, với trọng số là 0,3 cho mệnh đề truy vấn đầu tiên và 0,7 cho mệnh đề truy vấn thứ hai. Lưu ý, đây không phải là một truy vấn, khi bạn gửi các truy vấn đến đường ống tìm kiếm này, OpenSearch sẽ áp dụng các trọng số. phase_results_processor là một cấu trúc chung linh hoạt - các mệnh đề truy vấn có thể là từ vựng hoặc vectơ.

Để sử dụng đường ống, bạn gửi một truy vấn đến API đường ống. Sử dụng lệnh bên dưới để tạo truy vấn trong tệp hybrid_query.json.

![VPC](/images/5.fwd/image100.png)

Truy vấn kết hợp này chứa hai truy vấn phụ - truy vấn từ vựng cho "doanh thu giày dép" và truy vấn vectơ có nhúng biểu diễn "Doanh thu Nike 2023 là gì?". Giá trị k, 2, đảm bảo rằng kết quả sẽ chứa tối đa hai kết quả khớp vectơ.

Lệnh curl gửi truy vấn này đến nlp-search-pipeline mà bạn vừa xác định. Bạn sẽ nhận được những kết quả này (chúng tôi đã xóa siêu dữ liệu kết quả và các phần khác để hiển thị đầu ra có liên quan)

Kết quả khớp đầu tiên trong kết quả giống hệt với kết quả được truy xuất bằng truy vấn lân cận gần nhất. Kết quả khớp thứ hai và thứ tư bắt nguồn từ truy vấn từ vựng "doanh thu giày dép", trong khi kết quả thứ hai đến từ tìm kiếm vectơ. Cách tiếp cận này kết hợp hiệu quả các hiểu biết trừu tượng, ngữ nghĩa về doanh thu với các kết quả khớp dựa trên từ khóa cụ thể hơn cho "doanh thu giày dép".

Khi thiết kế hệ thống tìm kiếm, việc dự đoán chính xác các loại truy vấn mà người dùng sẽ chạy có thể là một thách thức. Bằng cách tận dụng các truy vấn kết hợp như thế này, bạn có thể cung cấp sự kết hợp cân bằng giữa kết quả ngữ nghĩa và từ vựng, nâng cao độ chính xác và tính liên quan của tìm kiếm.

Bạn cũng có thể thử nghiệm với phân phối trọng số trong nlp-search-pipeline để quan sát tác động của nó lên thứ hạng kết quả. Ví dụ, đặt trọng số truy vấn từ vựng thành 0,9 và trọng số tìm kiếm vectơ thành 0,1 sẽ chuyển kết quả hàng đầu thành "Doanh thu giày dép tăng 25% theo cơ sở trung lập tiền tệ,..." với điểm là 0,9. Trong khi đó, kết quả hàng đầu trước đó, "Trong năm tài chính 2023, NIKE, Inc. đạt doanh thu kỷ lục là 51,2 tỷ đô la", chuyển lên vị trí thứ hai với điểm là 0,1, tiếp theo là tất cả các kết quả khớp vectơ khác.

**Kết luận**

Thông qua quy trình này, bạn đã thực hiện và so sánh các kỹ thuật tìm kiếm khác nhau, bao gồm tìm kiếm từ vựng, k-NN chính xác, k-NN gần đúng và tìm kiếm kết hợp, trực tiếp bằng cách sử dụng API của OpenSearch. Hiện tại, OPEA chỉ dựa vào k-NN gần đúng, nhưng các cải tiến trong tương lai có thể giới thiệu các kỹ thuật tìm kiếm kết hợp nâng cao này!