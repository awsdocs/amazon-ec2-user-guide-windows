# Perform an automated upgrade<a name="automated-upgrades"></a>

You can perform an automated upgrade of your Windows and SQL Server instances on AWS with AWS Systems Manager Automation runbooks\. 

**Topics**
+ [Related services](#automated-related)
+ [Execution options](#automated-execution-option)
+ [Upgrade Windows Server](#automated-upgrades-windows)
+ [Upgrade SQL Server](#automated-upgrades-sql)

## Related services<a name="automated-related"></a>

The following AWS services are used in the automated upgrade process:
+ **AWS Systems Manager**\. AWS Systems Manager is a powerful, unified interface for centrally managing your AWS resources\. For more information, see the *[AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)*\.
+ AWS Systems Manager Agent \(SSM Agent\) is Amazon software that can be installed and configured on an Amazon EC2 instance, an on\-premises server, or a virtual machine \(VM\)\. SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources\. The agent processes requests from the Systems Manager service in the AWS Cloud, and then runs them as specified in the request\. For more information, see [Working with SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) in the *AWS Systems Manager User Guide*\.
+ **AWS Systems Manager SSM runbooks**\. An SSM runbook defines the actions that Systems Manager performs on your managed instances\. SSM runbooks use JavaScript Object Notation \(JSON\) or YAML, and they include steps and parameters that you specify\. This topic uses two Systems Manager SSM runbooks for automation\. For more information, see [AWS Systems Manager Automation runbook reference](https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-runbook-reference.html) in the *AWS Systems Manager User Guide*\.

## Execution options<a name="automated-execution-option"></a>

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

  In AWS, a resource is an entity you can work with\. Examples include Amazon EC2 instances, AWS CloudFormation stacks, or Amazon S3 buckets\. If you work with multiple resources, it might be useful to manage them as a group as opposed to moving from one AWS service to another for every task\. In some cases, you may want to manage large numbers of related resources, such as EC2 instances that make up an application layer\. In this case, you will likely need to perform bulk actions on these resources at one time\.
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

## Upgrade Windows Server<a name="automated-upgrades-windows"></a>

The `[AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html)` runbook creates an Amazon Machine Image \(AMI\) from a Windows Server instance in your account and upgrades this AMI to a supported version of your choice\. This multi\-step process can take up to two hours to complete\.

To upgrade your Windows Server 2008 R2 instance to Windows Server 2016, 2019, or 2022, an in\-place upgrade is performed twice, first from Windows Server 2008 R2 to Windows Server 2012 R2, and then from Windows Server 2012 R2 to Windows Server 2016, 2019, or 2022\. Directly upgrading Windows Server 2008 R2 to Windows Server 2016, 2019, or 2022 is not supported\. 

In this workflow, the automation creates an AMI from the instance and then launches the new AMI in the subnet you provide\. The automation workflow performs an in\-place upgrade from Windows Server 2008 R2, 2016, 2019 to the selected version \(Windows Server 2012 R2, 2016, 2019, or 2022\)\. It also updates or installs the AWS drivers required by the upgraded instance\. After the upgrade is complete, the workflow creates a new AMI and terminates the upgraded instance\. If you upgrade from Windows Server 2008 R2 to Windows Server 2016, 2019, or 2022, the automation creates two AMIs because the in\-place upgrade is performed twice\.

There are two AMIs included in the automated upgrade process:
+ **Current running instance**\. The first AMI is the current running instance, which is not upgraded\. This AMI is used to launch another instance to run the in\-place upgrade\. When the process is complete, this AMI is deleted from your account, unless you specifically request to keep the original instance\. This setting is handled by the parameter `KeepPreUpgradeImageBackUp` \(default value is `false`, which means the AMI is deleted by default\)\.
+ **Upgraded AMI**\. This AMI is the outcome of the automation process\. 

The final result is one AMI, which is the upgraded instance of the AMI\.

When the upgrade is complete, you can test your application functionality by launching the new AMI in your Amazon VPC\. After testing, and before you perform another upgrade, schedule application downtime before completely switching to the upgraded instance\.

### Windows Server automated upgrade paths<a name="automated-windows-paths"></a>

The Systems Manager Automation runbook [AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html) supports the following upgrade paths:
+ Windows Server 2008 R2 to Windows Server 2012 R2
+ Windows Server 2012 R2 to Windows Server 2016
+ Windows Server 2012 R2 to Windows Server 2019
+ Windows Server 2012 R2 to Windows Server 2022
+ Windows Server 2016 to Windows Server 2019
+ Windows Server 2016 to Windows Server 2022
+ Windows Server 2019 to Windows Server 2022

### Prerequisites<a name="automated-prereq-windows"></a>

In order to automate your Windows Server upgrade with the AWS Systems Manager Automation document, you must perform the following tasks:
+ Create an IAM role with the specified IAM policies to allow Systems Manager to perform automation tasks on your Amazon EC2 instances and verify that you meet the prerequisites to use Systems Manager\. For more information, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *AWS Identity and Access Management User Guide*\.
+ [Select the option for how you want the automation to be run](#automated-execution-option)\. The options for execution are **Simple execution**, **Rate control**, **Multi\-account and Region**, and **Manual execution**\. For more information about these options, see [Execution options](#automated-execution-option)\. 
+ Verify that SSM Agent is installed on your instance\. For more information see [Installing and configuring SSM Agent on Amazon EC2 instances for Windows Server](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)\. 
+ Windows PowerShell 3\.0 or later must be installed on your instance\.
+ For instances that are joined to a Microsoft Active Directory domain, we recommend specifying a `SubnetId` that does not have connectivity to your domain controllers to help avoid hostname conflicts\.
+ The `SubnetId` specified must be a public subnet with the auto\-assign public IPv4 address set to true\. For more information, see [Modifying the Public IPv4 Addressing Attribute for Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the *Amazon VPC User Guide*\.
+ This Automation works with only Windows Server 2008 R2, 2012 R2, 2016, and 2019 instances\.
+ This Automation works on only Amazon EC2 instances with an unencrypted Amazon EBS root volume\. If the specified instance has an encrypted root volume, the automation fails\.
+ Verify that the instance has 20 GB of free disk space in the boot disk\.
+ If the instance does not use a Windows license provided by AWS, then specify an Amazon EBS snapshot ID that includes Windows Server 2012 R2 installation media\. To do this:

  1. Verify that the Amazon EC2 instance is running Windows Server 2012 or later\.

  1. Create a 6 GB Amazon EBS volume in the same Availability Zone where the instance is running\. Attach the volume to the instance\. Mount it, for example, as drive D\.

  1. Right\-click the ISO and mount it to an instance as, for example, drive E\. 

  1. Copy the content of the ISO from drive E:\\ to drive D:\\

  1. Create an Amazon EBS snapshot of the 6 GB volume created in step 2 above\.

### Windows Server upgrade limitations<a name="automated-windows-limits"></a>

This automation doesn't support upgrading Windows domain controllers, clusters, or Windows desktop operating systems\. In addition, this automation doesn't support Amazon EC2 instances for Windows Server with the following roles installed:
+ Remote Desktop Session Host \(RDSH\)
+ Remote Desktop Connection Broker \(RDCB\) 
+ Remote Desktop Virtualization Host \(RDVH\) 
+ Remote Desktop Web Access \(RDWA\)

### Steps to perform an automated upgrade of Windows Server<a name="2008R2-2012R2"></a>

Follow these steps to upgrade your Windows Server instance using the [AWSEC2\-CloneInstanceAndUpgradeWindows](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeWindows.html) automation runbook\. 

1. Open Systems Manager from the **AWS Management Console**\.

1. From the left navigation pane, under **Change Management**, choose **Automation**\.

1. Choose **Execute automation**\.

1. Search for the automation document called `AWSEC2-CloneInstanceAndUpgradeWindows`\.

1. When the document name appears, select it\. When you select it, the document details appear\. 

1. Choose **Execute automation** to input the parameters for this document\. Leave **Simple execution** selected at the top of the page\.

1. Enter the requested parameters based on the following guidance\.
   + `InstanceID`

     **Type:** String

     \(Required\) The instance running Windows Server 2008 R2, 2012 R2, 2016, or 2019 with the SSM agent installed\.
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

1. After you have entered the parameters, choose **Execute**\. When the automation begins, you can monitor the execution progress\.

1. When the automation completes, you will see the AMI ID\. You can launch the AMI to verify that the Windows OS is upgraded\.
**Note**  
It is not necessary for the automation to run all of the steps\. The steps are conditional based on the behavior of the automation and instance\. Systems Manager might skip some steps that are not required\.  
Additionally, some steps may time out\. Systems Manager attempts to upgrade and install all of the latest patches\. Sometimes, however, patches time out based on a definable timeout setting for the given step\. When this happens, the Systems Manager automation continues to the next step to ensure that the internal OS is upgraded to the target Windows Server version\.

1. After the automation completes, you can launch an Amazon EC2 instance using the AMI ID to review your upgrade\. For more information about how to create an Amazon EC2 instance from an AWS AMI, see [ How do I launch an EC2 instance from a custom Amazon Machine Image \(AMI\)?](http://aws.amazon.com/premiumsupport/knowledge-center/launch-instance-custom-ami/)

## Upgrade SQL Server<a name="automated-upgrades-sql"></a>

The `[AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html)` script creates an AMI from an Amazon EC2 instance running SQL Server in your account, and then upgrades the AMI to a later version of SQL Server\. This multi\-step process can take up to two hours to complete\.

In this workflow, the automation creates an AMI from the instance and then launches the new AMI in the subnet you provide\. The automation then performs an in\-place upgrade of SQL Server\. After the upgrade is complete, the automation creates a new AMI before terminating the upgraded instance\. 

There are two AMIs included in the automated upgrade process:
+ **Current running instance**\. The first AMI is the current running instance, which is not upgraded\. This AMI is used to launch another instance to run the in\-place upgrade\. When the process is complete, this AMI is deleted from your account, unless you specifically request to keep the original instance\. This setting is handled by the parameter `KeepPreUpgradeImageBackUp` \(default value is `false`, which means the AMI is deleted by default\)\.
+ **Upgraded AMI**\. This AMI is the outcome of the automation process\.

The final result is one AMI, which is the upgraded instance of the AMI\.

When the upgrade is complete, you can test your application functionality by launching the new AMI in your Amazon VPC\. After testing, and before you perform another upgrade, schedule application downtime before completely switching to the upgraded instance\.

### SQL Server automated upgrade paths<a name="automated-sql-paths"></a>

The [AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html) automation runbook supports the following upgrade paths:
+ SQL Server 2008 to SQL Server 2017, 2016, or 2014
+ SQL Server 2008 R2 to SQL Server 2017, 2016, or 2014
+ SQL Server 2012 to SQL Server 2019, 2017, 2016, or 2014
+ SQL Server 2014 to SQL Server 2019, 2017, or 2016
+ SQL Server 2016 to SQL Server 2019 or 2017
+ SQL Server 2017 to SQL Server 2019

### Prerequisites<a name="automated-prereq-sql"></a>

In order to automate your SQL Server upgrade with the AWS Systems Manager Automation document, you must perform the following tasks:
+ Create an IAM role with the specified IAM policies to allow Systems Manager to perform automation tasks on your Amazon EC2 instances and verify that you meet the prerequisites to use Systems Manager\. For more information, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *AWS Identity and Access Management User Guide*\.
+ [Select the option for how you want the automation to be run](#automated-execution-option)\. The options for execution are **Simple execution**, **Rate control**, **Multi\-account and Region**, and **Manual execution**\. For more information about these options, see [Execution options](#automated-execution-option)\.
+ The Amazon EC2 instance must use Windows Server 2008 R2 or later and SQL Server 2008 or later\.
+ Verify that SSM Agent is installed on your instance\. For more information, see [Working with SSM Agent on Amazon EC2 instances for Windows Server](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)\.
+ Verify that the instance has 20 GB of free disk space in the instance boot disk\.
+ For instances that use a Bring Your Own License \(BYOL\) SQL Server version, the following additional prerequisites apply:
  + Provide an Amazon EBS snapshot ID that includes the target SQL Server installation media\. To do this: 

    1. Verify that the Amazon EC2 instance is running Windows Server 2008 R2 or later\. 

    1. Create a 6 GB Amazon EBS volume in the same Availability Zone where the instance is running\. Attach the volume to the instance\. Mount it, for example, as drive D\. 

    1. Right\-click the ISO and mount it to an instance as, for example, drive E\. 

    1. Copy the content of the ISO from drive E:\\ to drive D:\\ 

    1. Create an Amazon EBS snapshot of the 6 GB volume created in step 2\. 

### SQL Server automated upgrade limitations<a name="automated-sql-limits"></a>

The following limitations apply when using the [AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html) runbook to perform an automated upgrade:
+ The upgrade can be performed on only a SQL Server using Windows authentication\.
+ Verify that no security patch updates are pending on the instances\. Open **Control Panel**, then choose **Check for updates**\.
+ SQL Server deployments in HA and mirroring mode are not supported\.

### Steps to perform an automated upgrade of SQL Server<a name="SQL2008R2-SQL2016"></a>

Follow these steps to upgrade your SQL Server using the [AWSEC2\-CloneInstanceAndUpgradeSQLServer](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-CloneInstanceAndUpgradeSQLServer.html) automation runbook\. 

1. If you haven't already, download the SQL Server 2016 \.iso file and mount it to the source server\. 

1. After the \.iso file is mounted, copy all of the component files and place them on any volume of your choice\. 

1. Take an Amazon EBS snapshot of the volume and copy the snapshot ID onto a clipboard for later use\. For more information about creating an EBS snapshot, see [Create Amazon EBS snapshots](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html)\.

1. Attach the instance profile to the Amazon EC2 source instance\. This allows Systems Manager to communicate with the EC2 instance and run commands on it after it is added to the AWS Systems Manager service\. For this example, we named the role `SSM-EC2-Profile-Role` with the `AmazonSSMManagedInstanceCore ` policy attached to the role\. See [Create an IAM instance profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\.

1. In the AWS Systems Manager console, in the left navigation pane, choose **Managed Instances**\. Verify that your EC2 instance is in the list of managed instance\. If you don't see your instance after a few minutes, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) in the *AWS Systems Manager User Guide*\.

1. In the left navigation pane, under **Change Management** choose **Automation**\.

1. Choose **Execute automation**\.

1. Search for the automation document called `AWSEC2-CloneInstanceAndUpgradeSQLServer`\.

1. Choose the `AWSEC2-CloneInstanceAndUpgradeSQLServer` SSM document, and then choose **Next**\. 

1. Ensure that the **Simple execution **option is selected\.

1. Enter the requested parameters based on the following guidance\.
   + `InstanceId` 

     **Type:** String

     \(Required\) The instance running SQL Server 2008 R2 \(or later\)\. 
   + `IamInstanceProfile`

     **Type:** String

     \(Required\) The IAM instance profile\.
   + `SQLServerSnapshotId`

     **Type:** String

     \(Required\) The Snapshot ID for the target SQL Server installation media\. This parameter is not required for SQL Server license\-included instances\.
   + `SubnetId`

     **Type:** String

     \(Required\) This is the subnet for the upgrade process and where your source EC2 instance resides\. Verify that the subnet has outbound connectivity to AWS services, including Amazon S3, and also to Microsoft \(in order to download patches\)\. 
   + `KeepPreUpgradedBackUp`

     **Type:** String

     \(Optional\) If this parameter is set to `true`, the automation retains the image created from the instance\. The default setting is `false`\. 
   + `RebootInstanceBeforeTakingImage`

     **Type:** String

     \(Optional\) The default is `false` \(no reboot\)\. If this parameter is set to `true`, Systems Manager reboots the instance before creating an AMI for the upgrade\.
   + `TargetSQLVersion`

     **Type:** String

     \(Optional\) The target SQL Server version\. The default is `2016`\.

1. After you have entered the parameters, choose **Execute**\. When the automation begins, you can monitor the execution progress\.

1. When **Execution status** shows **Success**, expand **Outputs** to view the AMI information\. You can use the AMI ID to launch your SQL Server instance for the VPC of your choice\.

1. Open the Amazon EC2 console\. In the left navigation pane, choose **AMIs**\. You should see the new AMI\.

1. To verify that the new SQL Server version has been successfully installed, choose the new AMI and choose **Launch**\.

1. Choose the type of instance that you want for the AMI, the VPC and subnet that you want to deploy to, and the storage that you want to use\. Because you're launching the new instance from an AMI, the volumes are presented to you as an option to include within the new EC2 instance you are launching\. You can remove any of these volumes, or you can add volumes\.

1. Add a tag to help you identify your instance\.

1. Add the security group or groups to the instance\.

1. Choose **Launch Instance**\.

1. Choose the tag name for the instance and select **Connect** under the **Actions** dropdown\. 

1. Verify that the new SQL Server version is the database engine on the new instance\.