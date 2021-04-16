# Amazon EC2 instance root device volume<a name="ec2-root-device-storage-windows"></a>

When you launch an instance, the *root device volume* contains the image used to boot the instance\. When you launch a Windows instance, a root EBS volume is created from an EBS snapshot and attached to the instance\.

## Configure the root volume to persist<a name="configure-root-volume-to-persist"></a>

By default, the root volume is deleted when the instance terminates \(the `DeleteOnTermination` attribute is `true`\)\. Using the console, you can change `DeleteOnTermination` when you launch an instance\. To change this attribute for an existing instance, you must use the command line\.

**To configure the root volume of a new instance to persist using the console**

1. Open the Amazon EC2 console\.

1. From the Amazon EC2 console dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose the AMI to use and then choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect the **Delete On Termination** check box for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

**To configure the root volume of an existing instance to persist using the AWS Tools for Windows PowerShell**  
Use the [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command with a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> $ebs = New-Object Amazon.EC2.Model.EbsInstanceBlockDeviceSpecification
C:\> $ebs.DeleteOnTermination = $false
C:\> $bdm = New-Object Amazon.EC2.Model.InstanceBlockDeviceMappingSpecification
C:\> $bdm.DeviceName = "/dev/xvda"
C:\> $bdm.Ebs = $ebs
C:\> Edit-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -BlockDeviceMapping $bdm
```

## Verify root volume persistence<a name="verify-root-volume-persistence"></a>

You can check whether the root volume of an instance is configured to persist when the instance terminates\.

**To verify root volume persistence using the console**  
Open the Amazon EC2 console and select the instance\. In the **Storage** tab, under **Block devices**, locate the entry for the root device volume\. If **Delete on termination** is **No**, the volume is configured to persist\.

**To verify root volume persistence using the AWS Tools for Windows PowerShell**  
Use the [ Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) and verify that the `DeleteOnTermination` attribute in the `BlockDeviceMappings` response element is set to `false`\.

```
C:\> (Get-EC2Instance -InstanceId i-i-1234567890abcdef0).Instances.BlockDeviceMappings.Ebs
```

## Change the initial size of the root volume<a name="change-root-volume-initial-size"></a>

By default, the size of the root volume is determined by the size of the snapshot\. You can increase the initial size of the root volume using the block device mapping of the instance as follows\.

1. Determine the device name of the root volume specified in the AMI, as described in [View the EBS volumes in an AMI block device mapping](block-device-mapping-concepts.md#view-ami-bdm)\.

1. Confirm the size of the snapshot specified in the AMI block device mapping, as described in [View Amazon EBS snapshot information](ebs-describing-snapshots.md)\.

1. Override the size of the root volume using the instance block device mapping, as described in [Update the block device mapping when launching an instance](block-device-mapping-concepts.md#Using_OverridingAMIBDM), specifying a volume size that is larger than the snapshot size\.

For example, the following entry for the instance block device mapping increases the size of the root volume, `/dev/xvda`, to 100 GiB\. You can omit the snapshot ID in the instance block device mapping because the snapshot ID is already specified in the AMI block device mapping\.

```
{
    "DeviceName": "/dev/xvda",
    "Ebs": {
      "VolumeSize": 100
    }
}
```

For more information, see [Block device mappings](block-device-mapping-concepts.md)\.