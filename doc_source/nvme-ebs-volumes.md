# Amazon EBS and NVMe<a name="nvme-ebs-volumes"></a>

EBS volumes are exposed as NVMe block devices on [Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. 

**Note**  
The EBS performance guarantees stated in [Amazon EBS Product Details](https://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

Some of these instance types also support NVMe instance store volumes\. For more information, see [NVMe SSD Volumes](ssd-instance-store.md#nvme-ssd-volumes)\.

## Identifying the EBS Device<a name="identify-nvme-ebs-device"></a>

EBS uses single\-root I/O virtualization \(SR\-IOV\) to provide volume attachments on Nitro\-based instances using the NVMe specification\. These devices rely on standard NVMe drivers on the operating system\. These drivers typically discover attached devices by scanning the PCI bus during instance boot, and create device nodes based on the order in which the devices respond, not on how the devices are specified in the block device mapping\. In Linux, NVMe device names follow the pattern `/dev/nvme<x>n<y>`, where <x> is the enumeration order, and, for EBS, <y> is 1\. Occasionally, devices can respond to discovery in a different order in subsequent instance starts, which causes the device name to change\.

We recommend that you use stable identifiers for your EBS volumes within your instance, such as one of the following:
+ For Nitro\-based instances, the block device mappings that are specified in the Amazon EC2 console when you are attaching an EBS volume or during `AttachVolume` or `RunInstances` API calls are captured in the vendor\-specific data field of the NVMe controller identification\. With Amazon Linux AMIs later than version 2017\.09\.01, we provide a `udev` rule that reads this data and creates a symbolic link to the block\-device mapping\.
+ NVMe\-attached EBS volumes have the EBS volume ID set as the serial number in the device identification\.
+ When a device is formatted, a UUID is generated that persists for the life of the filesystem\. A device label can be specified at the same time\. For more information, see [Making an Amazon EBS Volume Available for Use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) and [Booting from the Wrong Volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-booting-from-wrong-volume.html)\.

You can use the [Get\-Disk](https://docs.microsoft.com/en-us/powershell/module/storage/get-disk) command to map Windows disk numbers to EBS volume IDs\.

**Windows Server 2016**  
To get the volume IDs, select `AdapterSerialNumber`\. In this example, the ID of volume 0 is "vol\-0651a78c608e09c6a"\.

```
PS C:\> Get-Disk | Select Number,AdapterSerialNumber | Sort-Object Number

Number AdapterSerialNumber
------ -------------------
     0 vol0651a78c608e09c6a
     1 vol03f93c68194556d14
     2 vol0dbd294c35c6174de
```

**Windows Server 2012 R2**  
To get the volume IDs, select `SerialNumber`\. In this example, the ID of volume 0 is "vol\-01257d42be427a58b"\.

```
PS C:\> Get-Disk | Select Number,SerialNumber | Sort-Object Number

Number SerialNumber
------ -------------------
     0 vol01257d42be427a58b_00000001.
     1 vol0da96b723afa69568_00000001.
     2 vol0d577fdabd6001831_00000001.
```

## Working with NVMe EBS Volumes<a name="using-nvme-ebs-volumes"></a>

The latest AWS Windows AMIs contain the AWS NVMe driver that is required by instance types that expose EBS volumes as NVMe block devices\. However, if you resize your root volume on a Windows system, you must rescan the volume in order for this change to be reflected in the instance\. If you launched your instance from a different AMI, it might not contain the required AWS NVMe driver\. If your instance does not have the latest AWS NVMe driver, you must install it\. For more information, see [AWS NVMe Drivers for Windows Instances](aws-nvme-drivers.md)\.

## I/O Operation Timeout<a name="timeout-nvme-ebs-volumes"></a>

Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. On Windows systems, the default timeout is 60 seconds and the maximum is 255 seconds\. You can modify the `TimeoutValue` disk class registry setting using the procedure described in [Registry Entries for SCSI Miniport Drivers](https://docs.microsoft.com/en-us/windows-hardware/drivers/storage/registry-entries-for-scsi-miniport-drivers)\.
+ Amazon Linux AMI 2017\.09\.01 or later
+ Canonical 4\.4\.0\-1041 or later
+ SLES 12 SP2 \(4\.4 kernel\) or later
+ RHEL 7\.5 \(3\.10\.0\-862 kernel\) or later

You can verify the maximum value for your Linux distribution by writing a value higher than the suggested maximum to `/sys/module/nvme_core/parameters/io_timeout` and checking for the `Numerical result out of range` error when attempting to save the file\.