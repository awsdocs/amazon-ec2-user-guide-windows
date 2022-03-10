# Make an Amazon EBS volume available for use on Windows<a name="ebs-using-volumes"></a>

After you attach an Amazon EBS volume to your instance that runs on Xen hypervisor, it is exposed as a block device, and appears as a removable disk in Windows\. You can format the volume with any file system and then mount it\. After you make the EBS volume available for use, you can access it in the same ways that you access any other volume\. Any data written to this file system is written to the EBS volume and is transparent to applications using the device\.

On Nitro instances, the Amazon EBS volume is exposed as a block device when the NVM Express \(NVMe\) controller scans the PCI bus\. The disk does not appear as removable\. Unlike Xen\-based instances, there is only one NVMe controller per EBS volume on Nitro instances\.

You can take snapshots of your EBS volume for backup purposes or to use as a baseline when you create another volume\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

If the EBS volume you are preparing for use is greater than 2 TiB, you must use a GPT partitioning scheme to access the entire volume\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

You can get directions for volumes on a Linux instance from [Make a volume available for use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) in the *Amazon EC2 User Guide for Linux Instances*\.

------
#### [ PowerShell ]

**To make all EBS volumes with raw partitions available to use with Windows PowerShell**

1. Log in to your Windows instance using Remote Desktop\. For more information, see [Connect to your Windows instance](connecting_to_windows_instance.md)\.

1. On the taskbar, open the Start menu, and choose **Windows PowerShell**\.

1. Use the provided series of Windows PowerShell commands within the opened PowerShell prompt\. The script performs the following actions by default:

   1. Stops the ShellHWDetection service\.

   1. Enumerates disks where the partition style is raw\.

   1. Creates a new partition that spans the maximum size the disk and partition type will support\.

   1. Assigns an available drive letter\.

   1. Formats the file system as NTFS with the specified file system label\.

   1. Starts the ShellHWDetection service again\.

   ```
   Stop-Service -Name ShellHWDetection
   Get-Disk | Where PartitionStyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "Volume Label" -Confirm:$false
   Start-Service -Name ShellHWDetection
   ```

------
#### [ DiskPart command line tool ]

**To make an EBS volume available to use with the DiskPart command line tool**

1. Log in to your Windows instance using Remote Desktop\. For more information, see [Connect to your Windows instance](connecting_to_windows_instance.md)\.

1. Determine the disk number that you want to make available:

   1. Open the Start menu, and select Windows PowerShell\.

   1. Use the `Get-Disk` Cmdlet to retrieve a list of available disks\.

   1. In the command output, note the **Number** corresponding to the disk that you're making available\.

1. Create a script file to execute DiskPart commands:

   1. Open the Start menu, and select **File Explorer**\.

   1. Navigate to a directory, such as C:\\, to store the script file\.

   1. Choose or right\-click an empty space within the folder to open the dialog box, position the cursor over **New** to access the context menu, and then choose **Text Document**\.

   1. Name the text file `diskpart.txt`\.

1. Add the following commands to the script file\. You may need to modify the disk number, partition type, volume label, and drive letter\. The script performs the following actions by default:

   1. Selects disk 1 for modification\.

   1. Configures the volume to use the master boot record \(MBR\) partition structure\.

   1. Formats the volume as an NTFS volume\.

   1. Sets the volume label\.

   1. Assigns the volume a drive letter\.
**Warning**  
If you're mounting a volume that already has data on it, do not reformat the volume or you will delete the existing data\.

   ```
   select disk 1 
   attributes disk clear readonly 
   online disk noerr
   convert mbr 
   create partition primary 
   format quick fs=ntfs label="volume_label" 
   assign letter="drive_letter"
   ```

   For more information, see [DiskPart Syntax and Parameters](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-vista/cc766465(v=ws.10)#diskpart-syntax-and-parameters)\.

1. Open a command prompt, navigate to the folder in which the script is located, and run the following command to make a volume available for use on the specified disk:

   ```
   C:\> diskpart /s diskpart.txt
   ```

------
#### [ Disk Management utility ]

**To make an EBS volume available to use with the Disk Management utility**

1. Log in to your Windows instance using Remote Desktop\. For more information, see [Connect to your Windows instance](connecting_to_windows_instance.md)\.

1. Start the Disk Management utility\. On the taskbar, open the context \(right\-click\) menu for the Windows logo, and choose **Disk Management**\.
**Note**  
In Windows Server 2008, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

1. Bring the volume online\. In the lower pane, open the context \(right\-click\) menu for the left panel for the disk for the EBS volume\. Choose **Online**\.  
![\[Bring the volume online.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-volume-online.png)

1. \(Conditional\) You must initialize the disk before you can use it\.
**Warning**  
If you're mounting a volume that already has data on it \(for example, a public data set, or a volume that you created from a snapshot\), do not reformat the volume or you will delete the existing data\.

   If the disk is not initialized, initialize it as follows:

   1. Open the context \(right\-click\) menu for the left panel for the disk, and choose **Initialize Disk**\.  
![\[Initialize the volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-volume-initialize.png)

   1. In the **Initialize Disk** dialog box, select a partition style, and choose **OK**\.  
![\[Initialize volume settings.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-volume-initialize-settings.png)

1. Open the context \(right\-click\) menu for the right panel for the disk, and choose **New Simple Volume**\.  
![\[Mount a simple volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume.png)

1. In the **New Simple Volume Wizard**, choose **Next**\.  
![\[Begin the New Simple Volume Wizard.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume-wizard-welcome.png)

1. If you want to change the default maximum value, specify the **Simple volume size in MB**, and then choose **Next\.**  
![\[Specify the volume size.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume-wizard-size.png)

1. Specify a preferred drive letter, if necessary, within the **Assign the following drive letter** dropdown, and then choose **Next\.**  
![\[Specify a drive letter.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume-wizard-letter.png)

1. Specify a **Volume Label** and adjust the default settings as necessary, and then choose **Next\.**  
![\[Specify settings to format the volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume-wizard-format.png)

1. Review your settings, and then choose **Finish** to apply the modifications and close the New Simple Volume wizard\.  
![\[Review your settings and finish the wizard.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-2016-new-simple-volume-wizard-finish.png)

------