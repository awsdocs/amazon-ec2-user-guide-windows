# Requirements when modifying volumes<a name="modify-volume-requirements"></a>

The following requirements and limitations apply when you modify an Amazon EBS volume\. To learn more about the general requirements for EBS volumes, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

## Supported instance types<a name="instance-support"></a>

Elastic Volumes are supported on the following instances:
+ All [current\-generation instances](instance-types.md#AvailableInstanceTypes)
+ The following previous\-generation instances: C1, C3, CC2, CR1, G2, I2, M1, M3, and R3

If your instance type does not support Elastic Volumes, see [Modifying an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.

## Requirements for Windows volumes<a name="window-volumes"></a>

By default, Windows initializes volumes with a master boot record \(MBR\) partition table\. Because MBR supports only volumes smaller than 2 TiB \(2,048 GiB\), Windows prevents you from resizing MBR volumes beyond this limit\. In such a case, the **Extend Volume** option is disabled in the Windows **Disk Management** utility\. If you use the AWS Management Console or AWS CLI to create an MBR\-partitioned volume that exceeds the size limit, Windows cannot detect or use the additional space\. For requirements affecting Linux volumes, see [Requirements for Linux Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/volume_constraints.html) in the *Amazon EC2 User Guide for Linux Instances*\.

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
+ Elastic Volume operations are not supported on Multi\-Attach enabled Amazon EBS volumes\.
+ The new volume size cannot exceed the supported volume capacity\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ If the volume was attached before November 3, 2016 23:40 UTC, you must initialize Elastic Volumes support\. For more information, see [Initializing Elastic Volumes Support](requesting-ebs-volume-modifications.md#initialize-modification-support)\.
+ If you are using an unsupported previous\-generation instance type, or if you encounter an error while attempting a volume modification, see [Modifying an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.
+ A `gp2` volume that is attached to an instance as a root volume cannot be modified to an `st1` or `sc1` volume\. If detached and modified to `st1` or `sc1`, it cannot be attached to an instance as the root volume\.
+ A `gp2` volume cannot be modified to an `st1` or `sc1` volume if the requested volume size is below the minimum size for `st1` and `sc1` volumes\.
+ In some cases, you must detach the volume or stop the instance for modification to proceed\. If you encounter an error message while attempting to modify an EBS volume, or if you are modifying an EBS volume attached to a previous\-generation instance type, take one of the following steps:
  + For a non\-root volume, detach the volume from the instance, apply the modifications, and then re\-attach the volume\.
  + For a root \(boot\) volume, stop the instance, apply the modifications, and then restart the instance\.
+ After provisioning over 32,000 IOPS on an existing `io1` or `io2` volume, you may need to do one of the following to see the full performance improvements:
  + Detach and attach the volume\.
  + Restart the instance\.
+ Decreasing the size of an EBS volume is not supported\. However, you can create a smaller volume and then migrate your data to it using an application\-level tool such as robocopy\.
+ Modification time is increased if you modify a volume that has not been fully initialized\. For more information see [Initializing Amazon EBS volumes](ebs-initialize.md)\.
+ After modifying a volume, wait at least six hours and ensure that the volume is in the `in-use` or `available` state before making additional modifications to the same volume\.
+ While `m3.medium` instances fully support volume modification, `m3.large`, `m3.xlarge`, and `m3.2xlarge` instances might not support all volume modification features\.