# Initializing Amazon EBS Volumes<a name="ebs-initialize"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\.

However, storage blocks on volumes that were restored from snapshots must be pulled down from Amazon S3 and written to the volume before they can be accessed\. This preliminary action takes time and can significantly increase the latency of I/O operations\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. The volume's performance is restored after all of the blocks have been downloaded and written to the volume\. To avoid this initial performance hit in a production environment, you can perform an initialization to read all of the blocks on the volume before you use it\.

**Important**  
While initializing `io1` volumes that were restored from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on `io1` volumes while you are initializing them\. For more information, see [EBS Volume Status Checks](monitoring-volume-status.md#monitoring-volume-checks)\.

## Initializing Amazon EBS Volumes on Windows<a name="ebs-initialize-windows"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. For volumes that have been restored from snapshots, use dd or fio for Windows to read from all of the blocks on a volume\. All existing data on the volume will be preserved\.

Before using either tool, gather information about the disks on your system as follows:

1. Use the wmic command to list the available disks on your system:

   ```
   wmic diskdrive get size,deviceid
   ```

   The following is example output:

   ```
   DeviceID            Size
   \\.\PHYSICALDRIVE2  80517265920
   \\.\PHYSICALDRIVE1  80517265920
   \\.\PHYSICALDRIVE0  128849011200
   \\.\PHYSICALDRIVE3  107372805120
   ```

1. Identify the disk to initialize using dd or fio\. The `C:` drive is on `\\.\PHYSICALDRIVE0`\. You can use the `diskmgmt.msc` utility to compare drive letters to disk drive numbers if you are not sure which drive number to use\. 

### Using dd<a name="using_dd"></a>

Complete the following procedures to install and use dd to initialize a volume\.

**Note**  
This step may take several minutes up to several hours, depending on your EC2 instance bandwidth, the IOPS provisioned for the volume, and the size of the volume\.

**Install dd for Windows**

The dd for Windows program provides a similar experience to the dd program that is commonly available for Linux and Unix systems, and it allows you to initialize Amazon EBS volumes that have been restored from snapshots\. At the time of this writing, the most recent beta version contains the `/dev/null` virtual device that is required to initialize volumes restored from snapshots\. Full documentation for the program is available at [http://www\.chrysocome\.net/dd](http://www.chrysocome.net/dd)\.

1. Download the most recent binary version of dd for Windows from [http://www\.chrysocome\.net/dd](http://www.chrysocome.net/dd)\. You must use version 0\.6 beta 3 or newer to initialize restored volumes\.

1. \(Optional\) Create a folder for command line utilities that is easy to locate and remember, such as `C:\bin`\. If you already have a designated folder for command line utilities, you can use that folder instead in the following step\.

1. Unzip the binary package and copy the `dd.exe` file to your command line utilities folder \(for example, `C:\bin`\)\.

1. Add the command line utilities folder to your Path environment variable so you can execute the programs in that folder from anywhere\.
**Important**  
The following steps don't update the environment variables in your current command prompt windows\. The command prompt windows that you open after you complete these steps will contain the updates\. This is why it's necessary for you to open a new command prompt window to verify that your environment is set up properly\.

   1. Choose **Start**, open the context \(right\-click\) menu for **Computer**, and then choose **Properties**\.

   1. Choose **Advanced system settings**, **Environment Variables**\.

   1. For **System Variables**, select the variable **Path** and choose **Edit**\.

   1. For **Variable value**, append a semicolon and the location of your command line utility folder \(**;C:\\bin\\\)** to the end of the existing value\.

   1. Choose **OK** to close the **Edit System Variable ** window\.

**Initialize a volume using dd for Windows**

1. <a name="prewarm_snapshot_command"></a>Execute the following command to read all blocks on the specified device \(and send the output to the `/dev/null` virtual device\)\. This command safely initializes your existing data\.
**Important**  
Incorrect use of dd can easily destroy a volume's data\. Be sure to follow precisely the example command below\. Only the `if=\\.\PHYSICALDRIVEn` parameter will vary depending on the name of the device you are reading\.

   ```
   dd if=\\.\PHYSICALDRIVEn of=/dev/null bs=1M --progress --size
   ```
**Note**  
You may see an error if dd attempts to read beyond the end of the volume\. This can be safely ignored\.

1. When the operation completes, you are ready to use your new volume\. For more information, see [Making an Amazon EBS Volume Available for Use on Windows](ebs-using-volumes.md)\.

### Using fio<a name="using_fio"></a>

Complete the following procedures to install and use fio to initialize a volume\.

**Install fio for Windows**

The fio for Windows program provides a similar experience to the fio program that is commonly available for Linux and Unix systems, and it allows you to initialize Amazon EBS volumes that have been restored from snapshots\. Full documentation for the program is available at [https://github\.com/axboe/fio](https://github.com/axboe/fio)\.

1. Download the [fio MSI](https://ci.appveyor.com/project/axboe/fio) installer \(select the latest x86 or x64 build, then select **Artifacts**\)\. 

1. Install fio\.

**Initialize a volume using fio for Windows**

1. Run a command similar to the following to initialize a volume:

   ```
   fio --filename=\\.\PHYSICALDRIVEn  --rw=read --bs=128k --iodepth=32 --direct=1 --name=volume-initialize
   ```

1. When the operation completes, you are ready to use your new volume\. For more information, see [Making an Amazon EBS Volume Available for Use on Windows](ebs-using-volumes.md)\.