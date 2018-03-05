# SSD Instance Store Volumes<a name="ssd-instance-store"></a>

The following instances support instance store volumes that use solid state drives \(SSD\) to deliver high random I/O performance: C3, F1, G2, I2, I3, M3, R3, and X1\. For more information about the instance store volumes support by each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

Like other instance store volumes, you must map the SSD instance store volumes for your instance when you launch it\. The data on an SSD instance volume persists only for the life of its associated instance\. For more information, see [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)\.

## NVMe SSD Volumes<a name="nvme-ssd-volumes"></a>

I3 and F1 instances offer non\-volatile memory express \(NVMe\) SSD instance store volumes\. To access the NVMe volumes, you must use an operating system that supports NVMe\. The following are the recommended operating systems:

+ The current Amazon Linux AMI

+ Ubuntu version 16\.04 or latest LTS release provided by AWS\. 

  We should be recommending: latest LTS \(16\.04\) or latest regular release\.\.\.which will change every 6 months and those releases are only supported for 9 months as opposed to 5 years for LTS releases\. 

  If you are using a different version, we recommend that you turn off memory hot add\.

+ Red Hat Enterprise Linux version 7\.4

+ CentOS 7 version 1708\_11

+ Windows Server 2016, Windows Server 2012 R2, or Windows Server 2008 R2\. Windows Server 2012 and Windows Server 2008 are not supported\.

SUSE Linux Enterprise Server 12 is not recommended at this time due to pending kernel updates\.

After you connect to your instance, you can verify that you see the NVMe volumes in Disk Manager\. On Windows Server 2016 or Windows Server 2012 R2, on the taskbar, open the context \(right\-click\) menu for the Windows logo and choose **Disk Management**\. On Windows Server 2008 R2, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

If you are using a supported version of Windows Server but you do not see the NVMe devices, verify that the NVMe storage controllers are operational using Device Manager\. Expand **Storage controllers** and look for **Standard NVM Express Controller**\. If you are using a custom Windows Server 2008 R2 AMI and can't see the instance storage volume, consider installing the Microsoft hotfix for [Native driver support in NVM Express in Windows 7 and Windows Server 2008 R2](https://support.microsoft.com/en-us/help/2990941/update-to-add-native-driver-support-in-nvm-express-in-windows-7-and-wi)\.

## Instance Store Volume TRIM Support<a name="InstanceStoreTrimSupport"></a>

The following instances support SSD volumes with TRIM: F1, I2, I3, and R3\.

**Important**  
Instances running Windows Server 2012 R2 support TRIM as of AWS PV Driver version 7\.3\.0\. Instances running earlier versions of Windows Server do not support TRIM\.

Instance store volumes that support TRIM are fully trimmed before they are allocated to your instance\. These volumes are not formatted with a file system when an instance launches, so you must format them before they can be mounted and used\. For faster access to these volumes, you should skip the TRIM operation when you format them\.

With instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller when you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. On Linux, you can use the `fstrim` command to enable periodic TRIM\. On Windows, you can use the `fsutil behavior set DisableDeleteNotify 1` command\. For more information, see the documentation for your operating system\.