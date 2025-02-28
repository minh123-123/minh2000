---
title : "Test the deployment and verify RAG workflow"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 3.3. </b> "
---
### Understand RAG and use the UI
Now that you've verified all services are running, let’s take a look at the UI provided by the implementation.

To access the UI, open any browser and go to the DNS of the ChatQnA Load Balancer: http://chatqna-ingress-xxxxxxx.us-east-2.elb.amazonaws.com  (Modify with your chatqna-ingressDNS URL)

In the UI you can see the chatbot interact with it

![VPC](/images/2/image052.png)

To verify the UI, go ahead and ask

![VPC](/images/2/image053.png)

![VPC](/images/2/image054.png)

The answer is correct again because we already indexed our knowledge base on the previous step.

Let's try something different. Will the app be able to answer about OPEA:

![VPC](/images/2/image055.png)

You may notice that the chatbot's initial response is outdated or lacks specific details about OPEA. This is because OPEA is a relatively new project and was not included in the dataset used to train the language model. Since most language models are static—meaning they rely on the data available at the time of training—they cannot automatically incorporate recent developments or newly emerging topics like OPEA.

However, RAG provides a solution by enabling real-time context retrieval. Within the UI, you'll find an option to upload relevant contextual information. When you do this, the document is sent to the DataPrep microservice, where it is converted into embeddings and stored in the Vector Database.

By uploading a document or a link, you effectively expand the chatbot’s knowledge base with the latest information, improving the relevance and accuracy of its responses.

![VPC](/images/4.s3/image057.png)

The deployment allows you to upload either a file or a site. For this case, use the OPEA site:

+ Click on the upload icon to open the right panel
+ Click on Paste Link
+ Copy/paste the text https://opea-project.github.io/latest/introduction/index.html to the entry box
+ Click Confirm to start the indexing process

When the indexing completes, you'll see an icon added below the text box, labeled https://opea-project.github.io/latest/introduction/index.html 

![VPC](/images/4.s3/image058.png)

Ask **"What is OPEA?"** again to see the updated answer.

![VPC](/images/4.s3/image059.png)

This time, the chatbot responds correctly based on the data it added to the prompt from the new source, the OPEA website.

**Conclusion**

In this task, you explored the core structure of a RAG application, gaining insight into how each component functions and interacts within the system. From retrieving relevant information to generating accurate responses, every part plays a vital role in OPEA’s RAG workflow—enhancing response relevance through retrieval while improving accuracy with advanced language modeling. This hands-on session provided a clear understanding of how OPEA leverages RAG to process complex queries efficiently and refine model performance through seamless component integration.

In the next task, you will implement guardrails for the chatbot. These guardrails are essential for detecting and mitigating biases, ensuring that AI-generated responses remain responsible, fair, and aligned with ethical AI principles.