# Hibernate your Windows instance<a name="Hibernate"></a>

When you hibernate an instance, Amazon EC2 signals the operating system to perform hibernation \(suspend\-to\-disk\)\. Hibernation saves the contents from the instance memory \(RAM\) to your Amazon EBS root volume\. Amazon EC2 persists the instance's Amazon EBS root volume and any attached Amazon EBS data volumes\. When you start your instance:
+ The Amazon EBS root volume is restored to its previous state
+ The RAM contents are reloaded
+ The processes that were previously running on the instance are resumed
+ Previously attached data volumes are reattached and the instance retains its instance ID

You can hibernate an instance only if it's [enabled for hibernation](#enabling-hibernation) and it meets the [hibernation prerequisites](#hibernating-prerequisites)\.

If an instance or application takes a long time to bootstrap and build a memory footprint to become fully productive, you can use hibernation to pre\-warm the instance\. To pre\-warm the instance, you:

1. Launch it with hibernation enabled\.

1. Bring it to a desired state\.

1. Hibernate it, ready to be resumed to the same state as needed\.

You're not charged for instance usage for a hibernated instance when it is in the `stopped` state\. You are charged for instance usage while the instance is in the `stopping` state, when the contents of the RAM are transferred to the Amazon EBS root volume\. \(This is different from when you [stop an instance](Stop_Start.md) without hibernating it\.\) You're not charged for data transfer\. However, you are charged for storage of any Amazon EBS volumes, including storage for the RAM contents\.

If you no longer need an instance, you can terminate it at any time, including when it is in a `stopped` \(hibernated\) state\. For more information, see [Terminate your instance](terminating-instances.md)\.

**Note**  
For information about using hibernation on Linux instances, see [Hibernate your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Overview of hibernation](#instance_hibernate)
+ [Hibernation prerequisites](#hibernating-prerequisites)
+ [Limitations](#hibernating-not-supported)
+ [Enabling hibernation for an instance](#enabling-hibernation)
+ [Hibernating an instance](#hibernating-instances)
+ [Starting a hibernated instance](#hibernating-resuming)
+ [Troubleshooting hibernation](#troubleshoot-instance-hibernate)

## Overview of hibernation<a name="instance_hibernate"></a>

The following diagram shows a basic overview of the hibernation process\.

![\[Overview of the hibernation flow\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/hibernation-flow.png)

When you hibernate a running instance, the following happens:
+ When you initiate hibernation, the instance moves to the `stopping` state\. Amazon EC2 signals the operating system to perform hibernation \(suspend\-to\-disk\)\. The hibernation freezes all of the processes, saves the contents of the RAM to the Amazon EBS root volume, and then performs a regular shutdown\.
+ After the shutdown is complete, the instance moves to the `stopped` state\.
+ Any Amazon EBS volumes remain attached to the instance, and their data persists, including the saved contents of the RAM\.
+ In most cases, the instance is migrated to a new underlying host computer when it's started\. This is also what happens when you stop and start an instance\.
+ When you start the instance, the instance boots up and the operating system reads in the contents of the RAM from the Amazon EBS root volume, before unfreezing processes to resume its state\.
+ The instance retains its private IPv4 addresses and any IPv6 addresses\. When you start the instance, the instance continues to retain its private IPv4 addresses and any IPv6 addresses\.
+ Amazon EC2 releases the public IPv4 address\. When you start the instance, Amazon EC2 assigns a new public IPv4 address to the instance\.
+ The instance retains its associated Elastic IP addresses\. You're charged for any Elastic IP addresses associated with a hibernated instance\. With EC2\-Classic, an Elastic IP address is disassociated from your instance when you hibernate it\. For more information, see [EC2\-Classic](ec2-classic-platform.md)\.
+ When you hibernate a ClassicLink instance, it's unlinked from the VPC to which it was linked\. You must link the instance to the VPC again after starting it\. For more information, see [ClassicLink](vpc-classiclink.md)\.

For information about how hibernation differs from reboot, stop, and terminate, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## Hibernation prerequisites<a name="hibernating-prerequisites"></a>

To hibernate an instance, the following prerequisites must be in place:
+ **Supported instance families** \- C3, C4, C5, M3, M4, M5, M5a, R3, R4, R5, R5a, and T2\.
+ **Instance RAM size** \- must be up to 16 GB\.
+ **Instance size** \- not supported for bare metal instances\.
+ **Supported AMIs** \(must be an HVM AMI that supports hibernation\):
  + Windows Server 2012 AMI released 2019\.09\.11 or later\.
  + Windows Server 2012 R2 AMI released 2019\.09\.11 or later\.
  + Windows Server 2016 AMI released 2019\.09\.11 or later\.
  + Windows Server 2019 AMI released 2019\.09\.11 or later\.

  For information about the supported AMIs for Linux, see [Hibernation prerequisites](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html#hibernating-prerequisites) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Root volume type** \- must be an Amazon EBS volume, not an instance store volume\.
+ **Supported Amazon EBS volume types** \- General Purpose SSD \(`gp2`\) or Provisioned IOPS SSD \(`io1` or `io2`\)\. If you choose a Provisioned IOPS SSD \(`io1` or `io2`\) volume type, to achieve optimum performance for hibernation, you must provision the EBS volume with the appropriate IOPS\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.
+ **Amazon EBS root volume size** \- must be large enough to store the RAM contents and accommodate your expected usage, for example, OS or applications\. If you enable hibernation, space is allocated on the root volume at launch to store the RAM\.
+ **Amazon EBS root volume encryption** \- To use hibernation, the root volume must be encrypted to ensure the protection of sensitive content that is in memory at the time of hibernation\. When RAM data is moved to the Amazon EBS root volume, it is always encrypted\. Encryption of the root volume is enforced at instance launch\. Use one of the following three options to ensure that the root volume is an encrypted Amazon EBS volume:
  + EBS “single\-step” encryption: You can launch encrypted EBS\-backed EC2 instances from an unencrypted AMI and also enable hibernation at the same time\. For more information, see [Using encryption with EBS\-backed AMIs](AMIEncryption.md)\.
  + EBS encryption by default: You can enable EBS encryption by default to ensure all new EBS volumes created in your AWS account are encrypted\. This way, you can enable hibernation for your instances without specifying encryption intent at instance launch\. For more information, see [Encryption by default](EBSEncryption.md#encryption-by-default)\.
  + Encrypted AMI: You can enable EBS encryption by using an encrypted AMI to launch your instance\. If your AMI does not have an encrypted root snapshot, you can copy it to a new AMI and request encryption\. For more information, see [Encrypt an unencrypted image during copy](AMIEncryption.md#copy-unencrypted-to-encrypted) and [Copying an AMI](CopyingAMIs.md#ami-copy-steps)\.
+ **Enable hibernation at launch** \- You cannot enable hibernation on an existing instance \(running or stopped\)\. For more information, see [Enabling hibernation for an instance](#enabling-hibernation)\.
+ **Purchasing options** \- This feature is available for On\-Demand Instances and Reserved Instances\. It is not available for Spot Instances\. For more information, see [Hibernating interrupted Spot Instances](spot-interruptions.md#hibernate-spot-instances)\.

## Limitations<a name="hibernating-not-supported"></a>
+ The following actions are not supported for hibernation:
  + Changing the instance type or size of a hibernated instance
  + Creating snapshots or AMIs from instances for which hibernation is enabled
  + Creating snapshots or AMIs from hibernated instances
+ You can't stop or hibernate instance store\-backed instances\.\*
+ You can't hibernate an instance that has more than 16 GB of RAM\.
+ You cannot hibernate an instance that is in an Auto Scaling group or used by Amazon ECS\. If your instance is in an Auto Scaling group and you try to hibernate it, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance\. For more information, see [Health Checks for Auto Scaling Instances](https://docs.aws.amazon.com/autoscaling/latest/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ We do not support keeping an instance hibernated for more than 60 days\. To keep the instance for longer than 60 days, you must start the hibernated instance, stop the instance, and start it\.
+ We constantly update our platform with upgrades and security patches, which can conflict with existing hibernated instances\. We notify you about critical updates that require a start for hibernated instances so that we can perform a shutdown or a reboot to apply the necessary upgrades and security patches\.

\*For C3 and R3 instances that are enabled for hibernation, do not use instance store volumes\.

## Enabling hibernation for an instance<a name="enabling-hibernation"></a>

To hibernate an instance, it must first be enabled for hibernation\. To enable hibernation, you must do it while launching the instance\.

**Important**  
You can't enable or disable hibernation for an instance after you launch it\.

------
#### [ Console ]

**To enable hibernation using the console**

1. Follow the [Launching an instance using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI that supports hibernation\. For more information about supported AMIs, see [Hibernation prerequisites](#hibernating-prerequisites)\.

1. On the **Choose an Instance Type** page, select a supported instance type, and choose **Next: Configure Instance Details**\. For information about supported instance types, see [Hibernation prerequisites](#hibernating-prerequisites)\.

1. On the **Configure Instance Details** page, for **Stop \- Hibernate Behavior**, select the **Enable hibernation as an additional stop behavior** check box\.

1. On the **Add Storage** page, for the root volume, specify the following information: 
   + For **Size \(GiB\)**, enter the Amazon EBS root volume size\. The volume must be large enough to store the RAM contents and accommodate your expected usage\.
   + For **Volume Type**, select a supported Amazon EBS volume type \(General Purpose SSD \(`gp2`\) or Provisioned IOPS SSD \(`io1` or `io2`\)\.
   + For **Encryption**, select the encryption key for the volume\. If you enabled encryption by default in this AWS Region, the default encryption key is selected\.

   For more information about the prerequisites for the root volume, see [Hibernation prerequisites](#hibernating-prerequisites)\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

------
#### [ AWS CLI ]

**To enable hibernation using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch an instance\. Specify the EBS root volume parameters using the `--block-device-mappings file://mapping.json` parameter, and enable hibernation using the `--hibernation-options Configured=true` parameter\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type m5.large \
    --block-device-mappings file://mapping.json \
    --hibernation-options Configured=true \
    --count 1 \
    --key-name MyKeyPair
```

Specify the following in `mapping.json`:

```
[
    {
        "DeviceName": "/dev/xvda",
        "Ebs": {
            "VolumeSize": 30,
            "VolumeType": "gp2",
            "Encrypted": true
        }
    }
]
```

**Note**  
The value for `DeviceName` must match the root device name associated with the AMI\. To find the root device name, use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, as follows:  

```
aws ec2 describe-images --image-id ami-0abcdef1234567890
```
If you enabled encryption by default in this AWS Region, you can omit `"Encrypted": true`\.

------
#### [ AWS Tools for Windows PowerShell ]

**To enable hibernation using the AWS Tools for Windows PowerShell**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command to launch an instance\. Specify the EBS root volume by first defining the block device mapping, and then adding it to the command using the `-BlockDeviceMappings` parameter\. Enable hibernation using the `-HibernationOptions_Configured $true` parameter\.

```
PS C:\> $ebs_encrypt = New-Object Amazon.EC2.Model.BlockDeviceMapping
PS C:\> $ebs_encrypt.DeviceName = "/dev/xvda"
PS C:\> $ebs_encrypt.Ebs = New-Object Amazon.EC2.Model.EbsBlockDevice
PS C:\> $ebs_encrypt.Ebs.VolumeSize = 30
PS C:\> $ebs_encrypt.Ebs.VolumeType = "gp2"
PS C:\> $ebs_encrypt.Ebs.Encrypted = $true

PS C:\> New-EC2Instance `
             -ImageId ami-0abcdef1234567890 `
             -InstanceType m5.large `
             -BlockDeviceMappings $ebs_encrypt `
             -HibernationOptions_Configured $true `
             -MinCount 1 `
             -MaxCount 1 `
             -KeyName MyKeyPair
```

**Note**  
The value for `DeviceName` must match the root device name associated with the AMI\. To find the root device name, use the [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) command, as follows:  

```
Get-EC2Image -ImageId ami-0abcdef1234567890
```
If you enabled encryption by default in this AWS Region, you can omit `Encrypted = $true` from the block device mapping\.

------

 

------
#### [ Console ]

**To view if an instance is enabled for hibernation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, in the details pane, inspect **Stop \- Hibernation behavior**\. **Enabled** indicates that the instance is enabled for hibernation\.

------
#### [ AWS CLI ]

**To view if an instance is enabled for hibernation using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters "Name=hibernation-options.configured,Values=true"` parameter to filter instances that are enabled for hibernation\.

```
aws ec2 describe-instances \
    --filters "Name=hibernation-options.configured,Values=true"
```

The following field in the output indicates that the instance is enabled for hibernation\.

```
"HibernationOptions": {
    "Configured": true
}
```

------
#### [ AWS Tools for Windows PowerShell ]

**To view if an instance is enabled for hibernation using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) command and specify the `-Filter @{ Name="hibernation-options.configured"; Value="true"}` parameter to filter instances that are enabled for hibernation\.

```
Get-EC2Instance `
    -Filter @{ Name="hibernation-options.configured"; Value="true"}
```

The output lists the EC2 instances that are enabled for hibernation\. 

------

## Hibernating an instance<a name="hibernating-instances"></a>

You can hibernate an instance if the instance is [enabled for hibernation](#enabling-hibernation) and meets the [hibernation prerequisites](#hibernating-prerequisites)\. If an instance cannot hibernate successfully, a normal shutdown occurs\.

------
#### [ Console ]

**To hibernate an Amazon EBS\-backed instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance, and choose **Actions**, **Instance State**, **Stop \- Hibernate**\. If **Stop \- Hibernate** is disabled, the instance is already hibernated or stopped, or it can't be hibernated\. For more information, see [Hibernation prerequisites](#hibernating-prerequisites)\.

1. In the confirmation dialog box, choose **Yes, Stop \- Hibernate**\. It can take a few minutes for the instance to hibernate\. The **Instance State** changes to **Stopping** while the instance is hibernating, and then **Stopped** when the instance has hibernated\.

------
#### [ AWS CLI ]

**To hibernate an Amazon EBS\-backed instance using the AWS CLI**  
Use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command and specify the `--hibernate` parameter\.

```
aws ec2 stop-instances \
    --instance-ids i-1234567890abcdef0 \
    --hibernate
```

------
#### [ AWS Tools for Windows PowerShell ]

**To hibernate an Amazon EBS\-backed instance using the AWS Tools for Windows PowerShell**  
Use the [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) command and specify the `-Hibernate $true` parameter\.

```
Stop-EC2Instance `
    -InstanceId i-1234567890abcdef0 `
    -Hibernate $true
```

------

 

------
#### [ Console ]

**To view if hibernation was initiated on an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, in the details pane, inspect **State transition reason message**\. The message **Client\.UserInitiatedHibernate: User initiated hibernate** indicates that hibernation was initiated on the instance\.

------
#### [ AWS CLI ]

**To view if hibernation was initiated on an instance using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `state-reason-code` filter to see the instances on which hibernation was initiated\.

```
aws ec2 describe-instances \
    --filters "Name=state-reason-code,Values=Client.UserInitiatedHibernate"
```

The following field in the output indicates that hibernation was initiated on the instance\.

```
"StateReason": {
    "Code": "Client.UserInitiatedHibernate"
}
```

------
#### [ AWS Tools for Windows PowerShell ]

**To view if hibernation was initiated on an instance using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) command and specify the `state-reason-code` filter to see the instances on which hibernation was initiated\.

```
Get-EC2Instance `
    -Filter @{Name="state-reason-code";Value="Client.UserInitiatedHibernate"}
```

The output lists the EC2 instances on which hibernation was initiated\. 

------

## Starting a hibernated instance<a name="hibernating-resuming"></a>

Start a hibernated instance by starting it in the same way that you would start a stopped instance\.

------
#### [ Console ]

**To start a hibernated instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select a hibernated instance, and choose **Actions**, **Instance State**, **Start**\. It can take a few minutes for the instance to enter the `running` state\. During this time, the instance [status checks](monitoring-system-instance-status-check.md#types-of-instance-status-checks) show the instance in a failed state until the instance has started\.

------
#### [ AWS CLI ]

**To start a hibernated instance using the AWS CLI**  
Use the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command\.

```
aws ec2 start-instances \
    --instance-ids i-1234567890abcdef0
```

------
#### [ AWS Tools for Windows PowerShell ]

**To start a hibernated instance using the AWS Tools for Windows PowerShell**  
Use the [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) command\.

```
Start-EC2Instance `
    -InstanceId i-1234567890abcdef0
```

------

## Troubleshooting hibernation<a name="troubleshoot-instance-hibernate"></a>

Use this information to help diagnose and fix issues that you might encounter when hibernating an instance\.

### Can't hibernate immediately after launch<a name="hibernate-troubleshooting-1"></a>

If you try to hibernate an instance too quickly after you've launched it, you get an error\.

You must wait for about five minutes after launch before hibernating\.

### Takes too long to transition from `stopping` to `stopped`, and memory state not restored after start<a name="hibernate-troubleshooting-2"></a>

If it takes a long time for your hibernating instance to transition from the `stopping` state to `stopped`, and if the memory state is not restored after you start, this could indicate that hibernation was not properly configured\.







**Windows Server 2016 and later**  
Check the EC2 launch log and look for messages that are related to hibernation\. To access the EC2 launch log, [connect](connecting_to_windows_instance.md) to the instance and open the `C:\ProgramData\Amazon\EC2-Windows\Launch\Log\Ec2Launch.log` file in a text editor\.

**Note**  
By default, Windows hides files and folders under `C:\ProgramData`\. To view EC2 Launch directories and files, enter the path in Windows Explorer or change the folder properties to show hidden files and folders\.

Find the log lines for hibernation\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that hibernation failed to configure: `Message: Failed to enable hibernation.`

If the log line contains `HibernationEnabled: true`, hibernation was successfully configured\.

**Windows Server 2012 R2 and earlier**  
Check the EC2 config log and look for messages that are related to hibernation\. To access the EC2 config log, [connect](connecting_to_windows_instance.md) to the instance and open the `C:\Program Files\Amazon\Ec2ConfigService\Logs\Ec2ConfigLog.txt` file in a text editor\. Find the log lines for `SetHibernateOnSleep`\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that the instance root volume is not large enough: `SetHibernateOnSleep: Failed to enable hibernation: Hibernation failed with the following error: There is not enough space on the disk.`

If the log line is `SetHibernateOnSleep: HibernationEnabled: true`, hibernation was successfully configured\.

If you do not see any logs from these processes, your AMI might not support hibernation\. For information about supported AMIs, see [Hibernation prerequisites](#hibernating-prerequisites)\. 

### Instance "stuck" in the stopping state<a name="hibernate-troubleshooting-3"></a>

If you hibernated your instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshooting stopping your instance](TroubleshootingInstancesStopping.md)\.