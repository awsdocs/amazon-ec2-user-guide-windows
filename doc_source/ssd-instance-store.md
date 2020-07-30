# SSD instance store volumes<a name="ssd-instance-store"></a>

Like other instance store volumes, you must map the SSD instance store volumes for your instance when you launch it\. The data on an SSD instance volume persists only for the life of its associated instance\. For more information, see [Add instance store volumes to your EC2 instance](add-instance-store-volumes.md)\.

## NVMe SSD volumes<a name="nvme-ssd-volumes"></a>

The following instances offer non\-volatile memory express \(NVMe\) solid state drives \(SSD\) instance store volumes: C5d, G4, I3, I3en, F1, M5ad, M5d, `p3dn.24xlarge`, R5ad, R5d, and z1d\. The latest AWS Windows AMIs for the following operating systems contain the AWS NVMe drivers used to interact with SSD instance store volumes that are exposed as NVMe block devices for better performance:
+ Windows Server 2019
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012
+ Windows Server 2008 R2

After you connect to your instance, you can verify that you see the NVMe volumes in Disk Manager\. On the taskbar, open the context \(right\-click\) menu for the Windows logo and choose **Disk Management**\. On Windows Server 2008 R2, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

The AWS Windows AMIs provided by Amazon include the AWS NVMe driver\. If you are not using the latest AWS Windows AMIs, you can [install the current AWS NVMe driver](aws-nvme-drivers.md)\.

The data on NVMe instance storage is encrypted using an XTS\-AES\-256 block cipher implemented in a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

## Non\-NVMe SSD volumes<a name="ssd-volumes"></a>

The following instances support instance store volumes that use non\-NVMe SSDs to deliver high random I/O performance: C3, G2, I2, M3, R3, and X1\. For more information about the instance store volumes support by each instance type, see [Instance store volumes](InstanceStorage.md#instance-store-volumes)\.

## Instance store volume TRIM support<a name="InstanceStoreTrimSupport"></a>

The following instances support SSD volumes with TRIM: C5d, F1, G4, I2, I3, I3en, M5ad, M5d, `p3dn.24xlarge`, R3, R5ad, R5d, and z1d\.

Instances running Windows Server 2012 R2 support TRIM as of AWS PV Driver version 7\.3\.0\. Instances running earlier versions of Windows Server do not support TRIM\.

Instance store volumes that support TRIM are fully trimmed before they are allocated to your instance\. These volumes are not formatted with a file system when an instance launches, so you must format them before they can be mounted and used\. For faster access to these volumes, you should skip the TRIM operation when you format them\. On Windows, to temporarily disable TRIM support during initial formatting, use the `fsutil behavior set DisableDeleteNotify 1` command\. After formatting is complete, re\-enable TRIM support by using `fsutil behavior set DisableDeleteNotify 0`\.

With instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller when you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. On Windows, use the the `fsutil behavior set DisableDeleteNotify 0` command to ensure TRIM support is enabled during normal operation\.