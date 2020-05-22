# Performing an automated upgrade<a name="automated-upgrades"></a>

You can perform an automated upgrade on your Windows Server 2008 R2, Windows Server 2012 R2, Windows Server 2016, and SQL Server 2008 R2 with Service Pack 3 instances on AWS with AWS Systems Manager Automation documents\. 

The Systems Manager Automation documents provide two upgrade paths:
+ Windows Server 2008 R2, 2012 R2, or 2016 to Windows Server 2012 R2, 2016, or 2019 using the Systems Manager document for Automation named [AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html)
+ SQL Server 2008 R2 on Windows Server 2012 R2 to SQL Server 2016 using the Systems Manager document for Automation named [AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html) 

**Topics**
+ [Related services](#automated-related)
+ [Prerequisites](#automated-prereq)
+ [Upgrade paths](#upgrade-paths)
+ [Steps for performing an automated upgrade](#upgrade-steps-auto)

## Related services<a name="automated-related"></a>

The following AWS services are used in the automated upgrade process:
+ **AWS Systems Manager**\. AWS Systems Manager is a powerful, unified interface for centrally managing your AWS resources\. For more information, see the *[AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)*\.
+ AWS Systems Manager Agent \(SSM Agent\) is Amazon software that can be installed and configured on an Amazon EC2 instance, an on\-premises server, or a virtual machine \(VM\)\. SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources\. The agent processes requests from the Systems Manager service in the AWS Cloud, and then runs them as specified in the request\. For more information, see [Working with SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) in the *AWS Systems Manager User Guide*\.
+ **AWS Systems Manager SSM documents**\. An SSM document defines the actions that Systems Manager performs on your managed instances\. SSM documents use JavaScript Object Notation \(JSON\) or YAML, and they include steps and parameters that you specify\. This topic uses two Systems Manager SSM documents for Automation\. For more information, see [AWS Systems Manager Documents](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-ssm-docs.html) in the *AWS Systems Manager User Guide*\.

## Prerequisites<a name="automated-prereq"></a>

In order to automate your upgrade with AWS Systems Manager Automation documents, you must perform the following tasks:
+ [Create an IAM role with the specified IAM policies](#automated-iam) to allow Systems Manager to perform automation tasks on your Amazon EC2 instances and verify that you meet the prerequisites to use Systems Manager\.
+ [Select the option for how you want the automation to be executed](#automated-execution-option)\. The options for execution are **Simple execution**, **Rate control**, **Multi\-account and Region**, and **Manual execution**\. 

### Create IAM role with specified permissions<a name="automated-iam"></a>

For steps on how to create an IAM role in order to allow AWS Systems Manager to access resources on your behalf, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\. This topic also contains information on how to verify that your account meets the prerequisites to use Systems Manager\.

### Select execution option<a name="automated-execution-option"></a>

When you select **Automation** on the Systems Manager console, select **Execute**\. After you select an Automation document, you are then prompted to choose an automation execution option\. You choose from the following options\. In the steps for the paths provided later in this topic, we use the **Simple execution** option\.

**Simple execution**  
Choose this option if you want to update a single instance but do not want to go through each automation step to audit the results\. This option is explained in further detail in the upgrade steps that follow\.

**Rate control**

Choose this option if you want to apply the upgrade to more than one instance\. You define the following settings\.
+ **Parameter**

  This setting, which is also set in Multi\-Account and Region settings, defines how your automation branches out\.
+ **Targets**

  Select the target to which you want to apply the automation\. This setting is also set in Multi\-Account and Region settings\.
+ **Parameter Values**

  Use the values defined in the automation document parameters\.
+ **Resource Group**

  In AWS, a resource is an entity you can work with\. Examples include Amazon EC2 instances, AWS CloudFormation stacks, or Amazon S3 buckets\. If you work with multiple resources, it may be useful to manage them as a group as opposed to moving from one AWS service to another for every task\. In some cases, you may want to manage large numbers of related resources, such as EC2 instances that make up an application layer\. In this case, you will likely need to perform bulk actions on these resources at one time\.
+ **Tags**

  Tags help you categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. This categorization is useful when you have many resources of the same type\. You can quickly identify a specific resource using the assigned tags\.
+ **Rate Control**

  Rate Control is also set in Multi\-Account and Region settings\. When you set the rate control parameters, you define how many of your fleet to apply the automation to, either by target count or by percentage of the fleet\.

 **Multi\-Account and Region**

In addition to the parameters specified under Rate Control that are also used in the Multi\-Account and Region settings, there are two additional settings: 
+ **Accounts and organizational units \(OUs\)**

  Specify multiple accounts on which you want to run the automation\.
+ **AWS Regions**

  Specify multiple AWS Regions where you want to run the automation\.

**Manual execution**  
This option is similar to **Simple execution**, but allows you to step through each automation step and audit the results\.

## Upgrade paths<a name="upgrade-paths"></a>

There are two upgrade paths, which use two different AWS Systems Manager Automation documents\.
+ `[AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html)`\. This script creates an Amazon Machine Image \(AMI\) from a Windows Server 2008 R2, 2012 R2, or 2016 instance in your account and upgrades this AMI to a supported version of your choice \(Windows Server 2012 R2, 2016, or 2019\)\. This multi\-step process can take up to two hours to complete\.

  To upgrade your Windows Server 2008 R2 instance to Windows Server 2016 or 2019, an in\-place upgrade is performed twice, first from Windows Server 2008 R2 to Windows Server 2012 R2, and then from Windows Server 2012 R2 to Windows Server 2016 or 2019\. Directly upgrading Windows Server 2008 R2 to Windows Server 2016 or 2019 is not supported\. 

  In this workflow, the automation creates an AMI from the instance and then launches the new AMI in the subnet you provide\. The automation workflow performs an in\-place upgrade from Windows Server 2008 R2, 2012 R2 or 2016 to the selected version \(Windows Server 2012 R2, 2016, or 2019\)\. It also updates or installs the AWS drivers required by the upgraded instance\. After the upgrade is complete, the workflow creates a new AMI and terminates the upgraded instance\. If you upgrade from Windows Server 2008 R2 to Windows Server 2016 or 2019, the automation creates two AMIs because the in\-place upgrade is performed twice\.
+ `[AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html)`\. This script creates an AMI from an Amazon EC2 instance running SQL Server 2008 R2 SP3 in your account, and then upgrades the AMI to SQL Server 2016 SP2\. This multi\-step process can take up to two hours to complete\.

  In this workflow, the automation creates an AMI from the instance and then launches the new AMI in the subnet you provide\. The automation then performs an in\-place upgrade of SQL Server 2008 R2 to SQL Server 2016 SP2\. After the upgrade is complete, the automation creates a new AMI before terminating the upgraded instance\. 

  There are two AMIs included in the automated upgrade process:
  + **Current running instance**\. The first AMI is the current running instance, which is not upgraded\. This AMI is used to launch another instance to run the in\-place upgrade\. When the process is complete, this AMI is deleted from your account, unless you specifically request to keep the original instance\. This setting is handled by the parameter `KeepPreUpgradeImageBackUp` \(default value is `false`, which means the AMI is deleted by default\)\.
  + **Upgraded AMI**\. This AMI is the outcome of the automation process\. The second AMI includes SQL Server 2016 SP2 instead of SQL Server 2008 R2\. 

  The final result is one AMI, which is the upgraded instance of the AMI\.

  When the upgrade is complete, you can test your application functionality by launching the new AMI in your VPC\. After testing, and before you perform another upgrade, schedule application downtime before completely switching to the upgraded instance\.

## Steps for performing an automated upgrade<a name="upgrade-steps-auto"></a>

**Topics**
+ [Upgrade Windows Server 2008 R2, 2012 R2, or 2016 to Windows Server 2012 R2, 2016, or 2019](#2008R2-2012R2)
+ [Upgrade SQL Server 2008 R2 to SQL Server 2016](#SQL2008R2-SQL2016)

### Upgrade Windows Server 2008 R2, 2012 R2, or 2016 to Windows Server 2012 R2, 2016, or 2019<a name="2008R2-2012R2"></a>

This upgrade path requires additional prerequisites to work successfully\. These prerequisites can be found in the automation document details for [AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html) in the *AWS Systems Manager User Guide*\. 

After you have verified the additional prerequisite tasks, follow these steps to upgrade your Windows 2008 R2 instance to Windows 2012 R2 by using the automation document on AWS Systems Manager\. 

1. Open Systems Manager from the **AWS Management Console**\.

1. From the left navigation pane, choose **Automation**\.

1. Choose **Execute automation**\.

1. Search for the automation document called `AWSEC2-CloneInstanceAndUpgradeWindows`\.

1. When the document name appears, select it\. When you select it, the document details appear\. 

1. Select **Next** to input the parameters for this document\. Leave **Simple execution** selected at the top of the page\.

1. Enter the requested parameters based on the following guidance\.
   + `InstanceID`

     **Type:** String

     \(Required\) The instance running Windows Server 2008 R2, 2012 R2, or 2016 with the SSM agent installed\.
   + `InstanceProfile`\. 

     **Type:** String

     \(Required\) The IAM instance profile\. This is the IAM role used to perform the Systems Manager automation against the Amazon EC2 instance and AWS AMIs\. For more information, see [Create an IAM Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\.
   + `TargetWindowsVersion`

     **Type:** String

     \(Required\) Select the target Windows version\.
   + `SubnetId`

     **Type:** String

     \(Required\) This is the subnet for the upgrade process and where your source EC2 instance resides\. Verify that the subnet has outbound connectivity to AWS services, including Amazon S3, and also to Microsoft \(in order to download patches\)\. 
   + `KeepPreUpgradedBackUp`

     **Type:** String

     \(Optional\) If this parameter is set to `true`, the automation retains the image created from the instance\. The default setting is `false`\. 
   + `RebootInstanceBeforeTakingImage`

     **Type:** String

     \(Optional\) The default is `false` \(no reboot\)\. If this parameter is set to `true`, Systems Manager reboots the instance before creating an AMI for the upgrade\.

1. After you have entered the parameters, select **Execute**\. When the automation begins, you can monitor the execution progress\.

1. When the automation completes, you will see the AMI ID\. You can launch the AMI to verify that the Windows OS is upgraded\.
**Note**  
It is not necessary for the automation to run all of the steps\. The steps are conditional based on the behavior of the automation and instance\. Systems Manager might skip some steps that are not required\.  
Additionally, some steps may time out\. Systems Manager attempts to upgrade and install all of the latest patches\. Sometimes, however, patches time out based on a definable timeout setting for the given step\. When this happens, the Systems Manager automation continues to the next step to ensure that the internal OS is upgraded to the target Windows Server version\.

1. After the automation completes, you can launch an Amazon EC2 instance using the AMI ID to review your upgrade\. For more information about how to create an Amazon EC2 instance from an AWS AMI, see [ How do I launch an EC2 instance from a custom Amazon Machine Image \(AMI\)?](https://aws.amazon.com/premiumsupport/knowledge-center/launch-instance-custom-ami/)

### Upgrade SQL Server 2008 R2 to SQL Server 2016<a name="SQL2008R2-SQL2016"></a>

This upgrade path requires additional prerequisites to work successfully\. These prerequisites can be found in the automation document details for [AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html) in the *AWS Systems Manager User Guide*\.

After you have verified the additional prerequisite tasks, follow these steps to upgrade your SQL Server 2008 R2 database engine to SQL Server 2016 using the automation document on AWS Systems Manager\. 

1. If you haven't already, download the SQL Server 2016 \.iso file and mount it to the source server\. 

1. After the \.iso file is mounted, copy all of the component files and place them on any volume of your choice\. 

1. Take an EBS snapshot of the volume and copy the snapshot ID onto a clipboard for later use\. For more information about creating an EBS snapshot, see [Creating an EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html) in the *Amazon Elastic Compute Cloud User Guide*\.

1. Attach the instance profile to the EC2 source instance\. This allows Systems Manager to communicate with the EC2 instance and run commands on it after it is added to the AWS Systems Manager service\. For this example, we named the role `SSM-EC2-Profile-Role` with the `AmazonSSMManagedInstanceCore ` policy attached to the role\. See [Create an IAM Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\.

1. In the AWS Systems Manager console, in the left navigation pane, choose **Managed Instances**\. Verify that your EC2 instance is in the list of managed instance\. If you don't see your instance after a few minutes, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) in the *AWS Systems Manager User Guide*\.

1. In the left navigation pane, choose **Automation**\.

1. Choose **Execute automation**\.

1. Choose the button beside the `AWSEC2-CloneInstanceAndUpgradeSQLServer` SSM document, and then choose **Next**\. 

1. Ensure that the **Simple execution **option is selected\.

1. Enter the requested parameters based on the following guidance\.
   + `InstanceId` 

     **Type:** String

     \(Required\) The instance running SQL Server 2008 R2 \(or later\)\. 
   + `IamInstanceProfile`

     **Type:** String

     \(Required\) The IAM instance profile\.
   + `SnapshotId`

     **Type:** String

     \(Required\) The Snapshot ID for SQL Server 2016 installation media\.
   + `SubnetId`

     **Type:** String

     \(Required\) This is the subnet for the upgrade process and where your source EC2 instance resides\. Verify that the subnet has outbound connectivity to AWS services, including Amazon S3, and also to Microsoft \(in order to download patches\)\. 
   + `KeepPreUpgradedBackUp`

     **Type:** String

     \(Optional\) If this parameter is set to `true`, the automation retains the image created from the instance\. The default setting is `false`\. 
   + `RebootInstanceBeforeTakingImage`

     **Type:** String

     \(Optional\) The default is `false` \(no reboot\)\. If this parameter is set to `true`, Systems Manager reboots the instance before creating an AMI for the upgrade\.

1. After you have entered the parameters, choose **Execute**\. When the automation begins, you can monitor the execution progress\.

1. When **Execution status** shows **Success**, expand **Outputs** to view the AMI information\. You can use the AMI ID to launch your SQL Server 2016 instance for the VPC of your choice\.

1. Open the EC2 console\. In the left navigation pane, choose **AMIs**\. You should see the new AMI\.

1. To verify that SQL Server 2016 has been successfully installed, choose the new AMI and choose **Launch**\.

1. Choose the type of instance that you want for the AMI, the VPC and subnet that you want to deploy to, and the storage that you want to use\. Because you're launching the new instance from an AMI, the volumes are presented to you as an option to include within the new EC2 instance you are launching\. You can remove any of these volumes, or you can add volumes\.

1. Add a tag to help you identify your instance\.

1. Add the security group or groups to the instance\.

1. Choose **Launch Instance**\.

1. Choose the tag name for the instance and select **Connect** under the **Actions** dropdown\. 

1. Verify that SQL Server 2016 is the new database engine on the new instance\.