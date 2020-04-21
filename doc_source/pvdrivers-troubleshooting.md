# Troubleshooting PV Drivers<a name="pvdrivers-troubleshooting"></a>

The following are solutions to issues that you might encounter with older Amazon EC2 images and PV drivers\.

**Topics**
+ [Windows Server 2012 R2 loses network and storage connectivity after an instance reboot](#server2012R2-instance-unavailable)
+ [TCP Offloading](#citrix-tcp-offloading)
+ [Time Synchronization](#citrix-time-sync)

## Windows Server 2012 R2 loses network and storage connectivity after an instance reboot<a name="server2012R2-instance-unavailable"></a>

**Important**  
This issue occurs only with AMIs made available before September 2014\.

Windows Server 2012 R2 Amazon Machine Images \(AMIs\) made available before September 10, 2014 can lose network and storage connectivity after an instance reboot\. The error in the AWS Management Console system log states: “Difficulty detecting PV driver details for Console Output\.” The connectivity loss is caused by the Plug and Play Cleanup feature\. This features scans for and disables inactive system devices every 30 days\. The feature incorrectly identifies the EC2 network device as inactive and removes it from the system\. When this happens, the instance loses network connectivity after a reboot\.

For systems that you suspect could be affected by this issue, you can download and run an in\-place driver upgrade\. If you are unable to perform the in\-place driver upgrade, you can run a helper script\. The script determines if your instance is affected\. If it is affected, and the Amazon EC2 network device has not been removed, the script disables the Plug and Play Cleanup scan\. If the network device was removed, the script repairs the device, disables the Plug and Play Cleanup scan, and enables your instance to reboot with network connectivity enabled\.

**Topics**
+ [Choose How You Want to Fix This Problem](#choose-fix)
+ [Method 1 \- Enhanced Networking](#plug-n-play-fix-method1)
+ [Method 2 \- Registry configuration](#plug-n-play-fix-method2)
+ [Run the Remediation Script](#plug-n-play-script)

### Choose How You Want to Fix This Problem<a name="choose-fix"></a>

There are two methods for restoring network and storage connectivity to an instance affected by this issue\. Choose one of the following methods:


| Method | Prerequisites | Procedure Overview | 
| --- | --- | --- | 
| Method 1 \- Enhanced networking | Enhanced networking is only available in a virtual private cloud \(VPC\) which requires a C3 instance type\. If the server does not currently use the C3 instance type, then you must temporarily change it\. | You change the server instance type to a C3 instance\. Enhanced networking then enables you to connect to the affected instance and fix the problem\. After you fix the problem, you change the instance back to the original instance type\. This method is typically faster than Method 2 and less likely to result in user error\. You will incur additional charges as long as the C3 instance is running\. | 
| Method 2 \- Registry configuration | Ability to create or access a second server\. Ability to change Registry settings\. | You detach the root volume from the affected instance, attach it to a different instance, connect, and make changes in the Registry\. You will incur additional charges as long as the additional server is running\. This method is slower than Method 1, but this method has worked in situations where Method 1 failed to resolve the problem\. | 

### Method 1 \- Enhanced Networking<a name="plug-n-play-fix-method1"></a>

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Locate the affected instance\. Open the context \(right\-click\) menu for the instance, choose **Instance State**, and then choose **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. After the instance is stopped create a backup\. Open the context \(right\-click\) menu for the instance, choose **Image**, and then choose **Create Image**\.

1. [Change](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-instance-resize.html) the instance type to any C3 instance type\.

1. [Start](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Stop_Start.html) the instance\.

1. Connect to the instance using Remote Desktop and then [download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the AWS PV Drivers Upgrade package to the instance\.

1. Extract the contents of the folder and run `AWSPVDriverSetup.msi`\.

   After running the MSI, the instance automatically reboots and then upgrades the drivers\. The instance will not be available for up to 15 minutes\. 

1. After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, connect to the instance using Remote Desktop and verify that the new drivers were installed\. In Device Manager, under **Storage Controllers**, locate **AWS PV Storage Host Adapter**\. Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV Driver Package History](xen-drivers-overview.md#pv-driver-history)\.

1. Stop the instance and change the instance back to its original instance type\.

1. Start the instance and resume normal use\.

### Method 2 \- Registry configuration<a name="plug-n-play-fix-method2"></a>

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Locate the affected instance\. Open the context \(right\-click\) menu for the instance, choose **Instance State**, and then choose **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. Choose **Launch Instance** and create a temporary Windows Server 2008 or Windows Server 2012 instance in the same Availability Zone as the affected instance\. Do not create a Windows Server 2012 R2 instance\.
**Important**  
If you do not create the instance in the same Availability Zone as the affected instance you will not be able to attach the root volume of the affected instance to the new instance\.

1. In the navigation pane, choose **Volumes**\.

1. Locate the root volume of the affected instance\. [Detach the volume](ebs-detaching-volume.md) and then [attach the volume](ebs-attaching-volume.md) to the temporary instance you created earlier\. Attach it with the default device name \(xvdf\)\.

1. Use Remote Desktop to connect to the temporary instance, and then use the Disk Management utility to [make the volume available for use](ebs-using-volumes.md)\.

1. On the temporary instance, open the **Run** dialog box, type **regedit**, and press Enter\.

1. In the Registry Editor navigation pane, choose **HKEY\_Local\_Machine**, and then from the **File** menu choose **Load Hive**\.

1. In the **Load Hive** dialog box, navigate to *Affected Volume*\\Windows\\System32\\config\\System and type a temporary name in the **Key Name** dialog box\. For example, enter OldSys\.

1. In the navigation pane of the Registry Editor, locate the following keys:

    **HKEY\_LOCAL\_MACHINE\\*your\_temporary\_key\_name*\\ControlSet001\\Control\\Class\\4d36e97d\-e325\-11ce\-bfc1\-08002be10318** 

    **HKEY\_LOCAL\_MACHINE\\*your\_temporary\_key\_name*\\ControlSet001\\Control\\Class\\4d36e96a\-e325\-11ce\-bfc1\-08002be10318** 

1. For each key, double\-click **UpperFilters**, enter a value of XENFILT, and then choose **OK**\.  
![\[Registry key for affected volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/troubleshooting-server2012R2-regedit.png)

1. Locate the following key:

    **HKEY\_LOCAL\_MACHINE\\*your\_temporary\_key\_name*\\ControlSet001\\Services\\XENBUS\\Parameters** 

1. Create a new string \(REG\_SZ\) with the name ActiveDevice and the following value:

    **PCI\\VEN\_5853&DEV\_0001&SUBSYS\_00015853&REV\_01** 

1. Locate the following key:

    **HKEY\_LOCAL\_MACHINE\\*your\_temporary\_key\_name*\\ControlSet001\\Services\\XENBUS** 

1. Change the **Count** from 0 to 1\.

1. Locate and delete the following keys:

    **HKEY\_LOCAL\_MACHINE\\*your\_temporary\_key\_name*\\ControlSet001\\Services\\xenvbd\\StartOverride** 

    **HKEY\_LOCAL\_MACHINE \\*your\_temporary\_key\_name*\\ControlSet001\\Services\\xenfilt\\StartOverride** 

1. In the Registry Editor navigation pane, choose the temporary key that you created when you first opened the Registry Editor\.

1. From the **File** menu, choose **Unload Hive**\.

1. In the Disk Management Utility, choose the drive you attached earlier, open the context \(right\-click\) menu, and choose **Offline**\.

1. In the Amazon EC2 console, detach the affected volume from the temporary instance and reattach it to your Windows Server 2012 R2 instance with the device name /dev/sda1\. You must specify this device name to designate the volume as a root volume\.

1. [Start](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Stop_Start.html) the instance\.

1. Connect to the instance using Remote Desktop and then [download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/AWSPV/Latest/AWSPVDriver.zip) the AWS PV Drivers Upgrade package to the instance\.

1. Extract the contents of the folder and run `AWSPVDriverSetup.msi`\.

   After running the MSI, the instance automatically reboots and then upgrades the drivers\. The instance will not be available for up to 15 minutes\. 

1. After the upgrade is complete and the instance passes both health checks in the Amazon EC2 console, connect to the instance using Remote Desktop and verify that the new drivers were installed\. In Device Manager, under **Storage Controllers**, locate **AWS PV Storage Host Adapter**\. Verify that the driver version is the same as the latest version listed in the Driver Version History table\. For more information, see [AWS PV Driver Package History](xen-drivers-overview.md#pv-driver-history)\.

1. Delete or stop the temporary instance you created in this procedure\.

### Run the Remediation Script<a name="plug-n-play-script"></a>

If you are unable to perform an in\-place driver upgrade or migrate to a newer instance you can run the remediation script to fix the problems caused by the Plug and Play Cleanup task\.

**To run the remediation script**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the instance for which you want to run the remediation script\. Open the context \(right\-click\) menu for the instance, choose **Instance State**, and then choose **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. After the instance is stopped create a backup\. Open the context \(right\-click\) menu for the instance, choose **Image**, and then choose **Create Image**\.

1. Open the context \(right\-click\) menu for the instance, choose **Instance State**, and then choose **Start**\.

1. Connect to the instance by using Remote Desktop and then [download](https://s3.amazonaws.com/ec2-downloads-windows/Scripts/RemediateDriverIssue.zip) the RemediateDriverIssue\.zip folder to the instance\.

1. Extract the contents of the folder\.

1. Run the remediation script according to the instructions in the Readme\.txt file\. The file is located in the folder where you extracted RemediateDriverIssue\.zip\.

## TCP Offloading<a name="citrix-tcp-offloading"></a>

**Important**  
This issue does not apply to instances running AWS PV or Intel network drivers\.

By default, TCP offloading is enabled for the Citrix PV drivers in Windows AMIs\. If you encounter transport\-level errors or packet transmission errors \(as visible on the Windows Performance Monitor\)—for example, when you're running certain SQL workloads—you may need to disable this feature\.

**Warning**  
Disabling TCP offloading may reduce the network performance of your instance\.

**To disable TCP offloading for Windows Server 2012 and 2008**

1. Connect to your instance and log in as the local administrator\.

1. If you're using Windows Server 2012, press **Ctrl\+Esc** to access the **Start** screen, and then choose **Control Panel**\. If you're using Windows Server 2008, choose **Start** and select **Control Panel**\.

1. Choose **Network and Internet**, then **Network and Sharing Center**\.

1. Choose **Change adapter settings**\.

1. Right\-click **Citrix PV Ethernet Adapter \#0** and select **Properties**\.  
![\[Local area connection properties\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/citrix-local-area-conn.png)

1. In the **Local Area Connection Properties** dialog box, choose **Configure** to open the **Citrix PV Ethernet Adapter \#0 Properties** dialog box\.

1. On the **Advanced** tab, disable each of the properties, except for **Correct TCP/UDP Checksum Value**\. To disable a property, select it from **Property** and choose **Disabled** from **Value**\.

1. Choose **OK**\.

1. Run the following commands from a Command Prompt window\.

   ```
   netsh int ip set global taskoffload=disabled
   netsh int tcp set global chimney=disabled
   netsh int tcp set global rss=disabled
   netsh int tcp set global netdma=disabled
   ```

1. Reboot the instance\.

## Time Synchronization<a name="citrix-time-sync"></a>

Prior to the release of the 2013\.02\.13 Windows AMI, the Citrix Xen guest agent could set the system time incorrectly\. This can cause your DHCP lease to expire\. If you have issues connecting to your instance, you might need to update the agent\.

To determine whether you have the updated Citrix Xen guest agent, check whether the `C:\Program Files\Citrix\XenGuestAgent.exe` file is from March 2013\. If the date on this file is earlier than that, update the Citrix Xen guest agent service\. For more information, see [Upgrade Your Citrix Xen Guest Agent Service](Upgrading_PV_drivers.md#citrix-pv-guest-agent-upgrade)\.