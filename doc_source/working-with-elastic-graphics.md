# Working with Elastic Graphics<a name="working-with-elastic-graphics"></a>

You can launch an instance and associate it with an Elastic Graphics accelerator during launch\. You must then manually install the necessary libraries on your instance that enable communication with the accelerator\.

**Topics**
+ [Configuring Your Security Groups](#elastic-graphics-security)
+ [Launching an Instance with an Elastic Graphics accelerator](#attach-elastic-graphics-launch)
+ [Installing and Updating the Elastic Graphics Packages](#elastic-graphics-install-libraries)
+ [Verifying Elastic Graphics Functionality on Your Instance](#elastic-graphics-verify-instance)
+ [Viewing Elastic Graphics Information](#viewing-elastic-graphics)
+ [Submitting Feedback](#elastic-gpu-feedback)

## Configuring Your Security Groups<a name="elastic-graphics-security"></a>

Create a new security group for the Elastic Graphics network interface\. To ensure communication between your instance and the accelerator, the security group rules must include a rule that allows both inbound and outbound TCP traffic over port 2007 from your instance's network interface, or from the security group associated with your instance\.

**To create the security group for your Elastic Graphics network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. For **Security group name** type a value such as `Elastic Graphics Security Group`\. For **Description**, type a value\. For **VPC ID**, enter a VPC with which to associate the security group and then choose **Create**\.

1. Select the security group that you just created and choose **Actions**, **Edit inbound rules**\.

1. Create the ingress security group rule:

   1. For **Type**, choose **Custom TCP Rule**\. For **Protocol**, choose **TCP**\. For **Port Range**, enter `2007`\. For **Source**, choose **Custom** and enter the ID of the security group that you created in the previous step\.

   1. Choose **Save**\.

1. Select the security group that you just created and choose **Actions**, **Edit outbound rules**\.

1. Create the egress security group rule:

   1. For **Type**, choose **Custom TCP Rule**\. For **Protocol**, choose **TCP**\. For **Port Range**, enter `0-65535`\. For **Destination**, choose **Custom** and enter the ID of the security group that you created in the previous step\.

   1. Choose **Save**\.

For more information about security groups, see [Amazon EC2 Security Groups for Windows Instances](using-network-security.md)\.

## Launching an Instance with an Elastic Graphics accelerator<a name="attach-elastic-graphics-launch"></a>

You can associate an Elastic Graphics accelerator to an instance during launch\. If the launch fails, it may be for one of the following reasons:
+ Insufficient Elastic Graphics accelerator capacity
+ Exceeded limit on Elastic Graphics accelerators in the Region
+ Not enough private IPv4 addresses in your VPC to create a network interface for the accelerator

For more information, see [Elastic Graphics Limitations](elastic-graphics.md#elastic-graphics-limitations)\.

**To associate an Elastic Graphics accelerator during instance launch \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Select an AMI and an instance type\. For more information about supported AMIs and instance types, see [Elastic Graphics Basics](elastic-graphics.md#elastic-graphics-basics)\.

1. On the **Configure Instance Details** page, select a VPC and subnet in which to launch your instance\.

1. Choose **Add Graphics Acceleration**, and select an Elastic Graphics accelerator type\.

1. On the **Add Storage** and **Add Tags** pages, choose the options that meet your instance needs\.

1. On the **Configure Security Group** page, choose **Select an existing security group** and select the security group that you created in the [Configuring Your Security Groups](#elastic-graphics-security) step\. Add additional security groups as needed to meet your needs\.

1. Choose **Review and Launch** to review your instance options and then choose **Launch**\.

**To associate an Elastic Graphics accelerator during instance launch \(AWS CLI\)**  
You can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command\. It may be necessary to update to the latest version of the AWS CLI to use this feature\.

```
aws ec2 run-instances --elastic-gpu-specification Type=eg1.medium --region us-east-1 --image-id ami-1a2b3c4d --subnet subnet-11223344 --instance-type r4.large --key-name keypair_name --security-group-ids sg-1234
```

Alternatively, you can use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) Tools for Windows PowerShell command\.

## Installing and Updating the Elastic Graphics Packages<a name="elastic-graphics-install-libraries"></a>

To use the Elastic Graphics accelerator, your instance requires Amazon\-optimized OpenGL libraries and the Elastic Graphics agents\.

**Installing the Elastic Graphics packages on an instance with Elastic Graphics enabled**

1. Launch an instance with the desired Elastic Graphics accelerator type\.

1. After connecting to the instance, download the [Elastic Graphics driver installer](http://ec2-elasticgpus.s3-website-us-east-1.amazonaws.com/latest) and open it up to install it\. The installation manager connects to the Elastic Graphics endpoint and downloads the latest version of all required components\.

1. Reboot the instance to verify\.

## Verifying Elastic Graphics Functionality on Your Instance<a name="elastic-graphics-verify-instance"></a>

The Elastic Graphics packages on your instance include tools that you can use to view the status of the accelerator, and to verify that OpenGL commands from your instance to the accelerator are functional\.

If your instance was launched with an AMI that does not have the Elastic Graphics packages pre\-installed, you can download and install them yourself\. For more information, see [Installing and Updating the Elastic Graphics Packages](#elastic-graphics-install-libraries)\.

**Topics**
+ [Using the Elastic Graphics Status Monitor](#elastic-graphics-verify-status)
+ [Using the Elastic Graphics Command Line Tool](#elastic-graphics-command-tools)

### Using the Elastic Graphics Status Monitor<a name="elastic-graphics-verify-status"></a>

You can use the status monitor tool to view information about the status of an attached Elastic Graphics accelerator\. By default, this tool is available in the notification area of the taskbar in your Windows instance and shows the status of the graphics accelerator as healthy, updating, or out of service\.

**Healthy**  
The Elastic Graphics accelerator is enabled and healthy\. 

**Updating**  
The status of the Elastic Graphics accelerator is currently updating, and may take a few minutes to display\.

**Out of service**  
The Elastic Graphics accelerator is out of service\. To get more information about the error, choose **Read More**\. 

### Using the Elastic Graphics Command Line Tool<a name="elastic-graphics-command-tools"></a>

The Elastic Graphics command line tool can be used to check the status of the accelerator\. If there is a problem with the accelerator functionality, it returns an error message\.

To launch the tool, open a command prompt from within your instance and enter the following:

```
C:\Program Files\Amazon\EC2ElasticGPUs\manager\egcli.exe
```

If the accelerator is available and functioning normally, you receive the following output:

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

Otherwise, the tool returns an error and a message explaining the reason for the error\. The following are the possible status values that can be returned:

OK  
The Elastic Graphics accelerator is enabled and healthy\.

UPDATING  
The Elastic Graphics driver is being updated\.

NEEDS\_REBOOT  
The Elastic Graphics driver has been updated and a reboot of the Amazon EC2 instance is required\.

LOADING\_DRIVER  
The Elastic Graphics driver is being loaded\.

CONNECTING\_EGPU  
The Elastic Graphics driver is verifying the connectivity with the Elastic Graphics accelerator\.

ERROR\_UPDATE\_RETRY  
An error occurred while updating the Elastic Graphics driver, an update will be retried soon\.

ERROR\_UPDATE  
An unrecoverable error occurred while updating the Elastic Graphics driver\.

ERROR\_LOAD\_DRIVER  
An error occurred loading the Elastic Graphics driver\.

ERROR\_EGPU\_CONNECTIVITY  
The Elastic Graphics accelerator is unreacheable\.

The following parameters are supported:


| Parameter | Type | Description | Default value | 
| --- | --- | --- | --- | 
| json j | Boolean | If enabled, shows the JSON message that accompanies the status\. | true | 
| imds i | Boolean | If enabled, checks the instance metadata to verify if the accelerator is available\. | true | 

To use these parameters, you can use the following syntax:

\[`-`\|`--`\|`/`\]\[*argument*\]\[`=`\|`:`\| \]\[*value*\]

For example, the following command disables the JSON message output:

```
EG-CLI.exe --json false
```

## Viewing Elastic Graphics Information<a name="viewing-elastic-graphics"></a>

You can view information about the Elastic Graphics accelerator attached to your instance, including its ID and state\. 

**To view information about an Elastic Graphics accelerator \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. In the details pane, you can view information about the Elastic Graphics Accelerator by looking at the following fields:
   + **Elastic Graphics**
   + **Elastic Graphics type**
   + **Elastic Graphics status**

**To view information about an Elastic Graphics accelerator \(AWS CLI\)**  
You can use the [describe\-elastic\-gpus](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-elastic-gpus.html) AWS CLI command:

```
aws ec2 describe-elastic-gpus
```

You can use the [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) AWS CLI command and filter by owner ID to view information about the Elastic Graphics network interface\.

```
aws ec2 describe-network-interfaces --filters "Name=attachment.instance-owner-id,Values=amazon-elastic-graphics"
```

Alternatively, you can use the following Tools for Windows PowerShell commands:
+ [Get\-EC2ElasticGpu](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ElasticGpu.html)
+ [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html)

**To view information about an Elastic Graphics accelerator using instance metadata**  
You can query the metadata associated with an Elastic Graphics accelerator from within the instance using the following steps\.

**To view information about an Elastic Graphics accelerator using PowerShell**

1. Access your Windows instance that is using an Elastic Graphics Accelerator\.

1. From PowerShell, use this command to query the metadata of your accelerator:

   ```
   PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/meta-data/elastic-gpus/associations/elastic-gpu-id
   ```

   For example:

   ```
   PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
   ```

**To view information about an Elastic Graphics accelerator from a browser**

1. Access your Windows instance that is using an Elastic Graphics Accelerator\.

1. From your browser, navigate to this URL to query the metadata of your accelerator:

   ```
   http://169.254.169.254/latest/meta-data/elastic-gpus/associations/elastic-gpu-id
   ```

   For example:

   ```
   http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
   ```

## Submitting Feedback<a name="elastic-gpu-feedback"></a>

You can submit feedback about your experience with Elastic Graphics so the team can make further improvements\.

**To submit feedback using the Elastic Graphics Status Monitor**

1. In the notification area of the taskbar in your Windows instance, open the Elastic Graphics Status Monitor\. 

1. In the lower left corner, choose **Feedback**\.

1. Enter your feedback and choose **Submit**\.