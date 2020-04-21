# Managing EC2 Instances Using AWS Systems Manager for Microsoft SCVMM<a name="scvmm-manage-ec2"></a>

After you log in to the Systems Manager console using your AWS credentials, you can manage your EC2 instances\.

**Topics**
+ [Creating an EC2 Instance](#create-instances)
+ [Viewing Your Instances](#view-instances)
+ [Connecting to Your Instance](#connect-instances)
+ [Rebooting Your Instance](#reboot-instance)
+ [Stopping Your Instance](#stop-instance)
+ [Starting Your Instance](#start-instance)
+ [Terminating Your Instance](#terminate-instance)

## Creating an EC2 Instance<a name="create-instances"></a>

The permissions that you've been granted by your administrator determine whether you can create instances\.

**Prerequisites**
+ A virtual private cloud \(VPC\) with a subnet in the Availability Zone where you'll launch the instance\. For more information about creating a VPC, see the [Amazon VPC Getting Started Guide](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/)\.

**To create an EC2 instance**

1. Open SCVMM\.

1. On the ribbon, click **Create Amazon EC2 Instance**\.

1. Complete the **Create Amazon EC2 Instance** dialog box as follows:

   1. Select a Region for your instance\. By default, we select the Region that you configured as your default Region\.

   1. Select a template \(known as an AMI\) for your instance\. To use an AMI provided by Amazon, select **Windows** or **Linux** and then select an AMI from **Image**\. To use an AMI that you created, select **My images** and then select the AMI from **Image**\.

   1. Select an instance type for the instance\. First, select one of the latest instance families from **Family**, and then select an instance type from **Instance type**\. To include previous generation instance families in the list, select **Show previous generations\.** For more information, see [Amazon EC2 Instances](https://aws.amazon.com/ec2/instance-types/) and [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation/)\.

   1. Create or select a key pair\. To create a key pair, select `Create a new key pair` from **Key pair name** and enter a name for the key pair in the highlighted field \(for example, `my-key-pair`\)\.

   1. \(Optional\) Under **Advanced settings**, specify a display name for the instance\.

   1. \(Optional\) Under **Advanced settings**, select a VPC from **Network \(VPC\)**\. Note that this list includes all VPCs for the region, including VPCs created using the Amazon VPC console and the default VPC \(if it exists\)\. If you have a default VPC in this region, we select it by default\. If the text is "There is no VPC available for launch or import operations in this region", then you must create a VPC in this Region using the Amazon VPC console\.

   1. \(Optional\) Under **Advanced settings**, select a subnet from **Subnet**\. Note that this list includes all subnets for the selected VPC, including any default subnets\. If this list is empty, you must add a subnet to the VPC using the Amazon VPC console, or select a different VPC\. Otherwise, we select a subnet for you\.

   1. \(Optional\) Under **Advanced settings**, create a security group or select one or more security groups\. If you select `Create default security group`, we create a security group that grants RDP and SSH access to everyone, which you can modify using the Amazon EC2 or Amazon VPC console\. You can enter a name for this security group in the **Group name** box\.

   1. \(Optional\) Under **Advanced settings**, select an IAM role\. If this list is empty, you can create a role using the IAM console\.  
![\[The Create Amazon EC2 Instance dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_systems_manager_create.png)

1. Click **Create**\. If you are creating a key pair, you are prompted to save the `.pem` file\. Save this file in a secure place; you'll need it to log in to your instance\. You'll receive confirmation that the instance has launched\. Click **Close**\.

After you've created your instance, it appears in the list of instances for the Region in which you launched it\. Initially, the status of the instance is `pending`\. After the status changes to `running`, your instance is ready for use\.

You can manage the lifecycle of your instance using Systems Manager, as described on this page\. To perform other tasks, such as the following, you must use the AWS Management Console:
+ [Attach an Amazon EBS volume to your instance](ebs-attaching-volume.md)
+ [Associate an Elastic IP address with your instance](elastic-ip-addresses-eip.md#using-instance-addressing-eips-associating)
+ [Enable termination protection](terminating-instances.md#Using_ChangingDisableAPITermination)

## Viewing Your Instances<a name="view-instances"></a>

The permissions that your administrator grants you determine whether you can view instances and get detailed information about them\.

**To view your instances and get detailed information**

1. Open the [AWS Systems Manager console](https://console.aws.amazon.com/systems-manager)\.

1. From the list of Regions, select a Region\.

1. From the list of instances, select one or more instances\.

1. In the lower pane, click the down arrow next to each instance to view detailed information about the instance\.  
![\[View instance details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_systems_manager_details.png)

## Connecting to Your Instance<a name="connect-instances"></a>

You can log in to an EC2 instance if you have the private key \(`.pem` file\) for the key pair that was specified when launching the instance\. The tool that you'll use to connect to your instance depends on whether the instance is a Windows instance or a Linux instance\.

**To connect to a Windows EC2 instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance, right\-click, and then click **Retrieve Windows Password**\.

1. In the **Retrieve Default Windows Administrator Password** dialog box, click **Browse**\. Select the private key file for the key pair and then click **Open**\.

1. Click **Decrypt Password**\. Save the password or copy it to the clipboard\.

1. Select the instance, right\-click, and then click **Connect via RDP**\. When prompted for credentials, use the name of the administrator account and the password that you saved in the previous step\.

1. Because the certificate is self\-signed, you might get a warning that the security certificate is not from a trusted certifying authority\. Click **Yes** to continue\.

   If the connection fails, see [Troubleshooting Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-windows-instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**To connect to a Linux EC2 instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance\.

1. In the lower pane, click the down arrow next to the instance ID to view detailed information about the instance\.

1. Locate the public DNS name\. You'll need this information to connect to your instance\.

1. Connect to the instance using PuTTY\. For step\-by\-step instructions, see [Connect to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Rebooting Your Instance<a name="reboot-instance"></a>

The permissions that you've been granted by your administrator determine whether you can reboot instances\.

**To reboot your instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance\.

1. Right\-click the instance, and then click **Reset \(Reboot\)**\.

1. When prompted for confirmation, click **Yes**\.

## Stopping Your Instance<a name="stop-instance"></a>

The permissions that you've been granted by your administrator determine whether you can stop instances\.

**To stop your instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance\.

1. Right\-click the instance, and then click **Shut Down \(Stop\)**\.

1. When prompted for confirmation, click **Yes**\.

## Starting Your Instance<a name="start-instance"></a>

The permissions that you've been granted by your administrator determine whether you can start instances\.

**To start your instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance\.

1. Right\-click the instance, and then click **Power On \(Start\)**\.

1. When prompted for confirmation, click **Yes**\.

If you get a quota error when you try to start an instance, you have reached your concurrent running instance limit\. The default limit for your AWS account is 20\. If you need additional running instances, complete the form at [Request to Increase Amazon EC2 Instance Limit](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.

## Terminating Your Instance<a name="terminate-instance"></a>

The permissions that you've been granted by your administrator determine whether you can terminate instances\.

**To terminate your instance**

1. Open AWS Systems Manager\.

1. From the list of instances, select the instance\.

1. Right\-click the instance, and then click **Delete \(Terminate\)**\.

1. When prompted for confirmation, click **Yes**\.