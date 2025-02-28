---
title : "Using Your Own Account"
date : "2025-02-21"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

### Preparing Your AWS Environment for the Workshop

If you are not participating in an AWS-led event via Workshop Studio, you must set up your environment before proceeding with these labs. Keep in mind that the resources you create will incur costs, so be sure to clean them up once you have completed the workshop.

#### Step 1: Configuring Your Environment
To set up your AWS account, follow these steps:

1. Click Launch Stack below to initiate AWS CloudFormation with pre-configured values in the us-east-1 region.

    [Launch Stack](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/50cf2fdb-1972-4e79-814f-12d2e6fb0dab/LaunchStack.yml&stackName=LaunchStack)

If you prefer a different AWS region, remember to change the region accordingly.
The CloudFormation stack will provision the following resources:

EKS Cluster Deployment

-Creates a new EKS cluster named opea-eks-cluster.

-Deploys a worker node within the cluster using an M7i.24xlarge instance.

CloudFormation Templates for Workshop Modules
The stack also includes templates for the following modules:

Module 1: ChatQnA Default

Module 2: ChatQnA with Guardrails

Module 3: ChatQnA with OpenSearch (open-source) as the vector database

Module 4: ChatQnA with Remote Inference (Denvr) as the LLM

Module 5: ChatQnA with Bedrock as the LLM

Configuring Stack Parameters
Before launching the stack, review and configure the following parameters:

For Modules 1, 2, and 3:

+ **HuggingFaceToken**: This token is required to download models from Hugging Face. If you plan to use the Guardrails feature, ensure your token has access to the meta-llama/Meta-Llama-Guard-2-8B model.

+ **ModelID**: The OPEA system primarily uses the Text Generation Inference toolkit. Select any compatible model from Hugging Face and provide 
its model ID.

+ **OpeaRoleArn**: Enter the ARN or name of the IAM role associated with your AWS account.
    + If you are unsure, check the user information displayed in the top-right corner of the AWS Management Console.
    + If your username does not contain a forward slash (/), copy the full name.
    + If it does contain a forward slash, only use the portion before the slash.

**Example**:
+ If the display name is "USER", enter "USER".
+ If the display name is "ADMIN-ROLE/USER", enter "ADMIN-ROLE".
Alternatively, you can retrieve your ARN using the AWS CLI.

aws sts get-caller-identity --query 'Arn' --output text | awk -F: '{print $NF}' | (read id; if [[ $id == *"user"* ]]; then aws sts get-caller-identity --query 'Arn' --output text; else role=$(echo $id | cut -d'/' -f2); aws iam get-role --role-name $role --query 'Role.Arn' --output text; fi)

{{% notice info %}}
Mark the checkbox next to I acknowledge that AWS CloudFormation might create IAM resources
{{% /notice %}}

Deploying the CloudFormation Stack
Once the parameters are set, click Create Stack.

![VPC](/images/2/image008.png)

This process will initiate an AWS CodeBuild Project, which pulls in the opea-demo-builder open-source library.

The AWS Cloud Development Kit (CDK) will generate the necessary CloudFormation templates to configure your AWS environment for the workshop.

Monitoring Deployment Progress
The deployment process takes approximately 25 minutes.
Open the AWS CloudFormation Console and monitor the progress.

![VPC](/images/2/image009.png)

Ensure that the following stacks reach the CREATE_COMPLETE status:

+ The EKS cluster
+ The ChatQnA application (default deployment)

Some stacks may remain in REVIEW_IN_PROGRESS, as they will be deployed onto the EKS cluster later.

![VPC](/images/2/image010.png)

#### Step 2: Configuring Access to Your EKS Cluster
Once the CloudFormation stacks are successfully deployed, you need to configure your local environment to interact with the Amazon EKS cluster using kubectl.

Updating Your Kubernetes Configuration (kubeconfig)
1. Open AWS CloudShell

+ Click the CloudShell icon in the AWS Management Console.
+ Alternatively, use your local AWS CLI, ensuring kubectl and the AWS CLI client are installed on your system.

2. Update kubeconfig

+ Run the following command (updating the region if necessary):
![VPC](/images/2/image011.png)

+ If successful, you should receive a confirmation message indicating that your configuration file has been updated.
![VPC](/images/2/image012.png)

3. Verify Connectivity to Your EKS Cluster
Ensure you can interact with the cluster using kubectl.

#### Step 3: Verifying EKS Cluster Access
After updating your kubeconfig, test whether you can connect to the cluster. If you encounter issues accessing pods via the AWS console or CloudShell, follow these steps:

1. Check Your IAM Access in the EKS Console

    + Navigate to the EKS Console.
![VPC](/images/2/image013.png)
    
    + Look for your IAM principal ARN under Access Entries.
![VPC](/images/2/image014.png)

![VPC](/images/2/image015.png)

2. If Your ARN Is Not Listed, Add It Manually
    + Click Create Access Entry.
    + Enter your IAM User ARN or Role ARN.
![VPC](/images/2/image016.png)

3. Attach Required Policies
Assign the following permissions to your IAM role:
    + AmazonEKSAdminPolicy
    + AmazonEKSClusterAdminPolicy
![VPC](/images/2/image017.png)

4. Confirm Access by Listing Nodes
    + Run the following command to check if worker nodes are visible:
![VPC](/images/2/image018.png)

    + If the command returns a list of nodes, your cluster is successfully configured.
**Note**: If no nodes appear, wait a few minutes and retry, as node provisioning may still be in progress.
![VPC](/images/2/image019.png)

#### Next Steps: Explore the Workshop Modules
Once you have successfully connected to your EKS cluster, you are ready to proceed with any of the workshop modules. Choose the module that best fits your learning objectives and begin your hands-on experience with AWS, Kubernetes, and AI-powered solutions.
