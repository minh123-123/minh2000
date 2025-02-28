---
title : "Verify OPEA Chat QnA with Inferenece API"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 6.2. </b> "
---
### Testing with the ChatQnA UI
Now that all services are up and running, it's time to explore the ChatQnA user interface.

**Accessing the UI**

To open the UI, launch any web browser and navigate to the DNS URL of the ChatQnA Load Balancer:
👉 http://denvr-ingress-xxxxxxx.us-east-2.elb.amazonaws.com
(Replace this with your actual denvr-ingress DNS URL.)

**Interacting with the Chatbot**

Once inside the UI, you can interact with the chatbot and test its responses in real time.

![VPC](/images/5.fwd/image110.png)

To verify the UI, go ahead and ask

![VPC](/images/5.fwd/image111.png)

![VPC](/images/5.fwd/image112.png)

**Enhancing Chatbot Responses with Updated Context**

You may notice that the chatbot’s initial response is outdated, generic, or lacks specific details about Denvr Cloud. This occurs because Denvr Cloud was not included in the dataset used to train the language model. Since most language models are static, they rely only on the information available at the time of training.

**Adding Context Through the UI**
To address this limitation, the UI includes an upload icon that allows you to provide relevant context. When you upload a document or link, the following process is triggered:

1. The document is sent to the DataPrep microservice, where embeddings are generated.
2. The processed data is then ingested into the Vector Database.

By uploading new information, you effectively expand the chatbot’s knowledge base, ensuring its responses are more accurate, relevant, and up to date.

![VPC](/images/5.fwd/image113.png)

**Uploading a File or Website for Enhanced Responses**

The deployment allows you to upload either a file or a website to improve the chatbot’s contextual knowledge. For this example, use the Denvr Datawork’s site by following these steps:

1. Click the upload icon to open the right panel.
2. Select "Paste Link."
3. Copy and paste the following URL into the entry box:
👉 https://www.denvrdata.com/intel
4. Click Confirm to initiate the indexing process.
5. Once indexing is complete, you will see an icon labeled https://www.denvrdata.com/intel appear below the text box, indicating that the information has been successfully added.

![VPC](/images/5.fwd/image114.png)

Ask "What is Denvr?" again to see the updated answer.

![VPC](/images/5.fwd/image115.png)

This time, the chatbot responds correctly based on the data it added to the prompt from the new source, the Denvr Cloud website.

**Conclusion**

In this workshop, participants gained hands-on experience in integrating managed Inference APIs. The session demonstrated that OPEA is a highly flexible framework, capable of deploying pipelines across multiple cloud providers, including AWS and Denvr Cloud. Additionally, it showcased how OPEA microservices can be leveraged to deploy LLMs on Intel Gaudi2 AI Accelerators, ensuring efficient and scalable AI model deployment.