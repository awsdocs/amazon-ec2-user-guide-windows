# AWS NVMe drivers for Windows instances<a name="aws-nvme-drivers"></a>

EBS volumes and instance store volumes are exposed as NVMe block devices on [Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. You must have the AWS NVMe driver installed in order to use an NVMe block device\. The latest AWS Windows AMIs for Windows Server 2008 R2 and later contain the required AWS NVMe driver\.

For more information about EBS and NVMe, see [Amazon EBS and NVMe on Windows instances](nvme-ebs-volumes.md)\. For more information about SSD instance store and NVMe, see [SSD instance store volumes](ssd-instance-store.md)\.

## Installing or upgrading AWS NVMe drivers<a name="install-nvme-drivers"></a>

If you are not using the latest AWS Windows AMIs provided by Amazon, use the following procedure to install the current AWS NVMe driver\. You should perform this update at a time when it is convenient to reboot your instance\. Either the install script will reboot your instance or you must reboot it as the final step\.

**Prerequisites**

PowerShell 3\.0 or later

**To download and install the latest AWS NVMe driver**

1. Connect to your instance and log in as the local administrator\.

1. Download and extract the drivers using one of the following options:
   + Using a browser:

     1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\.

     1. Extract the zip archive\.
   + Using PowerShell:

     ```
     invoke-webrequest https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip -outfile $env:USERPROFILE\nvme_driver.zip
     expand-archive $env:userprofile\nvme_driver.zip -DestinationPath $env:userprofile\nvme_driver
     ```

1. Install the driver by running the `install.ps1` PowerShell script\. If you get an error, make sure you are using PowerShell 3\.0 or later\.

1. If the installer does not reboot your instance, reboot the instance\.

## AWS NVMe driver version history<a name="nvme-driver-version-history"></a>

The following table describes the released versions of the AWS NVMe driver\.


| Driver version | Details | Release date | 
| --- | --- | --- | 
| 1\.3\.2 |  Fixed issue with modifying EBS volumes actively processing IO, which may result in data corruption\. Customers who do not modify online EBS volumes \(for example, resizing or changing type\) are not impacted\.  | 10 September 2019 | 
| 1\.3\.1 |  Reliability Improvements | 21 May 2019 | 
| 1\.3\.0 | Device optimization improvements | 31 August 2018 | 
| 1\.2\.0 | Performance and reliability improvements for AWS NVMe devices on all supported instances, including bare metal instances | 13 June 2018 | 
| 1\.0\.0 | AWS NVMe driver for supported instance types running Windows Server | 12 February 2018 | 

## Subscribing to notifications<a name="drivers-subscribe-notifications"></a>

Amazon SNS can notify you when new versions of EC2 Windows Drivers are released\. Use the following procedure to subscribe to these notifications\.

**To subscribe to EC2 notifications from the console**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must select this Region because the SNS notifications that you are subscribing to are in this Region\.

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

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the checkbox for the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.

**To subscribe to EC2 notifications using the AWS CLI**  
To subscribe to EC2 notifications with the AWS CLI, use the following command\. 

```
aws sns subscribe --topic-arn arn:aws:sns:us-east-1:801119661308:ec2-windows-drivers --protocol email --notification-endpoint YourUserName@YourDomainName.ext
```

**To subscribe to EC2 notifications using AWS Tools for Windows PowerShell**  
To subscribe to EC2 notifications with AWS Tools for Windows PowerShell, use the following command\. 

```
Connect-SNSNotification -TopicArn 'arn:aws:sns:us-east-1:801119661308:ec2-windows-drivers' -Protocol email -Region us-east-1 -Endpoint 'YourUserName@YourDomainName.ext'
```