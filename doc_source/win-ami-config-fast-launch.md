# Configure your Windows AMI for faster launching<a name="win-ami-config-fast-launch"></a>

Windows AMIs that are optimized for faster launching use pre\-provisioned snapshots to reduce the time it takes to launch an EC2 Windows instance\. To create the pre\-provisioned snapshots, Amazon EC2 launches an instance from the Windows AMI and completes the Sysprep specialize and Windows Out of Box Experience \(OOBE\) steps, rebooting as required\. Then it stops the instance, and creates a set of reserved snapshots that are used for subsequent launches\.

The reserved snapshots are automatically replenished as they are used, depending on your settings for launch frequency\. If you expect a spike in the number of instances that are launched from your AMI – during a special event, for example – you can increase the launch frequency in advance to cover the additional instances that you'll need\. When your launch volume returns to normal, you can adjust the frequency back down\.

When you experience a higher number of launches than anticipated, you might use up the fast\-launching snapshots that you have in reserve\. This does not cause any launches to fail\. However, it can result in some instances going through the standard launch process, until snapshots can be replenished\.

You can configure Windows AMIs that you own for faster launching using the Amazon EC2 console, API, SDKs, or ec2 commands in the AWS CLI\. The following sections cover configuration steps for the Amazon EC2 console and AWS CLI\.

**Topics**
+ [Prerequisites](#win-start-fast-launch-prereqs)
+ [Configuration scenarios](#fast-launch-configuration-scenarios)
+ [Start faster launching for Windows AMIs \(console\)](#win-start-fast-launch-console)
+ [Stop faster launching for Windows AMIs \(console\)](#win-stop-fast-launch-console)
+ [AWS CLI commands to configure faster launching for EC2 Windows instances](#win-fast-launch-cli-commands)
+ [Service\-linked role for faster launching for EC2 Windows instances](#slr-windows-fast-launch)

## Prerequisites<a name="win-start-fast-launch-prereqs"></a>

Before you set up faster launching for EC2 Windows instances, you must verify that the following prerequisites are met:
+ Ensure that a default VPC is configured for the Region in which you use faster launching for EC2 Windows instances\. You cannot have EC2 Classic enabled in the Region, even if you have a default VPC configured\. For more information about EC2 Classic, see [EC2\-Classic Networking is Retiring \- Here's How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.
+ Your AWS account must own the Windows AMI that you use to configure faster launching for EC2 Windows instances\.
+ The Windows AMI that you use to configure faster launching for EC2 Windows instances must be created using Sysprep with the shutdown option\. AMIs that are created from an instance without running Sysprep are not currently supported\. To create an AMI using Sysprep, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

## Configuration scenarios<a name="fast-launch-configuration-scenarios"></a>

This section addresses specific scenarios to help you configure your Windows AMI for faster launching\.

**You have deleted your default VPC or you are using IMDSv2 for your launch**  
Considerations:
+ You must use a default VPC in the Region for which you are configuring faster launching\. To create a default VPC, see [Create a default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html#create-default-vpc) in the *Amazon VPC User Guide*\. To use a different VPC, specify the VPC in a launch template, and specify the launch template when you run the [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) AWS CLI command or when you call the [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API\.
+ If your account includes a policy that enforces IMDSv2 for Amazon EC2 instances, configure faster launching by creating a launch template that specifies the metadata configuration to enforce IMDSv2\. Then, specify the launch template you just created in the [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API call or when you run the [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) AWS CLI command\. For this scenario, you can configure faster launching by using only the AWS CLI or API\. You cannot configure faster launching using the Amazon EC2 console\. 

## Start faster launching for Windows AMIs \(console\)<a name="win-start-fast-launch-console"></a>

To start faster launching for EC2 Windows instances using the Amazon EC2 console, follow these steps\.

**Note**  
To change these settings, make sure that your AMI, and the Region that you run in meet all [Prerequisites](#win-start-fast-launch-prereqs)\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Images**, choose **AMIs**\.

1. Choose the AMI to update by selecting the check box next to the **Name**\.

1. From the **Actions** menu above the list of AMIs, choose **Manage image optimization**\. This opens the **Manage image optimizations** page, where you configure the settings for faster launching\.

1. To start using pre\-provisioned snapshots to launch instances from your Windows AMI faster, select the **Enable Windows faster launching** check box\.

1. From the **Set anticipated launch frequency** drop\-down list, select a value to specify the number of snapshots that are created and maintained to cover your expected instance launch volume\.

1. Choose **Save changes** when you are done\.

## Stop faster launching for Windows AMIs \(console\)<a name="win-stop-fast-launch-console"></a>

To stop faster launching for EC2 Windows instances using the Amazon EC2 console, follow these steps\.

**Note**  
To change these settings, make sure that your AMI, and the Region that you run in meet all [Prerequisites](#win-start-fast-launch-prereqs)\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Images**, choose **AMIs**\.

1. Choose the AMI to update by selecting the check box next to the **Name**\.

1. From the **Actions** menu above the list of AMIs, choose **Manage image optimizations**\. This opens the **Manage image optimizations** page, where you configure the settings for faster launching\.

1. Clear the **Enable Windows faster launching** check box to stop faster launching for EC2 Windows instances and to remove pre\-provisioned snapshots\. This results in the AMI using the standard launch process for each instance, going forward\.
**Note**  
When you stop Windows image optimization, any existing pre\-provisioned snapshots are automatically deleted\. This step must be completed before you can start using the feature again\.

1. Choose **Save changes** when you are done\.

## AWS CLI commands to configure faster launching for EC2 Windows instances<a name="win-fast-launch-cli-commands"></a>

You can configure faster launching for EC2 Windows instances, and describe instances that have faster launching enabled, by using the following AWS CLI commands\. For more information about the command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**Note**  
To start, stop, or change settings for a Windows AMI, you must own the AMI\.

**To start using faster launching for EC2 Windows instances**  
Use the [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) AWS CLI command to enable fast launching for an image\.

```
aws ec2 enable-fast-launch \
	--image-id <value> \
	--resource-type <value> \ (optional)
	--snapshot-configuration <value> \ (optional)
	--launch-template <value> \ (optional)
	--max-parallel-launches <value> \ (optional)
	--dry-run | --no-dry-run \  (optional)
	--cli-input-json <value> \ (optional)
	--generate-cli-skeleton <value> \ (optional)
```

The following [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) example starts fast launching on the specified AMI and sets the maximum number of parallel instances to launch to 6\. The type of resource to use to pre\-provision the AMI is set to `snapshot`, which is also the default value\.

```
aws ec2 enable-fast-launch \
        --image-id ami-01234567890abcedf  \
        --max-parallel-launches 6 \
        --resource-type snapshot
```

Output:

```
{
    "ImageId": "ami-01234567890abcedf",
    "ResourceType": "snapshot",
    "SnapshotConfiguration": {
        "TargetResourceCount": 10
    },
    "LaunchTemplate": {},
    "MaxParallelLaunches": 6,
    "OwnerId": "0123456789123",
    "State": "enabling",
    "StateTransitionReason": "Client.UserInitiated",
    "StateTransitionTime": "2022-01-27T22:16:03.199000+00:00"
}
```

**To disable faster launching for EC2 Windows instances**  
Use the [disable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-launch.html) AWS CLI command to disable fast launching for an image\.

```
aws ec2 disable-fast-launch \
	--image-id <value> \
	--force | --no-force \ (optional)
	--dry-run | --no-dry-run \ (optional)
	--cli-input-json <value> \ (optional)
	--generate-cli-skeleton <value> \ (optional)
```

The following [disable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-launch.html) example discontinues fast launching on the specified AMI, and cleans up existing pre\-provisioned snapshots\.

```
aws ec2 disable-fast-launch \
        --image-id ami-01234567890abcedf \
```

Output:

```
{
    "ImageId": "ami-01234567890abcedf",
    "ResourceType": "snapshot",
    "SnapshotConfiguration": {},
    "LaunchTemplate": {
        "LaunchTemplateId": "lt-01234567890abcedf",
        "LaunchTemplateName": "EC2FastLaunchDefaultResourceCreation-a8c6215d-94e6-441b-9272-dbd1f87b07e2",
        "Version": "1"
    },
    "MaxParallelLaunches": 6,
    "OwnerId": "0123456789123",
    "State": "disabling",
    "StateTransitionReason": "Client.UserInitiated",
    "StateTransitionTime": "2022-01-27T22:47:29.265000+00:00"
}
```

**To describe the details for Windows AMIs that are configured for faster launching for EC2 Windows instances**  
Use the [describe\-fast\-launch\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-launch-images.html) AWS CLI command to describe the details for Windows AMIs that are configured for faster launching\. The details provided about each AMI configured for faster launching include the following:
+ The image ID that identifies the fast\-launch enabled Windows image\.
+ The resource type that is used for pre\-provisioning the Windows AMI\. Supported values include: `snapshot`\.
+ The snapshot configuration, which is a group of parameters that are used for pre\-provisioning the associated Windows AMI using snapshots\.
+ Launch template information, including the ID, name, and version of the launch template that the AMI uses when it launches Window instances from pre\-provisioned snapshots\.
+ The maximum number of parallel instances that are launched for creating resources\. 
+ The owner ID for the fast\-launch enabled Windows AMI\.
+ The current state of faster launching for the specified Windows AMI\. Supported values include `enabling | enabling-failed | enabled | enabled-failed | disabling | disabling-failed`\.
+ The reason that faster launching for the Windows AMI changed to the current state\.
+ The time that faster launching for the Windows AMI changed to the current state\.

```
aws ec2 describe-fast-launch-images \
	--image-ids <value> \ (optional)
	--filters <value> \ (optional)
	--dry-run | --no-dry-run \ (optional)
	--cli-input-json <value> \ (optional)
	--starting-token <value> \ (optional)
	--page-size <value> \ (optional)
	--max-items <value> \ (optional)
	--generate-cli-skeleton <value> \ (optional)
```

The following [describe\-fast\-launch\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-launch-images.html) example describes the details for each of the AMIs in the user's account that are configured for faster launching\. In this example, only one AMI in the account is configured for faster launching\.

```
    aws ec2 describe-fast-launch-images
```

Output:

```
{
    "FastLaunchImages": [
        {
            "ImageId": "ami-01234567890abcedf",
            "ResourceType": "snapshot",
            "SnapshotConfiguration": {},
            "LaunchTemplate": {
                "LaunchTemplateId": "lt-01234567890abcedf",
                "LaunchTemplateName": "EC2FastLaunchDefaultResourceCreation-a8c6215d-94e6-441b-9272-dbd1f87b07e2",
                "Version": "1"
            },
            "MaxParallelLaunches": 6,
            "OwnerId": "0123456789123",
            "State": "enabled",
            "StateTransitionReason": "Client.UserInitiated",
            "StateTransitionTime": "2022-01-27T22:20:06.552000+00:00"
        }
    ]
}
```

## Service\-linked role for faster launching for EC2 Windows instances<a name="slr-windows-fast-launch"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information about how Amazon EC2 uses IAM roles, including service\-linked roles, see [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.

Amazon EC2 uses the service\-linked role named `AWSServiceRoleForEC2FastLaunch` to create and manage a set of pre\-provisioned snapshots that reduce the time it takes to launch instances from your Windows AMI\.

You don't need to create this service\-linked role manually\. When you start using faster launching for EC2 Windows instances for your AMI, Amazon EC2 creates the service\-linked role for you, if it does not already exist\.

**Note**  
If the service\-linked role is deleted from your account, you can start faster launching for EC2 Windows instances for another Windows AMI to re\-create the role in your account\. Alternatively, you can stop faster launching for EC2 Windows instances for your current AMI, and then start it again\. However, stopping the feature results in your AMI using the standard launch process for all new instances while Amazon EC2 removes all of your pre\-provisioned snapshots\. After all of the pre\-provisioned snapshots are gone, you can start using faster launching for EC2 Windows instances for your AMI again\.

Amazon EC2 does not allow you to edit the `AWSServiceRoleForEC2FastLaunch` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role by using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

You can delete a service\-linked role only after first deleting all of the related resources\. This protects the Amazon EC2 resources that are associated with your faster launching\-enabled AMI because you can't inadvertently remove permission to access the resources\.

Amazon EC2 supports the faster launching for EC2 Windows instances service\-linked role in all of the Regions where the Amazon EC2 service is available\. For more information, see [Regions](using-regions-availability-zones.md#concepts-regions)\.

### Permissions granted by `AWSServiceRoleForEC2FastLaunch`<a name="slr-permissions-granted-win-faster-launching"></a>

Amazon EC2 uses the `EC2FastLaunchServiceRolePolicy` managed policy to complete the following actions:
+ `cloudwatch:PutMetricData` – Post metric data associated with faster launching for EC2 Windows instances to the Amazon EC2 namespace\.
+ `ec2:CreateLaunchTemplate` – Create a launch template for your faster launching\-enabled AMI\.
+ `ec2:CreateSnapshot` – Create pre\-provisioned snapshots for your faster launching\-enabled AMI\.
+ `ec2:CreateTags` – Create tags for resources that are associated with launching and pre\-provisioning Windows instances for your faster launching\-enabled AMI\.
+ `ec2:DeleteSnapshots` – Delete all associated pre\-provisioned snapshots if faster launching for EC2 Windows instances is turned off for a previously enabled AMI\.
+ `ec2:DescribeImages` – Describe images for all resources\.
+ `ec2:DescribeInstanceAttribute` – Describe instance attributes for all resources\.
+ `ec2:DescribeInstanceStatus` – Describe instance status for all resources\.
+ `ec2:DescribeInstances` – Describe instances for all resources\.
+ `ec2:DescribeInstanceTypeOfferings` – Describe instance type offerings for all resources\.
+ `ec2:DescribeLaunchTemplates` – Describe launch templates for all resources\.
+ `ec2:DescribeLaunchTemplateVersions` – Describe launch template versions for all resources\.
+ `ec2:DescribeSnapshots` – Describe snapshot resources for all resources\.
+ `ec2:DescribeSubnets` – Describe subnets for all resources\.
+ `ec2:RunInstances` – Launch instances from a faster launching\-enabled AMI, in order to perform provisioning steps\.
+ `ec2:StopInstances` – Stop instances that were launched from a faster launching\-enabled AMI in order to create pre\-provisioned snapshots\.
+ `ec2:TerminateInstances` – Terminate an instance that was launched from a faster launching\-enabled AMI after creating the pre\-provisioned snapshot from it\.
+ `iam:PassRole` – Allows the `AWSServiceRoleForEC2FastLaunch` service\-linked role to launch instances on your behalf using the instance profile from your launch template\.

For more information about using managed policies for Amazon EC2, see [AWS managed policies for Amazon Elastic Compute Cloud](security-iam-awsmanpol.md)\.

### Access to customer managed keys for use with encrypted AMIs and EBS snapshots<a name="win-faster-launching-slr-access-to-cust-keys"></a>

**Prerequisite**
+ To enable Amazon EC2 to access an encrypted AMI on your behalf, you must have permission for the `createGrant` action in the customer managed key\.

When you enable faster launching for EC2 Windows instances for an encrypted AMI, Amazon EC2 ensures that permission is granted for the `AWSServiceRoleForEC2FastLaunch` role to use the customer managed key to access your AMI\. This permission is needed to launch instances and create pre\-provisioned snapshots on your behalf\.