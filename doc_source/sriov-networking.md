# Enable enhanced networking with the Intel 82599 VF interface on Windows instances<a name="sriov-networking"></a>

Amazon EC2 provides enhanced networking capabilities through the Intel 82599 VF interface, which uses the Intel `ixgbevf` driver\.

**Topics**
+ [Requirements](#ixgbevf-requirements)
+ [Test whether enhanced networking is enabled](#test-enhanced-networking)
+ [Enable enhanced networking on Windows](#enable-enhanced-networking)

## Requirements<a name="ixgbevf-requirements"></a>

To prepare for enhanced networking using the Intel 82599 VF interface, set up your instance as follows:
+ Select from the following supported instance types: C3, C4, D2, I2, M4 \(excluding `m4.16xlarge`\), and R3\.
+ Launch the instance from a 64\-bit HVM AMI\. You can't enable enhanced networking on Windows Server 2008 and Windows Server 2003\. Enhanced networking is already enabled for Windows Server 2012 R2 and Windows Server 2016 and later AMIs\. Windows Server 2012 R2 includes Intel driver 1\.0\.15\.3 and we recommend that you upgrade that driver to the latest version using the Pnputil\.exe utility\. 
+ Ensure that the instance has internet connectivity\.
+ Use [AWS CloudShell](https://console.aws.amazon.com/cloudshell) from the AWS Management Console, or install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Access Amazon EC2](concepts.md#access-ec2) or the [AWS CloudShell User Guide](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html)\. Enhanced networking cannot be managed from the Amazon EC2 console\.
+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `sriovNetSupport` attribute, might render incompatible instances or operating systems unreachable\. If you have a recent backup, your data will still be retained if this happens\.

## Test whether enhanced networking is enabled<a name="test-enhanced-networking"></a>

Enhanced networking with the Intel 82599 VF interface is enabled if the driver is installed on your instance and the `sriovNetSupport` attribute is set\. 

**Driver**  
To verify that the driver is installed, connect to your instance and open Device Manager\. You should see "Intel\(R\) 82599 Virtual Function" listed under **Network adapters**\.

**Instance attribute \(sriovNetSupport\)**  
To check whether an instance has the enhanced networking `sriovNetSupport` attribute set, use one of the following commands:
+ [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html) \(AWS CLI/AWS CloudShell\)

  ```
  aws ec2 describe-instance-attribute --instance-id instance_id --attribute sriovNetSupport
  ```
+ [https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

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
+ [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI/AWS CloudShell\)

  ```
  aws ec2 describe-images --image-id ami_id --query "Images[].SriovNetSupport"
  ```
+ [https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  (Get-EC2Image -ImageId ami-id).SriovNetSupport
  ```

If the attribute isn't set, `SriovNetSupport` is empty\. If the attribute is set, the value is simple\.

## Enable enhanced networking on Windows<a name="enable-enhanced-networking"></a>

If you launched your instance and it does not have enhanced networking enabled already, you must download and install the required network adapter driver on your instance, and then set the `sriovNetSupport` instance attribute to activate enhanced networking\. You can only enable this attribute on supported instance types\. For more information, see [Enhanced networking support](enhanced-networking.md#supported_instances)\. 

**Important**  
 To view the latest version of the Intel driver in the Windows AMIs, see [Details about AWS Windows AMI versions](aws-windows-ami.md#windows-ami-versions)\.

**Warning**  
There is no way to disable the enhanced networking attribute after you've enabled it\.

**To enable enhanced networking**

1. <a name="amazon-linux-enhanced-networking-start-step"></a>Connect to your instance and log in as the local administrator\.

1. \[Windows Server 2016 and later\] Run the following EC2 Launch PowerShell script to configure the instance after the driver is installed\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -Schedule
   ```
**Important**  
The administrator password will reset when you enable the initialize instance EC2 Launch script\. You can modify the configuration file to disable the administrator password reset by specifying it in the settings for the initialization tasks\. For steps on how to disable password reset, see [Configure initialization tasks](ec2launch.md#ec2launch-inittasks)\.

1. From the instance, download the Intel network adapter driver for your operating system:
   + **Windows Server 2019** including for Server version 1809 and later\*

     Visit the [ download page](https://www.intel.com/content/www/us/en/download/19372/intel-network-adapter-driver-for-windows-server-2019.html) and download `Wired_driver_version_x64.zip`\.
   + **Windows Server 2016** including for Server version 1803 and earlier\*

     Visit the [ download page](https://www.intel.com/content/www/us/en/download/18737/intel-network-adapter-driver-for-windows-server-2016.html) and download `Wired_driver_version_x64.zip`\.
   + **Windows Server 2012 R2**

     Visit the [ download page](https://www.intel.com/content/www/us/en/download/17480/intel-network-adapter-driver-for-windows-server-2012-r2.html) and download `Wired_driver_version_x64.zip`\.
   + **Windows Server 2012**

     Visit the [ download page](https://www.intel.com/content/www/us/en/download/16789/intel-network-adapter-driver-for-windows-server-2012.html) and download `Wired_driver_version_x64.zip`\.
   + **Windows Server 2008 R2**

     Visit the [ download page](https://www.intel.com/content/www/us/en/download/15590/intel-network-adapter-driver-for-windows-7-final-release.html) and download `PROWinx64Legacy.exe`\.

   \*Server versions 1803 and earlier as well as 1809 and later are not specifically addressed on the Intel Drivers and Software pages\.

1. Install the Intel network adapter driver for your operating system\.
   + **Windows Server 2008 R2**

     1. In the **Downloads** folder, locate the `PROWinx64Legacy.exe` file and rename it to `PROWinx64Legacy.zip`\.

     1. Extract the contents of the `PROWinx64Legacy.zip` file\.

     1. Open the command line, navigate to the extracted folder, and run the following command to use the `pnputil` utility to add and install the INF file in the driver store\.

        ```
        C:\> pnputil -a PROXGB\Winx64\NDIS62\vxn62x64.inf
        ```
   + **Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, and Windows Server 2012**

     1. In the **Downloads** folder, extract the contents of the `Wired_driver_version_x64.zip` file\.

     1. In the extracted folder, locate the `Wired_driver_version_x64.exe` file and rename it to `Wired_driver_version_x64.zip`\.

     1. Extract the contents of the `Wired_driver_version_x64.zip` file\.

     1. Open the command line, navigate to the extracted folder, and run one of the following commands to use the `pnputil` utility to add and install the INF file in the driver store\.
        + Windows Server 2019

          ```
          C:\> pnputil -i -a PROXGB\Winx64\NDIS68\vxn68x64.inf
          ```
        + Windows Server 2016

          ```
          C:\> pnputil -i -a PROXGB\Winx64\NDIS65\vxn65x64.inf
          ```
        + Windows Server 2012 R2

          ```
          C:\> pnputil -i -a PROXGB\Winx64\NDIS64\vxn64x64.inf
          ```
        + Windows Server 2012

          ```
          C:\> pnputil -i -a PROXGB\Winx64\NDIS63\vxn63x64.inf
          ```

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI/AWS CloudShell\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --sriov-net-support simple
     ```
   + [https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance_id -SriovNetSupport "simple"
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\. The AMI inherits the enhanced networking attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.