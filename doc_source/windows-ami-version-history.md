# AWS Windows AMIs<a name="windows-ami-version-history"></a>

AWS provides a set of publicly available AMIs that contain software configurations specific to the Windows platform\. Using these AMIs, you can quickly start building and deploying your applications using Amazon EC2\. First choose the AMI that meets your specific requirements, and then launch an instance using that AMI\. You retrieve the password for the administrator account and then log in to the instance using Remote Desktop Connection, just as you would with any other Windows server\.

When you launch an instance from a Windows AMI, the root device for the Windows instance is an Amazon Elastic Block Store \(Amazon EBS\) volume\. Windows AMIs do not support instance store for the root device\.

Windows AMIs that have been configured for faster launching are pre\-provisioned, using snapshots to launch instances up to 65% faster\. To learn more about faster launching for Windows AMIs, including how you can configure faster launching for your Windows AMI, see [Configure your AMI for faster launching](#win-ami-config-fast-launch)\.

Some Windows AMIs include an edition of Microsoft SQL Server \(SQL Enterprise Edition, SQL Server Standard, SQL Server Express, or SQL Server Web\)\. Launching an instance from a Windows AMI with Microsoft SQL Server enables you to run the instance as a database server\. Alternatively, you can launch an instance from any Windows AMI and then install the database software that you need on the instance\.

**Note**  
Microsoft no longer supports Windows Server 2003, 2008, and 2008 R2\. We recommend that you launch new EC2 instances using a supported version of Windows Server\. If you have existing EC2 instances that are running an unsupported version of Windows Server, we recommend that you upgrade those instances to a supported version of Windows Server\. For more information, see [Upgrade an Amazon EC2 Windows instance to a newer version of Windows Server](serverupgrade.md)\.

**Topics**
+ [Select an initial Windows AMI](#select-windows-ami)
+ [Configure your AMI for faster launching](#win-ami-config-fast-launch)
+ [Keep your AMIs up\-to\-date](#WinAMI_Updating)
+ [Virtualization types](#virtualization-types)
+ [Managed AWS Windows AMIs](aws-windows-ami.md)
+ [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)
+ [Deregister your Windows AMI](deregister-ami.md)
+ [Specialized Windows AMIs](ami-windows-specialized.md)
+ [AWS Windows AMI version history](ec2-windows-ami-version-history.md)

## Select an initial Windows AMI<a name="select-windows-ami"></a>

To view the Windows AMIs provided by AWS, you can use the Amazon EC2 console or [AWS Marketplace](https://aws.amazon.com/marketplace/)\. For more information, see [Find a Windows AMI](finding-an-ami.md)\.

You can also create an AMI from your own Windows computer\. For more information, see the following services:
+ [AWS Server Migration Service](https://docs.aws.amazon.com/server-migration-service/latest/userguide/)
+ [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/)

## Configure your AMI for faster launching<a name="win-ami-config-fast-launch"></a>

Windows AMIs that are optimized for faster launching use pre\-provisioned snapshots to reduce the time it takes to launch an EC2 Windows instance\. To create the pre\-provisioned snapshots, Amazon EC2 launches an instance from the Windows AMI and completes the Sysprep specialize and Windows Out of Box Experience \(OOBE\) steps, rebooting as required\. Then it stops the instance, and creates a set of reserved snapshots that are used for subsequent launches\.

The reserved snapshots are automatically replenished as they are used, depending on your settings for launch frequency\. If you expect a spike in the number of instances that are launched from your AMI – during a special event, for example – you can increase the launch frequency in advance to cover the additional instances that you'll need\. When your launch volume returns to normal, you can adjust the frequency back down\.

When you experience a higher number of launches than anticipated, you might use up the fast\-launching snapshots that you have in reserve\. This does not cause any launches to fail\. However, it can result in some instances going through the standard launch process, until snapshots can be replenished\.

You can configure Windows AMIs that you own for faster launching using the Amazon EC2 console, API, SDKs, or ec2 commands in the AWS CLI\. The following sections cover configuration steps for the Amazon EC2 console and AWS CLI\.

**Topics**
+ [Prerequisites](#win-start-fast-launch-prereqs)
+ [Start faster launching for Windows AMIs \(console\)](#win-start-fast-launch-console)
+ [Stop faster launching for Windows AMIs \(console\)](#win-stop-fast-launch-console)
+ [AWS CLI commands to configure faster launching for EC2 Windows instances](#win-fast-launch-cli-commands)
+ [Service\-linked role for faster launching for EC2 Windows instances](#slr-windows-fast-launch)
+ [Access to customer managed keys for use with encrypted AMIs and EBS snapshots](#win-faster-launching-slr-access-to-cust-keys)

### Prerequisites<a name="win-start-fast-launch-prereqs"></a>

Before you set up faster launching for EC2 Windows instances, you must verify that the following prerequisites are met:
+ Ensure that a default VPC is configured for the Region in which you use faster launching for EC2 Windows instances\. You cannot have EC2 Classic enabled in the Region, even if you have a default VPC configured\. For information and options to migrate away from EC2 Classic, see [Using a default VPC](vpc-migrate.md#get-default-vpc)\. For more information about EC2 Classic, see [EC2\-Classic Networking is Retiring \- Here's How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.
+ Your AWS account must own the Windows AMI that you use to configure faster launching for EC2 Windows instances\.
+ The Windows AMI that you use to configure faster launching for EC2 Windows instances must be created using Sysprep with the shutdown option\. AMIs that are created from an instance without running Sysprep are not currently supported\. To create an AMI using Sysprep, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

### Start faster launching for Windows AMIs \(console\)<a name="win-start-fast-launch-console"></a>

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

### Stop faster launching for Windows AMIs \(console\)<a name="win-stop-fast-launch-console"></a>

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

### AWS CLI commands to configure faster launching for EC2 Windows instances<a name="win-fast-launch-cli-commands"></a>

You can configure faster launching for EC2 Windows instances, and describe instances that have faster launching enabled, by using the following ec2 commands in the AWS CLI\. For more information about the command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**Note**  
To start, stop, or change settings for a Windows AMI, you must own the AMI\.
+ Start using faster launching for EC2 Windows instances
  + [enable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-launch.html) \(AWS CLI\)
+ Stop using faster launching for EC2 Windows instances
  + [disable\-fast\-launch](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-launch.html) \(AWS CLI\)
+ Describe images that have faster launching for EC2 Windows instances enabled
  + [describe\-fast\-launch\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-launch-images.html) \(AWS CLI\)

### Service\-linked role for faster launching for EC2 Windows instances<a name="slr-windows-fast-launch"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information about how Amazon EC2 uses IAM roles, including service\-linked roles, see [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\.

Amazon EC2 uses the service\-linked role named `AWSServiceRoleForEC2FastLaunch` to create and manage a set of pre\-provisioned snapshots that reduce the time it takes to launch instances from your Windows AMI\.

You don't need to create this service\-linked role manually\. When you start using faster launching for EC2 Windows instances for your AMI, Amazon EC2 creates the service\-linked role for you, if it does not already exist\.

**Note**  
If the service\-linked role is deleted from your account, you can start faster launching for EC2 Windows instances for another Windows AMI to re\-create the role in your account\. Alternatively, you can stop faster launching for EC2 Windows instances for your current AMI, and then start it again\. However, stopping the feature results in your AMI using the standard launch process for all new instances while Amazon EC2 removes all of your pre\-provisioned snapshots\. After all of the pre\-provisioned snapshots are gone, you can start using faster launching for EC2 Windows instances for your AMI again\.

Amazon EC2 does not allow you to edit the `AWSServiceRoleForEC2FastLaunch` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role by using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

You can delete a service\-linked role only after first deleting all of the related resources\. This protects the Amazon EC2 resources that are associated with your faster launching\-enabled AMI because you can't inadvertently remove permission to access the resources\.

Amazon EC2 supports the faster launching for EC2 Windows instances service\-linked role in all of the Regions where the Amazon EC2 service is available\. For more information, see [Regions](using-regions-availability-zones.md#concepts-regions)\.

#### Permissions granted by `AWSServiceRoleForEC2FastLaunch`<a name="slr-permissions-granted-win-faster-launching"></a>

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

## Keep your AMIs up\-to\-date<a name="WinAMI_Updating"></a>

AWS provides updated, fully\-patched Windows AMIs within five business days of Microsoft's patch Tuesday \(the second Tuesday of each month\)\. For more information, see [Details about AWS Windows AMI versions](aws-windows-ami.md#windows-ami-versions)\.

The AWS Windows AMIs contain the latest security updates available at the time they were created\. For more information, see [Patches, security updates, and AMI IDs](aws-windows-ami.md#ami-patches-security-ID)\.

## Virtualization types<a name="virtualization-types"></a>

AMIs use one of two types of virtualization: paravirtual \(PV\) or hardware virtual machine \(HVM\)\. The main differences between PV and HVM AMIs are the way in which they boot and whether they can take advantage of special hardware extensions for better performance\. Windows AMIs are HVM AMIs\.

HVM AMIs are presented with a fully virtualized set of hardware and boot by executing the master boot record of the root block device of your image\. This virtualization type provides the ability to run an operating system directly on top of a virtual machine without any modification, as if it were run on the bare\-metal hardware\. The Amazon EC2 host system emulates some or all of the underlying hardware that is presented to the guest\.

HVM guests can take advantage of hardware extensions that provide fast access to the underlying hardware on the host system\. HVM AMIs are required to take advantage of enhanced networking and GPU processing\. In order to pass through instructions to specialized network and GPU devices, the OS needs to be able to have access to the native hardware platform; HVM virtualization provides this access\.

Paravirtual guests traditionally performed better with storage and network operations than HVM guests because they could leverage special drivers for I/O that avoided the overhead of emulating network and disk hardware, whereas HVM guests had to translate these instructions to emulated hardware\. Now PV drivers are available for HVM guests, so Windows instances can get performance advantages in storage and network I/O by using them\. With these PV on HVM drivers, HVM guests can get the same performance as paravirtual guests, or better\.