# AWS NVMe Drivers for Windows Instances<a name="aws-nvme-drivers"></a>

The latest AWS Windows AMIs of the following Windows operating systems contain the AWS NVMe drivers used to interact with EBS and SSD instance store volumes that are exposed as NVMe block devices for better performance:
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012 RTM
+ Windows Server 2008 R2

For more information about EBS and NVMe, see [Amazon EBS and NVMe](nvme-ebs-volumes.md)\. For more information about SSD instance store and NVMe, see [SSD Instance Store Volumes](ssd-instance-store.md)\.

## Installing or Upgrading AWS NVMe Drivers<a name="install-nvme-drivers"></a>

The AWS Windows AMIs provided by Amazon include the AWS NVMe driver\. If you are not using the latest AWS Windows AMIs, you can install the current AWS NVMe driver\.

**To download and install the latest AWS NVMe driver**

1. Connect to your instance and log in as the local administrator\.

1. [Download](https://s3.amazonaws.com/ec2-windows-drivers-downloads/NVMe/Latest/AWSNVMe.zip) the latest driver package to the instance\.

1. Extract the zip archive\.

1. Install the driver by running the `install.ps1` PowerShell script\.

1. If the installer does not reboot your instance for you, restart the instance\.

## AWS NVMe Driver Version History<a name="nvme-driver-version-history"></a>

The following table describes the released versions of the AWS NVMe driver\.


| Driver version | Details | Release date | 
| --- | --- | --- | 
| 1\.2\.0 | Performance and reliability improvements for AWS NVMe devices on all supported instances, including bare metal instances\. | 13 June 2018 | 
| 1\.0\.0 | AWS NVMe driver for supported instance types running Windows Server | 12 February 2018 | 