# Viewing information about an Amazon EBS volume<a name="ebs-describing-volumes"></a>

You can view descriptive information about your EBS volumes\. For example, you can view information about all volumes in a specific Region or view detailed information about a single volume, including its size, volume type, whether the volume is encrypted, which master key was used to encrypt the volume, and the specific instance to which the volume is attached\.

You can get additional information about your EBS volumes, such as how much disk space is available, from the operating system on the instance\.

## Viewing volume information<a name="ebs-view-information-console"></a>

**To view information about an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. \(Optional\) Use the **Filter** options in the Search bar to display only the volumes that interest you\. For example, if you know the instance ID you want to see volumes for, go to the Search bar and choose **Instance ID** from the filter menu, then choose the instance ID you want from the list provided\. To remove a filter, choose it again\.

1. To view more information about a volume, select it\.

1. In the details pane, you can inspect the information provided about the volume\. **Attachment information** shows the instance ID this volume is attached to and the device name under which it is attached\.

1. \(Optional\) Choose the **Attachment information** link to view instance details\.

**To view the EBS volumes that are attached to an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. To view more information about an instance, select it\.

1. In the details pane, you can inspect the information provided about root and block devices\. Choose the device name link to show information on the volume attached under that device name\.

1. \(Optional\) Choose the **EBS ID** link to view details for the volume attached to this instance under the device name chosen in the prior step\.

**To view information about an EBS volume using the command line**  
You can use one of the following commands to view volume attributes\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) \(AWS CLI\)
+ [Get\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

### Volume state<a name="volume-state"></a>

Volume state describes the availability of an Amazon EBS volume\. You can view the volume state in the **State** column on the **Volumes** page in the console, or by using the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) AWS CLI command\.

The possible volume states are:

creating  
The volume is being created\.

available  
The volume is not attached to an instance\.

in\-use  
The volume is attached to an instance\.

deleting  
The volume is being deleted\.

deleted  
The volume is deleted\.

error  
The underlying hardware related to your EBS volume has failed, and the data associated with the volume is unrecoverable\. For information about how to restore the volume or recover the data on the volume, see [My EBS volume has a status of "error"](http://aws.amazon.com/premiumsupport/knowledge-center/ebs-error-status/)\.

## Viewing volume metrics<a name="ebs-view-volume-metrics"></a>

You can get additional information about your EBS volumes from Amazon CloudWatch\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

## Viewing free disk space<a name="ebs-view-free-disk-space"></a>

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