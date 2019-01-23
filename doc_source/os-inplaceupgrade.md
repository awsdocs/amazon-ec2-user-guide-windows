# Performing an In\-Place Upgrade<a name="os-inplaceupgrade"></a>

Before you perform an in\-place upgrade, you must determine which network drivers the instance is running\. PV network drivers enable you to access your instance using Remote Desktop\. Starting with Windows Server 2008 R2, instances use either *AWS PV*, Intel Network Adapter, or the Enhanced Networking drivers\. Instances with Windows Server 2003 and Windows Server 2008 use *Citrix PV* drivers\. For more information, see [Paravirtual Drivers for Windows Instances](xen-drivers-overview.md)\.

## Before You Begin an In\-Place Upgrade<a name="os-upgrade-before"></a>

Complete the following tasks and note the following important details before you begin your in\-place upgrade\.
+ Read the Microsoft documentation to understand the upgrade requirements, known issues, and restrictions\. Also review the official instructions for upgrading\.
  + [Upgrading to Windows Server 2008](https://technet.microsoft.com/en-us/library/cc754728.aspx)
  + [Upgrading to Windows Server 2008 R2](https://technet.microsoft.com/en-us/library/ff968983.aspx)
  + [Upgrade Options for Windows Server 2012](https://technet.microsoft.com/en-us/library/jj574204.aspx)
  + [Upgrade Options for Windows Server 2012 R2](https://technet.microsoft.com/en-us/library/dn303416.aspx)
  + [Upgrade and conversion options for Windows Server 2016](https://docs.microsoft.com/en-us/windows-server/get-started/supported-upgrade-paths)
  + [Upgrade and conversion options for Windows Server 2019](https://docs.microsoft.com/en-us/windows-server/get-started-19/install-upgrade-migrate-19)
  + [Windows Server Upgrade Center](https://www.microsoft.com/upgradecenter)
+ We do not recommend performing an operating system upgrade on a T1 or T2 instance type\. These types of instances might not have enough resources to manage the upgrade process\. To upgrade one of these instances, you must resize the instance to another instance type, perform the upgrade, and then resize it back to a T1 or T2 instance type\. For more information, see [Changing the Instance Type](ec2-instance-resize.md)\.
+ Verify that the root volume on your Windows instance has enough free disk space\. The Windows Setup process might not warn you of insufficient disk space\. For information about how much disk space is required to upgrade a specific operating system, see the Microsoft documentation\. If the volume does not have enough space, it can be expanded\. For more information, see [Modifying the Size, IOPS, or Type of an EBS Volume on Windows](ebs-modify-volume.md)\.
+ Determine your upgrade path\. You must upgrade the operating system to the same architecture\. For example, you must upgrade a 32\-bit system to a 32\-bit system\. Windows Server 2008 R2 and later are 64\-bit only\.
+ Disable antivirus and anti\-spyware software and firewalls\. These types of software can conflict with the upgrade process\. Re\-enable antivirus and anti\-spyware software and firewalls after the upgrade completes\.
+ Update to the latest drivers as described in [Migrating to Latest Generation Instance Types](migrating-latest-types.md)\.
+ The Upgrade Helper Service only supports instances running Citrix PV drivers\. If the instance is running Red Hat drivers, you must manually [upgrade those drivers](Upgrading_PV_drivers.md) first\.

## Upgrade an Instance In\-Place with AWS PV, Intel Network Adapter, or the Enhanced Networking Drivers<a name="os-upgrade-pv"></a>

Use the following procedure to upgrade a Windows Server instance using the AWS PV, Intel Network Adapter, or the Enhanced Networking network drivers\.

**To perform the in\-place upgrade**

1. Create an AMI of the system you plan to upgrade for either backup or testing purposes\. You can then perform the upgrade on the copy to simulate a test environment\. If the upgrade completes, you can switch traffic to this instance with little downtime\. If the upgrade fails, you can revert to the backup\. For more information, see [Creating a Custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

1. Ensure that your Windows Server instance is using the latest network drivers\. See [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md) for information on upgrading your AWS PV driver\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. Locate the instance\. Make a note of the instance ID and Availability Zone for the instance\. You need this information later in this procedure\.

1. If you are upgrading from Windows Server 2012 or 2012 R2 to Windows Server 2019, do the following on your instance before proceeding:

   1. Uninstall the EC2Config service\. For more information, see [Stopping, Restarting, Deleting, or Uninstalling EC2Config](ec2config-service.md#UsingConfig_StopDelete)\.

   1. Install the EC2Launch service\. For more information, see [Installing the Latest Version of EC2Launch](ec2launch-download.md)\.

   1. Install the Amazon SSM Agent\. For more information, see [Installing SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html)\.

1. Create a new volume from a Windows Server installation media snapshot\.

   1. In the navigation pane, choose **Snapshots**, **Public Snapshots**\.

   1. Add the **Owner** filter and choose **Amazon images**\.

   1. Add the **Description** filter and type **Windows**\. Press Enter\.

   1. Select the snapshot that matches the system architecture and language preference you are upgrading to\. For example, select **Windows 2019 English Installation Media** to upgrade to Windows Server 2019\.

   1. Choose **Actions**, **Create Volume**\.

   1. In the **Create Volume** dialog box, choose the Availability Zone that matches your Windows instance, and choose **Create**\.

1. In the **Volume Successfully Created** message, choose the volume that you just created\.

1. Choose **Actions**, **Attach Volume**\.

1. In the **Attach Volume** dialog box, type the instance ID and choose **Attach**\.

1. Begin the upgrade by using Windows PowerShell to open the installation media volume you attached to the instance\.

   1. If you are upgrading to Windows Server 2019, run the following:

      ```
      ./setup.exe /auto upgrade
      ```

      If you are upgrading to an earlier version of Windows Server, run the following:

      ```
      Sources/setup.exe
      ```

   1. For **Select the operating system you want to install**, select the full installation SKU for your Windows Server instance, and choose **Next**\.

   1. For **Which type of installation do you want?**, choose **Upgrade**\.

   1. Complete the wizard\.

Windows Server Setup copies and processes files\. After several minutes, your Remote Desktop session closes\. The time it takes to upgrade depends on the number of applications and server roles running on your Windows Server instance\. The upgrade process could take as little as 40 minutes or several hours\. The instance fails status check 1 of 2 during the upgrade process\. When the upgrade completes, both status checks pass\. You can check the system log for console output or use Amazon CloudWatch metrics for disk and CPU activity to determine whether the upgrade is progressing\.

**Note**  
If upgrading to Windows Server 2019, after the upgrade is complete you can change the desktop background manually to remove the previous operating system name if desired\.

If the instance has not passed both status checks after several hours, see [Troubleshooting an Upgrade](os-upgrade-trbl.md)\.

## Upgrade an Instance In\-Place with Citrix PV Drivers<a name="os-upgrade-citrix"></a>

Citrix PV drivers are used in Windows Server 2003 and 2008\. There is a known issue during the upgrade process where Windows Setup removes portions of the Citrix PV drivers that enable you to connect to the instance by using Remote Desktop\. To avoid this problem, the following procedure describes how to use the Upgrade Helper Service during your in\-place upgrade\.

### Using the Upgrade Helper Service<a name="os-upgradehelper"></a>

You must run the Upgrade Helper Service before you start the upgrade\. After you run it, the utility creates a Windows service that executes during the post\-upgrade steps to correct the driver state\. The executable is written in C\# and can run on \.NET Framework versions 2\.0 through 4\.0\.

When you run Upgrade Helper Service on the system *before* the upgrade, it performs the following tasks:
+ Creates a new Windows service named `UpgradeHelperService`\.
+ Verifies that the Citrix PV drivers are installed\.
+ Checks for unsigned boot critical drivers and presents a warning if any are found\. Unsigned boot critical drivers could cause system failure after the upgrade if the drivers are not compatible with the newer Windows Server version\.

When you run Upgrade Helper Service on the system *after* the upgrade, it performs the following tasks:
+ Enables the `RealTimeIsUniversal` registry key for the correct time synchronization\.
+ Restores the missing PV driver by executing the following command:

  ```
  pnputil -i -a "C:\Program Files (x86)\Citrix\XenTools\*.inf"
  ```
+ Installs the missing device by executing the following command:

  ```
  C:\Temp\EC2DriverUtils.exe install "C:\Program Files (x86)\Citrix\XenTools\xevtchn.inf" ROOT\XENEVTCHN
  ```
+ Automatically removes `UpgradeHelperService` when complete\.

### Performing the Upgrade on Instances Running Citrix PV Drivers<a name="os-upgrade-citrix-go"></a>

To complete the upgrade, you must attach the installation media volume to your EC2 instance and use `UpgradeHelperService.exe`\.

**To upgrade a Windows Server instance running Citrix PV drivers**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and locate the instance\. Make a note of the instance ID and Availability Zone for the instance\. You need this information later in this procedure\.

1. Create a new volume from a Windows Server installation media snapshot\.

   1. In the navigation pane, choose **Snapshots**, **Public Snapshots**\.

   1. Add the **Owner** filter and choose **Amazon images**\.

   1. Add the **Description** filter and type **Windows**\. Press Enter\.

   1. Select the snapshot that matches the system architecture of your instance\. For example, **Windows 2008 64\-bit Installation Media**\.

   1. Choose **Actions**, **Create Volume**\.

   1. In the **Create Volume** dialog box, select the Availability Zone that matches your Windows instance, and choose **Create**\.

1. In the **Volume Successfully Created** dialog box, choose the volume that you just created\.

1. Choose **Actions**, **Attach Volume**\.

1. In the **Attach Volume** dialog box, type the instance ID and choose **Attach**\.

1. On your Windows instance, on the `C:\` drive, create a folder named `temp`\.
**Important**  
This folder must be available in the same location after the upgrade\. Creating the folder in a Windows system folder or a user profile folder, such as the desktop, can cause the upgrade to fail\.

1. [Download OSUpgrade\.zip](https://s3.amazonaws.com/ec2-downloads-windows/Upgrade/OSUpgrade.zip) and extract the files into the `C:\temp` folder\.

1. Run `C:\temp\UpgradeHelperService.exe` and review the `C:\temp\Log.txt` file for any warnings\.

1. Use [Knowledge Base article 950376](http://support.microsoft.com/en-us/kb/950376) from Microsoft to uninstall PowerShell from a Windows 2003 instance\.

1. Begin the upgrade by using Windows Explorer to open the installation media volume that you attached to the instance\.

1. Run the `Sources\Setup.exe` file\.

1. For **Select the operating system you want to install**, select the full installation SKU for your Windows Server instance, and then choose **Next**\.

1. For **Which type of installation do you want?**, choose **Upgrade**\.

1. Complete the wizard\.

Windows Server Setup copies and processes files\. After several minutes, your Remote Desktop session closes\. The time it takes to upgrade depends on the number of applications and server roles running on your Windows Server instance\. The upgrade process could take as little as 40 minutes or several hours\. The instance fails status check 1 of 2 during the upgrade process\. When the upgrade completes, both status checks pass\. You can check the system log for console output or use Amazon CloudWatch metrics for disk and CPU activity to determine whether the upgrade is progressing\.

## Post Upgrade Tasks<a name="os-post"></a>

1. Log in to the instance to initiate an upgrade for the \.NET Framework and reboot the system when prompted\.

1. Install the latest version of the EC2Config service\. For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

1. Install Microsoft hotfix [KB2800213](http://support2.microsoft.com/default.aspx?scid=kb;EN-US;2800213)\.

1. Install Microsoft hotfix [KB2922223](http://support.microsoft.com/en-us/kb/2922223)\.

1. If you upgraded to Windows Server 2012 R2, we recommend that you upgrade the PV drivers to AWS PV drivers\. For more information, see [Windows Server 2012 R2](https://aws.amazon.com/windows/products/ec2/server2012r2/network-drivers/)\.

1. Re\-enable antivirus and anti\-spyware software and firewalls\.