# Best Practices for Windows on Amazon EC2<a name="ec2-best-practices"></a>

This list of practices will help you get the best results from running Windows on Amazon EC2\.

**Update Windows Drivers**

Maintain the latest drivers on all Windows EC2 instances to ensure the latest issue fixes and performance enhancements are applied across your fleet\. Depending on your instance type, you should update AWS PV, ENA, and NVMe drivers\.
+ Leverage [Trusted Advisor](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices) to keep Amazon EC2 Windows up to date with AWS\-provided Windows drivers\.
+ Use [SNS topics](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html#drivers-subscribe-notifications) to receive updates for new driver releases\.
+ Use the public [AWSSupport\-UpgradeWindowsAWSDrivers](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awssupport-upgradewindowsawsdrivers.html) SSM document to easily apply the updates across your instances\.

**Launch New Instances with the Latest Windows AMIs**

AWS releases new Windows AMIs each month, which contain the latest OS patches, drivers, and launch agents\. You should leverage the latest AMI when you launch new instances or when you build your own custom images\. 
+ To build with the latest available AMIs, see [ Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store)\.

**Test System/Application Performance Before Migration**

Migrating enterprise applications to AWS can involve many variables and configurations\. Always performance test the EC2 solution to ensure that:
+ Instance types are properly configured, including instance size, enhanced networking, and tenancy \(shared or dedicated\)\.
+ Instance topology is appropriate for the workload and leverages high\-performance features when necessary \(dedicated tenancy, placement groups, instance store volumes, bare metal\)\.

**Update Launch Agents**

Update to the latest EC2Config \(2012 R2 and earlier\) or EC2Launch \(2016 and later\) agents to ensure the latest issue fixes are applied across your fleet\.
+ For EC2Config update instructions, see [ Installing the Latest Version of EC2Config](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html)\. Leverage [ Trusted Advisor](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices) to describe all instances running older versions of EC2Config\. You can use SNS topics to receive updates for new releases\.
+ For EC2Launch update instructions, see [ Installing the Latest Version of EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-download.html)\.

**Security and Network**
+ Manage access to AWS resources and APIs using identity federation, IAM users, and IAM roles\. Establish credential management policies and procedures for creating, distributing, rotating, and revoking AWS access credentials\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html) in the *IAM User Guide*\.
+ Implement the least permissive rules for your security group\. For more information, see [Security Group Rules](using-network-security.md#security-group-rules)\.
+ Regularly patch, update, and secure the operating system and applications on your instance\. For more information about updating Amazon Linux 2 or the Amazon Linux AMI, see [Managing Software on Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about updating your Windows instance, see [Updating Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#update-windows-instance)\.

**Storage**
+ Use separate Amazon EBS volumes for the operating system versus your data\. Ensure that the volume with your data persists after instance termination\. For more information, see [Preserving Amazon EBS Volumes on Instance Termination](terminating-instances.md#preserving-volumes-on-termination)\.
+ Use the instance store available for your instance to store temporary data\. Remember that the data stored in instance store is deleted when you stop or terminate your instance\. If you use instance store for database storage, ensure that you have a cluster with a replication factor that ensures fault tolerance\.

**Resource Management**
+ Use instance metadata and custom resource tags to track and identify your AWS resources\. For more information, see [Instance Metadata and User Data](ec2-instance-metadata.md) and [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.
+ View your current limits for Amazon EC2\. Plan to request any limit increases in advance of the time that you'll need them\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.

**Backup and Recovery**
+ Regularly back up your EBS volumes using [Amazon EBS snapshots](EBSSnapshots.md), and create an [Amazon Machine Image \(AMI\)](AMIs.md) from your instance to save the configuration as a template for launching future instances\.
+ Deploy critical components of your application across multiple Availability Zones, and replicate your data appropriately\.
+ Design your applications to handle dynamic IP addressing when your instance restarts\. For more information, see [Amazon EC2 Instance IP Addressing](using-instance-addressing.md)\.
+ Monitor and respond to events\. For more information, see [Monitoring Amazon EC2](monitoring_ec2.md)\.
+ Ensure that you are prepared to handle failover\. For a basic solution, you can manually attach a network interface or Elastic IP address to a replacement instance\. For more information, see [Elastic Network Interfaces](using-eni.md)\. For an automated solution, you can use Amazon EC2 Auto Scaling\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/)\.
+ Regularly test the process of recovering your instances and Amazon EBS volumes if they fail\.