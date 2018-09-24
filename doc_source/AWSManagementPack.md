# AWS Management Pack for Microsoft System Center<a name="AWSManagementPack"></a>

Amazon Web Services \(AWS\) offers a complete set of infrastructure and application services for running almost anything in the cloud—from enterprise applications and big data projects to social games and mobile apps\. The AWS Management Pack for Microsoft System Center provides availability and performance monitoring capabilities for your applications running in AWS\.

The AWS Management Pack allows Microsoft System Center Operations Manager to access your AWS resources \(such as instances and volumes\), so that it can collect performance data and monitor your AWS resources\. The AWS Management Pack is an extension to System Center Operations Manager\. There are two versions of the AWS Management Pack: one for System Center 2012 — Operations Manager and another for System Center Operations Manager 2007 R2\.

The AWS Management Pack uses Amazon CloudWatch metrics and alarms to monitor your AWS resources\. Amazon CloudWatch metrics appear in Microsoft System Center as performance counters and Amazon CloudWatch alarms appear as alerts\.

You can monitor the following resources:
+ EC2 instances
+ EBS volumes
+ ELB load balancers
+ Amazon EC2 Auto Scaling groups and Availability Zones
+ Elastic Beanstalk applications
+ CloudFormation stacks
+ CloudWatch Alarms
+ CloudWatch Custom Metrics

**Topics**
+ [Overview of AWS Management Pack for System Center 2012](#overview-2012)
+ [Overview of AWS Management Pack for System Center 2007 R2](#overview-2007)
+ [Downloading the AWS Management Pack](DownloadAWSmp.md)
+ [Deploying the AWS Management Pack](DeployingAWSmp.md)
+ [Using the AWS Management Pack](UsingAWSmp.md)
+ [Upgrading the AWS Management Pack](upgrading-awsmp.md)
+ [Uninstalling the AWS Management Pack](uninstalling-awsmp.md)
+ [Troubleshooting the AWS Management Pack](TroubleshootingAWSmp.md)

## Overview of AWS Management Pack for System Center 2012<a name="overview-2012"></a>

The AWS Management Pack for System Center 2012 — Operations Manager uses a resource pool that contains one or more management servers to discover and monitor your AWS resources\. You can add management servers to the pool as you increase the number of AWS resources that you use\.

The following diagram shows the main components of AWS Management Pack\.

![\[AWS Management Pack monitoring components\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AWSMPcomponents.png)


| Item | Component | Description | 
| --- | --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/1.png)  |  Operations Manager infrastructure  |  One or more management servers and their dependencies, such as Microsoft SQL Server and a Microsoft Active Directory domain\. These servers can either be deployed on\-premises or in the AWS cloud; both scenarios are supported\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/2.png)  |  Resource pool  |  One or more management servers used for communicating with AWS using the AWS SDK for \.NET\. These servers must have Internet connectivity\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/3.png)  |  AWS credentials  |  An access key ID and a secret access key used by the management servers to make AWS API calls\. You must specify these credentials while you configure the AWS Management Pack\. We recommend that you create an IAM user with read\-only privileges and use those credentials\. For more information about creating an IAM user, see [Adding a New User to Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html) in the *IAM User Guide*\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/4.png)  |  EC2 instances  |  Virtual computers running in the AWS cloud\. Some instances might have the Operations Manager Agent installed, others might not\. When you install Operations Manager Agent you can see the operating system and application health apart from the instance health\.  | 

## Overview of AWS Management Pack for System Center 2007 R2<a name="overview-2007"></a>

The AWS Management Pack for System Center Operations Manager 2007 R2 uses a designated computer that connects to your System Center environment and has Internet access, called a *watcher node*, to call AWS APIs to remotely discover and collect information about your AWS resources\.

The following diagram shows the main components of AWS Management Pack\.

![\[AWS Management Pack monitoring components\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AWSMPcomponents2007.png)


| Item | Component | Description | 
| --- | --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/1.png)  |  Operations Manager infrastructure  |  One or more management servers and their dependencies, such as Microsoft SQL Server and a Microsoft Active Directory domain\. These servers can either be deployed on\-premises or in the AWS cloud; both scenarios are supported\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/2.png)  |  Watcher node  |  A designated agent\-managed computer used for communicating with AWS using the AWS SDK for \.NET\. It can either be deployed on\-premises or in the AWS cloud, but it must be an agent\-managed computer, and it must have Internet connectivity\. You can use exactly one watcher node to monitor an AWS account\. However, one watcher node can monitor multiple AWS accounts\. For more information about setting up a watcher node, see [Deploying Windows Agents](http://technet.microsoft.com/en-us/library/cc950516.aspx) in the Microsoft System Center documentation\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/3.png)  |  AWS credentials  |  An access key ID and a secret access key used by the watcher node to make AWS API calls\. You must specify these credentials while you configure the AWS Management Pack\. We recommend that you create an IAM user with read\-only privileges and use those credentials\. For more information about creating an IAM user, see [Adding a New User to Your AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html) in the *IAM User Guide*\.  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/callouts/4.png)  |  EC2 instances  |  Virtual computers running in the AWS cloud\. Some instances might have the Operations Manager Agent installed, others might not\. When you install the Operations Manager Agent you can see the operating system and application health apart from the instance health\.  | 