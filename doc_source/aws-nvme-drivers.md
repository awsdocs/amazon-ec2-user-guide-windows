# AWS NVMe Drivers for Windows Instances<a name="aws-nvme-drivers"></a>

EBS volumes and instance store volumes are exposed as NVMe block devices on [Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. You must have the AWS NVMe driver installed in order to use an NVMe block device\. The latest AWS Windows AMIs for Windows Server 2008 R2 and later contain the required AWS NVMe driver\.

For more information about EBS and NVMe, see [Amazon EBS and NVMe](nvme-ebs-volumes.md)\. For more information about SSD instance store and NVMe, see [SSD Instance Store Volumes](ssd-instance-store.md)\.

## Installing or Upgrading AWS NVMe Drivers<a name="install-nvme-drivers"></a>

If you are not using the latest AWS Windows AMIs provided by Amazon, use the following procedure to install the current AWS NVMe driver\.

**To download and install the latest AWS NVMe driver**

1. Connect to your instance and log in as the local administrator\.

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\.

1. Extract the zip archive\.

1. Install the driver by running the `install.ps1` PowerShell script\.

1. If the installer does not reboot your instance for you, restart the instance\.

## AWS NVMe Driver Version History<a name="nvme-driver-version-history"></a>

The following table describes the released versions of the AWS NVMe driver\.


| Driver version | Details | Release date | 
| --- | --- | --- | 
| 1\.3\.0 | Device optimization improvements | 31 August 2018 | 
| 1\.2\.0 | Performance and reliability improvements for AWS NVMe devices on all supported instances, including bare metal instances | 13 June 2018 | 
| 1\.0\.0 | AWS NVMe driver for supported instance types running Windows Server | 12 February 2018 | 

## Subscribing to Notifications<a name="drivers-subscribe-notifications"></a>

Amazon SNS can notify you when new versions of EC2 Windows Drivers are released\. Use the following procedure to subscribe to these notifications\.

**To subscribe to EC2 notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation bar, change the region to **US East \(N\. Virginia\)**, if necessary\. You must select this region because the SNS notifications that you are subscribing to are in this region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. In the **Create subscription** dialog box, do the following:

   1. For **TopicARN**, copy the following Amazon Resource Name \(ARN\):

      arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-drivers

   1. For **Protocol**, choose `Email`\.

   1. For **Endpoint**, type an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive a confirmation email\. Open the email and follow the directions to complete your subscription\.

Whenever new EC2 Windows drivers are released, we send notifications to subscribers\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from Amazon EC2 Windows driver notification**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the checkbox for the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.