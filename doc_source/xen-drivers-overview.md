# Paravirtual Drivers for Windows Instances<a name="xen-drivers-overview"></a>

Windows AMIs contain a set of drivers to permit access to virtualized hardware\. These drivers are used by Amazon EC2 to map instance store and Amazon EBS volumes to their devices\. The following table shows key differences between the different drivers\.


|  | RedHat PV | Citrix PV | AWS PV | 
| --- | --- | --- | --- | 
|  Instance type  |  Not supported for all instance types\. If you specify an unsupported instance type, the instance is impaired\.  |  Supported for all instance types\.  |  Supported for all instance types\.  | 
|  Attached volumes  |  Supports up to 16 attached volumes\.  |  Supports more than 16 attached volumes\.  |  Supports more than 16 attached volumes\.  | 
|  Network  |  The driver has known issues where the network connection resets under high loads; for example, fast FTP file transfers\.  |   |  The driver automatically configures jumbo frames on the network adapter when on a compatible instance type\. When the instance is in a [cluster placement group](placement-groups.md), this offers better network performance between instances in the cluster placement group\.  | 

The following list shows which PV drivers you should run on each version of Windows Server on Amazon EC2\.
+ Windows Server 2019: AWS PV
+ Windows Server 2016: AWS PV
+ Windows Server 2012 and 2012 R2: AWS PV
+ Windows Server 2008 R2: AWS PV 
+ Windows Server 2008: Citrix PV 5\.9

**Topics**
+ [AWS PV Drivers](#xen-driver-awspv)
+ [Citrix PV Drivers](#xen-driver-citrix)
+ [RedHat PV Drivers](#xen-driver-redhat)
+ [Subscribing to Notifications](#drivers-subscribe-notifications)
+ [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)
+ [Troubleshooting PV Drivers](pvdrivers-troubleshooting.md)

## AWS PV Drivers<a name="xen-driver-awspv"></a>

The AWS PV drivers are stored in the `%ProgramFiles%\Amazon\Xentools` directory\. This directory also contains public symbols and a command line tool, `xenstore_client.exe`, that enables you to access entries in XenStore\. For example, the following PowerShell command returns the current time from the Hypervisor:

```
PS C:\> [DateTime]::FromFileTimeUTC((gwmi -n root\wmi -cl AWSXenStoreBase).XenTime).ToString("hh:mm:ss")
11:17:00
```

The AWS PV driver components are listed in the Windows registry under `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services`\. These driver components are as follows: xenbus, xeniface, xennet, xenvbd, and xenvif\.

AWS PV drivers also have a Windows service named LiteAgent, which runs in user\-mode\. It handles tasks such as shutdown and restart events from AWS APIs on Xen generation instances\. You can access and manage services by running `Services.msc` from the command line\. When running on Nitro generation instances, the AWS PV drivers are not used and the LiteAgent service will self\-stop starting with driver version 8\.2\.4\. Updating to the latest AWS PV driver also updates the LiteAgent and improves reliability on all instance generations\.

### Installing the Latest AWS PV Drivers<a name="aws-pv-download"></a>

Amazon Windows AMIs contain a set of drivers to permit access to virtualized hardware\. These drivers are used by Amazon EC2 to map instance store and Amazon EBS volumes to their devices\. We recommend that you install the latest drivers to improve stability and performance of your EC2 Windows instances\.

**Installation Options**
+ You can use AWS Systems Manager to automatically update the PV drivers\. For more information, see [Walkthrough: Automatically Update PV Drivers on EC2 Windows Instances \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-state-pvdriver.html) in the *AWS Systems Manager User Guide*\.
+ You can [download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the setup package and run the install program manually\. For information about downloading and installing the AWS PV drivers, or if you are upgrading a domain controller, see [Upgrade Windows Server Instances \(AWS PV Upgrade\)](Upgrading_PV_drivers.md#aws-pv-upgrade)\. 

### AWS PV Driver Package History<a name="pv-driver-history"></a>

The following table shows the changes to AWS PV drivers for each driver release\.


| Package version | Details | Release date | 
| --- | --- | --- | 
| 8\.3\.4 | Improved reliability of network device attachment\. | 4 August 2020 | 
| 8\.3\.3 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html) To update this driver on Windows Server 2008 R2 instances, you must first verify that the appropriate patches are installed to address the following Microsoft Security Advisory: [https://docs\.microsoft\.com/en\-us/security\-updates/SecurityAdvisories/2014/2949927](https://docs.microsoft.com/en-us/security-updates/SecurityAdvisories/2014/2949927)\.    | 4 February 2020 | 
| 8\.3\.2 |  Enhanced reliability of networking components\.  | 30 July 2019 | 
| 8\.3\.1 |  Improved performance and robustness of storage component\.  | 12 June 2019 | 
| 8\.2\.7 |  Improved efficiency to support migrating to latest generation instance types\.  | 20 May 2019 | 
| 8\.2\.6 |  Improved efficiency of crash dump path\.  | 15 January 2019 | 
| 8\.2\.5 |  Additional security enhancements\. PowerShell installer now available in package\.  | 12 December 2018 | 
| 8\.2\.4 |  Reliability improvements\.  | 2 October 2018 | 
| 8\.2\.3 |  Bug fixes and performance improvements\. Report EBS volume ID as disk serial number for EBS volumes\. This enables cluster scenarios such as S2D\.  | 29 May 2018 | 
| 8\.2\.1 |  Network and storage performance improvements plus multiple robustness fixes\. To verify that this version has been installed, refer to the following Windows registry value: `HKLM\Software\Amazon\PVDriver\Version 8.2.1`\.  | 8 March 2018 | 
| 7\.4\.6 | Stability fixes to make AWS PV drivers more resilient\. | 26 April 2017 | 
| 7\.4\.3 |  Added support for Windows Server 2016\. Stability fixes for all supported Windows OS versions\. \*AWS PV driver version 7\.4\.3's signature expires on March 29, 2019\. We recommend updating to the latest AWS PV driver\.   | 18 Nov 2016 | 
| 7\.4\.2 | Stability fixes for support of X1 instance type\. | 2 Aug 2016 | 
| 7\.4\.1 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html)  | 12 July 2016 | 
| 7\.3\.2 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html)  | 24 June 2015 | 
| 7\.3\.1 |  TRIM update: Fix related to TRIM requests\. This fix stabilizes instances and improves instance performance when managing large numbers of TRIM requests\.  |  | 
| 7\.3\.0 |  TRIM support: The AWS PV driver now sends TRIM requests to the hypervisor\. Ephemeral disks will properly process TRIM requests given the underlying storage supports TRIM \(SSD\)\. Note that EBS\-based storage does not support TRIM as of March 2015\.  |  | 
| 7\.2\.5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html)  |  | 
| 7\.2\.4 |  Device ID persistence: This driver fix masks the platform PCI device ID and forces the system to always surface the same device ID, even if the instance is moved\. More generally, the fix affects how the hypervisor surfaces virtual devices\. The fix also includes modifications to the co\-installer for the AWS PV drivers so the system persists mapped virtual devices\.  |  | 
| 7\.2\.2 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html)  |  | 
| 7\.2\.1 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html)  |  | 
| 7\.2\.0 | Base: The AWS PV base version\. |  | 

## Citrix PV Drivers<a name="xen-driver-citrix"></a>

The Citrix PV drivers are stored in the `%ProgramFiles%\Citrix\XenTools` \(32\-bit instances\) or `%ProgramFiles(x86)%\Citrix\XenTools` \(64\-bit instances\) directory\.

The Citrix PV driver components are listed in the Windows registry under `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services`\. These driver components are as follows: xenevtchn, xeniface, xennet, Xennet6, xensvc, xenvbd, and xenvif\.

Citrix also has a driver component named XenGuestAgent, which runs as a Windows service\. It handles tasks such as shutdown and restart events from the API\. You can access and manage services by running `Services.msc` from the command line\.

If you are encountering networking errors while performing certain workloads, you may need to disable the TCP offloading feature for the Citrix PV driver\. For more information, see [TCP Offloading](pvdrivers-troubleshooting.md#citrix-tcp-offloading)\.

## RedHat PV Drivers<a name="xen-driver-redhat"></a>

RedHat drivers are supported for legacy instances, but are not recommended on newer instances with more than 12GB of RAM due to driver limitations\. Instances with more than 12GB of RAM running RedHat drivers can fail to boot and become inaccessible\. We recommend upgrading RedHat drivers to Citrix PV drivers, and then upgrade Citrix PV drivers to AWS PV drivers\.

The source files for the RedHat drivers are in the `%ProgramFiles%\RedHat` \(32\-bit instances\) or `%ProgramFiles(x86)%\RedHat` \(64\-bit instances\) directory\. The two drivers are `rhelnet`, the RedHat Paravirtualized network driver, and `rhelscsi`, the RedHat SCSI miniport driver\.

## Subscribing to Notifications<a name="drivers-subscribe-notifications"></a>

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