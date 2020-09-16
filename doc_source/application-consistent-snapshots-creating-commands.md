# Creating a VSS Application\-Consistent Snapshot Using the AWS CLI, AWS Tools for Windows PowerShell, or the AWSEC2\-ManageVssIO SSM Document<a name="application-consistent-snapshots-creating-commands"></a>

This section includes procedures for creating VSS\-enabled EBS snapshots by using the AWS CLI or AWS Tools for Windows PowerShell\. It also contains an advanced method for creating VSS\-enabled snapshots using the AWSEC2\-ManageVssIO SSM document\.

**Topics**
+ [Install the VSS Package Using the AWS CLI or Tools for Windows PowerShell](#application-consistent-snapshots-vss-package-command)
+ [Create VSS\-Enabled EBS Snapshots Using the AWS CLI, Tools for Windows PowerShell, or the AWSEC2\-ManageVssIO SSM Document](#application-cosistent-snapshots-cli)

## Install the VSS Package Using the AWS CLI or Tools for Windows PowerShell<a name="application-consistent-snapshots-vss-package-command"></a>

Use one of the following command\-line procedures to download and install the VSS components to the Windows on EC2 instance\.

### Install the VSS Package by Using the AWS CLI<a name="application-consistent-snapshots-vss-package-cli"></a>

Use the following procedure to download and install the `AwsVssComponents` package on your instances by using Run Command from the AWS CLI\. The package installs two components: a VSS requestor and a VSS provider\. The system copies these components to a directory on the instance, and then registers the provider DLL as a VSS provider\.

**To install the VSS package by using the AWS CLI**

1. Install and configure the AWS CLI, if you have not already\.

   For information, see [Install or Upgrade and then Configure the AWS CLI](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-cli.html) in the *AWS Systems Manager User Guide*\.

1. Run the following command to download and install the required VSS components for Systems Manager\.

   ```
   aws ssm send-command --document-name "AWS-ConfigureAWSPackage" --instance-ids "i-12345678" --parameters '{"action":["Install"],"name":["AwsVssComponents"]}'
   ```

### Install the VSS Package by Using Tools for Windows PowerShell<a name="application-consistent-snapshots-vss-package-powershell"></a>

Use the following procedure to download and install the AwsVssComponents package on your instances by using Run Command from the Tools for Windows PowerShell\. The package installs two components: a VSS requestor and a VSS provider\. The system copies these components to a directory on the instance, and then registers the provider DLL as a VSS provider\.

**To install the VSS package by using AWS Tools for Windows PowerShell**

1. Open AWS Tools for Windows PowerShell and run the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2 or have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Run the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Run the following command to download and install the required VSS components for Systems Manager\.

   ```
   Send-SSMCommand -DocumentName AWS-ConfigureAWSPackage -InstanceId "$instance"-Parameter @{'action'='Install';'name'='AwsVssComponents'}
   ```

## Create VSS\-Enabled EBS Snapshots Using the AWS CLI, Tools for Windows PowerShell, or the AWSEC2\-ManageVssIO SSM Document<a name="application-cosistent-snapshots-cli"></a>

Use one of the following command\-line procedures to create VSS\-enabled EBS snapshots\.

### Creating VSS\-Enabled EBS Snapshots Using the AWS CLI<a name="integration-vss-cli"></a>

Use the following procedure to create VSS\-enabled EBS snapshots by using the AWS CLI\. When you run the command, you can specify the following parameters:
+ Instance \(Required\): Specify one or more Amazon EC2 Windows instances\. You can either manually specify instances, or you can specify tags\.
+ Description \(Optional\): Specify details about this backup\.
+ Tags \(Optional\): Specify key\-value tag pairs that you want to assign to the snapshots\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. You can also add custom tags to this list by using the following format: `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

  This parameter is optional, but we recommended that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.
+ Exclude Boot Volume \(Optional\): Use this parameter to exclude boot volumes from the backup process\.

**To create VSS\-enabled EBS snapshots by using the AWS CLI**

1. Install and configure the AWS CLI, if you have not already\.

   For information, see [Install or Upgrade and then Configure the AWS CLI](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-cli.html) in the *AWS Systems Manager User Guide*\.

1. Run the following command to create VSS\-enabled EBS snapshots\.

   ```
   aws ssm send-command --document-name "AWSEC2-CreateVssSnapshot" --instance-ids "i-12345678" --parameters '{"ExcludeBootVolume":["False"],"description":["Description"],"tags":["Key=key_name,Value=tag_value"]}'
   ```

If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the command output for details about why the execution failed\.

You can automate backups by creating a maintenance window task that uses the `AWSEC2-CreateVssSnapshot` SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html) in the *AWS Systems Manager User Guide*\.

### Creating VSS\-Enabled EBS Snapshots Using AWS Tools for Windows PowerShell<a name="integration-vss-ps"></a>

Use the following procedure to create VSS\-enabled EBS snapshots by using the AWS Tools for Windows PowerShell\. When you run the command, you can specify the following parameters:
+ Instance \(Required\): Specify one or more Amazon EC2 Windows instances\. You can either manually specify instances, or you can specify tags\.
+ Description \(Optional\): Specify details about this backup\.
+ Tags \(Optional\): Specify key\-value tag pairs that you want to assign to the snapshots\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. You can also add custom tags to this list by using the following format: `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

  This parameter is optional, but we recommend that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.
+ Exclude Boot Volume \(Optional\): Use this parameter to exclude boot volumes from the backup process\.

**To create VSS\-enabled EBS snapshots by using AWS Tools for Windows PowerShell**

1. Open AWS Tools for Windows PowerShell and run the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2, or you must have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Execute the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Execute the following command to create VSS\-enabled EBS snapshots\.

   ```
   Send-SSMCommand -DocumentName AWSEC2-CreateVssSnapshot -InstanceId "$instance" -Parameter @{'ExcludeBootVolume'='False';'description'='a_description'
   ;'tags'='Key=key_name,Value=tag_value'}
   ```

If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the command output for details about why the execution failed\. If the command successfully completed, but a specific volume backup failed, you can troubleshoot the failure in the list of EBS snapshots\.

You can automate backups by creating a maintenance window task that uses the `AWSEC2-CreateVssSnapshot` SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html) in the *AWS Systems Manager User Guide*\.

### Creating VSS\-Enabled EBS Snapshots by Using the AWSEC2\-ManageVssIO SSM Document \(Advanced\)<a name="integration-vss-AWSEC2-ManageVssIO"></a>

You can use the following script and the pre\-defined `AWSEC2-ManageVssIO` SSM document to temporarily pause I/O, create VSS\-enabled EBS snapshots, and restart I/O\. This process runs in the context of the user who runs the command\. If the user has sufficient permission to create and tag snapshots, then AWS Systems Manager can create and tag VSS\-enabled EBS snapshots without the need for the additional IAM snapshot role on the instance\.

In contrast, the `AWSEC2-CreateVssSnapshot` document requires that you assign the IAM snapshot role to each instance for which you want to create EBS snapshots\. If you don’t want to provide additional IAM permissions to your instances for policy or compliance reasons, then you can use the following script\.

**Before You Begin**  
Note the following important details about this process:
+ This process uses a PowerShell script \(`CreateVssSnapshotAdvancedScript.ps1`\) to take snapshots of all volumes on the instances you specify, except root volumes\. If you need to take snapshots of root volumes, then you must use the `AWSEC2-CreateVssSnapshot` SSM document\.
+ The script calls the `AWSEC2-ManageVssIO` document twice\. The first time with the `Action` parameter set to `Freeze`, which pauses all I/O on the instances\. The second time, the `Action` parameter is set to `Thaw`, which forces I/O to resume\.
+ Don't attempt to use the `AWSEC2-ManageVssIO` document without using the CreateVssSnapshotAdvancedScript\.ps1 script\. A limitation in VSS requires that the `Freeze` and `Thaw` actions be called no more than ten seconds apart, and manually calling these actions without the script could result in errors\.

**To create VSS\-enabled EBS snapshots by using the AWSEC2\-ManageVssIO SSM document**

1. Open AWS Tools for Windows PowerShell and run the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2 or have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Execute the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Download the [CreateVssSnapshotAdvancedScript\.zip](https://docs.aws.amazon.com/systems-manager/latest/userguide/samples/CreateVssSnapshotAdvancedScript.zip) file and extract the file contents\. 

1. Open `CreateVssSnapshotAdvancedScript.ps1` in a text editor, edit the sample call at the bottom of the script with a valid EC2 instance ID, snapshot description, and desired tag values, and then run the script from PowerShell\.

If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the command output for details about why the execution failed\. If the command was successfully completed, but a specific volume backup failed, you can troubleshoot the failure in the list of EBS volumes\.