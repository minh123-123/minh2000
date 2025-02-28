---
title : "Verify OPEA Chat QnA with Inferenece API"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 7.2 </b> "
---
**Test application**

You can check the deployment by accessing to the DNS url of the load balancer the cloud formation templated created.

1. Look for the load balancer:

![VPC](/images/5.fwd/image122.png)

2. Copy your DNS name for bedrock-ingress:

![VPC](/images/5.fwd/image123.png)

3. Paste it on a new browser tab to access to the interface

In the UI you can see the chatbot to interact with it

![VPC](/images/5.fwd/image124.png)

4. Check if the model is able to give us an answer about OPEA:

![VPC](/images/5.fwd/image125.png)

![VPC](/images/5.fwd/image126.png)

You may notice that the chatbot’s initial response is outdated or lacks specific details about OPEA. This is because OPEA is a relatively new project and was not included in the dataset used to train the language model. Since most LLMs (Large Language Models) are static, they rely solely on pre-existing training data and cannot automatically incorporate new developments or emerging technologies like OPEA.

5. Uploading Context to Improve Accuracy

To address this limitation, RAG (Retrieval-Augmented Generation) enables real-time context retrieval. The ChatQnA UI includes an upload icon, allowing you to add relevant context.

**How It Works:**

    1. When you upload a document or link, it is sent to the DataPrep microservice.

    2. DataPrep processes the content and generates embeddings.

    3. The processed data is then stored in the Vector Database for retrieval.

By uploading updated documents or links, you expand the chatbot’s knowledge base, ensuring it provides more relevant, accurate, and up-to-date responses.

![VPC](/images/5.fwd/image127.png)

The deployment allows you to upload either a file or a site. For this case, use the OPEA site:

+ Click on the upload icon to open the right panel

+ Click on Paste Link

+ Copy/paste the text https://opea-project.github.io/latest/introduction/index.html to the entry box

+ Click Confirm to start the indexing process
When the indexing completes, you'll see an icon added below the text box, labeled https://opea-project.github.io/latest/introduction/index.html 


6. Ask the application after the context is provided:

Ask "What is OPEA?" again to see the updated answer.

![VPC](/images/2.prerequisite/image129.png)

This time, the chat bot responds correctly based on the data it added to the prompt from the new source, the OPEA web site.

**Conclusion**

In this task, you successfully deployed a RAG-powered chatbot using Amazon Bedrock. By uploading relevant context, you enabled the model to dynamically update and refine its responses based on new information. This process demonstrated how RAG integration enhances real-time adaptability, allowing the system to continuously improve its accuracy and relevance while leveraging the power of Amazon Bedrock.
