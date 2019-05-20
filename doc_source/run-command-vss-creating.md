# Creating VSS\-Enabled EBS Snapshots<a name="run-command-vss-creating"></a>

This section includes procedures for creating VSS\-enabled EBS snapshots by using the Amazon EC2 console, the AWS CLI, or AWS Tools for Windows PowerShell\. It also contains an advanced method for creating VSS\-enabled snapshots using the AWSEC2\-ManageVssIO SSM document\.

**Topics**
+ [Creating VSS\-Enabled EBS Snapshots Using the Console](#run-command-vss-creating-console)
+ [Creating VSS\-Enabled EBS Snapshots Using the AWS CLI](#integration-vss-cli)
+ [Creating VSS\-enabled EBS Snapshots Using AWS Tools for Windows PowerShell](#integration-vss-ps)
+ [Creating VSS\-Enabled EBS Snapshots by Using the AWSEC2\-ManageVssIO SSM Document \(Advanced\)](#integration-vss-AWSEC2-ManageVssIO)

## Creating VSS\-Enabled EBS Snapshots Using the Console<a name="run-command-vss-creating-console"></a>

Use the following procedure to create a VSS\-enabled EBS snapshot by using the AWS Systems Manager Console\.

**To create VSS\-enabled EBS snapshots by using the console**

1. Open the AWS Systems Manager Console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a Command**\.

1. In the **Command document** list, choose **AWSEC2\-CreateVssSnapshot**\.

1. In the **Targets** section, identify the instances on which you want to run this operation by specifying tags or selecting instances manually\.
**Note**  
If you choose to select instances manually, and an instance you expect to see is not included in the list, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) for troubleshooting tips\.

1. In the **Command parameters** section:

   1. Choose an option from the **Exclude Boot Volume** list\. Use this parameter to exclude boot volumes from the backup process\.

   1. In the **Description** field, type a description\. This description is applied to any snapshot created by this process \(optional, but recommended\)\.

   1. In the **Tags** field, type keys and values for tags that you want to apply to any snapshot created by this process\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. If you want to specify additional tags, separate tags by using a semicolon\. For example, `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

      This parameter is optional, but we recommended that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.

   1. Set the **Copy Only** option to **True** to perform a *copy only* backup operation\. This option is set to **False** by default so that the system performs a *full* backup operation\. A full backup operation prevents the system from breaking the differential backup chain in SQL Server when performing a backup\.
**Note**  
This option requires that AWS VSS provider version 1\.2\.00 or later be installed\.

   1. Set the **No Writers** to **True** to exclude application VSS writers from the snapshot process\. This may help you to resolve conflicts with third\-party VSS backup components\. This option is set to **False** by default\.
**Note**  
This option requires that AWS VSS provider version 1\.2\.00 or later be installed\.

1. For **Other parameters**:
   + For **Comment**, type information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. \(Optional\) For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of instances on which to run the command at the same time\.
**Note**  
If you selected targets by choosing Amazon EC2 tags, and you are not certain how many instances use the selected tags, then limit the number of instances that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other instances after it fails on either a number or a percentage of instances\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Instances still processing the command might also send errors\.

1. In the **Output options** section, if you want to save the command output to a file, select the **Write command output to an Amazon S3 bucket**\. Type the bucket and prefix \(folder\) names in the boxes\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile assigned to the instance, not those of the IAM user performing this task\. For more information, see [Configuring Access to Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-access.html)\. 

1. For more information about configuring Amazon SNS notifications for Run Command, see [Configuring Amazon SNS Notifications for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-sns-notifications.html)\.

1. Choose **Run**\.

   If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the Systems Manager command output for details about why the execution failed\. If the command successfully completed, but a specific volume backup failed, you can troubleshoot the failure in the list of EBS volumes\.

   If the command failed and you are using Systems Manager with VPC endpoints, verify that you configured the **com\.amazonaws\.*region*\.ec2** endpoint\. Without the EC2 endpoint defined, the call to enumerate attached EBS volumes fails, which causes the Systems Manager command to fail\. For more information about setting up VPC endpoints with Systems Manager, see [Create a Virtual Private Cloud Endpoint](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html) in the *AWS Systems Manager User Guide*\.
**Note**  
You can automate backups by creating a Maintenance Windows task that uses the AWSEC2\-CreateVssSnapshot SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html)\.

## Creating VSS\-Enabled EBS Snapshots Using the AWS CLI<a name="integration-vss-cli"></a>

Use the following procedure to create VSS\-enabled EBS snapshots by using the AWS CLI\. When you execute the command, you can specify the following parameters:
+ Instance \(Required\): Specify one or more Amazon EC2 Windows instances\. You can either manually specify instances, or you can specify tags\.
+ Description \(Optional\): Specify details about this backup\.
+ Tags \(Optional\): Specify key\-value tag pairs that you want to assign to the snapshots\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. You can also add custom tags to this list by using the following format: `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

  This parameter is optional, but we recommended that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.
+ Exclude Boot Volume \(Optional\): Use this parameter to exclude boot volumes from the backup process\.

**To create VSS\-enabled EBS snapshots by using the AWS CLI**

1. Open the AWS CLI and run the following command to specify your credentials and a Region\. You must either have administrator privileges in Amazon EC2 or you must have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\. 

   ```
   aws configure
   ```

   The system prompts you to specify the following\.

   ```
   AWS Access Key ID [None]: key_name
   AWS Secret Access Key [None]: key_name
   Default region name [None]: region
   Default output format [None]: ENTER
   ```

1. Execute the following command to create VSS\-enabled EBS snapshots\.

   ```
   aws ssm send-command -document-name "AWSEC2-CreateVssSnapshot" -instance-ids "i-12345678" -parameters '{"ExcludeBootVolume":["False"],"description":["Description"],"tags":["Key=key_name,Value=tag_value"]}'
   ```

If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the command output for details about why the execution failed\.

You can automate backups by creating a maintenance window task that uses the AWSEC2\-CreateVssSnapshot SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html)\.

## Creating VSS\-enabled EBS Snapshots Using AWS Tools for Windows PowerShell<a name="integration-vss-ps"></a>

Use the following procedure to create VSS\-enabled EBS snapshots by using the AWS Tools for Windows PowerShell\. When you execute the command, you can specify the following parameters:
+ Instance \(Required\): Specify one or more Amazon EC2 Windows instances\. You can either manually specify instances, or you can specify tags\.
+ Description \(Optional\): Specify details about this backup\.
+ Tags \(Optional\): Specify key\-value tag pairs that you want to assign to the snapshots\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. You can also add custom tags to this list by using the following format: `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

  This parameter is optional, but we recommended that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.
+ Exclude Boot Volume \(Optional\): Use this parameter to exclude boot volumes from the backup process\.

**To create VSS\-enabled EBS snapshots by using AWS Tools for Windows PowerShell**

1. Open AWS Tools for Windows PowerShell and execute the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2, or you must have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

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

You can automate backups by creating a maintenance window task that uses the AWSEC2\-CreateVssSnapshot SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html)\.

## Creating VSS\-Enabled EBS Snapshots by Using the AWSEC2\-ManageVssIO SSM Document \(Advanced\)<a name="integration-vss-AWSEC2-ManageVssIO"></a>

You can use the following script and the pre\-defined AWSEC2\-ManageVssIO SSM document to temporarily pause I/O, create VSS\-enabled EBS snapshots, and restart I/O\. This process runs in the context of the user who executes the command\. If the user has sufficient permission to create and tag snapshots, then AWS Systems Manager can create and tag VSS\-enabled EBS snapshots without the need for the additional IAM snapshot role on the instance\.

In contrast, the AWSEC2\-CreateVssSnapshot document requires that you assign the IAM snapshot role to each instance for which you want to create EBS snapshots\. If you don’t want to provide additional IAM permissions to your instances for policy or compliance reasons, then you can use the following script\.

**Before You Begin**  
Note the following important details about this process:
+ This process uses a PowerShell script \(CreateVssSnapshotAdvancedScript\.ps1\) to take snapshots of all volumes on the instances you specify, except root volumes\. If you need to take snapshots of root volumes, then you must use the AWSEC2\-CreateVssSnapshot SSM document\.
+ The script calls the AWSEC2\-ManageVssIO document twice\. The first time with the `Action` parameter set to `Freeze`, which pauses all I/O on the instances\. The second time, the `Action` parameter is set to `Thaw`, which forces I/O to resume\.
+ Don't attempt to use the AWSEC2\-ManageVssIO document without using the CreateVssSnapshotAdvancedScript\.ps1 script\. A limitation in VSS requires that the `Freeze` and `Thaw` actions be called no more than ten seconds apart, and manually calling these actions without the script could result in errors\.

**To create VSS\-enabled EBS snapshots by using the AWSEC2\-ManageVssIO SSM document**

1. Open AWS Tools for Windows PowerShell and execute the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2, or you must have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Execute the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Download the [CreateVssSnapshotAdvancedScript\.zip](samples/CreateVssSnapshotAdvancedScript.zip) file and extract the file contents\. 

1. Open the script in a simple text editor, edit the sample call at the bottom of the script, and then run it\.

   ```
   ```

If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the command output for details about why the execution failed\. If the command was successfully completed, but a specific volume backup failed, you can troubleshoot the failure in the list of EBS volumes\.
