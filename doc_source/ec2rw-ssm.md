# Using EC2Rescue for Windows Server with Systems Manager Run Command<a name="ec2rw-ssm"></a>

AWS Support provides you with a Systems Manager Run Command document to interface with your Systems Manager\-enabled instance to run EC2Rescue for Windows Server\. The Run Command document is called `AWSSupport-RunEC2RescueForWindowsTool`\.

This Systems Manager Run Command document performs the following tasks:
+ Downloads and verifies EC2Rescue for Windows Server\.
+ Imports a PowerShell module to ease your interaction with the tool\.
+ Runs EC2RescueCmd with the provided command and parameters\.

The Systems Manager Run Command document accepts three parameters:
+ **Command**—The EC2Rescue for Windows Server action\. The current allowed values are:
  + **ResetAccess**—Resets the local Administrator password\. The local Administrator password of the current instance will be reset and the randomly generated password will be securely stored in Parameter Store as `/EC2Rescue/Password/<INSTANCE_ID>`\. If you select this action and provide no parameters, passwords are encrypted automatically with the default KMS key\. Optionally, you can specify a KMS Key ID in Parameters to encrypt the password with your own key\.
  + **CollectLogs**—Runs EC2Rescue for Windows Server with the `/collect:all` action\. If you select this action, `Parameters` must include an Amazon S3 bucket name to upload the logs to\.
  + **FixAll**—Runs EC2Rescue for Windows Server with the `/rescue:all` action\. If you select this action, `Parameters` must include the block device name to rescue\.
+ **Parameters**—The PowerShell parameters to pass for the specified command\.

**Note**  
In order for the **ResetAccess** action to work, your Amazon EC2 instance needs to have the following policy attached in order to write the encrypted password to Parameter Store\. Please wait a few minutes before attempting to reset the password of an instance after you have attached this policy to the related IAM role\.  
Using the default KMS key:  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:PutParameter"
      ],
      "Resource": [
        "arn:aws:ssm:region:account_id:parameter/EC2Rescue/Passwords/<instanceid>"
      ]
    }
  ]
}
```
Using a custom KMS key:  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:PutParameter"
      ],
      "Resource": [
        "arn:aws:ssm:region:account_id:parameter/EC2Rescue/Passwords/<instanceid>"
        ] 
      }, 
      { 
      "Effect": "Allow",
      "Action": [
      "kms:Encrypt"
      ],
      "Resource": [
        "arn:aws:kms:region:account_id:key/<kmskeyid>"
      ]
    }
  ]
}
```

The following procedure describes how to view the JSON for this document in the Amazon EC2 console\.

**To view the JSON for the Systems Manager Run Command document**

1. Open the Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/home](https://console.aws.amazon.com/systems-manager/home)\.

1. In the navigation pane, expand **Shared Services** and choose **Documents**\.

1. In the search bar, set **Owner** as **Owned by Me or Amazon** and set the **Document name prefix** to `AWSSupport-RunEC2RescueForWindowsTool`\.

1. Select the `AWSSupport-RunEC2RescueForWindowsTool` document, choose **Contents**, and then view the JSON\.

## Examples<a name="ec2rw-ssm-examples"></a>

Here are some examples on how to use the Systems Manager Run Command document to execute EC2Rescue for Windows Server, using the AWS CLI\. For more information about sending commands with the AWS CLI, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/ssm/send-command.html)\.

### Attempt to fix all identified issues on an offline root volume<a name="ec2rw-ssm-exam1"></a>

Attempt to fix all identified issues on an offline root volume attached to an Amazon EC2 Windows instance:

```
aws ssm send-command --instance-ids "i-0cb2b964d3e14fd9f" --document-name "AWSSupport-RunEC2RescueForWindowsTool" --comment "EC2Rescue offline volume xvdf" --parameters "Command=FixAll, Parameters='xvdf'" --output text
```

### Collect logs from the current Amazon EC2 Windows instance<a name="ec2rw-ssm-exam2"></a>

Collect all logs from the current online Amazon EC2 Windows instance and upload them to an Amazon S3 bucket:

```
aws ssm send-command --instance-ids "i-0cb2b964d3e14fd9f" --document-name "AWSSupport-RunEC2RescueForWindowsTool" --comment "EC2Rescue online log collection to S3" --parameters "Command=CollectLogs, Parameters='YOURS3BUCKETNAME'" --output text
```

### Collect logs from an offline Amazon EC2 Windows instance volume<a name="ec2rw-ssm-exam3"></a>

Collect all logs from an offline volume attached to an Amazon EC2 Windows instance and upload them to Amazon S3 with a presigned URL: 

```
aws ssm send-command --instance-ids "i-0cb2b964d3e14fd9f" --document-name "AWSSupport-RunEC2RescueForWindowsTool" --comment "EC2Rescue offline log collection to S3" --parameters "Command=CollectLogs, Parameters=\"-Offline -BlockDeviceName xvdf -S3PreSignedUrl 'YOURS3PRESIGNEDURL'\"" --output text
```

### Reset the local Administrator password<a name="ec2rw-ssm-exam4"></a>

The following examples show methods you can use to reset the local Administrator password\. The output provides a link to Parameter Store, where you can find the randomly generated secure password you can then use to RDP to your Amazon EC2 Windows instance as the local Administrator\.

Reset the local Administrator password of an online instance using the default KMS key alias/aws/ssm:

```
aws ssm send-command --instance-ids "i-0cb2b964d3e14fd9f" --document-name "AWSSupport-RunEC2RescueForWindowsTool" --comment "EC2Rescue online password reset" --parameters "Command=ResetAccess" --output text
```

Reset the local Administrator password of an online instance using a KMS key:

```
aws ssm send-command --instance-ids "i-0cb2b964d3e14fd9f" --document-name "AWSSupport-RunEC2RescueForWindowsTool" --comment "EC2Rescue online password reset" --parameters "Command=ResetAccess, Parameters=a133dc3c-a2g4-4fc6-a873-6c0720104bf0" --output text
```

**Note**  
In this example, the KMS key is `a133dc3c-a2g4-4fc6-a873-6c0720104bf0`\.