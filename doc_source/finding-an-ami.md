# Finding a Windows AMI<a name="finding-an-ami"></a>

Before you can launch an instance, you must select an AMI to use\. As you select an AMI, consider the following requirements you might have for the instances that you'll launch:

+ The region

+ The operating system

+ The architecture: 32\-bit \(`i386`\) or 64\-bit \(`x86_64`\)

+ The provider \(for example, Amazon Web Services\)

+ Additional software \(for example, SQL server\)

If you need to find a Linux AMI, see [Finding a Linux AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Linux Instances*\.


+ [Finding a Windows AMI Using the Amazon EC2 Console](#finding-an-ami-console)
+ [Finding an AMI Using the AWS Tools for Windows PowerShell](#finding-an-ami-psh)
+ [Finding an AMI Using the AWS CLI](#finding-an-ami-aws-cli)

## Finding a Windows AMI Using the Amazon EC2 Console<a name="finding-an-ami-console"></a>

You can find Windows AMIs using the Amazon EC2 console\. You can search through all available AMIs using the **Images** page, or select from commonly used AMIs on the **Quick Start** tab when you use the console to launch an instance\. AMI IDs are unique to each region\.

**To find a Windows AMI using the Choose AMI page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region in which to launch your instances\. You can select any region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch Instance**\.

1. On **Quick Start** tab, select from one of the commonly used AMIs in the list\. If you don't see the AMI that you need, select the **AWS Marketplace** or **Community AMIs** tab to find additional AMIs\.

**To find a Windows AMI using the Images page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region in which to launch your instances\. You can select any region that's available to you, regardless of your location\.

1. In the navigation pane, choose **AMIs**\.

1. \(Optional\) Use the **Filter** options to scope the list of displayed AMIs to see only the AMIs that interest you\. For example, to list all Windows AMIs provided by AWS, select **Public images**\. Choose the Search bar and select **Owner** from the menu, then select **Amazon images**\. Choose the Search bar again to select **Platform** and then the operating system from the list provided\.

1. \(Optional\) Choose the **Show/Hide Columns** icon to select which image attributes to display, such as the root device type\. Alternatively, you can select an AMI from the list and view its properties in the **Details** tab\.

1. To launch an instance from this AMI, select it and then choose **Launch**\. For more information about launching an instance using the console, see [Launching Your Instance from an AMI](launching-instance.md#choose-an-instance-type-page)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

## Finding an AMI Using the AWS Tools for Windows PowerShell<a name="finding-an-ami-psh"></a>

You can use cmdlets for Amazon EC2 or AWS Systems Manager to list only the Windows AMIs that meet your needs\. After locating an AMI that meets your needs, make note of its ID so that you can use it to launch instances\. For more information, see [Launch an Instance Using Windows PowerShell](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-launch.html) in the *AWS Tools for Windows PowerShell User Guide*\.

**Amazon EC2**  
For information and examples, see [Find an AMI Using Windows PowerShell](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html) in the *AWS Tools for Windows PowerShell User Guide*\.

**Systems Manager Parameter Store**  
For information and examples, see [Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store/)\.

## Finding an AMI Using the AWS CLI<a name="finding-an-ami-aws-cli"></a>

You can use AWS CLI commands for Amazon EC2 or AWS Systems Manager to list only the Windows AMIs that meet your needs\. After locating an AMI that meets your needs, make note of its ID so that you can use it to launch instances\. For more information, see [Launching an Instance Using the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

**Amazon EC2**  
The [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command supports filtering parameters\. For example, use the `--owners` parameter to public AMIs owned by Amazon\.

```
aws ec2 describe-images --owners self amazon
```

You can add the following filter to the previous command to display only Windows AMIs:

```
--filters "Name=platform,Values=windows"
```

**Systems Manager Parameter Store**  
For information and examples, see [Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store/)\.