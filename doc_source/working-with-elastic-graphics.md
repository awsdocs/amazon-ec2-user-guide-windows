# Working with Elastic Graphics<a name="working-with-elastic-graphics"></a>

You can launch an instance and associate it with an Elastic Graphics accelerator during launch\. You must then manually install the necessary libraries on your instance that enable communication with the accelerator\. For limitations, see [Elastic Graphics limitations](elastic-graphics.md#elastic-graphics-limitations)\.

**Topics**
+ [Configuring your security groups](#elastic-graphics-security)
+ [Launching an instance with an Elastic Graphics accelerator](#attach-elastic-graphics-launch)
+ [Installing the required software for Elastic Graphics](#elastic-graphics-install-libraries)
+ [Verifying Elastic Graphics functionality on your instance](#elastic-graphics-verify-instance)
+ [Viewing Elastic Graphics information](#viewing-elastic-graphics)
+ [Submitting feedback](#elastic-gpu-feedback)

## Configuring your security groups<a name="elastic-graphics-security"></a>

If you use the Amazon EC2 console to launch your instance with an Elastic Graphics accelerator and create a security group for you, the console adds the inbound and outbound rules that are required to allow traffic on the Elastic Graphics port\. If you are launching your instance using the AWS CLI or an SDK, you must ensure that your security group allows traffic on the Elastic Graphics port\.

**To create a security group for Elastic Graphics**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. Provide a name for your security group, such as "Elastic Graphics security group", and a description for the security group\. Select the VPC that you will use to launch your instance with an Elastic Graphics accelerator\.

1. Create an inbound security group rule as follows:

   1. On the **Inbound** tab, choose **Add Rule**

   1. For **Type**, choose **Elastic Graphics**\. For **Source**, choose **Custom** and type the ID of the security group\.

1. Create an outbound security group rule as follows:

   1. On the **Outbound** tab, choose **Add Rule**

   1. For **Type**, choose **All TCP**\. For **Destination**, choose **Custom** and type the ID of the security group\.

1. Choose **Create**\.

For more information, see [Amazon EC2 security groups for Windows instances](ec2-security-groups.md)\.

## Launching an instance with an Elastic Graphics accelerator<a name="attach-elastic-graphics-launch"></a>

You can associate an Elastic Graphics accelerator to an instance during launch\. If the launch fails, the following are possible reasons:
+ Insufficient Elastic Graphics accelerator capacity
+ Exceeded limit on Elastic Graphics accelerators in the Region
+ Not enough private IPv4 addresses in your VPC to create a network interface for the accelerator

For more information, see [Elastic Graphics limitations](elastic-graphics.md#elastic-graphics-limitations)\.

**To associate an Elastic Graphics accelerator during instance launch \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Select a Windows AMI and a supported instance type\. For more information, see [Elastic Graphics basics](elastic-graphics.md#elastic-graphics-basics)\.

1. On the **Configure Instance Details** page, select a VPC and subnet in which to launch your instance\.

1. Choose **Add Graphics Acceleration**, and select an Elastic Graphics accelerator type\.

1. \(Optional\) On the **Add Storage** and **Add Tags** pages, add volumes and tags as needed\.

1. On the **Configure Security Group** page, you can let the console create a security group for you with the required inbound and outbound rules, or you can use the security group that you created manually in [Configuring your security groups](#elastic-graphics-security)\. Add additional security groups as needed\.

1. Choose **Review and Launch** to review your instance options and then choose **Launch**\.

**To associate an Elastic Graphics accelerator during instance launch \(AWS CLI\)**  
You can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command with the following parameter:

```
--elastic-gpu-specification Type=eg1.medium
```

For the `--security-group-ids` parameter, you must include a security group that has the required inbound and outbound rules\. For more information, see [Configuring your security groups](#elastic-graphics-security)\.

**To associate an Elastic Graphics accelerator during instance launch \(Tools for Windows PowerShell\)**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) Tools for Windows PowerShell command\.

## Installing the required software for Elastic Graphics<a name="elastic-graphics-install-libraries"></a>

If you launched your instance using a current AWS Windows AMI, the required software is installed automatically during the first boot\. If you launched your instance using Windows AMIs that do not automatically install the required software, you must install the required software on the instance manually\.

**To install the required software for Elastic Graphics \(if necessary\)**

1. Connect to the instance\.

1. Download the [Elastic Graphics installer](http://ec2-elasticgpus.s3-website.us-east-1.amazonaws.com/latest) and open it\. The installation manager connects to the Elastic Graphics endpoint and downloads the latest version of the required software\.

1. Reboot the instance to verify\.

## Verifying Elastic Graphics functionality on your instance<a name="elastic-graphics-verify-instance"></a>

The Elastic Graphics packages on your instance include tools that you can use to view the status of the accelerator, and to verify that OpenGL commands from your instance to the accelerator are functional\.

If your instance was launched with an AMI that does not have the Elastic Graphics packages pre\-installed, you can download and install them yourself\. For more information, see [Installing the required software for Elastic Graphics](#elastic-graphics-install-libraries)\.

**Topics**
+ [Using the Elastic Graphics status monitor](#elastic-graphics-verify-status)
+ [Using the Elastic Graphics command line tool](#elastic-graphics-command-tools)

### Using the Elastic Graphics status monitor<a name="elastic-graphics-verify-status"></a>

You can use the status monitor tool to view information about the status of an attached Elastic Graphics accelerator\. By default, this tool is available in the notification area of the taskbar in your Windows instance and shows the status of the graphics accelerator\. The following are the possible values\.

**Healthy**  
The Elastic Graphics accelerator is enabled and healthy\.

**Updating**  
The status of the Elastic Graphics accelerator is currently updating\. It might take a few minutes to display the status\.

**Out of service**  
The Elastic Graphics accelerator is out of service\. To get more information about the error, choose **Read More**\.

### Using the Elastic Graphics command line tool<a name="elastic-graphics-command-tools"></a>

You can use the Elastic Graphics command line tool, `egcli.exe`, to check the status of the accelerator\. If there is a problem with the accelerator, the tool returns an error message\.

To launch the tool, open a command prompt from within your instance and run the following command:

```
C:\Program Files\Amazon\EC2ElasticGPUs\manager\egcli.exe
```

The tool also supports the following parameters:

`--json`, `-j`  
Indicates whether to show the JSON message\. The possible values are `true` and `false`\. The default is `true`\.

`--imds`, `-i`  
Indicates whether to check the instance metadata for the availability of the accelerator\. The possible values are `true` and `false`\. The default is `true`\.

The following is example output\. A status of `OK` indicates that the accelerator is enabled and healthy\.

```
EG Infrastructure is available.
Instance ID egpu-f6d94dfa66df4883b284e96db7397ee6
Instance Type eg1.large
EG Version 1.0.0.885 (Manager) / 1.0.0.95 (OpenGL Library) / 1.0.0.69 (OpenGL Redirector)
EG Status: Healthy
JSON Message:
{
  "version": "2016-11-30",
  "status": "OK"
}
```

The following are the possible values for `status`:

`OK`  
The Elastic Graphics accelerator is enabled and healthy\.

`UPDATING`  
The Elastic Graphics driver is being updated\.

`NEEDS_REBOOT`  
The Elastic Graphics driver has been updated and a reboot of the Amazon EC2 instance is required\.

`LOADING_DRIVER`  
The Elastic Graphics driver is being loaded\.

`CONNECTING_EGPU`  
The Elastic Graphics driver is verifying the connectivity with the Elastic Graphics accelerator\.

`ERROR_UPDATE_RETRY`  
An error occurred while updating the Elastic Graphics driver, an update will be retried soon\.

`ERROR_UPDATE`  
An unrecoverable error occurred while updating the Elastic Graphics driver\.

`ERROR_LOAD_DRIVER`  
An error occurred loading the Elastic Graphics driver\.

`ERROR_EGPU_CONNECTIVITY`  
The Elastic Graphics accelerator is unreacheable\.

## Viewing Elastic Graphics information<a name="viewing-elastic-graphics"></a>

You can view information about the Elastic Graphics accelerator attached to your instance\.

**To view information about an Elastic Graphics accelerator \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. On the **Description** tab, find **Elastic Graphics ID**\. Choose the ID to view the following information about the Elastic Graphics accelerator:
   + **Attachment State**
   + **Type**
   + **Health status**

**To view information about an Elastic Graphics accelerator \(AWS CLI\)**  
You can use the [describe\-elastic\-gpus](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-elastic-gpus.html) AWS CLI command:

```
aws ec2 describe-elastic-gpus
```

You can use the [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) AWS CLI command and filter by owner ID to view information about the Elastic Graphics network interface\.

```
aws ec2 describe-network-interfaces --filters "Name=attachment.instance-owner-id,Values=amazon-elasticgpus"
```

**To view information about an Elastic Graphics accelerator \(Tools for Windows PowerShell\)**

Use the following commands:
+ [Get\-EC2ElasticGpu](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ElasticGpu.html)
+ [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html)

**To view information about an Elastic Graphics accelerator using instance metadata**

1. Connect to your Windows instance that is using an Elastic Graphics accelerator\.

1. Do one of the following:
   + From PowerShell, use the following cmdlet:

     ```
     PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
     ```
   + From your web browser, paste the following URL into the address field:

     ```
     http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
     ```

## Submitting feedback<a name="elastic-gpu-feedback"></a>

You can submit feedback about your experience with Elastic Graphics so the team can make further improvements\.

**To submit feedback using the Elastic Graphics Status Monitor**

1. In the notification area of the taskbar in your Windows instance, open the Elastic Graphics Status Monitor\. 

1. In the lower left corner, choose **Feedback**\.

1. Enter your feedback and choose **Submit**\.