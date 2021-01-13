# Detach an Amazon EBS volume from a Windows instance<a name="ebs-detaching-volume"></a>

You need to detach an Amazon Elastic Block Store \(Amazon EBS\) volume from an instance before you can attach it to a different instance or delete it\. Detaching a volume does not affect the data on the volume\.

For information about detaching volumes from a Linux instance, see [Detaching a volume from a Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-detaching-volume.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Considerations](#considerations)
+ [Unmount and detach a volume](#umount-detach-volume)
+ [Troubleshoot](#detach-troubleshoot)

## Considerations<a name="considerations"></a>
+ You can detach an Amazon EBS volume from an instance explicitly or by terminating the instance\. However, if the instance is running, you must first unmount the volume from the instance\.
+ If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume\.
+ You can reattach a volume that you detached \(without unmounting it\), but it might not get the same mount point\. If there were writes to the volume in progress when it was detached, the data on the volume might be out of sync\.
+ After you detach a volume, you are still charged for volume storage as long as the storage amount exceeds the limit of the AWS Free Tier\. You must delete a volume to avoid incurring further charges\. For more information, see [Delete an Amazon EBS volume](ebs-deleting-volume.md)\.

## Unmount and detach a volume<a name="umount-detach-volume"></a>

Use the following procedures to unmount and detach a volume from an instance\. This can be useful when you need to attach the volume to a different instance or when you need to delete the volume\.

**Topics**
+ [Step 1: Unmount the volume](#unmount)
+ [Step 2: Detach the volume from the instance](#detach)
+ [Step 3: Uninstall the offline device locations](#uninstall)

### Step 1: Unmount the volume<a name="unmount"></a>

From your Windows instance, unmount the volume as follows\.

1. Start the Disk Management utility\.
   + \(Windows Server 2012 and later\) On the taskbar, right\-click the Windows logo and choose **Disk Management**\.
   + Windows Server 2008\) Choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

1. Right\-click the disk \(for example, right\-click **Disk 1**\) and then choose **Offline**\. Wait for the disk status to change to **Offline** before opening the Amazon EC2 console\.

### Step 2: Detach the volume from the instance<a name="detach"></a>

To detach the volume from the instance, use one of the following methods:

------
#### [ Console ]

**To detach an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select a volume and choose **Actions**, **Detach Volume**\. 

1. When prompted for confirmation, choose **Yes, Detach**\. 

------
#### [ Command line ]

**To detach an EBS volume from an instance using the command line**

After unmounting the volume, you can use one of the following commands to detach it\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [detach\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-volume.html) \(AWS CLI\)
+ [Dismount\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------

### Step 3: Uninstall the offline device locations<a name="uninstall"></a>

When you unmount and detach a volume from an instance, Windows flags the device location as offline\. The device location remains offline after rebooting, and stopping and restarting the instance\. When you restart the instance, Windows might mount one of the remaining volumes to the offline device location\. This causes the volume to be unavailable in Windows\. To prevent this from happening and to ensure that all volumes are attached to online device locations the next time Windows starts, perform the following steps:

1. On the instance, open the Device Manager\.

1. In the Device Manager, select **View**, **Show hidden devices**\.

1. In the list of devices, expand the **Storage controllers** node\.

   The device locations to which the detached volumes were mounted should appear greyed out\.

1. Right\-click each greyed out device location, select **Uninstall device** and choose **Uninstall**\.
**Important**  
Do not select the **Delete the driver software for this device** check box\.

## Troubleshoot<a name="detach-troubleshoot"></a>

The following are common problems encountered when detaching volumes, and how to resolve them\.

**Note**  
To guard against the possibility of data loss, take a snapshot of your volume before attempting to unmount it\. Forced detachment of a stuck volume can cause damage to the file system or the data it contains or an inability to attach a new volume using the same device name, unless you reboot the instance\.
+ If you encounter problems while detaching a volume through the Amazon EC2 console, it can be helpful to use the describe\-volumes CLI command to diagnose the issue\. For more information, see [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html)\.
+ If your volume stays in the `detaching` state, you can force the detachment by choosing **Force Detach**\. Use this option only as a last resort to detach a volume from a failed instance, or if you are detaching a volume with the intention of deleting it\. The instance doesn't get an opportunity to flush file system caches or file system metadata\. If you use this option, you must perform the file system check and repair procedures\. 
+ If you've tried to force the volume to detach multiple times over several minutes and it stays in the `detaching` state, you can post a request for help to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the volume ID and describe the steps that you've already taken\.
+ When you attempt to detach a volume that is still mounted, the volume can become stuck in the `busy` state while it is trying to detach\. The following output from describe\-volumes shows an example of this condition:

  ```
  "Volumes": [
      {
          "AvailabilityZone": "us-west-2b",
          "Attachments": [
              {
                  "AttachTime": "2016-07-21T23:44:52.000Z",
                  "InstanceId": "i-fedc9876",
                  "VolumeId": "vol-1234abcd",
                  "State": "busy",
                  "DeleteOnTermination": false,
                  "Device": "/dev/sdf"
              }
          ...
      }
  ]
  ```

  When you encounter this state, detachment can be delayed indefinitely until you unmount the volume, force detachment, reboot the instance, or all three\.