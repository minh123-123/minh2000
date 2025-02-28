---
title : "Using Workshop Studio"
date : "2025-02-21"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

If you're participating in these labs as part of an AWS-led event in Workshop Studio, there's no need to manually provision any resources—everything required for all modules will be pre-provisioned for you.

What will be set up in your AWS account?
The stack will automatically configure the following components:

EKS Cluster:
Creates a new EKS cluster named opea-eks-cluster.
Deploys a node within the cluster using a M7i.24xlarge instance for high-performance computing.
CloudFormation Templates:
The stack also generates CloudFormation templates for each module:

Module 1: ChatQnA Default

Module 2: ChatQnA with Guardrails

Module 3: ChatQnA with OpenSearch (open-source) as the vector database

Module 4: ChatQnA with Bedrock as the LLM

Module 5: ChatQnA with Remote Inference (Denvr) as the LLM

With these pre-configured resources, you can focus entirely on exploring and building your Generative AI applications without worrying about infrastructure setup.

Step 1: Configure Access to Your EKS Cluster
To interact with your EKS cluster using kubectl, you need to configure your local environment to recognize the cluster. This is done by updating the kubeconfig file, which stores authentication details and access configurations for your Kubernetes cluster.
1. Log in to the AWS Management Console:
Start by signing into your AWS Management Console.
![VPC](/images/2/image001.png)

2. Open Cloud Shell or Set Up Your Local Environment:
In the console, click the Cloud Shell icon to launch a preconfigured terminal.
Alternatively, if you prefer to use your own AWS CLI, ensure you have both the AWS CLI Client and kubectl installed on your local machine.

![VPC](/images/2/image002.png)

3. Update Your kubeconfig:
![VPC](/images/2/image004.png)
You should receive an output confirming your conf file was updated:
![VPC](/images/2/image005.png)
You are now ready to interact with the Kubernetes cluster using kubectl
Step 2: Verify Cluster Access
After updating your kubeconfig, verify that you can successfully connect to the cluster by listing the nodes:
![VPC](/images/2/image006.png)
If the command executes successfully, you should see an output displaying the nodes associated with your cluster.
![VPC](/images/2/image007.png)
You are now ready to explore the module of your choice and begin deploying workloads on your EKS cluster!
