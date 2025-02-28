---
title : "Test the deployment and verify the RAG workflow"
date : "2025-02-21"
weight : 3
chapter : false
pre : " <b> 7.3 </b> "
---
**Explore RAG and Interact with the UI**

Now that all services are up and running, let's explore the UI provided by the implementation.

To access the ChatQnA Bedrock UI, open a web browser and navigate to the DNS of the ChatQnA Bedrock Load Balancer:

👉 http://bedrock-ingress-xxxxxxx.us-east-2.elb.amazonaws.com (Replace with your actual Bedrock Ingress DNS URL).

Once inside the UI, you can interact with the chatbot, test its responses, and experience how it processes queries using RAG-powered retrieval.

![VPC](/images/5.fwd/image124.png)

Now when you send a prompt to the chatbot, the response will be coming from Anthropic's Claude Haiku through Amazon Bedrock.