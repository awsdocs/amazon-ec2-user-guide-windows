# Extending a Windows file system after resizing a volume<a name="recognize-expanded-volume-windows"></a>

After you increase the size of an EBS volume, use the Windows Disk Management utility to extend the disk size to the new size of the volume\. You can begin resizing the file system as soon as the volume enters the `optimizing` state\. For more information about this utility, see [Extend a basic volume](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/extend-a-basic-volume) on the Microsoft Docs website\.

For more information about extending a file system on Linux, see [Extending a Linux File System After Resizing a Volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To extend a Windows file system**

1. Before extending a file system that contains valuable data, it is a best practice to create a snapshot of the volume that contains it in case you need to roll back your changes\. For more information, see [Creating Amazon EBS snapshots](ebs-creating-snapshot.md)\.

1. Log in to your Windows instance using Remote Desktop\.

1. In the **Run** dialog, type **diskmgmt\.msc** and press Enter\. The Disk Management utility opens\.  
![\[Windows Server Disk Management Utility\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Expand-Volume-Win2008-before.png)

1. On the **Disk Management** menu, choose **Action**, **Rescan Disks**\.

1. Open the context \(right\-click\) menu for the expanded drive and choose **Extend Volume**\.
**Note**  
The unallocated space must be adjacent to the right side of the drive you want to extend\. If **Extend Volume** is grayed out, the unallocated space might not be adjacent to the drive\.  
![\[Windows Server Disk Management Utility\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Expand-Volume-Win2008-before-menu.png)

1. In the **Extend Volume** wizard, choose **Next**\. For **Select the amount of space in MB**, enter the number of megabytes by which to extend the volume\. Generally, you specify the maximum available space\. The highlighted text under **Selected** is the amount of space that is added, not the final size the volume will have\. Complete the wizard\.  
![\[Windows Server Extend Volume Wizard\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Extend-Volume-Wizard-Win2008.png)

1. If you increase the size of an NVMe volume on an instance that does not have the AWS NVMe driver, you must reboot the instance to enable Windows to see the new volume size\. For more information about installing the AWS NVMe driver, see [AWS NVMe drivers for Windows instances](aws-nvme-drivers.md)\.