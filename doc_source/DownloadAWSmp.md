# Downloading the AWS Management Pack<a name="DownloadAWSmp"></a>

To get started, download the AWS Management Pack\. The AWS Management Pack is free\. You might incur charges for Amazon CloudWatch, depending on how you configure monitoring or how many AWS resources you monitor\.

## System Center 2012<a name="download-2012"></a>

Before you download the AWS Management Pack, ensure that your systems meet the following system requirements and prerequisites\.

**System Requirements**
+ System Center Operations Manager 2012 R2 or System Center Operations Manager 2012 SP1
+ Cumulative Update 1 or later\. You must deploy the update to the management servers monitoring AWS resources, as well as agents running the watcher nodes and agents to be monitored by the AWS Management Pack\. We recommend that you deploy the latest available Operations Manager updates on all computers monitoring AWS resources\.
+ Microsoft\.Unix\.Library MP version 7\.3\.2026\.0 or later

**Prerequisites**
+ Your data center must have at least one management server configured with Internet connectivity\. The management servers must have the Microsoft \.NET Framework version 4\.5 or later and PowerShell 2\.0 or later installed\.
+ The action account for the management server must have local administrator privileges on the management server\.

**To download the AWS Management Pack**

1. On the [AWS Add\-Ins for Microsoft System Center](https://aws.amazon.com/windows/system-center/) website, click **SCOM 2012**\.

1. Save `AWS-SCOM-MP-2.5.zip` to your computer and unzip it\.

Continue with [Deploying the AWS Management Pack](DeployingAWSmp.md)\.

## System Center 2007 R2<a name="download-2007"></a>

Before you download the AWS Management Pack, ensure that your systems meet the following system requirements and prerequisites\.

**System Requirements**
+ System Center Operations Manager 2007 R2
+ Microsoft\.Unix\.Library MP version 6\.1\.7000\.256 or later

**Prerequisites**
+ Your data center must have an agent\-managed computer with Internet connectivity that you designate as the watcher node\. The watcher node must have the following Agent Proxy option enabled: **Allow this agent to act as a proxy and discover managed objects on other computers**\. The watcher node must have the Microsoft \.NET Framework version 3\.5\.1 or later and PowerShell 2\.0 or later installed\.
+ The action account for the watcher node must have local administrator privileges on the watcher node\.
+ You must ensure that your watcher node has the agent installed, has Internet access, and can communicate with the management servers in your data center\. For more information, see [Deploying Windows Agents](http://technet.microsoft.com/en-us/library/cc950516.aspx) in the Microsoft System Center documentation\.

**To download the AWS Management Pack**

1. On the [AWS Add\-Ins for Microsoft System Center](https://aws.amazon.com/windows/system-center/) website, click **SCOM 2007**\.

1. Save `AWS-MP-Setup-2.5.msi` to your computer\.

Continue with [Deploying the AWS Management Pack](DeployingAWSmp.md)\.