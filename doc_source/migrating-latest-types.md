# Migrating to latest generation instance types<a name="migrating-latest-types"></a>

The AWS Windows AMIs are configured with the default settings used by the Microsoft installation media, with some customizations\. The customizations include drivers and configurations that support the latest generation instance types\. However, when migrating to the latest generation of EC2 instances and Nitro instances, including bare metal instances, we recommend that you follow the steps in this topic in the following cases:
+ If you are launching instances from custom Windows AMIs
+ If you are launching instances from Windows AMIs provided by Amazon that were created before August 2018

For more information, see [Amazon EC2 Update — Additional Instance Types, Nitro System, and CPU Options](http://aws.amazon.com/blogs/aws/amazon-ec2-update-additional-instance-types-nitro-system-and-cpu-options/)\.

**Contents**
+ [Part 1: Installing and upgrading AWS PV drivers](#upgrade-pv)
+ [Part 2: Installing and upgrading ENA](#upgrade-ena)
+ [Part 3: Upgrading AWS NVMe drivers](#upgrade-nvme)
+ [Part 4: Updating EC2Config and EC2Launch](#upgdate-ec2config-ec2launch)
+ [Part 5: Installing the serial port driver for bare metal instances](#install-serial-port-bare-metal)
+ [Part 6: Updating power management settings](#power-management)
+ [Part 7: Updating Intel chipset drivers for new instance types](#power-management)
+ [\(Alternative\) Upgrading the AWS PV, ENA, and NVMe drivers using AWS Systems Manager](#auto-upgrade)

**Note**  
Alternatively, you can use the `AWSSupport-UpgradeWindowsAWSDrivers` automation document to automate the procedures described in Part 1, Part 2, and Part 3\. If you choose to use the automated procedure, see [\(Alternative\) Upgrading the AWS PV, ENA, and NVMe drivers using AWS Systems Manager](#auto-upgrade), and then continue with Part 4 and Part 5\.

**Before you begin** 

This procedure assumes that you are currently running on a previous generation Xen\-based instance type, such as an M4 or C4, and you are migrating to a latest generation instance type, such as an M5 or C5\. 

You must use PowerShell version 3\.0 or later to successfully perform the upgrade\. 

**Note**  
When migrating to the latest generation instances, the static IP or custom DNS network settings on the existing ENI may be lost as the instance will default to a new Enhanced Networking Adapter device\.

Before following the steps in this procedure, we recommend that you create a backup of the instance\. From the [EC2 console](https://console.aws.amazon.com/ec2/), choose the instance that requires the migration, open the context \(right\-click\) menu, and choose **Instance State**, **Stop**\. 

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To preserve data on instance store volumes, ensure that you back up the data to persistent storage\. 

 Open the context \(right\-click\) menu for the instance in the [EC2 console](https://console.aws.amazon.com/ec2/), choose **Image**, and then choose **Create Image**\. 

**Note**  
Parts 4 and 5 of these instructions can be completed after you migrate or change the instance type to the latest generation, such as M5 or C5\. However, we recommend that you complete them before you migrate if you are migrating specifically to an EC2 Bare Metal instance type\. 

## Part 1: Installing and upgrading AWS PV drivers<a name="upgrade-pv"></a>

Though AWS PV drivers are not used in the Nitro system, you should still upgrade them if you are on previous versions of either Citrix PV or AWS PV\. The latest AWS PV drivers resolve bugs in previous versions of the drivers that may appear while you are on a Nitro system, or if you need to migrate back to a Xen\-based instance\. As a best practice, we recommend always updating to the latest drivers for Windows instances on AWS\. 

Use the following procedure to perform an in\-place upgrade of AWS PV drivers, or to upgrade from Citrix PV drivers to AWS PV drivers on Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, or Windows Server 2019\. For more information, see [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)\. 

To upgrade a Domain Controller, see [Upgrade a Domain Controller \(AWS PV Upgrade\)](Upgrading_PV_drivers.md#aws-pv-upgrade-dc)\.

**To perform an upgrade of or to AWS PV drivers**

1. Connect to the instance using Remote Desktop and prepare the instance for upgrade\. Take all non\-system disks offline before you perform the upgrade\. If you are performing an in\-place update of AWS PV drivers, this step is not required\. Set non\-essential services to **Manual** start\-up in the Services console\. 

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the latest driver package to the instance\. 

1. Extract the contents of the folder and run `AWSPVDriverSetup.msi`\. 

After running the MSI, the instance automatically reboots and upgrades the driver\. The instance may not be available for up to 15 minutes\. 

After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, connect to the instance using Remote Desktop and verify that the new driver was installed\. In Device Manager, under **Storage Controllers**, locate **AWS PV Storage Host Adapter**\. Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV Driver Package History](xen-drivers-overview.md#pv-driver-history)\. 

## Part 2: Installing and upgrading ENA<a name="upgrade-ena"></a>

Upgrade to the latest Elastic Network Adapter driver to ensure that all network features are supported\. If you launched your instance and it does not have enhanced networking already enabled, you must download and install the required network adapter driver on your instance\. Then, set the enaSupport instance attribute to **activate enhanced networking**\. You can only enable this attribute on supported instance types and only if the ENA driver is installed\. For more information, see [Enabling enhanced networking with the Elastic Network Adapter \(ENA\) on Windows instances](enhanced-networking-ena.md)\. 

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/Latest/AwsEnaNetworkDriver.zip) the latest driver to the instance\.

1. Extract the zip archive\. 

1. Install the driver by running the `install.ps1` PowerShell script from the extracted folder\. 
**Note**  
To avoid installation errors, run the `install.ps1` script as an administrator\.

1.  Check if your AMI has enaSupport activated\. If not, continue by following the documentation at [Enabling enhanced networking with the Elastic Network Adapter \(ENA\) on Windows instances](enhanced-networking-ena.md)\. 

## Part 3: Upgrading AWS NVMe drivers<a name="upgrade-nvme"></a>

AWS NVMe drivers are used to interact with Amazon EBS and SSD instance store volumes that are exposed as NVMe block devices in the Nitro system for better performance\. 

**Important**  
The following instructions are modified specifically for when you install or upgrade AWS NVMe on a previous generation instance with the intention to migrate the instance to the latest generation instance type\.

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\. 

1. Extract the zip archive\.

1. Install the driver by running `dpinst.exe`\.

1. Open a PowerShell session and run the following command: 

   `start rundll32.exe sppnp.dll,Sysprep_Generalize_Pnp -wait`
**Note**  
To apply the command, you must run the PowerShell session as the administrator\.  
This command only runs sysprep on the driver devices\. It does not run the full sysprep preparation\.

1. For Windows Server 2008 R2 and Windows Server 2012, shut down the instance, change the instance type to a latest generation instance and start it, then proceed to Part 4\. If you start the instance again on a previous generation instance type before migrating to a latest generation instance type, it will not boot\. For other supported Windows AMIs, you can change the instance type anytime after the device sysprep\.

## Part 4: Updating EC2Config and EC2Launch<a name="upgdate-ec2config-ec2launch"></a>

For Windows instances, the latest EC2Config and EC2Launch utilities provide additional functionality and information when running on the Nitro system, including on EC2 Bare Metal\. By default, the EC2Config service is included in AMIs prior to Windows Server 2016\. EC2Launch replaces EC2Config on Windows Server 2016 and later AMIs\. 

When the EC2Config and EC2Launch services are updated, new Windows AMIs from AWS include the latest version of the service\. However, you must update your own Windows AMIs and instances with the latest version of EC2Config and EC2Launch\.

**To install or update EC2Config**

1. Download and unzip the [ EC2Config Installer](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip)\.

1. Run `EC2Install.exe`\. For a complete list of options, run `EC2Install` with the `/?` option\. By default, setup displays prompts\. To run the command with no prompts, use the `/quiet` option\.

For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

**To install or update EC2Launch**

1. If you have already installed and configured EC2Launch on an instance, make a backup of the EC2Launch configuration file\. The installation process does not preserve changes in this file\. By default, the file is located in the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config` directory\. 

1. Download [EC2\-Windows\-Launch\.zip](https://s3.amazonaws.com/ec2-downloads-windows/EC2Launch/latest/EC2-Windows-Launch.zip) to a directory on the instance\. 

1. Download [install\.ps1](https://s3.amazonaws.com/ec2-downloads-windows/EC2Launch/latest/install.ps1) to the same directory where you downloaded `EC2-Windows-Launch.zip`\. 

1. Run `install.ps1`\.
**Note**  
To avoid installation errors, run the `install.ps1` script as an administrator\.

1. If you made a backup of the EC2Launch configuration file, copy it to the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config` directory\. 

For more information, see [Configuring a Windows instance using EC2Launch](ec2launch.md)\.

## Part 5: Installing the serial port driver for bare metal instances<a name="install-serial-port-bare-metal"></a>

The `i3.metal` instance type uses a PCI\-based serial device rather than an I/O port\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device and have the serial port driver installed\. If you are not using an instance launched from an Amazon\-provided Windows AMI dated 2018\.04\.11 or later, you must install the Serial Port Driver to enable the serial device for EC2 features such as Password Generation and Console Output\. The latest EC2Config and EC2Launch utilities also support i3\.metal and provide additional functionality\. Follow the steps in Part 4, if you have not yet done so\. 

**To install the serial port driver**

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPCISerialDriver/Latest/AWSPCISerialDriver.zip) the serial driver package to the instance\. 

1. Extract the contents of the folder, open the context \(right\-click\) menu for aws\_ser\.INF, and choose **install**\. 

1. Choose **Okay**\.

## Part 6: Updating power management settings<a name="power-management"></a>

The following update to power management settings sets displays to never turn off, which allows for graceful OS shutdowns on the Nitro system\. All Windows AMIs provided by Amazon as of 2018\.11\.28 already have this default configuration\.

1. Open a command prompt or PowerShell session\.

1. Run the following commands:

   ```
   powercfg /setacvalueindex 381b4222-f694-41f0-9685-ff5bb260df2e 7516b95f-f776-4464-8c53-06167f40cc99 3c0bc021-c8a8-4e07-a973-6b14cbcb2b7e 0
   powercfg /setacvalueindex 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c 7516b95f-f776-4464-8c53-06167f40cc99 3c0bc021-c8a8-4e07-a973-6b14cbcb2b7e 0
   powercfg /setacvalueindex a1841308-3541-4fab-bc81-f71556f20b4a 7516b95f-f776-4464-8c53-06167f40cc99 3c0bc021-c8a8-4e07-a973-6b14cbcb2b7e 0
   ```

## Part 7: Updating Intel chipset drivers for new instance types<a name="power-management"></a>

The `u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instance types use hardware that requires chipset drivers that were not previously installed on Windows AMIs\. If you are not using an instance launched from an Amazon\-provided Windows AMI dated 2018\.11\.19 or later, you must install the drivers using the Intel Chipset INF Utility\. 

**To install the chipset drivers**

1. [Download the chipset utility](https://downloadcenter.intel.com/download/28182/Chipset-INF-Utility?product) to the instance\.

1. Extract the files\.

1. Run `SetupChipset.exe`\.

1. Accept the Intel software license agreement and install the chipset drivers\.

1. Reboot the instance\.

## \(Alternative\) Upgrading the AWS PV, ENA, and NVMe drivers using AWS Systems Manager<a name="auto-upgrade"></a>

The `AWSSupport-UpgradeWindowsAWSDrivers` automation document automates the steps described in Part 1, Part 2, and Part 3\. This method can also repair an instance where the driver upgrades have failed\. 

The `AWSSupport-UpgradeWindowsAWSDrivers` automation document upgrades or repairs storage and network AWS drivers on the specified EC2 instance\. The document attempts to install the latest versions of AWS drivers online by calling the AWS Systems Manager Agent \(SSM Agent\)\. If SSM Agent is not contactable, the document can perform an offline installation of the AWS drivers if explicitly requested\.

**Note**  
This procedure will fail on a domain controller\. To update drivers on a domain controller, see [Upgrade a Domain Controller \(AWS PV Upgrade\)](Upgrading_PV_drivers.md#aws-pv-upgrade-dc)\.

**To automatically upgrade the AWS PV, ENA, and NVMe drivers using AWS Systems Manager**

1. Open the Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager](https://console.aws.amazon.com/systems-manager)\.

1. Choose **Automation**, **Execute Automation**\.

1. Choose the **AWSSupport\-UpgradeWindowsAWSDrivers** automation document and then configure the following options in the **Input Parameters** section:  
Instance ID  
Enter the unique ID of the instance to upgrade\.  
AllowOffline  
\(Optional\) Choose one of the following options:  
   + `True` — Choose this option to perform an offline installation\. The instance is stopped and restarted during the upgrade process\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To preserve data on instance store volumes, ensure that you back up the data to persistent storage\.
   + `False` — \(Default\) To perform an online installation, leave this option selected\. The instance is restarted during the upgrade process\.
Online and offline upgrades create an AMI before attempting the upgrade operations\. The AMI persists after the automation completes\. Secure your access to the AMI, or delete it if it is no longer needed\.  
SubnetId  
\(Optional\) Enter one of the following values:  
   + `SelectedInstanceSubnet` — \(Default\) The upgrade process launches the *helper* instance into the same subnet as the instance that is to be upgraded\. The subnet must allow communication to the Systems Manager endpoints \(`ssm.*`\)\.
   + `CreateNewVPC` — The upgrade process launches the *helper* instance into a new VPC\. Use this option if you're not sure whether the target instance's subnet allows communication to the `ssm.*` endpoints\. Your IAM user must have permission to create a VPC\.
   + A specific subnet ID — Specify the ID of a specific subnet into which to launch the *helper* instance\. The subnet must be in the same Availability Zone as the instance that is to be upgraded, and it must allow communication with the `ssm.*` endpoints\.

1. Choose **Execute automation**\.

1. Allow the upgrade to complete\. It could take up to 10 minutes to complete an online upgrade, and up to 25 minutes to complete an offline upgrade\.