# Amazon EC2 instance root device volume<a name="ec2-root-device-storage-windows"></a>

When you launch an instance, the *root device volume* contains the image used to boot the instance\. When you launch a Windows instance, a root EBS volume is created from an EBS snapshot and attached to the instance\.

**Topics**
+ [Configure the root volume to persist](#configure-root-volume-to-persist)
+ [Confirm that a root volume is configured to persist](#confirm-root-device-persist)
+ [Change the initial size of the root volume](#change-root-volume-initial-size)

## Configure the root volume to persist<a name="configure-root-volume-to-persist"></a>

By default, the root volume is deleted when the instance terminates \(the `DeleteOnTermination` attribute is `true`\)\. Using the console, you can change `DeleteOnTermination` when you launch an instance\. To change this attribute for an existing instance, you must use the command line\.

**Topics**
+ [Configure the root volume to persist during instance launch](#configure-root-volume-to-persist-new)
+ [Configure the root volume to persist for an existing instance](#configure-root-volume-to-persist-new-existing)

### Configure the root volume to persist during instance launch<a name="configure-root-volume-to-persist-new"></a>

You can configure the root volume to persist when you launch an instance using the Amazon EC2 console or the command line tools\.

------
#### [ Console ]

**To configure the root volume to persist when you launch an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then choose **Launch instances**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select the AMI to use and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect **Delete On Termination** for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

------
#### [ AWS CLI ]

**To configure the root volume to persist when you launch an instance using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and include a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> aws ec2 run-instances --block-device-mappings file://mapping.json ...other parameters...
```

Specify the following in `mapping.json`\.

```
[
    {
        "DeviceName": "/dev/sda1",
        "Ebs": {
            "DeleteOnTermination": false
        }
    }
]
```

------
#### [ Tools for Windows PowerShell ]

**To configure the root volume to persist when you launch an instance using the Tools for Windows PowerShell**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command and include a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> $ebs = New-Object Amazon.EC2.Model.EbsBlockDevice
C:\> $ebs.DeleteOnTermination = $false
C:\> $bdm = New-Object Amazon.EC2.Model.BlockDeviceMapping
C:\> $bdm.DeviceName = "dev/xvda"
C:\> $bdm.Ebs = $ebs
C:\> New-EC2Instance -ImageId ami-0abcdef1234567890 -BlockDeviceMapping $bdm ...other parameters...
```

------

### Configure the root volume to persist for an existing instance<a name="configure-root-volume-to-persist-new-existing"></a>

You can configure the root volume to persist for a running instance using the command line tools only\. 

------
#### [ AWS CLI ]

**To configure the root volume to persist for an existing instance using the AWS CLI**  
Use the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) command with a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --block-device-mappings file://mapping.json
```

Specify the following in `mapping.json`\.

```
[
    {
        "DeviceName": "/dev/xvda",
        "Ebs": {
            "DeleteOnTermination": false
        }
    }
]
```

------
#### [ Tools for Windows PowerShell ]

**To configure the root volume to persist for an existing instance using the AWS Tools for Windows PowerShell**  
Use the [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command with a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> $ebs = New-Object Amazon.EC2.Model.EbsInstanceBlockDeviceSpecification
C:\> $ebs.DeleteOnTermination = $false
C:\> $bdm = New-Object Amazon.EC2.Model.InstanceBlockDeviceMappingSpecification
C:\> $bdm.DeviceName = "/dev/xvda"
C:\> $bdm.Ebs = $ebs
C:\> Edit-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -BlockDeviceMapping $bdm
```

------

## Confirm that a root volume is configured to persist<a name="confirm-root-device-persist"></a>

You can confirm that a root volume is configured to persist using the Amazon EC2 console or the command line tools\.

------
#### [ New console ]

**To confirm that a root volume is configured to persist using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select the instance\.

1. In the **Storage** tab, under **Block devices**, locate the entry for the root volume\. If **Delete on termination** is `No`, the volume is configured to persist\.

------
#### [ Old console ]

**To confirm that a root volume is configured to persist using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select the instance\.

1. In the **Description** tab, choose the entry for **Root device**\. If **Delete on termination** is `False`, the volume is configured to persist\.

------
#### [ AWS CLI ]

**To confirm that a root volume is configured to persist using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and verify that the `DeleteOnTermination` attribute in the `BlockDeviceMappings` response element is set to `false`\.

```
C:\> aws ec2 describe-instances --instance-id i-1234567890abcdef0
```

```
...
"BlockDeviceMappings": [
{
    "DeviceName": "/dev/sda1",
    "Ebs": {
        "Status": "attached",
        "DeleteOnTermination": false,
        "VolumeId": "vol-1234567890abcdef0",
        "AttachTime": "2013-07-19T02:42:39.000Z"
    }
}              
...
```

------
#### [ Tools for Windows PowerShell ]

**To confirm that a root volume is configured to persist using the AWS Tools for Windows PowerShell**  
Use the [ Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) and verify that the `DeleteOnTermination` attribute in the `BlockDeviceMappings` response element is set to `false`\.

```
C:\> (Get-EC2Instance -InstanceId i-i-1234567890abcdef0).Instances.BlockDeviceMappings.Ebs
```

------

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