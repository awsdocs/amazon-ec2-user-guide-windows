# Before You Begin<a name="run-command-vss-prereqs"></a>

Before you create VSS\-enabled EBS snapshots by using Run Command, review the following requirements and limitations, and complete the required tasks\. 

**Amazon EC2 Windows instance requirements**  
VSS\-enabled EBS snapshots are supported for instances running Windows Server 2008 R2 or later\. \(Windows Server 2008 R2 Core is currently not supported\.\) Verify that your instances meet all requirements for Amazon EC2 Windows\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html)\.

**SSM Agent version**  
Update your instances to use SSM Agent version 2\.2\.58\.0 or later\. If you are using an older version of SSM Agent, you can update it by using Run Command\. For more information, see [Update SSM Agent by using Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/rc-console.html#rc-console-agentexample)\.

**AWS Tools for Windows PowerShell version**  
Ensure that your instance is running version 3\.3\.48\.0 or later of the AWS Tools for Windows PowerShell\. To check your version number, run the following command on the instance:

```
Get-AWSPowerShellVersion
```

If you need to update the version of Tools for Windows PowerShell on your instance, see [Setting up the AWS Tools for Windows PowerShell on a Windows\-based Computer](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up-windows.html) in the *AWS Tools for Windows PowerShell User Guide*\.

**Topics**
+ [Create an IAM Role for VSS\-Enabled Snapshots](#run-command-vss-role)
+ [Download and Install VSS Components for Systems Manager](#run-command-vss-package)

## Create an IAM Role for VSS\-Enabled Snapshots<a name="run-command-vss-role"></a>

This section includes a procedure for creating an IAM policy, and a separate procedure for creating an IAM role that uses the policy you create in the first procedure\. The policy enables Systems Manager to create snapshots, tags snapshots, and attach metadata like a device ID to the default snapshot tags the system creates\. 

**To create an IAM policy for VSS\-enable snapshots**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

1. On the **Create policy** page, choose the **JSON** tab, and then copy and past the following JSON policy into the text box\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "ec2:CreateTags",
               "Resource": "arn:aws:ec2:*::snapshot/*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "ec2:DescribeInstances",
                   "ec2:CreateSnapshot"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. On the **Review Policy** page, type a name in the **Policy Name** field, and then choose **Create Policy**\. The system returns you to the IAM console\.

Use the following procedure to create an IAM role for VSS\-enabled snapshots\. This role includes policies for Amazon EC2 and Systems Manager\.

**To create an IAM role for VSS\-enabled EBS snapshots**

1. In the navigation pane, choose **Roles**, and then choose **Create role**\.

1. On the **Select type of trusted entity** page, under **AWS Service**, choose **EC2**\.

1. In the **Select your use case** section, choose **EC2**, and then choose **Next: Permissions**\.

1. On the **Attach permissions policy** page, choose the policy you just created, and then choose **Next: Review**\.

1. On the **Review** page, type a name in the **Role name** box, and then type a description\.

1. Choose **Create role**\. The system returns you to the **Roles** page\.

1. Choose the role you just created\. The role **Summary page opens**\.

1. Choose **Attach policy**\.

1. Search for and choose the **AmazonEC2RoleforSSM**\.

1. Choose **Attach policy**\.

1. Attach this role to the instances for which you want to create VSS\-enabled EBS snapshots\. For more information, see [Attaching an IAM Role to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role) in the *Amazon EC2 User Guide*\.

## Download and Install VSS Components for Systems Manager<a name="run-command-vss-package"></a>

Systems Manager requires VSS components to be installed on your instances\. This sections contains three methods for installing the VSS components: Using Systems Manager to automatically download and install new versions of this package when they become available, Run Command, and the AWS\-ConfigureAWSPackage SSM document to install the package on your instances\.

**Topics**
+ [Install the VSS Package Using the Systems Manager Console](#run-command-vss-package-console)
+ [Install the VSS Package by Using the AWS CLI](#integration-vss-package-cli)
+ [Install the VSS Package by Using Tools for Windows PowerShell](#integration-vss-package-ps)

### Install the VSS Package Using the Systems Manager Console<a name="run-command-vss-package-console"></a>

Use the following procedure to configure a State Manager association that automatically downloads and installs the components by using the AwsVssComponents package\. The State Manager association will automatically download and install new versions of the package when they are published by AWS\. The package installs two components: a VSS requestor and a VSS provider\.

**To download and install VSS components for Systems Manager**

1. Open the AWS Systems Manager Console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a Command**\.

1. In the **Command document** list, choose **AWS\-ConfigureAWSPackage**\.

1. For **Command parameters**: 

   1. Verify that **Action** is set to **Install**\.

   1. For **Name**, type AwsVssComponents\.

   1. For **Version**, leave the field empty so that Systems Manager installs the latest version\.

1. In the **Targets** section, identify the instances on which you want to run this operation by specifying tags or selecting instances manually\.
**Note**  
If you choose to select instances manually, and an instance you expect to see is not included in the list, see [Where Are My Instances?](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#where-are-instances) in the *AWS Systems Manager User Guide* for troubleshooting tips\.

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
The S3 permissions that grant the ability to write the data to an S3 bucket are those of the instance profile assigned to the instance, not those of the IAM user performing this task\. For more information, see [Create an IAM Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html) in the *AWS Systems Manager User Guide*\. 

1. For more information about configuring Amazon SNS notifications for Run Command, see [Configuring Amazon SNS Notifications for AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-sns-notifications.html)\.

1. Choose **Run**\.

### Install the VSS Package by Using the AWS CLI<a name="integration-vss-package-cli"></a>

Use the following procedure to download and install the AwsVssComponents package on your instances by using Run Command from the AWS CLI\. The package installs two components: a VSS requestor and a VSS provider\. The system copies these components to a directory on the instance, and then registers the provider DLL as a VSS provider\.

**To install the VSS package by using the AWS CLI**

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

1. Execute the following command to download and install the required VSS components for Systems Manager\.

   ```
   aws ssm send-command ()document-name "AWS-ConfigureAWSPackage" -instance-ids "i-12345678" -parameters '{"action":["Install"],"name":["AwsVssComponents"]}'
   ```

### Install the VSS Package by Using Tools for Windows PowerShell<a name="integration-vss-package-ps"></a>

Use the following procedure to download and install the AwsVssComponents package on your instances by using Run Command from the Tools for Windows PowerShell\. The package installs two components: a VSS requestor and a VSS provider\. The system copies these components to a directory on the instance, and then registers the provider DLL as a VSS provider\.

**To install the VSS package by using AWS Tools for Windows PowerShell**

1. Open AWS Tools for Windows PowerShell and execute the following command to specify your credentials\. You must either have administrator privileges in Amazon EC2, or you must have been granted the appropriate permission in IAM\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

   ```
   Set-AWSCredentials –AccessKey key_name –SecretKey key_name
   ```

1. Execute the following command to set the Region for your PowerShell session\. The example uses the us\-east\-2 Region\.

   ```
   Set-DefaultAWSRegion -Region us-east-2
   ```

1. Execute the following command to download and install the required VSS components for Systems Manager\.

   ```
   Send-SSMCommand -DocumentName AWS-ConfigureAWSPackage -InstanceId "$instance"-Parameter @{'action'='Install';'name'='AwsVssComponents'}
   ```
