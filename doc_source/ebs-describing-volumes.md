# View information about an Amazon EBS volume<a name="ebs-describing-volumes"></a>

You can view descriptive information about your EBS volumes\. For example, you can view information about all volumes in a specific Region or view detailed information about a single volume, including its size, volume type, whether the volume is encrypted, which master key was used to encrypt the volume, and the specific instance to which the volume is attached\.

You can get additional information about your EBS volumes, such as how much disk space is available, from the operating system on the instance\.

## View volume information<a name="ebs-view-information-console"></a>

You can view information about a volume using one of the following methods\.

------
#### [ New console ]

**To view information about a volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. To reduce the list, you can filter your volumes using tags and volume attributes\. Choose the filter field, select a tag or volume attribute, and then select the filter value\.

1. To view more information about a volume, choose its ID\.

**To view the EBS volumes that are attached to an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Storage** tab, the **Block devices** section lists the volumes that are attached to the instance\. To view information about a specific volume, choose its ID in the **Volume ID** column\.

------
#### [ Old console ]

**To view information about an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. \(Optional\) Use the filter options in the search field to display only the volumes that interest you\. For example, if you know the instance ID, choose **Instance ID** from the search field menu, and then choose the instance ID from the list provided\. To remove a filter, choose it again\.

1. Select the volume\.

1. In the details pane, you can inspect the information provided about the volume\. **Attachment information** shows the instance ID this volume is attached to and the device name under which it is attached\.

1. \(Optional\) Choose the **Attachment information** link to view additional details about the instance\.

**To view the EBS volumes that are attached to an instance using the **new console****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. In the **Description** tab, view the information provided for **Block devices**\. To view information about a specific volume, choose a link next to Block devices and then choose the volume ID\.

**To view the EBS volumes that are attached to an instance using the **old console****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. In the **Description** tab, for **Block devices**, select the block device mapping and then choose the **EBS ID** to view additional details for the volume\.

------
#### [ AWS CLI ]

**To view information about an EBS volume using the command line**  
You can use one of the following commands to view volume attributes\. For more information, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) \(AWS CLI\)
+ [Get\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------
#### [ Amazon EC2 Global View ]

You can use Amazon EC2 Global View to view your volumes across all Regions for which your AWS account is enabled\. For more information, see [List and filter resources across Regions using Amazon EC2 Global View](Using_Filtering.md#global-view)\.

------

### Volume state<a name="volume-state"></a>

Volume state describes the availability of an Amazon EBS volume\. You can view the volume state in the **State** column on the **Volumes** page in the console, or by using the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) AWS CLI command\.

The possible volume states are:

`creating`  
The volume is being created\.

`available`  
The volume is not attached to an instance\.

`in-use`  
The volume is attached to an instance\.

`deleting`  
The volume is being deleted\.

`deleted`  
The volume is deleted\.

`error`  
The underlying hardware related to your EBS volume has failed, and the data associated with the volume is unrecoverable\. For information about how to restore the volume or recover the data on the volume, see [My EBS volume has a status of "error"](http://aws.amazon.com/premiumsupport/knowledge-center/ebs-error-status/)\.

## View volume metrics<a name="ebs-view-volume-metrics"></a>

You can get additional information about your EBS volumes from Amazon CloudWatch\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

## View free disk space<a name="ebs-view-free-disk-space"></a>

You can get additional information about your EBS volumes, such as how much disk space is available, from the Windows operating system on the instance\. For example, you can view the free disk space by opening File Explorer and selecting **This PC**\.

You can also view the free disk space using the following `dir` command and examining the last line of the output:

```
C:\> dir C:
 Volume in drive C has no label.
 Volume Serial Number is 68C3-8081

 Directory of C:\

03/25/2018  02:10 AM    <DIR>          .
03/25/2018  02:10 AM    <DIR>          ..
03/25/2018  03:47 AM    <DIR>          Contacts
03/25/2018  03:47 AM    <DIR>          Desktop
03/25/2018  03:47 AM    <DIR>          Documents
03/25/2018  03:47 AM    <DIR>          Downloads
03/25/2018  03:47 AM    <DIR>          Favorites
03/25/2018  03:47 AM    <DIR>          Links
03/25/2018  03:47 AM    <DIR>          Music
03/25/2018  03:47 AM    <DIR>          Pictures
03/25/2018  03:47 AM    <DIR>          Saved Games
03/25/2018  03:47 AM    <DIR>          Searches
03/25/2018  03:47 AM    <DIR>          Videos
               0 File(s)              0 bytes
              13 Dir(s)  18,113,662,976 bytes free
```

You can also view the free disk space using the following `fsutil` command:

```
C:\> fsutil volume diskfree C:
Total # of free bytes        : 18113204224
Total # of bytes             : 32210153472
Total # of avail free bytes  : 18113204224
```

For information about viewing free disk space on a Linux instance, see [View free disk space](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-describing-volumes.html#ebs-view-free-disk-space) in the *Amazon EC2 User Guide for Linux Instances*\.