# Enabling enhanced networking with the Elastic Network Adapter \(ENA\) on Windows instances<a name="enhanced-networking-ena"></a>

Amazon EC2 provides enhanced networking capabilities through the Elastic Network Adapter \(ENA\)\. To use enhanced networking, you must install the required ENA module and enable ENA support\.

**Topics**
+ [Requirements](#ena-requirements)
+ [Enhanced networking performance](#ena-performance)
+ [Testing whether enhanced networking is enabled](#test-enhanced-networking-ena)
+ [Enabling enhanced networking on Windows](#enable-enhanced-networking-ena-WIN)
+ [Amazon ENA driver versions](#ena-adapter-driver-versions)
+ [Subscribing to notifications](#drivers-subscribe-notifications)
+ [Operating system optimizations](enhanced-networking-os.md)

## Requirements<a name="ena-requirements"></a>

To prepare for enhanced networking using the ENA, set up your instance as follows:
+ Launch the instance using a [current generation](instance-types.md#current-gen-instances) instance type, other than an M4 instance smaller than `m4.16xlarge`\.
+ Ensure that the instance has internet connectivity\.
+ Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\.
+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `enaSupport` attribute, might render incompatible instances or operating systems unreachable\. If you have a recent backup, your data will still be retained if this happens\.

## Enhanced networking performance<a name="ena-performance"></a>

The following documentation provides a summary of the network performance for the instance types that support ENA enhanced networking:
+ [Network Performance for Accelerated Computing Instances](accelerated-computing-instances.md#gpu-network-performance)
+ [Network Performance for Compute Optimized Instances](compute-optimized-instances.md#compute-network-performance)
+ [Network Performance for General Purpose Instances](general-purpose-instances.md#general-purpose-network-performance)
+ [Network Performance for Memory Optimized Instances](memory-optimized-instances.md#memory-network-perf)
+ [Network Performance for Storage Optimized Instances](storage-optimized-instances.md#storage-network-performance)

## Testing whether enhanced networking is enabled<a name="test-enhanced-networking-ena"></a>

To test whether enhanced networking is already enabled, verify that the driver is installed on your instance and that the `enaSupport` attribute is set\. 

**Instance attribute \(enaSupport\)**

To check whether an instance has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

  ```
  aws ec2 describe-instances --instance-ids instance_id --query "Reservations[].Instances[].EnaSupport"
  ```
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
  ```

**Image attribute \(enaSupport\)**  
To check whether an AMI has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.
+ [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)

  ```
  aws ec2 describe-images --image-id ami_id --query "Images[].EnaSupport"
  ```
+ [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Image -ImageId ami_id).EnaSupport
  ```

## Enabling enhanced networking on Windows<a name="enable-enhanced-networking-ena-WIN"></a>

If you launched your instance and it does not have enhanced networking enabled already, you must download and install the required network adapter driver on your instance, and then set the `enaSupport` instance attribute to activate enhanced networking\. You can only enable this attribute on supported instance types and only if the ENA driver is installed\. For more information, see [Enhanced networking types](enhanced-networking.md#supported_instances)\.

**To enable enhanced networking**

1. Connect to your instance and log in as the local administrator\.

1. \[Windows Server 2016 and later\] Run the following EC2Launch PowerShell script to configure the instance after the driver is installed\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -Schedule
   ```

1. From the instance, install the driver as follows:

   1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/Latest/AwsEnaNetworkDriver.zip) the latest driver to the instance\.

   1. Extract the zip archive\.

   1. Install the driver by running the `install.ps1` PowerShell script\.
**Note**  
If you get an execution policy error, set the policy to `Unrestricted` \(by default it is set to `Restricted` or `RemoteSigned`\)\. In a command line, run `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`, and then run the `install.ps1` PowerShell script again\.

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. Enable ENA support on your instance as follows:

   1. From your local computer, check the EC2 instance ENA support attribute on your instance by running one of the following commands\. If the attribute is not enabled, the output will be "\[\]" or blank\. `EnaSupport` is set to `false` by default\.
      + [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

        ```
        aws ec2 describe-instances --instance-ids instance_id --query "Reservations[].Instances[].EnaSupport"
        ```
      + [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

        ```
        (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
        ```

   1. To enable ENA support, run one of the following commands:
      + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

        ```
        aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
        ```
      + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

        ```
        Edit-EC2InstanceAttribute -InstanceId instance_id -EnaSupport $true
        ```

      If you encounter problems when you restart the instance, you can also disable ENA support using one of the following commands:
      + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

        ```
        aws ec2 modify-instance-attribute --instance-id instance_id --no-ena-support
        ```
      + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

        ```
        Edit-EC2InstanceAttribute -InstanceId instance_id -EnaSupport $false
        ```

   1. Verify that the attribute has been set to `true` using describe\-instances or Get\-EC2Instance as shown previously\. You should now see the following output:

      ```
      [
          true
      ]
      ```

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance using the AWS OpsWorks console so that the instance state remains in sync\.

1. On the instance, validate that the ENA driver is installed and enabled as follows:

   1. Right\-click the network icon and choose **Open Network and Sharing Center**\.

   1. Choose the Ethernet adapter \(for example, **Ethernet 2**\)\.

   1. Choose **Details**\. For **Network Connection Details**, check that **Description** is **Amazon Elastic Network Adapter**\.

1. \(Optional\) Create an AMI from the instance\. The AMI inherits the `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with ENA enabled by default\. For more information, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

## Amazon ENA driver versions<a name="ena-adapter-driver-versions"></a>

Windows AMIs include the Amazon ENA driver to enable enhanced networking\. The following table summarizes the changes for each release\.


| Driver version | Details | Release date | 
| --- | --- | --- | 
|  [2\.2\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.2.0/AwsEnaNetworkDriver.zip)  |  New Features [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html) Performance Optimizations [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html) Bug Fixes [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | August 12, 2020 | 
|  [2\.1\.5](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.1.5/AwsEnaNetworkDriver.zip)  |  Bug Fixes [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | June 23, 2020 | 
|  [2\.1\.4](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.1.4/AwsEnaNetworkDriver.zip)  |  Bug Fixes [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | November 25, 2019 | 
|  [2\.1\.2](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.1.2/AwsEnaNetworkDriver.zip)  | New Features [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html) Bug Fixes [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | November 4, 2019 | 
|  [2\.1\.1](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.1.1/AwsEnaNetworkDriver.zip)  | Bug Fixes [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | September 16, 2019 | 
|  [2\.1\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/2.1.0/AwsEnaNetworkDriver.zip)  | ENA Windows driver v2\.1 introduces new ENA device capabilities, provides a performance boost, adds new features, and includes multiple stability improvements\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html) | July 1, 2019 | 
|  [1\.5\.0](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/1.5.0/AwsEnaNetworkDriver.zip)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html)  | October 4, 2018 | 
|  [1\.2\.3](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/1.2.3/AwsEnaNetworkDriver.zip)  |  Includes reliability fixes and unifies support for Windows Server 2008 R2 through Windows Server 2016\.  | February 13, 2018 | 
|  [1\.0\.9](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/1.0.8/AwsEnaNetworkDriver.zip)  |  Includes some reliability fixes\. Applies only to Windows Server 2008 R2\. Not recommended for other versions of Windows Server\.  | December 2016 | 
|  [1\.0\.8](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/1.0.8/AwsEnaNetworkDriver.zip)  |  The initial release\. Included in AMIs for Windows Server 2008 R2, Windows Server 2012 RTM, Windows Server 2012 R2, and Windows Server 2016\.  | July 2016 | 

## Subscribing to notifications<a name="drivers-subscribe-notifications"></a>

Amazon SNS can notify you when new versions of EC2 Windows Drivers are released\. Use the following procedure to subscribe to these notifications\.

**To subscribe to EC2 notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must select this Region because the SNS notifications that you are subscribing to are in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. In the **Create subscription** dialog box, do the following:

   1. For **TopicARN**, copy the following Amazon Resource Name \(ARN\):

      arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-drivers

   1. For **Protocol**, choose `Email`\.

   1. For **Endpoint**, enter an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive a confirmation email\. Open the email and follow the directions to complete your subscription\.

Whenever new EC2 Windows drivers are released, we send notifications to subscribers\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from Amazon EC2 Windows driver notification**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the check box for the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.