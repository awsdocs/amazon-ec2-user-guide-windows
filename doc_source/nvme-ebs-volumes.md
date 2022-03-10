# Amazon EBS and NVMe on Windows instances<a name="nvme-ebs-volumes"></a>

EBS volumes are exposed as NVMe block devices on instances built on the [Nitro System](instance-types.md#ec2-nitro-instances)\. When you attach a volume to your instance, you include a device name for the volume\. This device name is used by Amazon EC2\. The block device driver for the instance assigns the actual volume name when mounting the volume, and the name assigned can be different from the name that Amazon EC2 uses\.

The EBS performance guarantees stated in [Amazon EBS Product Details](http://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

**Topics**
+ [Install or upgrade the NVMe driver](#install-nvme-driver)
+ [Identify the EBS device](#identify-nvme-ebs-device)
+ [Work with NVMe EBS volumes](#using-nvme-ebs-volumes)
+ [I/O operation timeout](#timeout-nvme-ebs-volumes)
+ [`Abort` command](#abort-command)

## Install or upgrade the NVMe driver<a name="install-nvme-driver"></a>

The AWS Windows AMIs for Windows Server 2008 R2 and later include the AWS NVMe driver\. If you are not using the latest AWS Windows AMIs provided by Amazon, see [Install or upgrade AWS NVMe drivers using PowerShell](aws-nvme-drivers.md#install-nvme-drivers)\.

## Identify the EBS device<a name="identify-nvme-ebs-device"></a>

EBS uses single\-root I/O virtualization \(SR\-IOV\) to provide volume attachments on Nitro\-based instances using the NVMe specification\. These devices rely on standard NVMe drivers on the operating system\. These drivers typically discover attached devices by scanning the PCI bus during instance boot, and create device nodes based on the order in which the devices respond, not on how the devices are specified in the block device mapping\. Additionally, the device name assigned by the block device driver can be different from the name specified in the block device mapping\.

### Windows Server 2008 R2 and later<a name="nvme-windows"></a>

You can also run the **ebsnvme\-id** command to map the NVMe device disk number to an EBS volume ID and device name\. By default, all EBS NVMe devices are enumerated\. You can pass a disk number to enumerate information for a specific device\. Ebsnvme\-id is included in the latest AWS provided Windows Server AMIs located in C:\\PROGRAMDATA\\AMAZON\\Tools\.

You can also download [ebsnvme\-id\.zip](https://s3.amazonaws.com/ec2-windows-drivers-downloads/EBSNVMeID/Latest/ebsnvme-id.zip) and extract the contents to your Amazon EC2 instance to get access to `ebsnvme-id.exe`\.

```
PS C:\Users\Administrator\Desktop> ebsnvme-id.exe
Disk Number: 0
Volume ID: vol-0d6d7ee9f6e471a7f
Device Name: sda1

Disk Number: 1
Volume ID: vol-03a26248ff39b57cf
Device Name: xvdd

Disk Number: 2
Volume ID: vol-038bd1c629aa125e6
Device Name: xvde

Disk Number: 3
Volume ID: vol-034f9d29ec0b64c89
Device Name: xvdb

Disk Number: 4
Volume ID: vol-03e2dbe464b66f0a1
Device Name: xvdc
PS C:\Users\Administrator\Desktop> ebsnvme-id.exe 4
Disk Number: 4
Volume ID: vol-03e2dbe464b66f0a1
Device Name: xvdc
```

## Work with NVMe EBS volumes<a name="using-nvme-ebs-volumes"></a>

The latest AWS Windows AMIs contain the AWS NVMe driver that is required by instance types that expose EBS volumes as NVMe block devices\. However, if you resize your root volume on a Windows system, you must rescan the volume in order for this change to be reflected in the instance\. If you launched your instance from a different AMI, it might not contain the required AWS NVMe driver\. If your instance does not have the latest AWS NVMe driver, you must install it\. For more information, see [AWS NVMe drivers for Windows instances](aws-nvme-drivers.md)\.

## I/O operation timeout<a name="timeout-nvme-ebs-volumes"></a>

Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. On Windows systems, the default timeout is 60 seconds and the maximum is 255 seconds\. You can modify the `TimeoutValue` disk class registry setting using the procedure described in [Registry Entries for SCSI Miniport Drivers](https://docs.microsoft.com/en-us/windows-hardware/drivers/storage/registry-entries-for-scsi-miniport-drivers)\.

## `Abort` command<a name="abort-command"></a>

The `Abort` command is an NVMe Admin command that is issued to abort a specific command that was previously submitted to the controller\. This command is typically issued by the device driver to storage devices that have exceeded the I/O operation timeout threshold\. Amazon EC2 instance types that support the `Abort` command by default will abort a specific command that was previously submitted to the controller of the attached Amazon EBS device to which an `Abort` command is issued\.

The following instance types support the `Abort` command for all attached Amazon EBS volumes by default: `R5b`, `R6i`, `M6i`, `M6a`, `C6gn`, `C6i`, `X2gd`, `X2iezn`, `Im4gn`, `Is4gen`\.

Other instance types take no action when `Abort` commands are issued to attached Amazon EBS volumes\.

Amazon EBS devices with NVMe device version `1.4` or higher support the `Abort` command\.

For more information, see section **5\.1 Abort command** of the [ NVM Express Base Specification](https://nvmexpress.org/wp-content/uploads/NVM-Express-1_4-2019.06.10-Ratified.pdf)\.