# Enabling enhanced networking with the Intel 82599 VF interface on Windows instances<a name="sriov-networking"></a>

Amazon EC2 provides enhanced networking capabilities through the Intel 82599 VF interface, which uses the Intel `ixgbevf` driver\.

**Topics**
+ [Requirements](#ixgbevf-requirements)
+ [Testing whether enhanced networking is enabled](#test-enhanced-networking)
+ [Enabling enhanced networking on Windows](#enable-enhanced-networking)

## Requirements<a name="ixgbevf-requirements"></a>

To prepare for enhanced networking using the Intel 82599 VF interface, set up your instance as follows:
+ Select from the following supported instance types: C3, C4, D2, I2, M4 \(excluding `m4.16xlarge`\), and R3\.
+ Launch the instance from a 64\-bit HVM AMI\. You can't enable enhanced networking on Windows Server 2008 and Windows Server 2003\. Enhanced networking is already enabled for Windows Server 2012 R2 and Windows Server 2016 and later AMIs\. Windows Server 2012 R2 includes Intel driver 1\.0\.15\.3 and we recommend that you upgrade that driver to the latest version using the Pnputil\.exe utility\. 
+ Ensure that the instance has internet connectivity\.
+ Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\.
+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `sriovNetSupport` attribute, might render incompatible instances or operating systems unreachable\. If you have a recent backup, your data will still be retained if this happens\.

## Testing whether enhanced networking is enabled<a name="test-enhanced-networking"></a>

Enhanced networking with the Intel 82599 VF interface is enabled if the driver is installed on your instance and the `sriovNetSupport` attribute is set\. 

**Driver**  
To verify that the driver is installed, connect to your instance and open Device Manager\. You should see "Intel\(R\) 82599 Virtual Function" listed under **Network adapters**\.

**Instance attribute \(sriovNetSupport\)**  
To check whether an instance has the enhanced networking `sriovNetSupport` attribute set, use one of the following commands:
+ [describe\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html) \(AWS CLI\)

  ```
  aws ec2 describe-instance-attribute --instance-id instance_id --attribute sriovNetSupport
  ```
+ [Get\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

  ```
  Get-EC2InstanceAttribute -InstanceId instance-id -Attribute sriovNetSupport
  ```

If the attribute isn't set, `SriovNetSupport` is empty\. If the attribute is set, the value is simple, as shown in the following example output\.

```
"SriovNetSupport": {
    "Value": "simple"
},
```

**Image attribute \(sriovNetSupport\)**  
To check whether an AMI already has the enhanced networking `sriovNetSupport` attribute set, use one of the following commands:
+ [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)

  ```
  aws ec2 describe-images --image-id ami_id --query "Images[].SriovNetSupport"
  ```
+ [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  (Get-EC2Image -ImageId ami-id).SriovNetSupport
  ```

If the attribute isn't set, `SriovNetSupport` is empty\. If the attribute is set, the value is simple\.

## Enabling enhanced networking on Windows<a name="enable-enhanced-networking"></a>

If you launched your instance and it does not have enhanced networking enabled already, you must download and install the required network adapter driver on your instance, and then set the `sriovNetSupport` instance attribute to activate enhanced networking\. You can only enable this attribute on supported instance types\. For more information, see [Enhanced networking types](enhanced-networking.md#supported_instances)\. 

**Important**  
 To view the latest version of the Intel driver in the Windows AMIs, see [Details about AWS Windows AMI versions](windows-ami-versions.md)\.

**Warning**  
There is no way to disable the enhanced networking attribute after you've enabled it\.

**To enable enhanced networking**

1. <a name="amazon-linux-enhanced-networking-start-step"></a>Connect to your instance and log in as the local administrator\.

1. \[Windows Server 2016 and later\] Run the following EC2Launch PowerShell script to configure the instance after the driver is installed\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -Schedule
   ```
**Important**  
The administrator password will reset when you enable the initialize instance EC2Launch script\. You can modify the configuration file to disable the administrator password reset by specifying it in the settings for the initialization tasks\. For steps on how to disable password reset, see [Configure initialization tasks](ec2launch.md#ec2launch-inittasks)\.

1. From the instance, install the driver as follows:

   1. Download the Intel network adapter driver for your operating system:
      + [Windows Server 2008 R2](https://downloadcenter.intel.com/detail_desc.aspx?agr=Y&DwnldID=18725)
      + [Windows Server 2012](https://downloadcenter.intel.com/download/21694/Network-Adapter-Driver-for-Windows-Server-2012-)
      + [Windows Server 2012 R2](https://downloadcenter.intel.com/download/23073/Network-Adapter-Driver-for-Windows-Server-2012-R2-)
      + [Windows Server 2016](https://downloadcenter.intel.com/download/26092/Ethernet-Intel-Network-Adapter-Driver-for-Windows-Server-2016-?product=83418) \(including for Server version 1803 and earlier\*\)
      + [Windows Server 2019](https://downloadcenter.intel.com/download/28396/Intel-Network-Adapter-Driver-for-Windows-Server-2019-?product=36773) \(including for Server version 1809 and later\*\)

      \*Server versions 1803 and earlier as well as 1809 and later are not specifically addressed on the Intel Drivers and Software pages\.

   1. In the **Download** folder, locate the `PROWinx64.exe` file\. Rename this file `PROWinx64.zip`\.

   1. Open a context \(right\-click\) menu on `PROWinx64.zip` and choose **Extract All**\. Specify a destination path and choose **Extract**\.

   1. Open a command prompt window, go to the folder with the extracted files, and use the `pnputil` utility to add and install the INF file in the driver store\. 

      **Windows Server 2019**

      ```
      pnputil -i -a PROXGB\Winx64\NDIS68\vxn68x64.inf 
      ```

      **Windows Server 2016**

      ```
      pnputil -i -a PROXGB\Winx64\NDIS65\vxn65x64.inf 
      ```

      **Windows Server 2012 R2**

      ```
      pnputil -i -a PROXGB\Winx64\NDIS64\vxn64x64.inf 
      ```

      **Windows Server 2012**

      ```
      pnputil -i -a PROXGB\Winx64\NDIS63\vxn63x64.inf
      ```

      **Windows Server 2008 R2**

      ```
      pnputil -a PROXGB\Winx64\NDIS62\vxn62x64.inf
      ```

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --sriov-net-support simple
     ```
   + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance_id -SriovNetSupport "simple"
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\. The AMI inherits the enhanced networking attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.