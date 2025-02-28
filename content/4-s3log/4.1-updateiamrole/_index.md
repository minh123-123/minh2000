  ---
title : "Set Bedrock integration"
date : "2025-02-21"
weight : 1
chapter : false
pre : " <b> 7.1 </b> "
---
**What is Amazon Bedrock?**
Amazon Bedrock is a fully managed service that provides access to a diverse selection of high-performance foundation models (FMs) from industry leaders such as AI21 Labs, Anthropic, Cohere, Meta, Mistral AI, Stability AI, and Amazon itself. Through a single API, Bedrock enables developers to build generative AI applications while ensuring security, privacy, and responsible AI practices.

Developers can interact with Amazon Bedrock via the AWS Software Development Kit (SDK) or the AWS Command Line Interface (CLI). Bedrock also offers native features that allow users to create RAG (Retrieval-Augmented Generation) knowledge bases, agentic workflows, and guardrails. Integrating Bedrock with OPEA expands access to a broader selection of foundation models while leveraging Bedrock’s advanced capabilities alongside OPEA.

**How Does the Architecture Change?**

Future integrations with OPEA will unlock the full potential of Amazon Bedrock’s capabilities, including Titan Embedding models. However, for this module, the focus is exclusively on LLMs.

Thanks to OPEA’s modular and interchangeable architecture, most components from the default ChatQnA setup (Module 1) remain unchanged. The TGI service (Hugging Face) is now replaced by a Bedrock container, which seamlessly integrates into the existing deployment.

**Updated Architecture Component:**

+ chatqna-bedrock
  - In this deployment, when a user sends a message through the ChatQnA UI, it is routed to the backend Bedrock container, which communicates with Amazon Bedrock to retrieve and return responses.

This integration maintains the ChatQnA architecture while enhancing it with Amazon Bedrock’s powerful LLM capabilities, ensuring scalability, efficiency, and seamless deployment.

{{% notice info %}}
We've used the bedrock Kubernetes namespace to separate out the pods and services pertaining to the Bedrock deployment. When you use kubectl and other Kubernetes commands in the below examples, be sure to qualify the command with -n bedrock.
{{% /notice %}}

**Deploying ChatQnA Using Amazon Bedrock LLMs**

For this lab, we've created a changeset with the full parallel deployment of the ChatQnA example in the same Kubernetes cluster you've been using. The following command will deploy pods to the cluster within the "bedrock" namespace that are identical to the original ChatQnA pods, except with Bedrock models instead of TGI.

aws cloudformation execute-change-set --change-set-name bedrock-change-set --stack-name OpeaBedrockStack

**Activating the Model**

This module works with just about any text-generation LLM supported by Bedrock, but for the purposes of this lab we've used the Anthropic Claude Haiku 3 model. So while you're waiting for the change set to deploy, let's go activate our model in the Bedrock console:

1. Switch to the us-west-2 region, you could test on other regions but usually us-west has more availabity:

![VPC](/images/5.fwd/image116.png)

2. Go to Amazon Bedrock:

![VPC](/images/5.fwd/image117.png)

3. Go to the model access tab:

![VPC](/images/5.fwd/image118.png)

4. At the top of the screen, click on the button that says Modify Model Access

![VPC](/images/5.fwd/image119.png)

5. Select Claude 3 Haiku

![VPC](/images/5.fwd/image120.png)

{{% notice info %}}
It may take a minute or two for the access to be granted, but don't worry it won't take much longer than that.
{{% /notice %}}

6. Once you've confirmed that model access has been granted, switch back to the us-east-2 region where your EKS cluster is located.

**Confirming Deployment**

Now let's confirm that our Bedrock deployment is complete. You can onitor the state of the Bedrock pods using the kubectl command:

...to get output like this:

![VPC](/images/5.fwd/image121.png)

{{% notice info %}}
It can take several minutes for Bedrock to fully initialize and be available. Only continue when you see the chatqna-bedrock-deployment pod in the Running state.
{{% /notice %}}

You are now able to use Amazon Bedrock in your environment.