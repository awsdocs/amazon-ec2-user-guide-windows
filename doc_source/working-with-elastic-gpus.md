# Working with Elastic GPUs<a name="working-with-elastic-gpus"></a>

You can launch an instance and associate it with an elastic GPU during launch\. You must then manually install the necessary libraries on your instance that enable communication with the elastic GPU\.


+ [Configuring Your Security Groups](#elastic-gpus-security)
+ [Launching an Instance with an Elastic GPU](#attach-elastic-gpus-launch)
+ [Installing and Updating the Elastic GPU Packages](#elastic-gpus-install-libraries)
+ [Verifying Elastic GPU Functionality on Your Instance](#elastic-gpus-verify-instance)
+ [Viewing Elastic GPU Information](#viewing-elastic-gpus)
+ [Submitting Feedback](#elastic-gpu-feedback)

## Configuring Your Security Groups<a name="elastic-gpus-security"></a>

You should create a new security group for the elastic GPU network interface\. To ensure communication between your instance and the elastic GPU, the security group rules must include a rule that allows both inbound and outbound TCP traffic over port 2007 from your instance's network interface, or from the security group associated with your instance\.

**To create the security group for your elastic GPU network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. For **Security group name** type a value such as `Elastic GPUs Security Group`\. For **Description**, type a value\. For **VPC ID**, enter a VPC with which to associate the security group and then choose **Create**\.

1. Select the security group that you just created and choose **Actions**, **Edit inbound rules**\.

1. Create the ingress security group rule:

   1. For **Type**, choose **Custom TCP Rule**\. For **Protocol**, choose **TCP**\. For **Port Range**, enter `2007`\. For **Source**, choose **Custom** and enter the ID of the security group that you created in the previous step\.

   1. Choose **Save**\.

1. Select the security group that you just created and choose **Actions**, **Edit outbound rules**\.

1. Create the egress security group rule:

   1. For **Type**, choose **Custom TCP Rule**\. For **Protocol**, choose **TCP**\. For **Port Range**, enter `0-65535`\. For **Destination**, choose **Custom** and enter the ID of the security group you created in the previous step\.

   1. Choose **Save**\.

For more information about security groups, see [Amazon EC2 Security Groups for Windows Instances](using-network-security.md)\.

## Launching an Instance with an Elastic GPU<a name="attach-elastic-gpus-launch"></a>

You can associate an elastic GPU to an instance during launch\. If the launch fails, it may be for one of the following reasons:

+ Insufficient elastic GPU capacity

+ Exceeded limit on elastic GPUs in the region

+ Not enough private IPv4 addresses in your VPC to create a network interface for the elastic GPU

For more information, see [Elastic GPU Limitations](elastic-gpus.md#elastic-gpus-limitations)\.

**To associate an elastic GPU during instance launch using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Select an AMI and an instance type\. For more information about supported AMIs and instance types, see [Elastic GPU Basics](elastic-gpus.md#elastic-gpus-basics)\.

1. On the **Configure Instance Details** page, select a VPC and subnet in which to launch your instance\.

1. Choose **Add GPU**, and select a GPU type\.

1. On the **Add Storage** and **Add Tags** pages, choose the options that meet your instance needs\.

1. On the **Configure Security Group** page, choose **Select an existing security group** and select the security group that you created in the [Configuring Your Security Groups](#elastic-gpus-security) step\. Add additional security groups as needed to meet your needs\.

1. Choose **Review and Launch** to review your instance options and then choose **Launch** to complete the instance creation process\.

**To associate an elastic GPU during instance launch using the command line**  
You can use the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command\. It may be necessary to update to the latest version of the AWS CLI to use this feature\.

```
aws ec2 run-instances --elastic-gpu-specification Type=eg1.medium --region us-east-1 --image-id ami-1a2b3c4d --subnet subnet-11223344 --instance-type r4.large --key-name keypair_name --security-group-ids sg-1234
```

Alternatively, you can use the [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) Tools for Windows PowerShell command\.

## Installing and Updating the Elastic GPU Packages<a name="elastic-gpus-install-libraries"></a>

Your instance requires Amazon\-optimized OpenGL libraries and Elastic GPU agents in order to use the elastic GPU\.

**Installing the Elastic GPU packages on an instance with Elastic GPU enabled**

1. Launch an instance with the desired elastic GPU type\.

1. After connecting to the instance, download the [Elastic GPU driver installer](http://ec2-elasticgpus.s3-website-us-east-1.amazonaws.com/latest) and open it up to install it\. The installation manager connects to the Elastic GPU endpoint and downloads the latest version of all required components\.

1. Reboot the instance to verify\.

## Verifying Elastic GPU Functionality on Your Instance<a name="elastic-gpus-verify-instance"></a>

The Elastic GPU packages on your instance include tools that you can use to view the status of the elastic GPU, and to verify that OpenGL commands from your instance to the elastic GPU are functional\.

If your instance was launched with an AMI that does not have the Elastic GPU packages pre\-installed, you can download and install them yourself\. For more information, see [Installing and Updating the Elastic GPU Packages](#elastic-gpus-install-libraries)\.


+ [Using the Elastic GPU Status Monitor](#elastic-gpus-verify-status)
+ [Using the Elastic GPU Command Line Tool](#elastic-gpus-command-tools)

### Using the Elastic GPU Status Monitor<a name="elastic-gpus-verify-status"></a>

You can use the status monitor tool to view information about the status of an attached elastic GPU\. By default, this tool is available in the notification area of the taskbar in your Windows instance and shows the status of the elastic GPU as healthy, updating, or out of service\.

**Healthy**  
The elastic GPU is enabled and healthy\. 

**Updating**  
The status of the elastic GPU is currently updating, and may take a few minutes to display\.

**Out of service**  
The elastic GPU is out of service\. Choose **Read More** to get more information about the error\.

### Using the Elastic GPU Command Line Tool<a name="elastic-gpus-command-tools"></a>

The Elastic GPU command line tool can be used to check the status of the elastic GPU\. If there is a problem with the elastic GPU functionality, it returns an error message\.

To launch the tool, open a command prompt from within your instance and enter the following:

```
C:\Program Files\Amazon\EC2ElasticGPUs\manager\egcli.exe
```

If the elastic GPU is available and functioning normally, you receive the following output:

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

Otherwise, the tool returns an error and a message explaining the reason for the error\.

The following parameters are supported:


| Parameter | Type | Description | Default value | 
| --- | --- | --- | --- | 
| json j | Boolean | If enabled, shows the JSON message that accompanies the status\. | true | 
| imds i | Boolean | If enabled, checks the instance metadata to verify if the elastic GPU is available\. | true | 

To use these parameters, you can use the following syntax:

\[`-`|`--`|`/`\]\[*argument*\]\[`=`|`:`| \]\[*value*\]

For example, the following command disables the JSON message output:

```
EG-CLI.exe --json false
```

## Viewing Elastic GPU Information<a name="viewing-elastic-gpus"></a>

You can view information about the elastic GPU attached to your instance, including its ID and state\. 

**To view information about an elastic GPU using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. In the details pane, you can view information about the elastic GPU by looking at the **Elastic GPU**, **Elastic GPU type**, and **Elastic GPU status** fields\.

**To view information about an elastic GPU using the command line**  
You can use the [describe\-elastic\-gpus](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-elastic-gpus.html) AWS CLI command:

```
aws ec2 describe-elastic-gpus
```

You can use the [describe\-network\-interfaces](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) AWS CLI command and filter by owner ID to view information about the elastic GPU network interface\.

```
aws ec2 describe-network-interfaces --filters "Name=attachment.instance-owner-id,Values=amazon-elastic-graphics"
```

Alternatively, you can use the following Tools for Windows PowerShell commands:

+ [Get\-EC2ElasticGpu](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ElasticGpu.html)

+ [Get\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html)

**To view information about an elastic GPU using instance metadata**  
You can view information about an elastic GPU from within the instance by accessing the instance metadata\.

**To view information about an elastic GPU using PowerShell**

1. Access your Windows instance that is using an elastic GPU\.

1. From PowerShell, use this command to query the metadata of your elastic GPU:

   ```
   PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/meta-data/elastic-gpus/associations/elastic-gpu-id
   ```

   For example:

   ```
   PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
   ```

**To view information about an elastic GPU from a browser**

1. Access your Windows instance that is using an elastic GPU\.

1. From your browser, navigate to this URL to query the metadata of your elastic GPU:

   ```
   http://169.254.169.254/latest/meta-data/elastic-gpus/associations/elastic-gpu-id
   ```

   For example:

   ```
   http://169.254.169.254/latest/meta-data/elastic-gpus/associations/egpu-f6d94dfa66df4883b284e96db7397ee6
   ```

## Submitting Feedback<a name="elastic-gpu-feedback"></a>

You can submit feedback about your experience with Elastic GPUs so the team can make further improvements by using the following steps\.

**To submit feedback using the Elastic GPU Status Monitor**

1. Open the Elastic GPU Statue Monitor\. This tool is available in the notification area of the taskbar in your Windows instance\.

1. Choose **Feedback** in the lower left corner\.

1. Enter your feedback and choose **Submit**\.