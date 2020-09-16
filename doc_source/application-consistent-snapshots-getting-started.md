# Getting Started<a name="application-consistent-snapshots-getting-started"></a>

These instructions describe how to install the VSS components and perform an application\-consistent snapshot of the EBS volumes attached to an EC2 Windows instance\. For more information, see [Getting Started with Amazon EC2 Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html)\.

**Topics**
+ [Create an IAM Role for VSS\-Enabled Snapshots](#run-command-vss-role)
+ [Download and Install VSS Components to the Windows on EC2 Instance](#run-command-vss-package)
+ [Creating a VSS Application\-Consistent Snapshot Using the Console](#application-consistent-snapshots-creating-console)

## Create an IAM Role for VSS\-Enabled Snapshots<a name="run-command-vss-role"></a>

The following procedures describes how to work with IAM policies and IAM roles\. The policy enables Systems Manager to create snapshots, tags snapshots, and attach metadata like a device ID to the default snapshot tags that the system creates\.

**To create an IAM policy for VSS\-enabled snapshots**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

1. On the **Create policy** page, choose the **JSON** tab, and then replace the default content with the following JSON policy\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "ec2:CreateTags",
               "Resource": [
                   "arn:aws:ec2:*::snapshot/*",
                   "arn:aws:ec2:*::image/*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "ec2:DescribeInstances",
                   "ec2:CreateSnapshot",
                   "ec2:CreateImage",
                   "ec2:DescribeImages"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   If you do not intend to set the **CreateAmi** parameter to **True**, then you can omit `arn:aws:ec2:*::image/*` from the first policy statement and you can omit `ec2:CreateImage` and `ec2:DescribeImages` from the second policy statement\. 

   If you intend to always set the **CreateAmi** parameter to **True**, then you can omit `ec2:CreateSnapshot` from the second policy statement\.

1. Choose **Review policy**\.

1. For **Name**, enter a name to identify the policy, such as **VssSnapshotRole** or another name that you prefer\. 

1. \(Optional\) For **Description**, enter a description of the role's purpose\.

1. Choose **Create policy**\.

Use the following procedure to create an IAM role for VSS\-enabled snapshots\. This role includes policies for Amazon EC2 and Systems Manager\.

**To create an IAM role for VSS\-enabled EBS snapshots**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, and then choose **Create role**\.

1. Under **Select type of trusted entity**, choose **AWS Service**\.

1. Immediately under **Choose the service that will use this role**, choose **EC2**, and then choose **Next: Permissions**\.

1. Under **Select your use case**, choose **EC2**, and then choose **Next: Permissions**\.

1. In the list of policies, choose the box next to **AmazonSSMManagedInstanceCore**\. \(Type **SSM** in the search box if you need to narrow the list\.\)

1. Choose **Next: Tags**\.

1. \(Optional\) Add one or more tag key\-value pairs to organize, track, or control access for this role, and then choose **Next: Review**\.

1. For **Role name**, enter a name for the role, such as **VssSnapshotRole** or another name that you prefer\.

1. \(Optional\) For **Role description**, replace the default text with a description of this role's purpose\.

1. Choose **Create role**\. The system returns you to the **Roles** page\.

1. Choose the role that you just created\. The role **Summary page** opens\.

1. Choose **Attach policies**\.

1. Search for and choose the box next to the policy your created in the previous procedure, such as **VssSnapshotRole** or another name that you chose\.

1. Choose **Attach policy**\.

1. Attach this role to the instances for which you want to create VSS\-enabled EBS snapshots\. For more information, see [Attaching an IAM Role to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role) in the *Amazon EC2 User Guide*\.

## Download and Install VSS Components to the Windows on EC2 Instance<a name="run-command-vss-package"></a>

Systems Manager requires VSS components to be installed on your instances\. Use the following procedure to install the components using the `AWSVssComponents` package\. The package installs two components: a VSS requestor and a VSS provider\. We recommend that you install the latest AWS VSS component package to improve reliability and performance of application\-consistent snapshots on your EC2 Windows instances\. To view the latest package version, see the [AWS VSS component package version history](application-consistent-snapshots-details.md)\.

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run command**\.

1. For **Command document**, choose the button next to **AWS\-ConfigureAWSPackage**\.

1. For **Command parameters**, do the following:

   1. Verify that **Action** is set to **Install**\.

   1. For **Name**, enter `AwsVssComponents`\.

   1. For **Version**, leave the field empty so that Systems Manager installs the latest version\.

1. For **Targets**, identify the instances on which you want to run this operation by specifying tags or selecting instances manually\.
**Note**  
If you choose to select instances manually, and an instance you expect to see is not included in the list, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) in the *AWS Systems Manager User Guide* for troubleshooting tips\.

1. For **Other parameters**:
   + \(Optional\) For **Comment**, type information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. \(Optional\) For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of instances on which to run the command at the same time\.
**Note**  
If you selected targets by choosing Amazon EC2 tags, and you are not certain how many instances use the selected tags, then limit the number of instances that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other instances after it fails on either a number or a percentage of instances\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Instances still processing the command might also send errors\.

1. \(Optional\) For **Output options** section, if you want to save the command output to a file, select the box next to **Enable writing to an S3 bucket**\. Specify the bucket and \(optional\) prefix \(folder\) names\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile assigned to the instance, not those of the IAM user performing this task\. For more information, see [Create an IAM Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\. 

1. \(Optional\) Specify options for **SNS notifications**\.

   For information about configuring Amazon SNS notifications for Run Command, see [Configuring Amazon SNS Notifications for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-sns-notifications.html)\.

1. Choose **Run**\.

## Creating a VSS Application\-Consistent Snapshot Using the Console<a name="application-consistent-snapshots-creating-console"></a>

Use the following procedure to create a VSS\-enabled EBS snapshot\.

**To create VSS\-enabled EBS snapshots using the console**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run command**\.

1. For **Command document**, choose `AWSEC2-CreateVssSnapshot` for the **Document name**, then choose `Latest version at runtime` as the **Document version**\.

1. For **Targets**, identify the instances on which you want to run this operation by specifying tags or selecting instances manually\.
**Note**  
If you choose to select instances manually, and an instance you expect to see is not included in the list, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) for troubleshooting tips\.

1. For **Command parameters**, do the following:

   1. Choose an option from the **Exclude Boot Volume** list\. Use this parameter to exclude boot volumes from the backup process\.

   1. \(Optional\) For **Description** field, type a description\. This description is applied to any snapshot created by this process\.

   1. \(Optional\) For **Tags**, type keys and values for tags that you want to apply to any snapshot created by this process\. Tags can help you locate, manage, and restore volumes from a list of snapshots\. By default, the system populates the tag parameter with a `Name` key\. For the value of this key, specify a name that you want to apply to snapshots created by this process\. If you want to specify additional tags, separate tags by using a semicolon\. For example, `Key=Environment,Value=Test`;`Key=User,Value=TestUser1`\.

      We recommended that you tag snapshots\. By default, the systems tags snapshots with the device ID, and `AppConsistent` \(for indicating successful, application\-consistent VSS\-enabled EBS snapshots\)\.

   1. For **Copy Only**, choose **True** to perform a *copy only* backup operation\. This option is set to **False** by default so that the system performs a *full* backup operation\. A full backup operation prevents the system from breaking the differential backup chain in SQL Server when performing a backup\.
**Note**  
This option requires that AWS VSS provider version 1\.2\.00 or later be installed\.

   1. For **No Writers**, choose **True** to exclude application VSS writers from the snapshot process\. This can help you resolve conflicts with third\-party VSS backup components\. This option is set to **False** by default\.
**Note**  
This option requires that AWS VSS provider version 1\.2\.00 or later be installed\.

   1. For **CreateAmi**, choose **True** to create an Amazon Machine Image \(AMI\) backup that is VSS\-enabled, instead of an EBS snapshot\. This option is set to **False** by default\. For more information about creating an AMI, see [Create a Windows AMI from a running instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/how-to-create-windows-ebs-ami.html)\.

   1. \(Optional\) For **AmiName**, specify a name for the created AMI\. This option applies only if the **CreateAmi** option is set to **True**\.

1. For **Other parameters**:
   + For **Comment**, type information about this command\.
   + For **Timeout \(seconds\)**, specify the number of seconds for the system to wait before failing the overall command execution\. 

1. \(Optional\) For **Rate control**:
   + For **Concurrency**, specify either a number or a percentage of instances on which to run the command at the same time\.
**Note**  
If you selected targets by choosing Amazon EC2 tags, and you are not certain how many instances use the selected tags, then limit the number of instances that can run the document at the same time by specifying a percentage\.
   + For **Error threshold**, specify when to stop running the command on other instances after it fails on either a number or a percentage of instances\. For example, if you specify three errors, then Systems Manager stops sending the command when the fourth error is received\. Instances still processing the command might also send errors\.

1. \(Optional\) For **Output options**, to save the command output to a file, select the box next to **Enable writing to an S3 bucket**\. Specify the bucket and \(optional\) prefix \(folder\) names\.
**Note**  
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile assigned to the instance, not those of the IAM user performing this task\. For more information, see [Setting Up Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html)\. 

1. \(Optional\) Specify options for **SNS notifications**\.

   For information about configuring Amazon SNS notifications for Run Command, see [Configuring Amazon SNS Notifications for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-sns-notifications.html) in the *AWS Systems Manager User Guide*\.

1. Choose **Run**\.

   If successful, the command populates the list of EBS snapshots with the new snapshots\. You can locate these snapshots in the list of EBS snapshots by searching for the tags you specified, or by searching for `AppConsistent`\. If the command execution failed, view the Systems Manager command output for details about why the execution failed\. If the command successfully completed, but a specific volume backup failed, you can troubleshoot the failure in the list of EBS volumes\.

   If the command failed and you are using Systems Manager with VPC endpoints, verify that you configured the **com\.amazonaws\.*region*\.ec2** endpoint\. Without the EC2 endpoint defined, the call to enumerate attached EBS volumes fails, which causes the Systems Manager command to fail\. For more information about setting up VPC endpoints with Systems Manager, see [Create a Virtual Private Cloud Endpoint](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html) in the *AWS Systems Manager User Guide*\.
**Note**  
You can automate backups by creating a maintenance window task that uses the `AWSEC2-CreateVssSnapshot` SSM document\. For more information, see [Working with Maintenance Windows \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-maintenance-working.html)\.