---
title : "Integrate Inference API (Denvr Cloud and Intel Gaudi AI Accelerator)"
date : "2025-02-21"
weight : 1
chapter : false
pre : " <b> 6.1. </b> "
---
### When Should You Use Managed Inference APIs?

As enterprise-level RAG (Retrieval-Augmented Generation) deployments grow in complexity, transitioning certain components to managed services becomes increasingly beneficial. This approach minimizes deployment challenges, streamlines infrastructure management, and ensures seamless auto-scaling. Moreover, it provides developers with a more efficient and accessible way to integrate Large Language Models (LLMs).

### How Was the Denvr Cloud Inference API Enabled?

The OPEA LLM and Embedding microservices run on Intel Gaudi2 AI Accelerators, hosted by Denvr Cloud. These APIs are secured using OPEA authentication services and optimized with load balancing for peak performance. While the initial implementation was built by OPEA, Denvr Cloud further refined and enhanced the system to meet specific performance and scalability requirements.

Refer the architecture for deployment information:

![VPC](/images/5.fwd/image101.png)

### How Does the OPEA Architecture Evolve?

OPEA’s modular design allows for seamless component interchangeability, enabling most elements from the default ChatQnA example (Module 1) to be integrated effortlessly into new deployments. The updated architecture remains largely similar to the original ChatQnA setup without guardrails but includes an additional component:

**chatqna-llm-uservice**
The chatqna-llm-uservice acts as a wrapper service for the Inference API, supporting vLLM and OpenAI-compatible endpoints. This service facilitates connections to the Inference APIs and ensures proper configuration.

### Deploying ChatQnA with the Inference API

For this lab, we've introduced a changeset that enables full parallel deployment of the ChatQnA example within the same Kubernetes cluster you've been using. Running the following command will deploy pods in the "remote-inference" namespace, mirroring the original ChatQnA setup but leveraging LLM remote inference models instead of TGI.

1. **Accessing the Inference APIs**: To use the Inference APIs, you’ll need API tokens provided by Denvr Cloud, available exclusively for private preview during the workshop. To request access, please contact Denvr Cloud at vaishali@denvrdata.com.

2. **Deploying ChatQnA with Denvr Cloud Inference API (Meta Llama 70B 3.1 Instruct)**: You can now deploy ChatQnA using Meta-Llama-3.1-70B-Instruct Inference API into your EKS Cluster. These APIs are pre-deployed on Denvr Cloud with Intel Gaudi2 accelerators and OPEA LLM microservices, ensuring optimal performance and scalability.

**Important Note:**
Remember to replace DenvrClientID and DenvrClientSecret with the credentials you previously received from Denvr Cloud.

{{% notice info %}}
The manifest for ChatQnA-Remote Inference can be found in the ChatQnA GenAIExamples repository , and the instructions for deploying it manually can be found here . The instructions here use AWS CloudFormation templates created by the AWS Marketplace EKS package.
{{% /notice %}}

Verify the new services are created

![VPC](/images/5.fwd/image102.png)

![VPC](/images/5.fwd/image103.png)

Test the Chat QnA on the console

Access to ngnix POD (copy your NGNIX pod name from kubectl get pods -n remote-inference and REPLACE chatqna-nginx-xxxxxxxx on the below command)

![VPC](/images/5.fwd/image104.png)

Your command prompt should now indicate that you are inside the container, reflecting the change in environment:

![VPC](/images/5.fwd/image105.png)

Get the "What is Deep Learning? Explain in 20 words"*:

![VPC](/images/5.fwd/image106.png)

![VPC](/images/5.fwd/image107.png)

Verify the deployment is done verifying the new load balancer on your managment console

![VPC](/images/5.fwd/image108.png)

![VPC](/images/5.fwd/image109.png)