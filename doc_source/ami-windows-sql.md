# SQL Server AMIs provided by AWS<a name="ami-windows-sql"></a>

For SQL Server license\-included AMIs, use the installation and setup media included in `C:\SQLServerSetup` to make changes to the default installation, add new features, or install additional named instances\.

You can find SQL Server\-included AMIs using the Amazon EC2 console, the AWS Tools for PowerShell, and the AWS CLI\. As you select a SQL Server\-included AMI, consider the following requirements you might have for the instances that you'll launch:
+ The Region
+ The operating system
+ The architecture: 32\-bit \(`i386`\), 64\-bit \(`x86_64`\), or 64\-bit ARM \(`arm64`\)
+ The root device type: Amazon EBS or instance store
+ The provider \(for example, Amazon Web Services\)
+ Additional software \(for example, SQL Server\)

**Topics**
+ [Amazon EC2 console](#finding-a-sql-ami-console)
+ [AWS Tools for Windows PowerShell](#finding-a-sql-ami-ps)
+ [AWS CLI](#finding-a-sql-ami-aws-cli)
+ [AMI versions](#sql-ami-version-history)

## Find a SQL Server\-included AMI using the Amazon EC2 console<a name="finding-a-sql-ami-console"></a>

You can find SQL Server\-included AMIs using the Amazon EC2 console\. You can select from the list of AMIs when you use the launch instance wizard to launch an instance, or you can search through all available AMIs using the **Images** page\. AMI IDs are unique to each AWS Region\.

**To find a SQL Server\-included AMI using the launch instance wizard**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instances**\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, enter `SQL` in the search bar and choose **Enter**\. You will be taken to the **AMIs** page, where you can browse and choose from AMIs with SQL Server included\. You can choose from AMIs under the **Quickstart AMIs**, **My AMIs**, **AWS Marketplace AMIs**, and the **Community AMIs** tabs\. You can filter by cost, operating system, and architecture\.

1. To launch an instance from this AMI, select it and then choose **Launch instance**\. For more information about launching an instance using the console, see [Launch an instance using the new launch instance wizard](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-launch-instance-wizard.html)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

**To find a SQL Server\-included AMI using the AMIs page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **AMIs**\.

1. Enter `SQL` in the search bar and choose **Enter**\. You can choose from SQL Server\-included AMIs under the **Quickstart AMIs**, **My AMIs**, **AWS Marketplace AMIs**, and the **Community AMIs** tabs\. You can filter by cost, operating system, and architecture\.

1. To launch an instance from this AMI, select it and then choose **Launch instance **\. For more information about launching an instance using the console, see [Launch an instance using the new launch instance wizard](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-launch-instance-wizard.html)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

## Find a SQL Server\-included AMI using the AWS Tools for Windows PowerShell<a name="finding-a-sql-ami-ps"></a>

You can use cmdlets for Amazon EC2 to list only the Windows AMIs that meet your requirements\. After locating an AMI that meets your requirements, take note of its ID so that you can use it to launch instances\. For more information, see [Launch an Instance Using Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-launch.html) in the *AWS Tools for Windows PowerShell User Guide*\.

You can use the `Get-EC2ImageByName` cmdlt to list SQL Server\-included AMIs\.

```
Get-EC2ImageByName -Names *SQL* 
```

For more information and examples, see [Find an AMI Using Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html) in the *AWS Tools for Windows PowerShell User Guide*\.

## Find a SQL Server\-included AMI using the AWS CLI<a name="finding-a-sql-ami-aws-cli"></a>

You can use AWS CLI commands for Amazon EC2 to list only the SQL Server\-included AMIs that meet your requirements\. After you locate an AMI that meets your requirements, take note of its ID so that you can use it to launch instances\. For more information, see [Launching an Instance Using the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

**Amazon EC2**  
The [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command supports filtering parameters\. For example, use the `--owners` parameter to display public AMIs owned by Amazon\.

```
aws ec2 describe-images --owners self amazon
```

You can add the following filter to the previous command to display only SQL Server\-included AMIs:

```
--filters "Name=name,Values=*SQL*"
```

**Important**  
Omitting the `--owners` flag from the describe\-images command will return all images for which you have launch permissions, regardless of ownership\.

## SQL Server\-included AMI version history<a name="sql-ami-version-history"></a>

To view changes to each release of the AWS Windows AMIs, including SQL Server updates, see the [AWS Windows AMI version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) in the *Amazon EC2 User Guide*\.