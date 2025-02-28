---
title : "Deploy ChatQnA"
date : "2025-02-21"
weight : 1
chapter : false
pre : " <b> 3.1. </b> "
---
### Introduction
In this section, you will deploy the OPEA blueprint for a RAG-based application, **ChatQnA**, on an Amazon Elastic Kubernetes Service (EKS) environment. This hands-on exploration will enhance your understanding of how the RAG application functions within a managed Kubernetes ecosystem, allowing you to analyze its components and their roles in the system.

The deployment configuration is available through the **AWS Marketplace**, and ChatQnA can be found in OPEA's **GenAIExamples** repository.

[ChatQnA on GitHub](https://github.com/opea-project/GenAIExamples/tree/main/ChatQnA)

Since you have already set up access to your Kubernetes cluster in the "Getting Set Up" section, you will now dive deeper into your deployed environment to explore and learn more about its structure and functionality.

#### Step 1: Deploy the ChatQnA CloudFormation Template
Open AWS CloudShell and deploy the ChatQnA CloudFormation template into your Amazon EKS cluster. This will initiate the deployment of the RAG application within your managed Kubernetes environment.
![VPC](/images/2/image020.png)

#### Step 2: Explore Cluster Resources
Navigate to the AWS Management Console and select your assigned EKS cluster to review its deployment. Each cluster includes critical configurations such as the Kubernetes version, networking setup, and logging options. Examining these settings will provide a deeper understanding of your application’s infrastructure, helping with efficient management and troubleshooting when necessary.

Reviewing Cluster Resources
Click on the Resources tab to view all applications currently running within your cluster, including ChatQnA and its associated microservices. 
![VPC](/images/2/image021.png)
![VPC](/images/2/image022.png)
Ensure that all microservices from the OPEA ChatQnA blueprint are installed correctly by listing the active pods:
![VPC](/images/2/image023.png)
The output should show all pods in a Running state (1/1), confirming that the application has been successfully deployed. At this point, you are ready to further explore the deployment and manage your resources within the cluster.
![VPC](/images/2/image024.png)