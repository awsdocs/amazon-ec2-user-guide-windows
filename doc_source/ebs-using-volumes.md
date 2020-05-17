# Making an Amazon EBS volume available for use on Windows<a name="ebs-using-volumes"></a>

After you attach an Amazon EBS volume to your instance, it is exposed as a block device, and appears as a removable disk in Windows\. You can format the volume with any file system and then mount it\. After you make the EBS volume available for use, you can access it in the same ways that you access any other volume\. Any data written to this file system is written to the EBS volume and is transparent to applications using the device\.

You can take snapshots of your EBS volume for backup purposes or to use as a baseline when you create another volume\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

You can get directions for volumes on a Linux instance from [Making a Volume Available for Use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can make an EBS volume available for use using the Disk Management utility and the DiskPart command line tool\.

**To make an EBS volume available for use using the Disk Management utility**

1. Log in to your Windows instance using Remote Desktop\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Start the Disk Management utility\. On the taskbar, open the context \(right\-click\) menu for the Windows logo and choose **Disk Management**\.
**Note**  
On Windows Server 2008, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

1. Bring the volume online\. In the lower pane, open the context \(right\-click\) menu for the left panel for the disk for the EBS volume\. Choose **Online**\.  
![\[Bring the volume online.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-volume-online.png)

1. \(Conditional\) You must initialize the disk before you can use it\.
**Warning**  
If you're mounting a volume that already has data on it \(for example, a public data set, or a volume that you created from a snapshot\), do not reformat the volume or you will delete the existing data\.

   If the disk is not initialized, initialize it as follows\. Open the context \(right\-click\) menu for the left panel for the disk and choose **Initialize Disk**\. In the **Initialize Disk** dialog box, select a partition style and choose **OK**\.  
![\[Initialize the volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-volume-initialize.png)

1. Open the context \(right\-click\) menu for the right panel for the disk and choose **New Simple Volume**\. Complete the wizard\.  
![\[Mount a simple volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume.png)

**To make an EBS volume available for use using the DiskPart command line tool**

1. Log in to your Windows instance using Remote Desktop\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Create a new script file named `diskpart.txt`\.

1. Add the following commands to the script file and specify the volume label and drive letter\. This script configures the volume to use the master boot record \(MBR\) partition structure, formats the volume as an NTFS volume, sets the volume label, and assigns it a drive letter\.
**Warning**  
If you're mounting a volume that already has data on it, do not reformat the volume or you will delete the existing data\.

   ```
   select disk 1 
   attributes disk clear readonly 
   online disk 
   convert mbr 
   create partition primary 
   format quick fs=ntfs label="volume_label" 
   assign letter="drive_letter"
   ```

   For more information, see [DiskPart Syntax and Parameters](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-vista/cc766465(v=ws.10)#diskpart-syntax-and-parameters)\.

1. Navigate to the folder in which the script is located and execute the following command:

   ```
   C:\> diskpart /s diskpart.txt
   ```