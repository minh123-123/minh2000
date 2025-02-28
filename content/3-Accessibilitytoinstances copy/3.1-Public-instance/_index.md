---
title : "Deploy Guardrails"
date : "2025-02-21"
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---
### Why Are Guardrails Necessary?
As AI becomes increasingly embedded in applications, ensuring safe, ethical, and reliable outcomes is essential. Guardrails in AI systems—particularly those that interact with users or make autonomous decisions—help regulate responses, prevent unintended behavior, and align outputs with predefined standards. Without guardrails, AI models may generate biased or unsafe content, make inappropriate decisions, or mishandle sensitive data.

Implementing guardrails enables you to:

+ **Enhance User Safety** – By filtering responses, you minimize the risk of generating harmful or inappropriate content.

+ **Ensure Regulatory Compliance** – As privacy and security regulations evolve, guardrails help maintain compliance with legal and ethical standards.

+ **Improve Accuracy and Reliability** – Guardrails refine AI outputs, reducing error rates and ensuring more consistent and trustworthy performance.

By integrating guardrails, you can harness the power of AI while mitigating risks, creating a more secure and responsible user experience.

### How Does the OPEA Architecture Evolve?
With OPEA’s flexible architecture, most core components from the default ChatQnA module (Module 1) remain unchanged. However, the introduction of guardrails requires adding two seamlessly integrated components to the deployment:

1. chatqna-tgi-guardrails – This microservice runs a TGI server using the meta-llama/Meta-Llama-Guard-2-8B model, which acts as a real-time safety filter, ensuring that all queries comply with defined security protocols.

2. chatqna-guardrails-usvc – This microservice functions as the Operational Endpoint Analyzer (OPEA), evaluating user queries and identifying any that request potentially unsafe content.

### Deploying ChatQnA guardrails

Kubernetes enables the isolation of development environments through the use of multiple namespaces. Since the pods for the ChatQnA pipeline are currently deployed in the default namespace, you will deploy the guardrails in a separate namespace guardrails, to avoid any interruptions.

If you have been logged out of your CloudShell, click in the CloudShell window to restart the shell, or click the icon in the AWS Console to open a new CloudShell

1. Go to your CloudShell and deploy the ChatQnA-Guardrails ClourFormation template into your EKS Cluster

![VPC](/images/4.s3/image060.png)

{{% notice info %}}
The manifest for ChatQnA-Guardrails can be found in the ChatQnA GenAIExamples repository , and the instructions for deploying it manually can be found here . The instructions you're using in this workshop use AWS CloudFormation templates created by the AWS Marketplace EKS package.
{{% /notice %}}

2. Verify the new namespace was created

![VPC](/images/4.s3/image061.png)

You will see the guardrails namespace

![VPC](/images/4.s3/image062.png)

3. Check pods on the guardrails namespace. It will take a few minutes for the models to download and for all the services to be up and running.
Run the following command to check if all the services are running:

![VPC](/images/4.s3/image063.png)

![VPC](/images/4.s3/image064.png)

Wait until the output shows that all the services including chatqna-tgi-guardrails and chatqna-guardrails-usvc are running (1/1).

4. Verify the deployment is done verifying the new load balancer on your managment console

{{% notice info %}}
Wait 5 minutes for the load balancer to be created.
{{% /notice %}}

![VPC](/images/4.s3/image065.png)

![VPC](/images/4.s3/image066.png)

By checking that both the load balancer and pods are running, we can confirm that the deployment is ready and start testing the behavior.