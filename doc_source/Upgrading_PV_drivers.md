# Upgrade PV drivers on Windows instances<a name="Upgrading_PV_drivers"></a>

We recommend that you install the latest PV drivers to improve the stability and performance of your EC2 Windows instances\. The directions on this page help you download the driver package and run the install program\.

**To verify which driver your Windows instance uses**

Open **Network Connections** in Control Panel and view **Local Area Connection**\. Check whether the driver is one of the following:
+ AWS PV Network Device
+ Citrix PV Ethernet Adapter
+ RedHat PV NIC Driver

Alternatively, you can check the output from the `pnputil -e` command\.

**System requirements**  
Be sure to check the `readme.txt` file in the download for system requirements\.

**Topics**
+ [Upgrade Windows Server instances \(AWS PV upgrade\)](#aws-pv-upgrade)
+ [Upgrade a domain controller \(AWS PV upgrade\)](#aws-pv-upgrade-dc)
+ [Upgrade Windows Server 2008 and 2008 R2 instances \(Redhat to Citrix PV upgrade\)](#win2008-citrix-upgrade)
+ [Upgrade your Citrix Xen guest agent service](#citrix-pv-guest-agent-upgrade)

## Upgrade Windows Server instances \(AWS PV upgrade\)<a name="aws-pv-upgrade"></a>

Use the following procedure to perform an in\-place upgrade of AWS PV drivers, or to upgrade from Citrix PV drivers to AWS PV drivers on Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2,Windows Server 2016, or Windows Server 2019\. This upgrade is not available for RedHat drivers, or for other versions of Windows Server\.

**Important**  
If your instance is a domain controller, see [Upgrade a domain controller \(AWS PV upgrade\)](#aws-pv-upgrade-dc)\. The upgrade process for domain controller instances is different than standard editions of Windows\. 

**To upgrade AWS PV drivers**

1. We recommend that you create an AMI as a backup as follows, in case you need to roll back your changes\.

   1. When you stop an instance, the data on any instance store volumes is erased\. Before you stop an instance, verify that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

   1. In the navigation pane, choose **Instances**\.

   1. Select the instance that requires the driver upgrade, and choose **Instance state**, **Stop instance**\.

   1. After the instance is stopped, select the instance, choose **Actions**, then **Image and templates**, and then choose **Create image**\.

   1. Choose **Instance state**, **Start instance**\.

1. Connect to the instance using Remote Desktop\.

1. We recommend that you take all non\-system disks offline and note any drive letter mappings to the secondary disks in Disk Management before you perform this upgrade\. This step is not required if you are performing an in\-place update of AWS PV drivers\. We also recommend setting non\-essential services to **Manual** start\-up in the Services console\.

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the latest driver package to the instance\.

    Or, run the following PowerShell command:

   ```
   PS C:\>invoke-webrequest https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip -outfile $env:USERPROFILE\pv_driver.zip
   expand-archive $env:userprofile\pv_driver.zip -DestinationPath $env:userprofile\pv_drivers
   ```

1. Extract the contents of the folder and then run `AWSPVDriverSetup.msi`\.

After running the MSI, the instance automatically reboots and then upgrades the driver\. The instance will not be available for up to 15 minutes\. After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, you can verify that the new driver was installed by connecting to the instance using Remote Desktop and then running the following PowerShell command:

```
Get-ItemProperty HKLM:\SOFTWARE\Amazon\PVDriver
```

Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV driver package history](xen-drivers-overview.md#pv-driver-history) Open Disk Management to review any offline secondary volumes and bring them online corresponding to the drive letters noted in Step 6\.

If you previously disabled [TCP offloading](pvdrivers-troubleshooting.md#citrix-tcp-offloading) using Netsh for Citrix PV drivers we recommend that you re\-enable this feature after upgrading to AWS PV drivers\. TCP Offloading issues with Citrix drivers are not present in the AWS PV drivers\. As a result, TCP Offloading provides better performance with AWS PV drivers\.

If you previously applied a static IP address or DNS configuration to the network interface, you must reapply the static IP address or DNS configuration after upgrading AWS PV drivers\.

## Upgrade a domain controller \(AWS PV upgrade\)<a name="aws-pv-upgrade-dc"></a>

Use the following procedure on a domain controller to perform either an in\-place upgrade of AWS PV drivers, or to upgrade from Citrix PV drivers to AWS PV drivers\.

**To upgrade a domain controller**

1. We recommend that you create a backup of your domain controller in case you need to roll back your changes\. Using an AMI as a backup is not supported\. For more information, see [Backup and Restore Considerations for Virtualized Domain Controllers](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#backup-and-restore-considerations-for-virtualized-domain-controllers) in the Microsoft documentation\.

1. Run the following command to configure Windows to boot into Directory Services Restore Mode \(DSRM\)\.
**Warning**  
Before running this command, confirm that you know the DSRM password\. You'll need this information so that you can log in to your instance after the upgrade is complete and the instance automatically reboots\.

   ```
   bcdedit /set {default} safeboot dsrepair
   ```

   PowerShell:

   ```
   PS C:\> bcdedit /set "{default}" safeboot dsrepair
   ```

   The system must boot into DSRM because the upgrade utility removes Citrix PV storage drivers so it can install AWS PV drivers\. Therefore we recommend noting any drive letter and folder mappings to the secondary disks in Disk Management\. When Citrix PV storage drivers are not present, secondary drives are not detected\. Domain controllers that use an NTDS folder on secondary drives will not boot because the secondary disk is not detected\.
**Warning**  
After you run this command do not manually reboot the system\. The system will be unreachable because Citrix PV drivers do not support DSRM\.

1. Run the following command to add **DisableDCCheck** to the registry:

   ```
   reg add HKLM\SOFTWARE\Wow6432Node\Amazon\AWSPVDriverSetup /v DisableDCCheck /t REG_SZ /d true
   ```

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the latest driver package to the instance\.

1. Extract the contents of the folder and then run `AWSPVDriverSetup.msi`\.

   After running the MSI, the instance automatically reboots and then upgrades the driver\. The instance will not be available for up to 15 minutes\. 

1. After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, connect to the instance using Remote Desktop\. Open Disk Management to review any offline secondary volumes and bring them online corresponding to the drive letters and folder mappings noted earlier\.

   You must connect to the instance by specifying the user name in the following format *hostname*\\administrator\. For example, Win2k12TestBox\\administrator\.

1. Run the following command to remove the DSRM boot configuration:

   ```
   bcdedit /deletevalue safeboot
   ```

1. Reboot the instance\.

1. To complete the upgrade process, verify that the new driver was installed\. In Device Manager, under **Storage Controllers**, locate **AWS PV Storage Host Adapter**\. Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV driver package history](xen-drivers-overview.md#pv-driver-history)\.

1. Run the following command to delete **DisableDCCheck** from the registry:

   ```
   reg delete HKLM\SOFTWARE\Wow6432Node\Amazon\AWSPVDriverSetup /v DisableDCCheck
   ```

**Note**  
If you previously disabled [TCP offloading](pvdrivers-troubleshooting.md#citrix-tcp-offloading) using Netsh for Citrix PV drivers we recommend that you re\-enable this feature after upgrading to AWS PV Drivers\. TCP Offloading issues with Citrix drivers are not present in the AWS PV drivers\. As a result, TCP Offloading provides better performance with AWS PV drivers\.

## Upgrade Windows Server 2008 and 2008 R2 instances \(Redhat to Citrix PV upgrade\)<a name="win2008-citrix-upgrade"></a>

Before you start upgrading your RedHat drivers to Citrix PV drivers, make sure you do the following:
+ Install the latest version of the EC2Config service\. For more information, see [Install the latest version of EC2Config](UsingConfig_Install.md)\.
+ Verify that you have Windows PowerShell 3\.0 installed\. To verify the version that you have installed, run the following command in a PowerShell window:

  ```
  PS C:\> $PSVersionTable.PSVersion
  ```

  Windows PowerShell 3\.0 is bundled in the Windows Management Framework \(WMF\) version 3\.0 install package\. If you need to install Windows PowerShell 3\.0, see [Windows Management Framework 3\.0](https://www.microsoft.com/en-us/download/details.aspx?id=34595) in the Microsoft Download Center\.
+ Back up your important information on the instance, or create an AMI from the instance\. For more information about creating an AMI, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\. If you create an AMI, make sure that you do the following:
  + Write down your password\.
  + Do not run the Sysprep tool manually or using the EC2Config service\.
  + Set your Ethernet adapter to obtain an IP address automatically using DHCP\. For more information, see [Configure TCP/IP Settings](https://technet.microsoft.com/en-us/library/cc731673.aspx) in the Microsoft TechNet Library\.

**To upgrade RedHat drivers**

1. Connect to your instance and log in as the local administrator\. For more information about connecting to your instance, see [Connect to your Windows instance](connecting_to_windows_instance.md)\.

1. In your instance, [download](https://s3.amazonaws.com/ec2-downloads-windows/Drivers/Citrix-Win_PV.zip) the Citrix PV upgrade package\.

1. Extract the contents of the upgrade package to a location of your choice\.

1. Double\-click the **Upgrade\.bat** file\. If you get a security warning, choose **Run**\.

1. In the **Upgrade Drivers** dialog box, review the information and choose **Yes** if you are ready to start the upgrade\.

1. In the **Red Hat Paravirtualized Xen Drivers for Windows uninstaller** dialog box, choose **Yes** to remove the RedHat software\. Your instance will be rebooted\.
**Note**  
If you do not see the uninstaller dialog box, choose **Red Hat Paravirtualize** in the Windows taskbar\.  
![\[Red Hat Paravirtualized in taskbar\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win2003-citrix-taskbar.png)

1. Check that the instance has rebooted and is ready to be used\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. On the **Instances** page, select **Actions**, then **Monitor and troubleshoot**, and then choose **Get system log**\.

   1. The upgrade operations should have restarted the server 3 or 4 times\. You can see this in the log file by the number of times `Windows is Ready to use` is displayed\.  
![\[Windows system log\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win2008-sys-log.png)

1. Connect to your instance and log in as the local administrator\.

1. Close the **Red Hat Paravirtualized Xen Drivers for Windows uninstaller** dialog box\.

1. Confirm that the installation is complete\. Navigate to the `Citrix-WIN_PV` folder that you extracted earlier, open the `PVUpgrade.log` file, and then check for the text `INSTALLATION IS COMPLETE`\.  
![\[PVUpgrade log file\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win2008-pvupgrade-log.png)

## Upgrade your Citrix Xen guest agent service<a name="citrix-pv-guest-agent-upgrade"></a>

If you are using Citrix PV drivers on Windows Server, you can upgrade the Citrix Xen guest agent service\. This Windows service handles tasks such as shutdown and restart events from the API\. You can run this upgrade package on any version of Windows Server, as long as the instance is running Citrix PV drivers\.

**Important**  
For Windows Server 2008 R2 and later, we recommend you upgrade to AWS PV drivers that include the Guest Agent update\.

Before you start upgrading your drivers, make sure you back up your important information on the instance, or create an AMI from the instance\. For more information about creating an AMI, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\. If you create an AMI, make sure you do the following:
+ Do not enable the Sysprep tool in the EC2Config service\.
+ Write down your password\.
+ Set your Ethernet adapter to DHCP\. 

**To upgrade your Citrix Xen guest agent service**

1. Connect to your instance and log in as the local administrator\. For more information about connecting to your instance, see [Connect to your Windows instance](connecting_to_windows_instance.md)\.

1. On your instance, [download](https://s3.amazonaws.com/ec2-downloads-windows/Drivers/Citrix-Win_PV.zip) the Citrix upgrade package\.

1. Extract the contents of the upgrade package to a location of your choice\.

1. Double\-click the **Upgrade\.bat** file\. If you get a security warning, choose **Run**\.

1. In the **Upgrade Drivers** dialog box, review the information and choose **Yes** if you are ready to start the upgrade\. 

1. When the upgrade is complete, the `PVUpgrade.log` file will open and contain the text `UPGRADE IS COMPLETE`\.

1. Reboot your instance\. 