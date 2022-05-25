# AWS NVMe drivers for Windows instances<a name="aws-nvme-drivers"></a>

Amazon EBS volumes and instance store volumes are exposed as NVMe block devices on [ Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. Windows Server 2012 R2 and later include an NVMe driver, [ StorNVMe](https://docs.microsoft.com/en-us/windows-hardware/drivers/storage/nvme-features-supported-by-stornvme), that is provided Microsoft\. However, to achieve the full performance and features provided by Amazon EBS you must have the AWS NVMe driver installed when using an NVMe block device\. The latest AWS Windows AMIs for Windows Server 2008 R2 and later contain the required AWS NVMe driver\.

For more information about EBS and NVMe, see [Amazon EBS and NVMe on Windows instances](nvme-ebs-volumes.md)\. For more information about SSD instance store and NVMe, see [SSD instance store volumes](ssd-instance-store.md)\.

## Install or upgrade AWS NVMe drivers using PowerShell<a name="install-nvme-drivers"></a>

If you are not using the latest AWS Windows AMIs provided by Amazon, use the following procedure to install the current AWS NVMe driver\. You should perform this update at a time when it is convenient to reboot your instance\. Either the install script will reboot your instance or you must reboot it as the final step\.

**Prerequisites**

PowerShell 3\.0 or later

**To download and install the latest AWS NVMe driver**

1. We recommend that you create an AMI as a backup as follows, in case you need to roll back your changes\.

   1. When you stop an instance, the data on any instance store volumes is erased\. Before you stop an instance, verify that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

   1. In the navigation pane, choose **Instances**\.

   1. Select the instance that requires the driver upgrade, and choose **Instance state**, **Stop instance**\.

   1. After the instance is stopped, select the instance, choose **Actions**, then **Image and templates**, and then choose **Create image**\.

   1. Choose **Instance state**, **Start instance**\.

1. Connect to your instance and log in as the local administrator\.

1. Download and extract the drivers to your instance using one of the following options:
   + Using a browser:

     1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\.

     1. Extract the zip archive\.
   + Using PowerShell:

     ```
     invoke-webrequest https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip -outfile $env:USERPROFILE\nvme_driver.zip
     expand-archive $env:userprofile\nvme_driver.zip -DestinationPath $env:userprofile\nvme_driver
     ```

1. Install the driver to your instance by running the `install.ps1` PowerShell script from the `nvme_driver` directory \(`.\install.ps1`\)\. If you get an error, make sure you are using PowerShell 3\.0 or later\. 

   `install.ps1` allows you to specify whether the `ebsnvme-id` tool should be installed with the driver\. To install the `ebsnvme-id` tool, specify `InstallEBSNVMeIdTool ‘Yes’`\. If you do not want to install the tool, specify `InstallEBSNVMeIdTool ‘No’`\. If you do not specify `InstallEBSNVMeIdTool`, and the tool is already present at `C:\ProgramData\Amazon\Tools`, the package will upgrade the tool by default\. If the tool is not present, `install.ps1` will not upgrade the tool by default\. If you do not want to install the tool as part of the package, and want to install it later, you can download it from Amazon S3:

   [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/EBSNVMeID/Latest/ebsnvme-id.zip) the latest `ebsnvme-id` tool\.

1. If the installer does not reboot your instance, reboot the instance\.

## Install or upgrade AWS NVMe drivers with SSM Distributor<a name="install-nvme-drivers-ssm-distributor"></a>

You can install the NVMe driver package with SSM Distributor one time, or with scheduled updates\.

1. For instructions for how to install the NVMe driver package using SSM Distributor, see the procedures in [Install or update packages](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html) in the *Amazon EC2 Systems Manager User Guide*\.

1. For **Document version**, select the `AWSNVMe` package\.

1. To install the `ebsnvme-id` tool , specify `{"SSM_InstallEBSNVMeIdTool": "Yes"}` for **Additional Arguments**\. If you do not want to install the tool, specify `{"SSM_InstallEBSNVMeIdTool": "No"}`\.

   If `SSM_InstallEBSNVMeIdTool` is not specified for **Additional Arguments**, and the tool is already present at `C:\ProgramData\Amazon\Tools`, the package will upgrade the tool by default\. If the tool is not present, the package will not upgrade the tool by default\. **Additional Arguments** must be formatted using valid JSON syntax\. For examples of how to pass additional arguments for the `aws configure` package, see the [Amazon EC2 Systems Manager documentation](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-plugins.html#aws-configurepackage)\. If you do not want to install the tool as part of the package, and want to install it later, you can download it from Amazon S3:

   [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/EBSNVMeID/Latest/ebsnvme-id.zip) the latest `ebsnvme-id` tool\.

1. If the installer does not reboot your instance, reboot the instance\.

## AWS NVMe driver version history<a name="nvme-driver-version-history"></a>

The following table shows the corresponding NVMe driver version to download for each Windows Server version on Amazon EC2\. 


| Windows Server version | AWSNVMe driver version | 
| --- | --- | 
| Windows Server 2019 |  latest  | 
| Windows Server 2016 | latest | 
| Windows Server 2012 R2 | latest | 
| Windows Server 2012 | latest | 
| Windows Server 2008 R2 | 1\.3\.2 and earlier | 

The following table describes the released versions of the AWS NVMe driver\.


| Driver version | Details | Release date | 
| --- | --- | --- | 
|  [1\.4\.1](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.4.1/AWSNVMe.zip)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-nvme-drivers.html)  | 20 May 2022 | 
|  [1\.4\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.4.0/AWSNVMe.zip)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-nvme-drivers.html)  | 23 November 2021 | 
|  [1\.3\.2](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.3.2/AWSNVMe.zip)  |  Fixed issue with modifying EBS volumes actively processing IO, which may result in data corruption\. Customers who do not modify online EBS volumes \(for example, resizing or changing type\) are not impacted\.  | 10 September 2019 | 
|  [1\.3\.1](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.3.1/AWSNVMe.zip)  |  Reliability Improvements | 21 May 2019 | 
|  [1\.3\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.3.0/AWSNVMe.zip)  | Device optimization improvements | 31 August 2018 | 
|  [1\.2\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.2.0/AWSNVMe.zip)  | Performance and reliability improvements for AWS NVMe devices on all supported instances, including bare metal instances | 13 June 2018 | 
|  [1\.0\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/1.0.0.146/AWSNVMe.zip)  | AWS NVMe driver for supported instance types running Windows Server | 12 February 2018 | 

## Subscribe to notifications<a name="drivers-subscribe-notifications"></a>

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

1. Select the check box for the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.

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