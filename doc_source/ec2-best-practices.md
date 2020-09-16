# Best practices for Windows on Amazon EC2<a name="ec2-best-practices"></a>

This list of practices will help you get the best results from running Windows on Amazon EC2\.

**Update Windows drivers**

Maintain the latest drivers on all Windows EC2 instances to ensure the latest issue fixes and performance enhancements are applied across your fleet\. Depending on your instance type, you should update AWS PV, ENA, and NVMe drivers\.
+ Leverage [Trusted Advisor](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices) to keep Amazon EC2 Windows up to date with AWS\-provided Windows drivers\.
+ Use [SNS topics](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html#drivers-subscribe-notifications) to receive updates for new driver releases\.
+ Use the AWS Systems Manager SSM document [AWSSupport\-UpgradeWindowsAWSDrivers](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awssupport-upgradewindowsawsdrivers.html) to easily apply the updates across your instances\.

**Launch new instances with the latest Windows AMIs**

AWS releases new [Windows AMIs](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#windows-ami-versions) each month, which contain the latest OS patches, drivers, and launch agents\. You should leverage the latest AMI when you launch new instances or when you build your own custom images\. 
+ To build with the latest available AMIs, see [ Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store)\.

**Test system/application performance before migration**

Migrating enterprise applications to AWS can involve many variables and configurations\. Always performance test the EC2 solution to ensure that:
+ Instance types are properly configured, including instance size, enhanced networking, and tenancy \(shared or dedicated\)\.
+ Instance topology is appropriate for the workload and leverages high\-performance features when necessary \(dedicated tenancy, placement groups, instance store volumes, bare metal\)\.

**Update launch agents**

Update to the latest EC2Launch v2 \(Windows Server 2008 and later\) agent to ensure that the latest issue fixes are applied across your fleet\. To update, see the instructions at [Install the latest version ofEC2Launch v2](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-v2-install.html)\.

If you want to continue to use the EC2Config \(Windows Server 2012 R2 and earlier\) or EC2Launch \(Windows Server 2016 and later\) agents, ensure that the latest issue fixes are applied across your fleet\.
+ For EC2Config update instructions, see [ Installing the Latest Version of EC2Config](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html)\. 
+ For EC2Launch update instructions, see [ Installing the Latest Version of EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-download.html)\.

**Security**

When securing Windows instances, we recommend that you implement Active Directory Domain Services to enable a scalable, secure, and manageable infrastructure for distributed locations\. Additionally, after launching instances through the AWS Console or using an Amazon EC2 provisioning tool, such as AWS CloudFormation, it is good practice to utilize native OS features, such as [Microsoft Windows PowerShell DSC](https://docs.microsoft.com/en-us/powershell/scripting/dsc/getting-started/wingettingstarted?view=powershell-6) to maintain configuration state in the event that configuration drift occurs\.

Windows instances in AWS should adhere to the following high\-level best practices:
+ **Least Access:** Grant access only to systems and locations that are trusted and expected\. This applies to all Microsoft products such as Active Directory, Microsoft business productivity servers, and infrastructure services such as Remote Desktop Services, reverse proxy servers, IIS web servers, etc\. Use AWS capabilities such as Amazon EC2 instance security groups, network access control lists \(ACLs\), and Amazon VPC public/private subnets to layer security across multiple locations in an architecture\. Within a Windows instance, customers can use Windows Firewall to further layer a defense\-in\-depth strategy within their deployment\. Install only the OS components and applications that are necessary for the system to function as designed\. Configure infrastructure services such as IIS to run under service accounts or to use features such as application pool identities to access resources locally and remotely across your infrastructure\.
+ **Least Privilege:** Determine the minimum set of privileges that instances and accounts need in order to perform their functions\. Restrict these servers and users to only allow these defined permissions\. Use techniques such as Role Based Access Controls to reduce the surface area of administrative accounts and create the most limited roles to accomplish a task\. Use OS features such as Encrypting File System \(EFS\) within NTFS to encrypt sensitive data at rest and control application and user access to it\.
+ **Configuration Management:** Create a baseline server configuration that incorporates up\-to\-date security patches and host\-based protection suites that include anti\-virus, anti\-malware, intrusion detection/prevention, and file integrity monitoring\. Assess each server against the current recorded baseline to identify and flag any deviations\. Ensure each server is configured to generate and securely store appropriate log and audit data\. For more information about updating your Windows instance, see [Updating Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#update-windows-instance)\.
+ **Change Management:** Create processes to control changes to server configuration baselines and work toward fully automated change processes\. Also, leverage Just Enough Administration \(JEA\) with Windows PowerShell DSC to limit administrative access to the minimum required functions\.
+ **Audit Logs:** Audit access and all changes to Amazon EC2 instances to verify server integrity and ensure only authorized changes are made\. Leverage features such as [Enhanced Logging for IIS](https://docs.microsoft.com/en-us/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85) to enhance default logging capabilities\. AWS capabilities such as VPC Flow Logs and AWS CloudTrail are also available to audit network access, including allowed/denied requests and API calls, respectively\.

**Storage**
+ Use separate Amazon EBS volumes for the operating system versus your data\. Ensure that the volume with your data persists after instance termination\. For more information, see [Preserving Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.
+ Use the instance store available for your instance to store temporary data\. Remember that the data stored in instance store is deleted when you stop or terminate your instance\. If you use instance store for database storage, ensure that you have a cluster with a replication factor that ensures fault tolerance\.
+ Encrypt EBS volumes and snapshots\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

**Resource management**
+ Use instance metadata and custom resource tags to track and identify your AWS resources\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md) and [Tagging your Amazon EC2 resources](Using_Tags.md)\.
+ View your current limits for Amazon EC2\. Plan to request any limit increases in advance of the time that you'll need them\. For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md)\.

**Backup and recovery**
+ Regularly back up your EBS volumes using [Amazon EBS snapshots](EBSSnapshots.md), and create an [Amazon Machine Image \(AMI\)](AMIs.md) from your instance to save the configuration as a template for launching future instances\.
+ Deploy critical components of your application across multiple Availability Zones, and replicate your data appropriately\.
+ Design your applications to handle dynamic IP addressing when your instance restarts\. For more information, see [Amazon EC2 instance IP addressing](using-instance-addressing.md)\.
+ Monitor and respond to events\. For more information, see [Monitoring Amazon EC2](monitoring_ec2.md)\.
+ Ensure that you are prepared to handle failover\. For a basic solution, you can manually attach a network interface or Elastic IP address to a replacement instance\. For more information, see [Elastic network interfaces](using-eni.md)\. For an automated solution, you can use Amazon EC2 Auto Scaling\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/)\.
+ Regularly test the process of recovering your instances and Amazon EBS volumes if they fail\.

**Networking**
+ Set the time\-to\-live \(TTL\) value for your applications to 255, for IPv4 and IPv6\. If you use a smaller value, there is a risk that the TTL will expire while application traffic is in transit, causing reachability issues for your instances\.