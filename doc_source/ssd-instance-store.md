# SSD Instance Store Volumes<a name="ssd-instance-store"></a>

The following instances support instance store volumes that use solid state drives \(SSD\) to deliver high random I/O performance: C3, C5d, F1, G2, I2, I3, M3, M5d, R3, X1, and z1d\. For more information about the instance store volumes support by each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

Like other instance store volumes, you must map the SSD instance store volumes for your instance when you launch it\. The data on an SSD instance volume persists only for the life of its associated instance\. For more information, see [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)\.

## NVMe SSD Volumes<a name="nvme-ssd-volumes"></a>

The following instances offer non\-volatile memory express \(NVMe\) SSD instance store volumes: C5d, I3, F1, M5d, and z1d\. To access the NVMe volumes, you must use an operating system that supports NVMe\. The following are the recommended operating systems:
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012
+ Windows Server 2008 R2

After you connect to your instance, you can verify that you see the NVMe volumes in Disk Manager\. On the taskbar, open the context \(right\-click\) menu for the Windows logo and choose **Disk Management**\. On Windows Server 2008 R2, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

If you are using a supported version of Windows Server but you do not see the NVMe devices, verify that the NVMe storage controllers are operational using Device Manager\. Expand **Storage controllers** and look for **Standard NVM Express Controller**\. If you are using a custom Windows Server 2008 R2 AMI and can't see the instance storage volume, consider installing the Microsoft hotfix for [Native driver support in NVM Express in Windows 7 and Windows Server 2008 R2](https://support.microsoft.com/en-us/help/2990941/update-to-add-native-driver-support-in-nvm-express-in-windows-7-and-wi)\.

The data on NVMe instance storage is encrypted using an XTS\-AES\-256 block cipher implemented in a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

## Instance Store Volume TRIM Support<a name="InstanceStoreTrimSupport"></a>

The following instances support SSD volumes with TRIM: C5d, F1, I2, I3, M5d, R3, R5d, and z1d\.

Instances running Windows Server 2012 R2 support TRIM as of AWS PV Driver version 7\.3\.0\. Instances running earlier versions of Windows Server do not support TRIM\.

Instance store volumes that support TRIM are fully trimmed before they are allocated to your instance\. These volumes are not formatted with a file system when an instance launches, so you must format them before they can be mounted and used\. For faster access to these volumes, you should skip the TRIM operation when you format them\.

With instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller when you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. On Windows, use the `fsutil behavior set DisableDeleteNotify 1` command\.\.