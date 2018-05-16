# Device Naming on Windows Instances<a name="device_naming"></a>

When you attach a volume to your instance, you include a device name for the volume\. This device name is used by Amazon EC2\. The block device driver for the instance assigns the actual volume name when mounting the volume, and the name assigned can be different from the name that Amazon EC2 uses\.

**Topics**
+ [Available Device Names](#available-ec2-device-names)
+ [Device Name Considerations](#device-name-limits)

For information about device names on Linux instances, see [Device Naming on Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/device_naming.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Available Device Names<a name="available-ec2-device-names"></a>

The following table lists the available device names for Windows instances\. The number of volumes that you can attach to your instance is determined by the operating system\. For more information, see [Instance Volume Limits](volume_limits.md)\.


| Driver Type | Available | Reserved for Root | Recommended for EBS Volumes | Instance Store Volumes | NVMe Volumes | 
| --- | --- | --- | --- | --- | --- | 
|  AWS PV, Citrix PV  |  xvd\[b\-z\] xvd\[b\-c\]\[a\-z\] /dev/sda1 /dev/sd\[b\-e\]  |  /dev/sda1  |  xvd\[f\-z\] †  |  xvd\[a\-e\] xvdc\[a\-x\] \(hs1\.8xlarge\)  | /dev/nvme\[0\-26\]n1 \* | 
|  Red Hat PV  |  xvd\[a\-z\] xvd\[b\-c\]\[a\-z\] /dev/sda1 /dev/sd\[b\-e\]  |  /dev/sda1  |  xvd\[f\-p\]  |  xvd\[a\-e\] xvdc\[a\-x\] \(hs1\.8xlarge\)  | /dev/nvme\[0\-26\]n1 \* | 

† If you map an EBS volume with the name `xvda`, Windows does not recognize the volume\.

\* NVMe instance store volumes are automatically enumerated and assigned a Windows drive letter\. There is no need to specify NVMe instance store volumes in your block device mapping\.

For more information about instance store volumes, see [Amazon EC2 Instance Store](InstanceStorage.md)\.

## Device Name Considerations<a name="device-name-limits"></a>

Keep the following in mind when selecting a device name:
+ Although you can attach your EBS volumes using the device names used to attach instance store volumes, we strongly recommend that you don't because the behavior can be unpredictable\.
+ AWS Windows AMIs come with additional software that prepares an instance when it first boots up\. This is either the Ec2Config service \(Windows AMIs prior to Windows Server 2016\) or EC2Launch \(Windows Server 2016\)\. After the devices have been mapped to drives, they are initialized and mounted\. The root drive is initialized and mounted as `C:\`\. The instance store volumes attached to the instance are initialized and mounted as `Z:\`, `Y:\`, and so on\. By default, when an EBS volume is attached to a Windows instance, it can show up as any drive letter on the instance\. You can change the settings to set the drive letters of the volumes per your specifications\. For more information, see [Configuring a Windows Instance Using the EC2Config Service](ec2config-service.md), [Configuring a Windows Instance Using EC2Launch](ec2launch.md), and [Mapping Disks to Volumes on Your Windows Instance](ec2-windows-volumes.md)\.
+ The number of NVMe instance store volumes for an instance depends on the size of the instance\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\.