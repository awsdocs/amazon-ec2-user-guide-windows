# Set up AWS Systems Manager for Microsoft SCVMM<a name="scvmm-set-up"></a>

When you set up AWS Systems Manager, users in your organization can access your AWS resources\. The process involves creating accounts, deploying the add\-in, and providing your credentials\.

**Topics**
+ [Sign up for AWS](#create-account)
+ [Set up access for users](#create-users)
+ [Deploy the add\-in](#deploy-add-in)
+ [Provide your AWS credentials](#provide-credentials)

## Sign up for AWS<a name="create-account"></a>

When you sign up for Amazon Web Services, your AWS account is automatically signed up for all services in AWS\. You are charged only for the services that you use\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, see [Sign up for an AWS account](get-set-up-for-amazon-ec2.md#sign-up-for-aws) for instructions on how to create one\.

## Set up access for users<a name="create-users"></a>

The first time that you use Systems Manager, you must provide AWS credentials\. To enable multiple users to access the same AWS account using unique credentials and permissions, create a user for each user\. You can create one or more groups with policies that grant permissions to perform limited tasks\. Then you can create one or more users, and add each user to the appropriate group\.

**To create an Administrators group**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Groups** and then choose **Create New Group**\. 

1. In the **Group Name** box, specify **Administrators** and then choose **Next Step**\.

1. On the **Attach Policy** page, select the **AdministratorAccess** AWS managed policy\.

1. Choose **Next Step** and then choose **Create Group**\.

**To create a group with limited access to Amazon EC2**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Groups** and then choose **Create New Group**\. 

1. In the **Group Name** box, specify a meaningful name for the group and then choose **Next Step**\.

1. On the **Attach Policy** page, do not select an AWS managed policy — choose **Next Step**, and then choose **Create Group**\.

1. Choose the name of the group you've just created\. On the **Permissions** tab, choose **Inline Policies**, and then **click here**\.

1. Select the **Custom Policy** radio button and then choose **Select**\.

1. Enter a name for the policy and a policy document that grants limited access to Amazon EC2, and then choose **Apply Policy**\. For example, you can specify one of the following custom policies\.

   **Grant users in this group permission to view information about EC2 instances only**

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "ec2:Describe*",
           "iam:ListInstanceProfiles"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

   **Grant users in this group permission to perform all operations on EC2 instances that are supported by the add\-in**

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "iam:ListInstanceProfiles", 
           "ec2:Describe*", "ec2:CreateKeyPair",
           "ec2:CreateTags", "ec2:DeleteTags",
           "ec2:RunInstances", "ec2:GetPasswordData",
           "ec2:RebootInstances", "ec2:StartInstances", 
           "ec2:StopInstances", "ec2:TerminateInstances"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

   **Grant users in this group permission to import a VM to Amazon EC2**

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:ListAllMyBuckets", "s3:CreateBucket",
           "s3:DeleteBucket", "s3:DeleteObject",
           "s3:GetBucketLocation", "s3:GetObject",
           "s3:ListBucket", "s3:PutObject",
           "ec2:DescribeTags", "ec2:CancelConversionTask", 
           "ec2:DescribeConversionTasks", "ec2:DescribeInstanceAttribute",
           "ec2:CreateImage", "ec2:AttachVolume",
           "ec2:ImportInstance", "ec2:ImportVolume",
           "dynamodb:DescribeTable", "dynamodb:CreateTable",
           "dynamodb:Scan", "dynamodb:PutItem", "dynamodb:UpdateItem"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

**To create a user, get the user's AWS credentials, and grant the user permissions**

Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS\.

To grant users programmatic access, choose one of the following options\.


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use temporary credentials to sign programmatic requests to the AWS CLI, AWS SDKs, or AWS APIs\. |  Following the instructions for the interface that you want to use\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/scvmm-set-up.html)  | 
| IAM | Use temporary credentials to sign programmatic requests to the AWS CLI, AWS SDKs, or AWS APIs\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | \(Not recommended\)Use long\-term credentials to sign programmatic requests to the AWS CLI, AWS SDKs, or AWS APIs\. |  Following the instructions for the interface that you want to use\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/scvmm-set-up.html)  | 

## Deploy the add\-in<a name="deploy-add-in"></a>

Add\-ins for System Center VMM are distributed as `.zip` files\. To deploy the add\-in, use the following procedure\.

**To deploy the add\-in**

1. From your instance, go to [AWS Systems Manager for Microsoft System Center Virtual Machine Manager](https://aws.amazon.com/windows/system-center/#scvmm) and click **SCVMM**\. Save the `aws-systems-manager-1.5.zip` file to your instance\.

1. Open the VMM console\.

1. In the navigation pane, click **Settings** and then click **Console Add\-Ins**\.

1. On the ribbon, click **Import Console Add\-in**\.

1. On the **Select an Add\-in** page, click **Browse** and select the `aws-systems-manager-1.5.zip` file for the add\-in that you downloaded\.

1. Ignore any warnings that there are assemblies in the add\-in that are not signed by a trusted authority\. Select **Continue installing this add\-in anyway** and then click **Next**\.

1. On the **Summary** page, click **Finish**\.

1. When the add\-in is imported, the status of the job is `Completed`\. You can close the **Jobs** window\.

## Provide your AWS credentials<a name="provide-credentials"></a>

When you use the Systems Manager for the first time, you must provide your AWS credentials\. Your access keys identify you to AWS\. There are two types of access keys: access key IDs \(for example, AKIAIOSFODNN7EXAMPLE\) and secret access keys \(for example, wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY\)\. You should have stored your access keys in a safe place when you received them\.

**To provide your AWS credentials**

1. Open the VMM console\.

1. In the navigation pane, click **VMs and Services**\.

1. On the ribbon, click **Amazon EC2**\.

1. On the **Credentials** tab, specify your AWS credentials, select a default region, and then click **Save**\.  
![\[AWS Systems Manager Credentials tab.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_systems_manager_credentials-2.png)

To change these credentials at any time, click **Configuration**\.