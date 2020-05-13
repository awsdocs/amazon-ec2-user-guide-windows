# RAID Configuration on Windows<a name="raid-config"></a>

With Amazon EBS, you can use any of the standard RAID configurations that you can use with a traditional bare metal server, as long as that particular RAID configuration is supported by the operating system for your instance\. This is because all RAID is accomplished at the software level\. For greater I/O performance than you can achieve with a single volume, RAID 0 can stripe multiple volumes together; for on\-instance redundancy, RAID 1 can mirror two volumes together\.

Amazon EBS volume data is replicated across multiple servers in an Availability Zone to prevent the loss of data from the failure of any single component\. This replication makes Amazon EBS volumes ten times more reliable than typical commodity disk drives\. For more information, see [Amazon EBS Availability and Durability](https://aws.amazon.com/ebs/details/#Amazon_EBS_Availability_and_Durability) in the Amazon EBS product detail pages\.

**Note**  
You should avoid booting from a RAID volume\. If one of the devices fails, you may be unable to boot the operating system\.

If you need to create a RAID array on a Linux instance, see [RAID Configuration on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [RAID Configuration Options](#raid-config-options)
+ [Creating a RAID Array on Windows](#windows-raid)
+ [Creating Snapshots of Volumes in a RAID Array](#ebs-snapshots-raid-array)

## RAID Configuration Options<a name="raid-config-options"></a>

The following table compares the common RAID 0 and RAID 1 options\.


| Configuration | Use | Advantages | Disadvantages | 
| --- | --- | --- | --- | 
|  RAID 0  |  When I/O performance is more important than fault tolerance; for example, as in a heavily used database \(where data replication is already set up separately\)\.  |  I/O is distributed across the volumes in a stripe\. If you add a volume, you get the straight addition of throughput and IOPS\.  |  Performance of the stripe is limited to the worst performing volume in the set\. Loss of a single volume results in a complete data loss for the array\.  | 
|  RAID 1  |  When fault tolerance is more important than I/O performance; for example, as in a critical application\.  |  Safer from the standpoint of data durability\.  |  Does not provide a write performance improvement; requires more Amazon EC2 to Amazon EBS bandwidth than non\-RAID configurations because the data is written to multiple volumes simultaneously\.   | 

**Important**  
RAID 5 and RAID 6 are not recommended for Amazon EBS because the parity write operations of these RAID modes consume some of the IOPS available to your volumes\. Depending on the configuration of your RAID array, these RAID modes provide 20\-30% fewer usable IOPS than a RAID 0 configuration\. Increased cost is a factor with these RAID modes as well; when using identical volume sizes and speeds, a 2\-volume RAID 0 array can outperform a 4\-volume RAID 6 array that costs twice as much\. 

Creating a RAID 0 array allows you to achieve a higher level of performance for a file system than you can provision on a single Amazon EBS volume\. A RAID 1 array offers a "mirror" of your data for extra redundancy\. Before you perform this procedure, you need to decide how large your RAID array should be and how many IOPS you want to provision\.

The resulting size of a RAID 0 array is the sum of the sizes of the volumes within it, and the bandwidth is the sum of the available bandwidth of the volumes within it\. The resulting size and bandwidth of a RAID 1 array is equal to the size and bandwidth of the volumes in the array\. For example, two 500 GiB Amazon EBS `io1` volumes with 4,000 provisioned IOPS each will create a 1000 GiB RAID 0 array with an available bandwidth of 8,000 IOPS and 1,000 MiB/s of throughput or a 500 GiB RAID 1 array with an available bandwidth of 4,000 IOPS and 500 MiB/s of throughput\.

This documentation provides basic RAID setup examples\. For more information about RAID configuration, performance, and recovery, see the Linux RAID Wiki at [https://raid\.wiki\.kernel\.org/index\.php/Linux\_Raid](https://raid.wiki.kernel.org/index.php/Linux_Raid)\.

## Creating a RAID Array on Windows<a name="windows-raid"></a>

Use the following procedure to create the RAID array\. Note that you can get directions for Linux instances from [Creating a RAID Array on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html#linux-raid) in the *Amazon EC2 User Guide for Linux Instances*\.

**To create a RAID array on Windows**

1. Create the Amazon EBS volumes for your array\. For more information, see [Creating an Amazon EBS volume](ebs-creating-volume.md)\.
**Important**  
Create volumes with identical size and IOPS performance values for your array\. Make sure you do not create an array that exceeds the available bandwidth of your EC2 instance\.

1. Attach the Amazon EBS volumes to the instance that you want to host the array\. For more information, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. Connect to your Windows instance\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Open a command prompt and type the diskpart command\.

   ```
   diskpart
   
   Microsoft DiskPart version 6.1.7601
   Copyright (C) 1999-2008 Microsoft Corporation.
   On computer: WIN-BM6QPPL51CO
   ```

1. At the DISKPART prompt, list the available disks with the following command\.

   ```
   DISKPART> list disk
   
     Disk ###  Status         Size     Free     Dyn  Gpt
     --------  -------------  -------  -------  ---  ---
     Disk 0    Online           30 GB      0 B
     Disk 1    Online            8 GB      0 B
     Disk 2    Online            8 GB      0 B
     Disk 3    Online            8 GB      0 B
     Disk 4    Online            8 GB      0 B
     Disk 5    Online          419 GB      0 B
     Disk 6    Online          419 GB      0 B
   ```

   Identify the disks you want to use in your array and take note of their disk numbers\.

1. <a name="windows_raid_disk_step"></a>Each disk you want to use in your array must be an online dynamic disk that does not contain any existing volumes\. Use the following steps to convert basic disks to dynamic disks and to delete any existing volumes\.

   1. Select a disk you want to use in your array with the following command, substituting *n* with your disk number\.

      ```
      DISKPART> select disk n
      
      Disk n is now the selected disk.
      ```

   1. If the selected disk is listed as `Offline`, bring it online by running the online disk command\.

   1. If the selected disk does not have an asterisk in the `Dyn` column in the previous list disk command output, you need to convert it to a dynamic disk\.

      ```
      DISKPART> convert dynamic
      ```
**Note**  
If you receive an error that the disk is write protected, you can clear the read\-only flag with the ATTRIBUTE DISK CLEAR READONLY command and then try the dynamic disk conversion again\.

   1. Use the detail disk command to check for existing volumes on the selected disk\.

      ```
      DISKPART> detail disk
      
      XENSRC PVDISK SCSI Disk Device
      Disk ID: 2D8BF659
      Type   : SCSI
      Status : Online
      Path   : 0
      Target : 1
      LUN ID : 0
      Location Path : PCIROOT(0)#PCI(0300)#SCSI(P00T01L00)
      Current Read-only State : No
      Read-only  : No
      Boot Disk  : No
      Pagefile Disk  : No
      Hibernation File Disk  : No
      Crashdump Disk  : No
      Clustered Disk  : No
      
        Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
        ----------  ---  -----------  -----  ----------  -------  ---------  --------
        Volume 2     D   NEW VOLUME   FAT32  Simple      8189 MB  Healthy
      ```

      Note any volume numbers on the disk\. In this example, the volume number is 2\. If there are no volumes, you can skip the next step\.

   1. \(Only required if volumes were identified in the previous step\) Select and delete any existing volumes on the disk that you identified in the previous step\.
**Warning**  
This destroys any existing data on the volume\. 

      1. Select the volume, substituting *n* with your volume number\.

         ```
         DISKPART> select volume n
         Volume n is the selected volume.
         ```

      1. Delete the volume\.

         ```
         DISKPART> delete volume
         
         DiskPart successfully deleted the volume.
         ```

      1. Repeat these substeps for each volume you need to delete on the selected disk\.

   1. Repeat [Step 6](#windows_raid_disk_step) for each disk you want to use in your array\.

1. Verify that the disks you want to use are now dynamic\.

   ```
   DISKPART> list disk
   
     Disk ###  Status         Size     Free     Dyn  Gpt
     --------  -------------  -------  -------  ---  ---
     Disk 0    Online           30 GB      0 B
     Disk 1    Online            8 GB      0 B   *
     Disk 2    Online            8 GB      0 B   *
     Disk 3    Online            8 GB      0 B   *
   * Disk 4    Online            8 GB      0 B   *
     Disk 5    Online          419 GB      0 B
     Disk 6    Online          419 GB      0 B
   ```

1. Create your raid array\. On Windows, a RAID 0 volume is referred to as a striped volume and a RAID 1 volume is referred to as a mirrored volume\.

   \(Striped volumes only\) To create a striped volume array on disks 1 and 2, use the following command \(note the `stripe` option to stripe the array\):

   ```
   DISKPART> create volume stripe disk=1,2
   
   DiskPart successfully created the volume.
   ```

   \(Mirrored volumes only\) To create a mirrored volume array on disks 3 and 4, use the following command \(note the `mirror` option to mirror the array\):

   ```
   DISKPART> create volume mirror disk=3,4
   
   DiskPart successfully created the volume.
   ```

1. Verify your new volume\.

   ```
   DISKPART> list volume
   
     Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
     ----------  ---  -----------  -----  ----------  -------  ---------  --------
     Volume 0     C                NTFS   Partition     29 GB  Healthy    System
   * Volume 1                      RAW    Mirror      8190 MB  Healthy
     Volume 2                      RAW    Stripe        15 GB  Healthy
     Volume 5     Z   Temporary S  NTFS   Partition    419 GB  Healthy
     Volume 6     Y   Temporary S  NTFS   Partition    419 GB  Healthy
   ```

   Note that for this example the `Type` column lists a `Mirror` volume and a `Stripe` volume\.

1. Select and format your volume so that you can begin using it\.

   1. Select the volume you want to format, substituting *n* with your volume number\.

      ```
      DISKPART> select volume n
      
      Volume n is the selected volume.
      ```

   1. Format the volume\.
**Note**  
To perform a full format, omit the `quick` option\.

      ```
      DISKPART> format quick recommended label="My new volume"
      
        100 percent completed
      
      DiskPart successfully formatted the volume.
      ```

   1. Assign an available drive letter to your volume\.

      ```
      DISKPART> assign letter f
      
      DiskPart successfully assigned the drive letter or mount point.
      ```

   Your new volume is now ready to use\.

## Creating Snapshots of Volumes in a RAID Array<a name="ebs-snapshots-raid-array"></a>

If you want to back up the data on the EBS volumes in a RAID array using snapshots, you must ensure that the snapshots are consistent\. This is because the snapshots of these volumes are created independently\. To restore EBS volumes in a RAID array from snapshots that are out of sync would degrade the integrity of the array\.

To create a consistent set of snapshots for your RAID array, use [EBS multi\-volume snapshots](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshots.html)\. Multi\-volume snapshots allow you to take point\-in\-time, data coordinated, and crash\-consistent snapshots across multiple EBS volumes attached to an EC2 instance\. You do not have to stop your instance to coordinate between volumes to ensure consistency because snapshots are automatically taken across multiple EBS volumes\. For more information, see the steps for creating multi\-volume snapshots under [Creating Amazon EBS Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html)\. 