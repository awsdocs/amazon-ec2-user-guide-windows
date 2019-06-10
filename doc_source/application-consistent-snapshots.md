# Creating a VSS Application\-Consistent Snapshot<a name="application-consistent-snapshots"></a>

You can take application\-consistent snapshots of all [Amazon Elastic Block Store \(Amazon EBS\)](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EBSVolumes.html) volumes attached to your Windows on Amazon EC2 instances by using [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\. The snapshot process uses the Windows [Volume Shadow Copy Service \(VSS\)](https://technet.microsoft.com/en-us/library/ee923636(v=ws.10).aspx) to take image\-level backups of VSS\-aware applications, including data from pending transactions between these applications and the disk\. Furthermore, you don't have to shut down your instances or disconnect them when you back up all attached volumes\. 

There is no additional cost to use VSS\-enabled EBS snapshots\. You only pay for EBS snapshots that are created by the backup process\. For more information, see [How is my EBS snapshot bill calculated?](https://aws.amazon.com/premiumsupport/knowledge-center/ebs-snapshot-billing/)

**Topics**
+ [How It Works](#application-consistent-snapshots-how)
+ [Before You Begin](application-consistent-snapshots-prereqs.md)
+ [Getting Started](application-consistent-snapshots-getting-started.md)
+ [Creating a VSS Application\-Consistent Snapshot by Using the AWS CLI, AWS Tools for Windows PowerShell, or the AWSEC2\-ManageVssIO SSM Document](application-consistent-snapshots-creating-commands.md)
+ [Restoring Volumes from VSS\-Enabled EBS Snapshots](application-consistent-snapshots-restore.md)

## How It Works<a name="application-consistent-snapshots-how"></a>

Here is the process of taking application\-consistent, VSS\-enabled EBS snapshots\.

1. You verify and configure Systems Manager prerequisites\.

1. You enter parameters for the AWSEC2\-CreateVssSnapshot SSM document and execute the document by using Run Command\. You can't create a VSS\-enabled EBS snapshot for a specific volume\. You can, however, specify a parameter to exclude the boot volume from the backup process\.

1. The VSS agent on your instance coordinates all ongoing I/O operations for running applications\. 

1. The system flushes all I/O buffers and temporarily pauses all I/O operations\. The pause lasts, at most, ten seconds\.

1. During the pause, the system creates snapshots of all volumes attached to the instance\.

1. The pause is lifted and I/O resumes operation\. 

1. The system adds all newly\-created snapshots to the list of EBS snapshots\. The system tags all VSS\-enabled EBS snapshots that were successfully created by this process with **AppConsistent:true**\. This tag helps you identify snapshots created by this process, as opposed to other processes\. If the system encounters an error, the snapshot created by this process does not include the **AppConsistent:true** tag\.

1. If you need to restore from a snapshot, you can restore by using the standard EBS process of creating a volume from a snapshot, or you can restore all volumes to an instance by using a sample script, which is described later in this section\. 