# Amazon EBS and NVMe<a name="nvme-ebs-volumes"></a>

With the following instances, EBS volumes are exposed as NVMe block devices: C5, C5d, `i3.metal`, M5, M5a, M5d, R5, R5a, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. 

**Note**  
The EBS performance guarantees stated in [Amazon EBS Product Details](https://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

Some of these instance types also support NVMe instance store volumes\. For more information, see [NVMe SSD Volumes](ssd-instance-store.md#nvme-ssd-volumes)\.

## Identifying the EBS Device<a name="identify-nvme-ebs-device"></a>

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