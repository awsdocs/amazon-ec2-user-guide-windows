# Requirements when modifying volumes<a name="modify-volume-requirements"></a>

The following requirements and limitations apply when you modify an Amazon EBS volume\. To learn more about the general requirements for EBS volumes, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

**Topics**
+ [Supported instance types](#instance-support)
+ [Requirements for Windows volumes](#window-volumes)
+ [Limitations](#elastic-volumes-limitations)

## Supported instance types<a name="instance-support"></a>

Elastic Volumes are supported on the following instances:
+ All [current\-generation instances](instance-types.md#AvailableInstanceTypes)
+ The following previous\-generation instances: C1, C3, CC2, CR1, G2, I2, M1, M3, and R3

If your instance type does not support Elastic Volumes, see [Modify an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.

## Requirements for Windows volumes<a name="window-volumes"></a>

By default, Windows initializes volumes with a master boot record \(MBR\) partition table\. Because MBR supports only volumes smaller than 2 TiB \(2,048 GiB\), Windows prevents you from resizing MBR volumes beyond this limit\. In such a case, the **Extend Volume** option is disabled in the Windows **Disk Management** utility\. If you use the AWS Management Console or AWS CLI to create an MBR\-partitioned volume that exceeds the size limit, Windows cannot detect or use the additional space\. For requirements affecting Linux volumes, see [Requirements for Linux volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/volume_constraints.html) in the *Amazon EC2 User Guide for Linux Instances*\.

To overcome this limitation, you can create a new, larger volume with a GUID partition table \(GPT\) and copy over the data from the original MBR volume\. 

**To create a GPT volume**

1. Create a new, empty volume of the desired size in the Availability Zone of the EC2 instance and attach it to your instance\. 
**Note**  
The new volume must not be a volume restored from a snapshot\.

1. Log in to your Windows system and open **Disk Management** \(diskmgmt\.exe\)\. 

1. Open the context \(right\-click\) menu for the new disk and choose **Online**\.

1. In the **Initialize Disk** window, select the new disk and choose **GPT \(GUID Partition Table\)**, **OK**\.

1. When initialization is complete, copy the data from the original volume to the new volume, using a tool such as robocopy or teracopy\.

1. In **Disk Management**, change the drive letters to appropriate values and take the old volume offline\.

1. In the Amazon EC2 console, detach the old volume from the instance, reboot the instance to verify that it functions properly, and delete the old volume\.

## Limitations<a name="elastic-volumes-limitations"></a>
+ There are limits to the maximum aggregated storage that can be requested across volume modifications\. For more information, see [Amazon EBS service quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#limits_ebs) in the *Amazon Web Services General Reference*\.
+ After modifying a volume, you must wait at least six hours and ensure that the volume is in the `in-use` or `available` state before you can modify the same volume\. This is sometimes referred to as a cooldown period\.
+ If the volume was attached before November 3, 2016 23:40 UTC, you must initialize Elastic Volumes support\. For more information, see [Initializing Elastic Volumes Support](requesting-ebs-volume-modifications.md#initialize-modification-support)\.
+ If you encounter an error message while attempting to modify an EBS volume, or if you are modifying an EBS volume attached to a previous\-generation instance type, take one of the following steps:
  + For a non\-root volume, detach the volume from the instance, apply the modifications, and then re\-attach the volume\.
  + For a root volume, stop the instance, apply the modifications, and then restart the instance\.
+ Modification time is increased for volumes that are not fully initialized\. For more information see [Initialize Amazon EBS volumes](ebs-initialize.md)\.
+ The new volume size can't exceed the supported capacity of its file system and partitioning scheme\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ If you modify the volume type of a volume, the size and performance must be within the limits of the target volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)
+ You can't decrease the size of an EBS volume\. However, you can create a smaller volume and then migrate your data to it using an application\-level tool such as  robocopy\.
+ After provisioning over 32,000 IOPS on an existing `io1` or `io2` volume, you might need to detach and re\-attach the volume, or restart the instance to see the full performance improvements\.
+ For `io2` volumes, you can't increase the size beyond `16` TiB or the IOPS beyond `64,000` while the volume is attached to an instance type that does not support `io2` Block Express volumes\. Currently, only R5b, X2idn, X2iedn, and C7g instances support `io2` Block Express volumes volumes\. For more information, see [`io2` Block Express volumes](ebs-volume-types.md#io2-block-express)
+ You can't modify the size or provisioned IOPS of an `io2` volume that is attached to an R5b, X2idn, X2iedn, and C7g instance\.
+ You can't modify the volume type of Multi\-Attach enabled `io2` volumes\.
+ You can't modify the volume type, size, or Provisioned IOPS of Multi\-Attach enabled `io1` volumes\.
+ A `gp2` volume that is attached to an instance as a root volume can't be modified to an `st1` or `sc1` volume\. If detached and modified to `st1` or `sc1`, it can't be re\-attached to an instance as the root volume\.
+ While `m3.medium` instances fully support volume modification, `m3.large`, `m3.xlarge`, and `m3.2xlarge` instances might not support all volume modification features\.