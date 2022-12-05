# Configure your Windows AMI for faster launching<a name="win-ami-config-fast-launch"></a>

Every EC2 Windows instance must go through the standard Windows operating system \(OS\) launch steps, which include several reboots, and often take 15 minutes or longer to complete\. Windows AMIs that are optimized for faster launching complete some of those steps and reboots in advance by launching a set of instances in the background, and then creating snapshots when they have completed the initial launch steps\. The use of these snapshots in the faster launching process can significantly reduce the time it takes to launch instances when they are needed\.

When you configure a Windows AMI for faster launching, Amazon EC2 automatically creates the snapshots for you, based on your settings, and you only pay for the resources that the process consumes\. This is not the same process as [EBS fast snapshot restore](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-fast-snapshot-restore.html)\. EBS fast snapshot restore must be explicitly enabled on a per\-snapshot basis, and has its own associated costs\.

**Note**  
Any account that has access to an AMI with faster launching enabled can benefit from reduced launch times\. However, it is the AMI owner's account that provides the snapshots that are consumed for the launch\.

**Key terms**
+ **Pre\-provisioned snapshot** – A snapshot of an instance that was launched from a Windows AMI with faster launching enabled, and that has completed the following Windows launch steps, rebooting as required\. Amazon EC2 creates these snapshots automatically, based on your configuration\.
  + Sysprep specialize
  + Windows Out of Box Experience \(OOBE\)

  When these steps are complete, Amazon EC2 stops the instance, and creates a snapshot that is later used for faster launching from the AMI\.
+ **Launch frequency** – Controls the number of pre\-provisioned snapshots that Amazon EC2 can launch within the specified timeframe\. When you enable faster launching for your AMI, Amazon EC2 creates the initial set of pre\-provisioned snapshots in the background\. For example, if the launch frequency is set to five launches per hour, which is the default, then Amazon EC2 creates an initial set of five pre\-provisioned snapshots\.

  When Amazon EC2 launches an instance from an AMI with faster launching enabled, it uses one of the pre\-provisioned snapshots to reduce the launch time\. As snapshots are used, they are automatically replenished, up to the number specified by the launch frequency\.

  If you expect a spike in the number of instances that are launched from your AMI – during a special event, for example – you can increase the launch frequency in advance to cover the additional instances that you'll need\. When your launch rate returns to normal, you can adjust the frequency back down\.

  When you experience a higher number of launches than anticipated, you might use up all the faster launching snapshots that you have available\. This does not cause any launches to fail\. However, it can result in some instances going through the standard launch process, until snapshots can be replenished\.
+ **Target resource count** – The number of pre\-provisioned snapshots to keep on hand for an AMI with faster launching enabled\.
+ **Max parallel launches** – Controls how many instances can be launched at a time for creating the pre\-provisioned snapshots\. If your target resource count is higher than the maximum number of parallel launches you've configured, Amazon EC2 will initially launch the number of instances specified by the **Max parallel launches** setting for creating the snapshots\. As those instances complete the process and Amazon EC2 takes the snapshot and stops the instance, more instances are launched until the total number of snapshots available has reached the target resource count\. This value must be 6 or greater\.

**Resource costs**  
There is no service charge to configure Windows AMIs for faster launching\. However, standard pricing applies for underlying AWS resources that are used to prepare and store the pre\-provisioned snapshots\. The following example demonstrates how associated costs are allocated\.

**Example scenario:** The AtoZ Example company has a Windows AMI with a 50 GiB EBS root volume\. They enable faster launching for their AMI, and set the target resource count to five\. Over the course of a month, using Windows faster launching for their AMI costs them around $5\.00, and the cost breakdown is as follows:

1. When AtoZ Example enables faster launching, Amazon EC2 launches five small instances\. Each instance runs through the Sysprep and OOBE Windows launch steps, rebooting as required\. This takes several minutes for each instance \(time can vary, based on how busy that Region or Availability Zone \(AZ\) is, and on the size of the AMI\)\.

**Costs**
   + Instance runtime costs \(or minimum runtime, if applicable\): five instances
   + Volume costs: five EBS root volumes

1. When the pre\-provisioning process completes, Amazon EC2 takes a snapshot of the instance, which it stores in Amazon S3\. Snapshots are typically stored for 4–8 hours before they are consumed by a launch\. In this case, the cost is roughly $0\.02 to $0\.05 per snapshot\.

**Costs**
   + Snapshot storage \(Amazon S3\): five snapshots

1. After Amazon EC2 takes the snapshot, it stops the instance\. At that point, the instance is no longer accruing costs\. However EBS volume costs continue to accrue\.

**Costs**
   + EBS volumes: costs continue for the associated EBS root volumes\.

**Note**  
The costs shown here are for demonstration purposes only\. Your costs will vary, depending on your AMI configuration and pricing plan\.

**Track faster launching costs on your bill**  
Cost allocation tags can help you organize your AWS bill to reflect the costs associated with faster launching for EC2 Windows instances\. You can use the following tag that Amazon EC2 adds to the resources it creates when it prepares and stores pre\-provisioned snapshots for faster launching:

**Tag key:** `CreatedBy`, **Value:** `EC2 Fast Launch`

After you activate the tag in the Billing and Cost Management console, and set up your detailed billing report, the `user:CreatedBy` column appears on the report\. The column includes values from all services\. However, if you download the CSV file, you can import the data into a spreadsheet, and filter for `EC2 Fast Launch` in the value\. This information also appears in the AWS Cost and Usage Report when the tag is activated\.

**Step 1: Activate user\-defined cost allocation tags**  
To include resource tags in your cost reports, you must first activate the tag in the Billing and Cost Management console\. For more information, see [Activating User\-Defined Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.html) in the *AWS Billing and Cost Management User Guide*\.

**Note**  
Activation can take up to 24 hours\.

**Step 2: Set up a cost report**  
If you already have a cost report set up, a column for your tag appears the next time the report runs after activation is complete\. To set up cost reports for the first time, choose one of the following\.
+ See [Setting up a monthly cost allocation report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html#allocation-report) in the *AWS Billing and Cost Management User Guide*\.
+ See [Creating Cost and Usage Reports](https://docs.aws.amazon.com/cur/latest/userguide/cur-create.html) in the *AWS Cost and Usage Report User Guide*\.

**Note**  
It can take up to 24 hours for AWS to start delivering reports to your S3 bucket\.

You can configure Windows AMIs that you own for faster launching using the Amazon EC2 console, API, SDKs, or ec2 commands in the AWS CLI\. The following sections cover configuration steps for the Amazon EC2 console and AWS CLI\.

**Topics**
+ [Prerequisites](#win-start-fast-launch-prereqs)
+ [Use a launch template when you set up faster launching](#win-fast-launch-with-template)
+ [Start faster launching for Windows AMIs](#win-start-fast-launch)
+ [Stop faster launching for Windows AMIs](#win-stop-fast-launch)
+ [View Windows AMIs that have faster launching enabled \(AWS CLI\)](#win-view-fast-launch)
+ [Service\-linked role for faster launching for EC2 Windows instances](#slr-windows-fast-launch)

## Prerequisites<a name="win-start-fast-launch-prereqs"></a>

Before you set up faster launching for EC2 Windows instances, verify that you've met the following prerequisites:
+ If you don't use a launch template to configure your settings, ensure that a default VPC is configured for the Region in which you use faster launching for EC2 Windows instances\. You cannot have EC2 Classic enabled in the Region, even if you have a default VPC configured\. For more information about EC2 Classic, see [EC2\-Classic Networking is Retiring \- Here's How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.
**Note**  
If you accidentally delete your default VPC in the Region where you plan to configure faster launching for EC2 Windows instances, you can create a new default VPC in that Region\. To learn more, see [Create a default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html#create-default-vpc) in the *Amazon VPC User Guide*\.
+ To specify a non\-default VPC, you must use a launch template when you configure Windows faster launching\. For more information, see [Use a launch template when you set up faster launching](#win-fast-launch-with-template)\.
+ If your account includes a policy that enforces IMDSv2 for Amazon EC2 instances, you must create a launch template that specifies the metadata configuration to enforce IMDSv2\.
+ To change the settings for faster launching for EC2 Windows instances, your AWS account must own the Windows AMI\.
+ The Windows AMI that you use to configure faster launching for EC2 Windows instances must be created using Sysprep with the shutdown option\. AMIs that are created from an instance without running Sysprep are not currently supported\. To create an AMI using Sysprep, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

## Use a launch template when you set up faster launching<a name="win-fast-launch-with-template"></a>

With a launch template, you can configure a set of launch parameters that Amazon EC2 uses each time it launches an instance from that template\. You can specify such things as an AMI to use for your base image, instance types, storage, network settings, and more\. Launch templates are optional, except for the following specific cases, where you must use a launch template for your Windows AMI when you configure faster launching:
+ You must use a launch template to specify a non\-default VPC for your Windows AMI\.
+ If your account includes a policy that enforces IMDSv2 for Amazon EC2 instances, you must create a launch template that specifies the metadata configuration to enforce IMDSv2\.

  Use the launch template that includes your metadata configuration from the EC2 console, or when you run the [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) command in the AWS CLI, or call the [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API action\.

### Specify a non\-default VPC<a name="win-fast-launch-non-default-vpc"></a>

**Step 1: Create a launch template**  
Create a launch template that specifies the VPC for your Windows instances from the AWS Management Console or from the AWS CLI\. For more information, see [Create a launch template](create-launch-template.md)\.

**Step 2: Specify the launch template for your faster launching AMI**  

+ To specify the launch template for faster launching for EC2 Windows instances, follow these steps:

  1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

  1. In the navigation pane, under **Images**, choose **AMIs**\.

  1. Choose the AMI to update by selecting the check box next to the **Name**\.

  1. From the **Actions** menu above the list of AMIs, choose **Manage image optimization**\. This opens the **Manage image optimization** page, where you configure the settings for faster launching\.

  1. The **Launch template** box performs a filtered search that finds launch templates in your account in the current Region that match the text you've entered\. Specify all or part of the launch template name or ID in the box to show a list of matching launch templates\. For example, if you enter `fast` in the box, Amazon EC2 finds all of the launch templates in your account in the current Region that have "fast" in the name\.

     To create a new launch template, you can choose **Create launch template**\.

  1. When you select a launch template, Amazon EC2 shows the default version for that template in the **Source template version** box\. To specify a different version, highlight the default version to replace it, and enter the version number you want in the box\.

  1. When you're done making changes, choose **Save changes**\.
+ Specify the launch template name or ID in the `--launch-template` parameter when you run the [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) command in the AWS CLI\.
+ Specify the launch template name or ID in the `LaunchTemplate` parameter when you call the [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API action\.

For more information about EC2 launch templates, see [Launch an instance from a launch template](ec2-launch-templates.md)\.

## Start faster launching for Windows AMIs<a name="win-start-fast-launch"></a>

To start faster launching for EC2 Windows instances, choose the tab that matches your environment, and follow the steps\.

**Note**  
Before changing these settings, make sure that your AMI, and the Region that you run in meet all [Prerequisites](#win-start-fast-launch-prereqs)\.

------
#### [ Console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Images**, choose **AMIs**\.

1. Choose the AMI to update by selecting the check box next to the **Name**\.

1. From the **Actions** menu above the list of AMIs, choose **Manage image optimization**\. This opens the **Manage image optimization** page, where you configure the settings for faster launching\.

1. To start using pre\-provisioned snapshots to launch instances from your Windows AMI faster, select the **Enable Windows faster launching** check box\.

1. From the **Set anticipated launch frequency** drop\-down list, choose a value to specify the number of snapshots that are created and maintained to cover your expected instance launch volume\.

1. When you're done making changes, choose **Save changes**\.

**Note**  
If you need to use a launch template to specify a non\-default VPC, or to configure metadata settings for IMDSv2, see [Use a launch template when you set up faster launching](#win-fast-launch-with-template)\.

------
#### [ AWS CLI ]

The enable\-fast\-launch command calls the Amazon EC2 [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API operation\.

**Syntax:**

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

**Example:**  
The following [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) example starts faster launching for the specified AMI, launching six parallel instances for pre\-provisioning\. The `ResourceType` is set to `snapshot`, which is the default value\.

```
aws ec2 enable-fast-launch \
        --image-id ami-01234567890abcedf  \
        --max-parallel-launches 6 \
        --resource-type snapshot
```

**Output:**

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

------
#### [ Tools for Windows PowerShell ]

The Enable\-EC2FastLaunch cmdlet calls the Amazon EC2 [EnableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableFastLaunch.html) API operation\.

**Syntax:**

```
Enable-EC2FastLaunch
     -ImageId <String>
     -LaunchTemplate_LaunchTemplateId <String>
     -LaunchTemplate_LaunchTemplateName <String>
     -MaxParallelLaunch <Int32>
     -ResourceType <String>
     -SnapshotConfiguration_TargetResourceCount <Int32>
     -LaunchTemplate_Version <String>
     -Select <String>
     -PassThru <SwitchParameter>
     -Force <SwitchParameter>
```

**Example:**  
The following [Enable\-EC2FastLaunch](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2FastLaunch.html) example starts faster launching for the specified AMI, launching six parallel instances for pre\-provisioning\. The `ResourceType` is set to `snapshot`, which is the default value\.

```
Enable-EC2FastLaunch `
-ImageId ami-01234567890abcedf `
-MaxParallelLaunch 6 `
-Region us-west-2 `
-ResourceType snapshot
```

**Output:**

```
ImageId               : ami-01234567890abcedf
LaunchTemplate        : 
MaxParallelLaunches   : 6
OwnerId               : 0123456789123
ResourceType          : snapshot
SnapshotConfiguration : Amazon.EC2.Model.FastLaunchSnapshotConfigurationResponse
State                 : enabling
StateTransitionReason : Client.UserInitiated
StateTransitionTime   : 2/25/2022 12:24:11 PM
```

------

## Stop faster launching for Windows AMIs<a name="win-stop-fast-launch"></a>

To stop faster launching for EC2 Windows instances, choose the tab that matches your environment, and follow the steps\.

**Note**  
Before changing these settings, make sure that your AMI, and the Region that you run in meet all [Prerequisites](#win-start-fast-launch-prereqs)\.

------
#### [ Console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Images**, choose **AMIs**\.

1. Choose the AMI to update by selecting the check box next to the **Name**\.

1. From the **Actions** menu above the list of AMIs, choose **Manage image optimization**\. This opens the **Manage image optimization** page, where you configure the settings for faster launching\.

1. Clear the **Enable Windows faster launching** check box to stop faster launching for EC2 Windows instances and to remove pre\-provisioned snapshots\. This results in the AMI using the standard launch process for each instance, going forward\.
**Note**  
When you stop Windows image optimization, any existing pre\-provisioned snapshots are automatically deleted\. This step must be completed before you can start using the feature again\.

1. When you're done making changes, choose **Save changes**\.

------
#### [ AWS CLI ]

The disable\-fast\-launch command calls the Amazon EC2 [DisableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableFastLaunch.html) API operation\.

**Syntax:**

```
aws ec2 disable-fast-launch \
	--image-id <value> \
	--force | --no-force \ (optional)
	--dry-run | --no-dry-run \ (optional)
	--cli-input-json <value> \ (optional)
	--generate-cli-skeleton <value> \ (optional)
```

**Example:**  
The following [disable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-launch.html) example stops faster launching on the specified AMI, and cleans up existing pre\-provisioned snapshots\.

```
aws ec2 disable-fast-launch \
        --image-id ami-01234567890abcedf
```

**Output:**

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

------
#### [ Tools for Windows PowerShell ]

The Disable\-EC2FastLaunch cmdlet calls the Amazon EC2 [DisableFastLaunch](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableFastLaunch.html) API operation\.

**Syntax:**

```
Disable-EC2FastLaunch 
     -ImageId <String>
     -ForceStop <Boolean>
     -Select <String>
     -PassThru <SwitchParameter>
     -Force <SwitchParameter>
```

**Example:**  
The following [Disable\-EC2FastLaunch](https://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2FastLaunch.html) example stops faster launching on the specified AMI, and cleans up existing pre\-provisioned snapshots\.

```
Disable-EC2FastLaunch -ImageId ami-01234567890abcedf
```

**Output:**

```
ImageId               : ami-01234567890abcedf
LaunchTemplate        : Amazon.EC2.Model.FastLaunchLaunchTemplateSpecificationResponse
MaxParallelLaunches   : 6
OwnerId               : 0123456789123
ResourceType          : snapshot
SnapshotConfiguration : 
State                 : disabling
StateTransitionReason : Client.UserInitiated
StateTransitionTime   : 2/25/2022 1:10:08 PM
```

------

## View Windows AMIs that have faster launching enabled \(AWS CLI\)<a name="win-view-fast-launch"></a>

You can use the [describe\-fast\-launch\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-launch-images.html) command in the AWS CLI, or the [Get\-EC2FastLaunchImage](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FastLaunchImage.html) Tools for Windows PowerShell cmdlet to get details for Windows AMIs that have faster launching enabled\.

Amazon EC2 provides the following details for each Windows AMI that is returned in the results:
+ The image ID that identifies the fast\-launch enabled Windows image\.
+ The resource type that is used for pre\-provisioning the Windows AMI\. Supported value: `snapshot`\.
+ The snapshot configuration, which is a group of parameters that is used for pre\-provisioning the associated Windows AMI using snapshots\.
+ Launch template information, including the ID, name, and version of the launch template that the AMI uses when it launches Window instances from pre\-provisioned snapshots\.
+ The maximum number of parallel instances that are launched for creating resources\. 
+ The owner ID for the fast\-launch enabled Windows AMI\.
+ The current state of faster launching for the specified Windows AMI\. Supported values include: `enabling | enabling-failed | enabled | enabled-failed | disabling | disabling-failed`\.
**Note**  
You can also see the current state displayed in the **Manage image optimization** page in the EC2 console, as **Image optimization state**\.
+ The reason that faster launching for the Windows AMI changed to the current state\.
+ The time that faster launching for the Windows AMI changed to the current state\.

Choose the tab that matches your command line environment:

------
#### [ AWS CLI ]

The describe\-fast\-launch\-images command calls the Amazon EC2 [DescribeFastLaunchImages](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFastLaunchImages.html) API operation\.

**Syntax:**

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

**Example:**  
The following [describe\-fast\-launch\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-launch-images.html) example describes the details for each of the AMIs in the account that are configured for faster launching\. In this example, only one AMI in the account is configured for faster launching\.

```
aws ec2 describe-fast-launch-images
```

**Output:**

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

------
#### [ Tools for Windows PowerShell ]

The Get\-EC2FastLaunchImage cmdlet calls the Amazon EC2 [DescribeFastLaunchImages](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFastLaunchImages.html) API operation\.

**Syntax:**

```
Get-EC2FastLaunchImage
-Filter <Filter[]>
-ImageId <String[]>
-MaxResult <Int32>
-NextToken <String>
-Select <String>
-NoAutoIteration <SwitchParameter>
```

**Example:**  
The following [Get\-EC2FastLaunchImage](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FastLaunchImage.html) example describes the details for each of the AMIs in the account that are configured for faster launching\. In this example, only one AMI in the account is configured for faster launching\.

```
Get-EC2FastLaunchImage -ImageId ami-01234567890abcedf
```

**Output:**

```
ImageId               : ami-01234567890abcedf
LaunchTemplate        : Amazon.EC2.Model.FastLaunchLaunchTemplateSpecificationResponse
MaxParallelLaunches   : 6
OwnerId               : 0123456789123
ResourceType          : snapshot
SnapshotConfiguration : 
State                 : enabled
StateTransitionReason : Client.UserInitiated
StateTransitionTime   : 2/25/2022 12:54:43 PM
```

------

## Service\-linked role for faster launching for EC2 Windows instances<a name="slr-windows-fast-launch"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information about how Amazon EC2 uses IAM roles, including service\-linked roles, see [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.

Amazon EC2 uses the service\-linked role named `AWSServiceRoleForEC2FastLaunch` to create and manage a set of pre\-provisioned snapshots that reduce the time it takes to launch instances from your Windows AMI\.

You don't need to create this service\-linked role manually\. When you start using faster launching for EC2 Windows instances for your AMI, Amazon EC2 creates the service\-linked role for you, if it doesn't already exist\.

**Note**  
If the service\-linked role is deleted from your account, you can start faster launching for EC2 Windows instances for another Windows AMI to re\-create the role in your account\. Alternatively, you can stop faster launching for EC2 Windows instances for your current AMI, and then start it again\. However, stopping the feature results in your AMI using the standard launch process for all new instances while Amazon EC2 removes all of your pre\-provisioned snapshots\. After all of the pre\-provisioned snapshots are gone, you can start using faster launching for EC2 Windows instances for your AMI again\.

Amazon EC2 does not allow you to edit the `AWSServiceRoleForEC2FastLaunch` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role by using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

You can delete a service\-linked role only after first deleting all of the related resources\. This protects the Amazon EC2 resources that are associated with your AMI with faster launching enabled, because you can't inadvertently remove permission to access the resources\.

Amazon EC2 supports the faster launching for EC2 Windows instances service\-linked role in all of the Regions where the Amazon EC2 service is available\. For more information, see [Regions](using-regions-availability-zones.md#concepts-regions)\.

### Permissions granted by `AWSServiceRoleForEC2FastLaunch`<a name="slr-permissions-granted-win-faster-launching"></a>

Amazon EC2 uses the `EC2FastLaunchServiceRolePolicy` managed policy to complete the following actions:
+ `cloudwatch:PutMetricData` – Post metric data associated with faster launching for EC2 Windows instances to the Amazon EC2 namespace\.
+ `ec2:CreateLaunchTemplate` – Create a launch template for your AMI with faster launching enabled\.
+ `ec2:CreateSnapshot` – Create pre\-provisioned snapshots for your AMI with faster launching enabled\.
+ `ec2:CreateTags` – Create tags for resources that are associated with launching and pre\-provisioning Windows instances for your AMI with faster launching enabled\.
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
+ `ec2:RunInstances` – Launch instances from an AMI with faster launching enabled, in order to perform provisioning steps\.
+ `ec2:StopInstances` – Stop instances that were launched from an AMI with faster launching enabled, in order to create pre\-provisioned snapshots\.
+ `ec2:TerminateInstances` – Terminate an instance that was launched from an AMI with faster launching enabled, after creating the pre\-provisioned snapshot from it\.
+ `iam:PassRole` – Allows the `AWSServiceRoleForEC2FastLaunch` service\-linked role to launch instances on your behalf using the instance profile from your launch template\.

For more information about using managed policies for Amazon EC2, see [AWS managed policies for Amazon Elastic Compute Cloud](security-iam-awsmanpol.md)\.

### Access to customer managed keys for use with encrypted AMIs and EBS snapshots<a name="win-faster-launching-slr-access-to-cust-keys"></a>

**Prerequisite**
+ To enable Amazon EC2 to access an encrypted AMI on your behalf, you must have permission for the `createGrant` action in the customer managed key\.

When you enable faster launching for EC2 Windows instances for an encrypted AMI, Amazon EC2 ensures that permission is granted for the `AWSServiceRoleForEC2FastLaunch` role to use the customer managed key to access your AMI\. This permission is needed to launch instances and create pre\-provisioned snapshots on your behalf\.