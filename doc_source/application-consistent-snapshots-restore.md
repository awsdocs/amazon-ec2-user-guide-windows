# Restoring Volumes from VSS\-Enabled EBS snapshots<a name="application-consistent-snapshots-restore"></a>

You can use the `RestoreVssSnapshotSampleScript.ps1` script to restore volumes on an instance from VSS\-enabled EBS snapshots\. This script performs the following tasks:
+ Stops an instance
+ Removes all existing drives from the instance \(except the boot volume, if it was excluded\)
+ Creates new volumes from the snapshots
+ Attaches the volumes to the instance by using the device ID tag on the snapshot
+ Restarts the instance

**Important**  
The following script detaches all volumes attached to an instance, and then creates new volumes from a snapshot\. Make sure that you have properly backed\-up the instance\. The old volumes are not deleted\. If you want, you can edit the script to delete the old volumes\.

**To restore volumes from VSS\-enabled EBS snapshots**

1. Open AWS Tools for Windows PowerShell and run the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2 or have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Run the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Download the [RestoreVssSnapshotSampleScript\.zip](https://docs.aws.amazon.com/systems-manager/latest/userguide/samples/RestoreVssSnapshotSampleScript.zip) file and extract the file contents\. 

1. Open [RestoreVssSnapshotSampleScript\.zip](https://docs.aws.amazon.com/systems-manager/latest/userguide/samples/RestoreVssSnapshotSampleScript.zip) in a text editor and edit the sample call at the bottom of the script with a valid EC2 instance ID and EBS snapshot ID, and then run the script from PowerShell\.