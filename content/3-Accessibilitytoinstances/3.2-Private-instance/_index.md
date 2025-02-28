---
title : "Explore the OPEA ChatQnA deployment"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---
### Exploring the OPEA Microservices Deployment
Now, let's dive into the OPEA ChatQnA RAG deployment. As a microservices-based blueprint, it is designed for scalability, resilience, and flexibility. In this task, you will explore each microservice to understand its role within the overall system. By examining these components, you will gain insights into how they interact and contribute to the application's functionality.

This architecture offers several key advantages:

+ **Scalability** – Each microservice can scale independently based on demand, ensuring optimal resource utilization and performance.

+ **Fault Isolation** – If one service encounters an issue, it won’t disrupt the entire system, enhancing reliability.

+ **Efficient Maintenance & Updates** – Microservices allow for rapid updates and easy adaptability to evolving business needs and user demands.

#### OPEA Microservices Architecture
OPEA deployments are built around three key components:

![VPC](/images/2/image025.png)

+ **Megaservice** – Acts as the orchestrator for all microservices, managing workflows and ensuring seamless interaction between components. This is essential for coordinating an end-to-end application with multiple moving parts. More details can be found in the OPEA documentation.

+ **Gateway** – Serves as the entry point for users, routing incoming requests to the appropriate microservices within the megaservice architecture. It ensures seamless connectivity between external users and internal components.

+ **Microservices** – These are the individual functional components of the application, handling tasks such as embeddings, retrieval, LLM processing, and vector database interactions.
Accessing the Microservices

Before you begin exploring, note that only the gateway and UI services are exposed externally. In this task, you will directly access each internal microservice for testing purposes, using the Nginx gateway to efficiently route requests to these internal services.

{{% notice info %}}
You'll need to take note of all pods deployed.
{{% /notice %}}

kubectl get svc lists all services in a Kubernetes cluster, showing their names, types, cluster IPs, and exposed ports. It provides an overview of how applications are exposed for internal or external access.

Run the following command on your CloudShell:

![VPC](/images/2/image026.png)

You will see output similar to this:

![VPC](/images/2/image027.png)

The kubectl get svc command is used to list the services running within a Kubernetes cluster. Services act as entry points that enable communication between different components of your application. Each service has a unique name (e.g., chatqna or chatqna-ui), which helps identify its role within the system.

Kubernetes services can be exposed in different ways:

+ **ClusterIP** – Only accessible within the cluster, allowing internal components to communicate securely.

+ **NodePort** – Exposes the service externally through a specific port on each node, making it accessible outside the cluster.
The Cluster-IP is the internal address used by other services to reach the application. If the service were accessible from outside the cluster, an External-IP would be displayed. However, in this case, these services are strictly internal.

The Ports column indicates which network ports the service listens on. For example:

+ chatqna might be running on port 8888/TCP, handling internal communication.

+ chatqna-nginx could be configured with 80:30144/TCP, where traffic from port 80 is forwarded to 30144 for routing purposes.
Lastly, the Age column displays how long the service has been running—for instance, 12 hours for all listed services in this scenario.

Now, let’s explore the architecture in detail.

#### Step 1 : Megaservice (Orchestrator) (POD:chatqna:8888)
The megaservice encapsulates the complete logic for the ChatQnA RAG application. This microservice is tasked with processing incoming requests and executing all the necessary internal operations to generate appropriate responses.

This service isn't directly exposed, but you can access it directly from the LoadBalancer, which forwards the request.

+ Look for the load balancer

![VPC](/images/2/image028.png)

+ Click on chatqna-Ingress

![VPC](/images/2/image029.png)

+ Note the DNS Name.As mentioned, it's the public URL that can be accessed externally.

![VPC](/images/2/image030.png)

You will use the curl command to send requests to the API endpoints, testing each microservice individually. The goal is to ask a question, such as "What was Nike's revenue in 2023?", and verify that the API responds correctly. This step ensures that all microservices in the system are functioning as expected.

![VPC](/images/2/image031.png)

If everything is working properly, you should receive a response, confirming that the Retrieval-Augmented Generation (RAG) workflow is operational.

However, you may notice that the model is unable to provide an accurate answer. This happens because it lacks the necessary context and relies on outdated information. Without access to current and relevant data, the model cannot generate precise responses.
In the next steps, you will enhance the system using RAG, allowing the model to retrieve up-to-date, contextually relevant information. This will ensure that it delivers more accurate and meaningful answers.

Now, let's explore each microservice in detail to understand its role and how it contributes to improving the model's ability to answer questions correctly.

#### Step 2 : Microservices
Each microservice follows the following logic performing a task within the RAG flow:

![VPC](/images/2/image032.png)

In the flow, you can observe the microservices and we can divide the RAG flow into two steps:

+ **Preprompting**: This step involves preparing the knowledge base (KB) by uploading relevant documents and ensuring that the information is organized for effective retrieval.

+ **Prompting**: This step focuses on retrieving the relevant data from the knowledge base and using it to generate an accurate answer to the user's question.

#### **Preprompting**

In this step, the logic is to start from a document (Nike's revenue PDF), and do the preprocessing needed to make it ready to be stored in a database. As shown, this process primarily involves 3 microservices: data preparation, embeddings and vector store. Let's explore each microservice

![VPC](/images/2/image033.png)

#### **Embedding Microservice (POD: chatqna-tei:80)**
An embedding is a numerical representation of an object—such as a word, phrase, or document—within a continuous vector space. In natural language processing (NLP), embeddings transform words, sentences, or text segments into vectors—sets of numbers that capture their meaning, relationships, and contextual significance. This transformation enables machine learning models to process and understand text more effectively.

For example, word embeddings represent words as points in a vector space, where words with similar meanings—like "king" and "queen"—are positioned closer together. The embedding model captures these relationships through vector arithmetic.

During training, if the model frequently encounters "king" in association with "man" and "queen" with "woman," it learns that "king" and "queen" share a similar relationship to "man" and "woman." This allows the model to position words in a way that reflects meaningful relationships, such as gender associations, in language.

![VPC](/images/2/image034.png)

Embeddings: A Key Component of RAG
Embeddings play a crucial role in Retrieval-Augmented Generation (RAG) by enhancing the model’s ability to process and retrieve relevant information. They provide several key advantages:

+ **Capturing Meaning** – Embeddings represent the semantic relationships between words, enabling RAG models to understand context, nuances, and deeper language structures. This improves their ability to generate relevant and coherent responses.

+ **Dimensionality Reduction** – By transforming complex textual data into fixed-size vectors, embeddings make data processing more efficient and scalable, improving the system's performance.

+ **Enhancing Model Performance** – By leveraging semantic similarities, embeddings enable more accurate information retrieval, refining the quality of generated responses and helping the model generalize better across various queries.

OPEA offers multiple options for running embedding microservices, as detailed in the OPEA embedding documentation. In this case, ChatQnA uses the Hugging Face TEI microservice, which runs the embedding model BAAI/bge-large-en-v1.5 locally.

Since some microservices are not exposed externally, you will use the Nginx pod to interact with them via curl. To do this, each microservice will be accessed using its internal DNS name.

1. Access to ngnix POD (copy your NGNIX entire pod name from kubectl get pods and REPLACE chatqna-nginx-xxxxxxxx on the below command)

![VPC](/images/2/image035.png)

Your command prompt should now indicate that you are inside the container, reflecting the change in environment:

![VPC](/images/2/image036.png)

Once inside, you will now have direct access to the internal pods.

2. Get the embedding from the Embeddings Microservice for the phrase "What was Deep Learning?":

![VPC](/images/2/image037.png)

The answer will be the vector representation of the phrase "What was Deep Learning?". This service returns the vector embedding for the inputs from the REST API.

![VPC](/images/2/image038.png)

### **Vector Database Microservice (POD: chatqna-redis-vector-db:80)**
The Vector Database microservice plays a critical role in the Retrieval-Augmented Generation (RAG) application by storing and retrieving embeddings. This is essential for applications like ChatQnA, where relevant information needs to be efficiently retrieved based on a user's query.

#### **Using Redis as a Vector Database**
In this task, Redis is used as the vector database. However, OPEA supports multiple alternatives, which can be found in the OPEA vector store repository.

A Vector Database (VDB) is specifically designed to store and manage high-dimensional vectors, which represent words, sentences, or images in numerical form. In AI and machine learning, these vectors—also known as embeddings—capture the meaning and relationships between data points, enabling efficient processing and retrieval.

#### **Data Preparation Microservice (POD: chatqna-data-prep:6007)**
The Data Preparation (Dataprep) Microservice is responsible for formatting and preprocessing data so that it can be converted into embeddings and stored in the vector database. This ensures that the data is clean, structured, and ready for efficient retrieval.

#### **Key Functions of the Data Preparation Microservice**
+ Receives raw data (e.g., documents or reports).

+ Processes and chunks the data into smaller segments.

+ Sends the processed data to the Embedding Microservice for vectorization.

+ Stores the resulting embeddings in the Vector Database.
Since different vector databases have unique data formatting requirements, the Dataprep Microservice ensures compatibility with the selected database.

#### **Testing the Microservices**
To verify the functionality of the system and help the model answer the initial question—
"What was Nike's revenue in 2023?"—you will need to upload a relevant context file (a revenue report) so it can be processed.

To do this, download a sample Nike revenue report to the Nginx pod using the command below. (If you are no longer logged into the Nginx pod, make sure to log in again before proceeding.)

Execute the following command to download a sample Nike revenue report  to the nginx pod (if you are no longer logged in to the NGinx pod, be sure to use the above command to log in again):

1. Download the document to the microservice :

![VPC](/images/2/image039.png)

2. Feed the knowledge base (Vectord) with the document (It will take ~30 seconds):

![VPC](/images/2/image040.png)

After running the previous command, you should receive a confirmation message like the one below. This command updated the knowledge base by uploading a local file for processing.

![VPC](/images/2/image041.png)

The data preparation microservice API can retrieve information about the list of files stored in the vector database.

3. Verify if the document was uploaded:

![VPC](/images/2/image042.png)

After running the previous command, you should receive the confirmation message.

![VPC](/images/2/image043.png)

Congratulations! You've successfully prepared your knowledge base. Now you'll explore the microservices involved in prompt handling.

#### Step 3: Prompting
Once the knowledge base is set up, you can begin interacting with the application by asking context-specific questions. Retrieval-Augmented Generation (RAG) ensures that responses are both accurate and grounded in relevant data.

The process begins with the application retrieving the most relevant information from the knowledge base in response to the user's query. This step ensures that the Large Language Model (LLM) has access to up-to-date and precise context to generate an informed response.

Next, the retrieved information is combined with the user’s input prompt and sent to the LLM. This enriched prompt enhances the model’s ability to provide answers that are not only based on its pre-trained knowledge but also supported by real-world, external data.

Finally, you will see how the LLM processes this enriched prompt to generate a coherent and contextually accurate response. By leveraging RAG, the application delivers highly relevant answers, grounded in the latest information from the knowledge base.

The microservices involved in this stage include:

   - Embeddings
   - Vector Database
   - Retriever
   - Re-ranking
   - LLM

![VPC](/images/2/image044.png)

#### **Retriever Microservice (POD: chatqna-retriever-usvc:7000)**
The Retriever Microservice is responsible for locating the most relevant information within the knowledge base and returning documents that closely match the user’s query. It interacts with various back-end systems that store knowledge and provide APIs for retrieving the best-matching data.

Different knowledge bases utilize different retrieval methods:

Vector databases use vector similarity matching between the user’s question and stored document embeddings.
Graph databases leverage graph locality to find related information.
Relational databases rely on string matching and regular expressions to locate relevant text.
In this task, you will use Redis as the vector database and retrieve information via the Redis retriever.

Since vector retrieval relies on embeddings, you first need to generate an embedding for the question:
"What was Nike's revenue in 2023?"

This will allow the retriever to search the knowledge base for the most relevant document—such as the Nike revenue report you uploaded in the previous step.

To create the embedding, use the chatqna-tei microservice. (Make sure you are logged into the Nginx pod before proceeding.)

1. Create the embedding and save locally (embed_question):

![VPC](/images/2/image045.png)

You should get the details about the writing task:

![VPC](/images/2/image046.png)

2. Check to see if your embedding was saved:

**echo $embed_question**

You should be able to see the vectors the embeddings microservice generated. You are now able to use the retriever microservice to get the most similar information from your knowledge base.

3. Get and save similar vectors from the initial embed_question locally similar_docs:

**similar_docs=$(curl chatqna-retriever-usvc:7000/v1/retrieval -X POST   -d "{\"text\":\"test\",\"embedding\":${embed_question}}"   -H 'Content-Type: application/json')**

By looking at the previous output, you can see the most similar passages (TOP_3) from the document Nike revenue report  and the question **"What was the Nike revenue in 2023?"**.

**echo $similar_docs**

{{% notice info %}}
The following output has been formatted for better readability. Your results will be presented in plain text and may vary slightly due to the similarity search algorithm. However, you can double check that the retrieved documents will be relevant to your initial query.
{{% /notice %}}

The application will use that information as context for prompting the LLM, but there is still one more step that you need to do to refine and check the quality of those retrieved documents: the reranker.

#### **Reranker Microservice (POD: chatqna-teirerank:80)**
The Reranking Microservice plays a crucial role in semantic search, leveraging reranking models to enhance the relevance of retrieved results. When given a user query and a set of documents, this microservice reorders the documents based on their semantic similarity to the query, ensuring that the most relevant results appear first.

Reranking is particularly valuable in text retrieval systems, where documents are initially retrieved using either:

+ Dense embeddings, which capture deep semantic meaning.
+ Sparse lexical search, which relies on keyword-based matching.

While these retrieval methods are effective, the reranking model refines the results by optimizing the order of retrieved documents. This step significantly improves accuracy, ensuring the final output is more relevant, precise, and contextually aligned with the user’s query.

![VPC](/images/2/image047.png)

OPEA has multiple options  for re-rankers. For this lab, you'll use the Hugging Face TEI for re-ranking. It is the chatqna-teirerank microservice in your cluster.

The reranker will use similar_docs from the previous stage and compare it with the question What was Nike Revenue in 2023? to check the quality of the retrieved documents.

Extract the 3 retrieved text snippets and save them in a new variable to be reranked:

1. Install jq dependencies to format similar_docs

**echo -e "deb http://deb.debian.org/debian bookworm main contrib non-free\ndeb http://security.debian.org/debian-security bookworm-security main contrib non-free\ndeb http://deb.debian.org/debian bookworm-updates main contrib non-free" > /etc/apt/sources.list && apt update && apt install -y jq**

2. Extract and format the texts into a valid JSON array of strings

**texts=$(echo "$similar_docs" | jq -r '[.retrieved_docs[].text | @json]')**

3. Send the request to the microservice with the query and the formatted texts:

**curl -X POST chatqna-teirerank:80/rerank \
  -d "{\"query\":\"What was Nike Revenue in 2023?\", \"texts\": $texts}" \
  -H 'Content-Type: application/json'**

**Response:**

{{% notice info %}}
The following output has been formatted for better readability. Your results are displayed in plain text and may vary slightly due to the similarity search algorithm. The retrieved documents are ranked by similarity to your query, with the highest-ranked index representing the most relevant match. You can confirm that the top-ranked document corresponds to the one most closely aligned with your query.
{{% /notice %}}

![VPC](/images/2/image048.png)

The server responds with a JSON array containing objects with two fields: index and score. This indicates how the snippets are ranked based on their relevance to the query: {"index":2,"score":0.9972289} means the first text (index 0) has a high relevance score of approximately 0.7982. {"index":0,"score":0.9776342},{"index":3,"score":0.9296986},{"index":1,"score":0.84730965} indicates that the other snippets (index 3,1 and 2) have a much lower score.

As you can see from similar_doc the id=2 has the below information where it EXACTLY refers to the revenue for 2023!

![VPC](/images/2/image049.png)

Just the first will be used to prompt the LLM.

#### **LLM Microservice (POD: chatqna-tgi:80)**
At the core of the RAG (Retrieval-Augmented Generation) application lies the Large Language Model (LLM), which plays a pivotal role in generating responses. By leveraging RAG, the system enhances the LLM’s performance, ensuring responses are accurate, relevant, and context-aware.

Types of LLMs
LLMs generally fall into two main categories, each with its own strengths and trade-offs:

+ Closed-Source Models
These proprietary models are developed by major tech companies such as Amazon Web Services (AWS), OpenAI, and Google. They are trained on extensive datasets and optimized for high-quality, reliable outputs. However, they come with certain limitations:

   - Limited Customization: Users have minimal control over fine-tuning.
   - Higher Costs: Access is usually metered and can be expensive.
   - Data Sovereignty Concerns: API access may restrict usage in applications requiring strict data governance.

+ Open-Source Models
Freely available for use and modification, open-source LLMs offer greater flexibility and control. They allow users to customize and fine-tune models according to specific needs. Running open-source models locally or on private cloud infrastructure ensures better data privacy and cost efficiency. However, they require:

   - Technical Expertise: Deploying and optimizing open-source models can be complex.
   - Computational Resources: Achieving comparable performance to closed models often demands powerful hardware.
Flexible Integration with OPEA: This microservice architecture supports both closed and open-source models, providing the flexibility to choose the best fit for your application. In this example, the TGI (Text Generation Inference) model from Hugging Face is used.

Testing the LLM Microservice: To verify its functionality, you can directly prompt the TGI LLM with a sample question:
"What was Nike's revenue in 2023?"
This test will demonstrate how well the model can retrieve and generate an informed response based on the loaded knowledge base.

1. Directly prompt the TGI(LLM) Microservice:

![VPC](/images/2/image050.png)

The model will give you the answer to the prompt like the following:

**"generated_text":" Nike revenue in 2023 has not been reported as it is still in the fourth quarter. The previous full financial year—which is 2022—brought in $48.9 billion in revenue for the American multinational sportswear company. They deal with the design, development, manufacturing, and worldwide marketing/sales of a diverse portfolio of products. From coming into being in 1964 as Blue Ribbon Sports, the firm was renamed Nike, Inc., in 1978. Jumpman logos (for example), include Michael Jordan, who is a former professional basketball player—are among the brands' numerous trademarked symbols, tied to the 'Swoosh' logo.\n\nNike revenues are clearly affected by the football World Cup. Consequently, for the 13 weeks ending January 29 in 2022, which were characterized by the football world cup**

Điều này trực tiếp nhắc LLM mà không cung cấp ngữ cảnh. Chúng ta có thể thấy rằng mô hình thực sự đưa ra câu trả lời sai. Để kiểm tra hiệu suất RAG tổng thể, chúng ta nên thử nghiệm với megaservice như chúng ta đã làm khi bắt đầu nhiệm vụ này, điều này sẽ liên quan đến toàn bộ luồng.

2. Thoát khỏi POD ngnix: Bạn có thể tìm thấy "công việc đang chờ xử lý", vui lòng bỏ qua và thử lại.

**root@chatqna-nginx-deployment-XXXXXXXXXXXX:/# exit**

Sử dụng URL bộ cân bằng tải mà bạn đã lưu ở trên trong lệnh bên dưới để gửi câu hỏi "Doanh thu của Nike năm 2023 là bao nhiêu?" đến ứng dụng ChatQNA.

3. Chạy lại curl đến bộ cân bằng tải:

![VPC](/images/2/image051.png)

4. Xem lại kết quả của bạn và bạn sẽ thấy rằng phản hồi được truyền phát. Đây là hành vi mong đợi của dịch vụ vi mô, vì nó cung cấp câu trả lời theo từng phần nhỏ hơn thay vì tất cả cùng một lúc. Streaming cho phép dữ liệu được xử lý và hiển thị theo từng bước khi dữ liệu có sẵn. Trong ứng dụng, UI sẽ ghi lại phản hồi này và định dạng thành màn hình có thể đọc được, cho phép người dùng xem thông tin theo thời gian thực khi dữ liệu đến.