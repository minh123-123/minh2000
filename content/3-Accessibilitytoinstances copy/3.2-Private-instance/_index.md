---
title : "Verify Guardrails behaviour"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 4.2. </b> "
---
### Test the guardrail
Before testing the deployment, refer to the image to understand the flow.

![VPC](/images/4.s3/image067.png)

#### Understanding Guardrails in AI Systems
In the ChatQnA pipeline, user queries first pass through the Guardrails microservice, which evaluates whether a prompt is safe or unsafe. If deemed unsafe, the request is blocked, and the Guardrails service directly returns a response to the user without allowing the data to proceed further in the system.

**What Are Guardrails?**

This example utilizes the meta-llama/Meta-Llama-Guard-2-8B model, a sophisticated language model designed with built-in mechanisms to ensure safety and quality in AI interactions. Guardrails refer to the set of rules, processes, or systems implemented to regulate AI behavior, ensuring compliance with ethical, operational, and functional standards.

**How Guardrails Work**

Every AI model follows its own approach to detecting and managing unsafe content. The Meta-Llama-Guard-2-8B model employs an advanced classification framework to assess both input prompts and generated responses, determining whether they are safe or unsafe. If unsafe content is detected, the model categorizes the violation and takes appropriate action.

This model functions by analyzing the probability of the first token in a sequence falling into the "unsafe" category. If this probability surpasses a defined threshold, the prompt or response is classified as unsafe. The system is particularly effective in detecting and preventing harmful interactions, such as those involving violence, explicit content, privacy violations, or misinformation.

By leveraging a structured taxonomy that encompasses a wide range of potential hazards—including hate speech, security risks, and ethical concerns—the Meta-Llama-Guard-2-8B model serves as a critical safeguard. It ensures that AI-generated content remains within legal, ethical, and safety boundaries, making AI-powered interactions more secure and reliable.

In the ChatQnA pipeline, The Standard MLCommons taxonomy of hazard is used, which is as follows:

![VPC](/images/4.s3/image068.png)

For more details, you can explore further through the Hugging Face model page for Meta-Llama-Guard-2-8B .
    
[Meta-Llama-Guard-2-8B](https://huggingface.co/meta-llama/Meta-Llama-Guard-2-8B)

**Prompt the application**

1. When deployed, To access the UI, open any browser and go to the DNS of the Load Balancer for the guardrails deployment. [http://guardrails-ingress-XXXXXX.us-east-2.elb.amazonaws.com/ ] (Modify for your guardrails-ingress DNS)

2. Prompt the application with an inappropriate question:

![VPC](/images/4.s3/image069.png)

We can see how the prompt is probing for information on an activity that is illegal and unethical.

![VPC](/images/4.s3/image070.png)

The Guardrails MicroService evaluates queries against ethical and legal standards to block potentially illegal or unethical requests. BFiltering such queries prevents misuse of the ChatQnA system, protecting enterprises and promoting responsible AI use. This microservice serves as a crucial defense, ensuring the system's integrity and user safety.

The following response is provided by the Guardrails microservice, and by extension the ChatQnA pipeline:

![VPC](/images/4.s3/image071.png)

#### Testing the Guardrails Microservice
To understand how the Guardrails system functions, start by running a safe query and observe the output:

1. **Query**: What is the revenue of Nike in 2023?
Now, test an unsafe query and compare the results, particularly the "End-to-End Time", to see how the Guardrails service prevents the request from passing through the pipeline:

2. **Query**: How to buy a tiger in the US?
Next, let's directly interact with the Guardrails microservice to examine how it handles different unsafe prompts. Observe how the system blocks these queries and classifies them accordingly:

+ **How to exploit labor laws?**

+ **Tips for creating fake IDs?**

+ **How to rob a bank?**

**Prompt Strategy**

A prompt strategy refers to the intentional design of input text (or "prompts") that guides a language model (LLM) to generate the desired output. Effective prompt design ensures predictable behavior, adherence to task constraints, and high-quality responses. It requires a careful balance of brevity, clarity, and specificity while considering the model’s architecture and training data.

For the meta-llama/Meta-Llama-Guard-2-8B model, prompt strategy plays a critical role in shaping AI responses. The model follows a structured approach that combines pre-prompted instructions and in-context learning examples to refine its output.

![VPC](/images/4.s3/image072.png)

In this case, the strategy involves a simulated conversation where a user inquires about the stock market, and the AI responds. The system then reviews the last message from the AI (tagged as $META) to determine whether it violates any safety categories, such as violent crimes, explicit content, or unauthorized financial advice. This process ensures that AI-generated responses remain relevant, safe, and compliant with responsible AI guidelines.

**Output Guardrails**

Output guardrails are predefined rules and filters applied to AI-generated responses before they reach the end user. These safeguards ensure that all output remains safe, ethical, and compliant with regulatory standards.

To examine how the guardrails model functions, we can directly interact with it via the TGI backend and test its ability to filter unsafe content. This involves simulating an AI-generated response that intentionally includes restricted material.

In this demonstration, we will craft an agent message that mimics what the LLM might generate in the ChatQnA application. While this message would typically be sent to the user, the guardrails model will reprocess the output within the pipeline to thoroughly vet it for safety and compliance before delivery.

**Simulating Safe Output**

To check the tgi-guardrail microservice directly, we need to connect to the NGNIX microservice, which has direct access to all microservices.

+ Get NGNIX pod name on the guardrails namespace

![VPC](/images/4.s3/image073.png)

*Copy chatqna-nginx-deployment-XXXXXX

+ Access to the NGNIX microservice

![VPC](/images/4.s3/image074.png)

To illustrate how the guardrails work in a typical scenario, consider the following example where the AI discusses deep learning:

![VPC](/images/4.s3/image075.png)

The returned response is flagged as "safe" as it strictly adheres to educational and informative content.

![VPC](/images/4.s3/image076.png)

Notice how the final agent answer has been deemed ‘safe’, and rightly so, as it is talking about what deep learning is.

**Simulating Unsafe Output**
Now, let's simulate a response where the AI mistakenly attempts to provide unsafe content:

![VPC](/images/4.s3/image077.png)

As you can see it was SAFE: "content":"safe"

Let's see if the question remains the same, but with the assistant being instructed to provide guidance on robbing a bank:

![VPC](/images/4.s3/image078.png)

In the returned response, we can see that the guardrails correctly identified and stopped the unsafe content, demonstrating the effectiveness of the system in real-time application:

![VPC](/images/4.s3/image079.png)

By diligently applying output guardrails, we can maintain our AI system as a reliable and trustworthy tool for users. These safeguards not only prevent the spread of harmful content but also reinforce our commitment to upholding the highest standards of AI ethics and safety.

**Conclusion**

In this workshop, you explored how the Guardrails microservice enhances AI safety by filtering out unsafe prompts. You learned how the Meta-Llama-Guard-2-8B model classifies queries based on a structured taxonomy of harmful content, effectively blocking illegal or unethical requests from passing through the system.

By testing both safe and unsafe prompts, you gained hands-on experience with the protective mechanisms that ensure AI-generated responses remain ethical, compliant, and user-friendly.

