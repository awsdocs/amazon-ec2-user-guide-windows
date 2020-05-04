# Deregistering your Windows AMI<a name="deregister-ami"></a>

You can deregister a Windows AMI when you have finished using it\. After you deregister an AMI, you can't use it to launch new instances\.

When you deregister an AMI, it doesn't affect any instances that you've already launched from the AMI or any snapshots created during the AMI creation process\. You'll continue to incur usage costs for these instances and storage costs for the snapshot\. Therefore, you should terminate any instances that you finished with and delete any snapshots that you are finished with\.

The following diagram illustrates the process for cleaning up your Windows AMI\.

![\[Process to clean up your Amazon EBS-backed Windows AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ami_delete_ebs.png)

**To clean up your Windows AMI**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. Select the AMI and take note of its ID — this can help you find the correct snapshot in the next step\. Choose **Actions**, and then **Deregister**\. When prompted for confirmation, choose **Continue**\.
**Note**  
It can take a few minutes before the console removes the AMI from the list\. Choose **Refresh** to refresh the status\.

1. In the navigation pane, choose **Snapshots**, and select the snapshot \(look for the AMI ID in the **Description** column\)\. Choose **Actions**, and then choose **Delete Snapshot**\. When prompted for confirmation, choose **Yes, Delete**\.

1. \(Optional\) If you are finished with an instance that you launched from the AMI, terminate it\. In the navigation pane, choose **Instances**\. Select the instance, choose **Actions**, then **Instance State**, and then **Terminate**\. When prompted for confirmation, choose **Yes, Terminate**\.