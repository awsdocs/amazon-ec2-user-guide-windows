# Initializing Amazon EBS volumes<a name="ebs-initialize"></a>

Empty EBS volumes receive their maximum performance the moment that they are created and do not require initialization \(formerly known as pre\-warming\)\.

For volumes that were created from snapshots, the storage blocks must be pulled down from Amazon S3 and written to the volume before you can access them\. This preliminary action takes time and can cause a significant increase in the latency of I/O operations the first time each block is accessed\. Volume performance is achieved after all blocks have been downloaded and written to the volume\.

**Important**  
While initializing Provisioned IOPS SSD volumes that were created from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on Provisioned IOPS SSD volumes while you are initializing them\. For more information, see [EBS volume status checks](monitoring-volume-status.md#monitoring-volume-checks)\.

For most applications, amortizing the initialization cost over the lifetime of the volume is acceptable\. To avoid this initial performance hit in a production environment, you can use one of the following options:
+ Force the immediate initialization of the entire volume\. For more information, see [Initializing Amazon EBS volumes on Windows](#ebs-initialize-windows)\.
+ Enable fast snapshot restore on a snapshot to ensure that the EBS volumes created from it are fully\-initialized at creation and instantly deliver all of their provisioned performance\. For more information, see [Amazon EBS fast snapshot restore](ebs-fast-snapshot-restore.md)\.

## Initializing Amazon EBS volumes on Windows<a name="ebs-initialize-windows"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. For volumes that have been created from snapshots, use dd or fio for Windows to read from all of the blocks on a volume\. All existing data on the volume will be preserved\.

For information about initializing Amazon EBS volumes on Linux, see [Initializing Amazon EBS volumes on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-initialize.html#ebs-initialize-windows)\.

Before using either tool, gather information about the disks on your system as follows:

**To gather information about the system disks**

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

**Important considerations**
+ Initializing a volume takes from several minutes up to several hours, depending on your EC2 instance bandwidth, the IOPS provisioned for the volume, and the size of the volume\.
+ Incorrect use of dd can easily destroy a volume's data\. Be sure to follow this procedure precisely\.

**To install dd for Windows**

The dd for Windows program provides a similar experience to the dd program that is commonly available for Linux and Unix systems, and it enables you to initialize Amazon EBS volumes that have been created from snapshots\. The most recent beta versions support the `/dev/null` virtual device\. If you install an earlier version, you can use the `nul` virtual device instead\. Full documentation is available at [http://www\.chrysocome\.net/dd](http://www.chrysocome.net/dd)\.

1. Download the most recent binary version of dd for Windows from [http://www\.chrysocome\.net/dd](http://www.chrysocome.net/dd)\.

1. \(Optional\) Create a folder for command line utilities that is easy to locate and remember, such as `C:\bin`\. If you already have a designated folder for command line utilities, you can use that folder instead in the following step\.

1. Unzip the binary package and copy the `dd.exe` file to your command line utilities folder \(for example, `C:\bin`\)\.

1. Add the command line utilities folder to your Path environment variable so you can run the programs in that folder from anywhere\.

   1. Choose **Start**, open the context \(right\-click\) menu for **Computer**, and then choose **Properties**\.

   1. Choose **Advanced system settings**, **Environment Variables**\.

   1. For **System Variables**, select the variable **Path** and choose **Edit**\.

   1. For **Variable value**, append a semicolon and the location of your command line utility folder \(**;C:\\bin\\\)** to the end of the existing value\.

   1. Choose **OK** to close the **Edit System Variable ** window\.

1. Open a new command prompt window\. The previous step doesn't update the environment variables in your current command prompt windows\. The command prompt windows that you open now that you completed the previous step are updated\.
<a name="prewarm_snapshot_command"></a>
**To initialize a volume using dd for Windows**  
Run the following command to read all blocks on the specified device \(and send the output to the `/dev/null` virtual device\)\. This command safely initializes your existing data\.

```
dd if=\\.\PHYSICALDRIVEn of=/dev/null bs=1M --progress --size
```

You might get an error if dd attempts to read beyond the end of the volume\. You can safely ignore this error\.

If you used an earlier version of the dd command, it does not support the `/dev/null` device\. Instead, you can use the `nul` device as follows\.

```
dd if=\\.\PHYSICALDRIVEn of=nul bs=1M --progress --size
```

### Using fio<a name="using_fio"></a>

Complete the following procedures to install and use fio to initialize a volume\.

**To install fio for Windows**

The fio for Windows program provides a similar experience to the fio program that is commonly available for Linux and Unix systems, and it allows you to initialize Amazon EBS volumes created from snapshots\. For more information, see [https://github\.com/axboe/fio](https://github.com/axboe/fio)\.

1. Download the [fio MSI](https://ci.appveyor.com/project/axboe/fio) installer \(select the latest x86 or x64 build, then select **Artifacts**\)\. 

1. Install fio\.

**To initialize a volume using fio for Windows**

1. Run a command similar to the following to initialize a volume:

   ```
   fio --filename=\\.\PHYSICALDRIVEn  --rw=read --bs=128k --iodepth=32 --direct=1 --name=volume-initialize
   ```

1. When the operation completes, you are ready to use your new volume\. For more information, see [Making an Amazon EBS volume available for use on Windows](ebs-using-volumes.md)\.