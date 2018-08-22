# Migrating to Latest Generation Instance Types<a name="migrating-latest-types"></a>

The AWS Windows AMIs are configured with the default settings used by the Microsoft installation media with some customizations, including drivers and configurations, that support the latest generation instance types\. However, if you are launching instances from custom Windows AMIs or from Windows AMIs provided by Amazon created prior to August 2018, we recommend you follow the steps in this guide when migrating to the latest generation of EC2 instances, including instances in the Nitro system and Bare Metal families\. 

For more information on the Nitro system, see [Amazon EC2 Update — Additional Instance Types, Nitro System, and CPU Options](https://aws.amazon.com/blogs/aws/amazon-ec2-update-additional-instance-types-nitro-system-and-cpu-options/)\. 

**Before you begin** 

This guide assumes you are currently running on a previous generation Xen\-based instance type, such as an M4 or C4, and you are migrating to a latest generation instance type, such as an M5 or C5\. 

Before following the steps in this guide, we recommend that you create a backup of the instance\. From the [EC2 console](https://console.aws.amazon.com/ec2/), choose the instance that requires the migration, open the context \(right\-click\) menu, choose **Instance State**, and then choose **Stop**\. 

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. Therefore, if you have any data on instance store volumes that you wish to preserve, ensure that you back it up to persistent storage\. 

 Open the context \(right\-click\) menu for the instance in the [EC2 console](https://console.aws.amazon.com/ec2/), choose **Image**, and then choose **Create Image**\. 

**Note**  
Parts 4 and 5 of these instructions can be completed after you migrate or change the instance type to the latest generation, such as M5 or C5\. However, we recommend that you complete them before you migrate if you are migrating specifically to an EC2 Bare Metal instance type\. 

## Part 1: Installing and upgrading AWS PV drivers<a name="upgrade-pv"></a>

Though AWS PV drivers are not utilized in the Nitro system, we still recommend upgrading them if you are on previous versions of either Citrix PV or AWS PV\. The latest AWS PV drivers resolve bugs in previous versions of the drivers that may appear while you are on a Nitro system, or if you need to migrate back to a Xen\-based instance\. As best practice, we recommend always updating to the latest drivers for Windows instances on AWS\. 

Use the following procedure to perform an in\-place upgrade of AWS PV drivers, or to upgrade from Citrix PV drivers to AWS PV drivers on Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, or Windows Server 2016\. See [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)\. 

1. Connect to the instance using Remote Desktop and prepare the instance for upgrade\. We recommend that you take all non\-system disks offline before you perform the upgrade\. Note that this step is not required if you are performing an in\-place update of AWS PV drivers\. We also recommend setting non\-essential services to **Manual** start\-up in the Services console\. 

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the latest driver package to the instance\. 

1. Extract the contents of the folder and then run `AWSPVDriverSetup.msi`\. 

   After running the MSI, the instance automatically reboots and then upgrades the driver\. The instance will not be available for up to 15 minutes\. After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, connect to the instance using Remote Desktop and verify that the new driver was installed\. In Device Manager, under **Storage Controllers**, locate **AWS PV Storage Host Adapter**\. Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV Driver Version History](xen-drivers-overview.md#pv-driver-history)\. 

## Part 2: Installing and upgrading ENA<a name="upgrade-ena"></a>

We recommend you upgrade to the latest Elastic Network Adapter driver to ensure all network features are supported\. If you launched your instance and it does not have enhanced networking already enabled, you must download and install the required network adapter driver on your instance and then set the enaSupport instance attribute to **activate enhanced networking**\. You can only enable this attribute on supported instance types and only if the ENA driver is installed\. For more information, see [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Windows Instances](enhanced-networking-ena.md)\. 

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/ENA/Latest/AwsEnaNetworkDriver.zip) the latest driver to the instance\. 

1. Extract the zip archive\. 

1. Install the driver by running the `install.ps1` PowerShell script from the extracted folder\. 

1.  Check if your AMI has enaSupport activated and, if not, continue by following the documentation at [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Windows Instances](enhanced-networking-ena.md)\. 

## Part 3: Upgrading AWS NVMe drivers<a name="upgrade-nvme"></a>

AWS NVMe drivers are used to interact with EBS and SSD instance store volumes that are exposed as NVMe block devices in the Nitro system for better performance\. 

**Important**  
The following instructions are modified specifically for when you install or upgrade AWS NVMe on a previous generation instance with the intention to migrate the instance to the latest generation instance type\.

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\. 

1. Extract the zip archive\.

1. Install the driver by running `dpinst.exe`\.

1. Open a PowerShell session and run a “device sysprep”: 

   `start rundll32.exe sppnp.dll,Sysprep_Generalize_Pnp -wait`
**Note**  
This step only performs a sysprep on the driver devices\. It does not perform a full sysprep\.

1. For Windows Server 2008 R2, shut down the instance, change the instance type to a latest generation instance and start it, then proceed to Part 4\. If you start the instance again on a previous generation instance type before migrating to a latest generation instance type, it will not boot\. For other supported Windows AMIs, you can change the instance type any time after the device sysprep\.

## Part 4: Updating EC2Config and EC2Launch<a name="upgdate-ec2config-ec2launch"></a>

For Windows instances, the latest EC2Config and EC2Launch will provide additional functionality and information when running on the Nitro system, including on EC2 Bare Metal\. By default, the EC2Config service is included in AMIs prior to Windows Server 2016\. EC2Launch replaces EC2Config on Windows Server 2016 AMIs\. When the EC2Config and EC2Launch services are updated, new Windows AMIs from AWS include the latest version of the service\. However, you need to update your own Windows AMIs and instances with the latest version of EC2Config and EC2Launch\.

** Installing or updating EC2Config**

1. Download and unzip the [ EC2Config Installer](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip)\.

1. Run `EC2Install.exe`\. For a complete list of options, run `EC2Install` with the `/?` option\. By default, setup displays prompts\. To run the command with no prompts, use the `/quiet` option\.

   For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

**Installing or updating EC2Launch**

1. If you have already installed and configured EC2Launch on an instance, make a backup of the EC2Launch configuration file\. The installation process does not preserve changes in this file\. By default, the file is located in the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config` directory\. 

1. Download [EC2\-Windows\-Launch\.zip](https://s3.amazonaws.com/ec2-downloads-windows/EC2Launch/latest/EC2-Windows-Launch.zip) to a directory on the instance\. 

1. Download [install\.ps1](https://s3.amazonaws.com/ec2-downloads-windows/EC2Launch/latest/install.ps1) to the same directory where you downloaded `EC2-Windows-Launch.zip`\. 

1. Run `install.ps1`\.

1. If you made a backup of the EC2Launch configuration file, copy it to the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config` directory\. 

   For more information, see [Configuring a Windows Instance Using EC2Launch](ec2launch.md)\.

## Part 5: Installing the Serial Port driver for Bare Metal Instances<a name="install-serial-port-bare-metal"></a>

 `i3.metal` instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device and have the serial port driver installed\. If you are not using an instance launched from an Amazon\-provided Windows AMI dated 2018\.04\.11 or later, you will need to install the Serial Port Driver to enable the serial device for EC2 features such as Password Generation and Console Output\. The latest EC2Config and EC2Launch also support i3\.metal and provide additional functionality, therefore we recommend following the steps in Part 4 if you have not yet done so\. 

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPCISerialDriver/Latest/AWSPCISerialDriver.zip) the serial driver package to the instance\. 

1. Extract the contents of the folder, right click on aws\_ser\.INF, and choose **install**\. 

1. Confirm the operation completed by selecting **Okay**\.