# Windows to Linux replatforming assistant for Microsoft SQL Server Databases<a name="replatform-sql-server"></a>

The Windows to Linux replatforming assistant for Microsoft SQL Server Databases service is a scripting tool\. It helps you move existing Microsoft SQL Server workloads from a Windows to a Linux operating system\. You can use the replatforming assistant with any Windows Server virtual machines \(VMs\) hosted in the cloud, or with on\-premises environments running Microsoft SQL Server 2008 and later\. The tool checks for common incompatibilities, exports databases from the Windows VM, and imports into an EC2 instance running Microsoft SQL Server 2017 on Ubuntu 16\.04\. The automated process results in a ready\-to\-use Linux VM configured with your selected SQL Server databases that can be used for experimenting and testing\.

**Topics**
+ [Concepts](#replatform-sql-server-concepts)
+ [Related services](#replatform-sql-server-related-services)
+ [How Windows to Linux replatforming assistant for Microsoft SQL Server works](#replatform-sql-server-how-it-works)
+ [Components](#replatform-sql-server-components)
+ [Setting up](replatform-sql-server-setting-up.md)
+ [Getting started](replatform-sql-server-getting-started.md)

## Concepts<a name="replatform-sql-server-concepts"></a>

The following terminology and concepts are central to your understanding and use of the Windows to Linux replatforming assistant for Microsoft SQL Server Databases\.

 **Backup** 

A Microsoft SQL Server backup copies data or log records from a Microsoft SQL Server database or its transaction log to a backup device, such as a disk\. For more information, see [Backup Overview \(Microsoft SQL Server\)](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017)\.

 **Restore** 

A logical and meaningful sequence for restoring a set of Microsoft SQL Server backups\. For more information, see [Restore and Recovery Overview \(Microsoft SQL Server\)](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-2017)\. 

 **Replatform** 

A Microsoft SQL Server database can be replatformed from an EC2 Windows instance to an EC2 Linux instance running Microsoft SQL Server\. It can also be replatformed to the VMware Cloud running Microsoft SQL Server Linux on AWS\.

## Related services<a name="replatform-sql-server-related-services"></a>

[AWS Systems Manager \(Systems Manager\)](https://aws.amazon.com/systems-manager) gives you visibility and control of your infrastructure on AWS\. The Windows to Linux replatforming assistant for Microsoft SQL Server Databases uses Systems Manager to move your Microsoft SQL databases to Microsoft SQL Server on EC2 Linux\. For more information about Systems Manager, see the *[AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)*\.

## How Windows to Linux replatforming assistant for Microsoft SQL Server works<a name="replatform-sql-server-how-it-works"></a>

Windows to Linux replatforming assistant for Microsoft SQL Server Databases allows you to migrate your Microsoft SQL Server databases from an on\-premises environment or from an EC2 Windows instance to Microsoft SQL Server 2017 on EC2 Linux using backup and restore\. For the destination EC2 Linux instance, you provide either the EC2 instance ID or the EC2 instance type with the subnet ID and EC2 Key Pair\.

When you execute the PowerShell script for the Windows to Linux replatforming assistant for Microsoft SQL Server Databases on the source Microsoft SQL Server databases, the Windows instance backs up the databases to an encrypted [Amazon Simple Storage Service \(S3\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html) storage bucket\. It then restores the backups to an existing Microsoft SQL Server on EC2 Linux instance, or it launches a new Microsoft SQL Server on EC2 Linux instance and restores the backups to the newly created instance\. This process can be used to replatform your 2\-tier databases running enterprise applications\. It also enables you to replicate your database to Microsoft SQL Server on Linux to test the application while the source Microsoft SQL Server remains online\. After testing, you can schedule application downtime and rerun the PowerShell backup script during your final cutover\. 

The entire replatforming process can also be automated and run unattended\. You can run the Systems Manager SSM document [AWSEC2\-SQLServerDBRestore](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-sqlserverdbrestore.html) to import your existing database backup files into Microsoft SQL Server on EC2 Linux without using the PowerShell backup script\.

## Components<a name="replatform-sql-server-components"></a>

The Windows to Linux replatforming assistant for Microsoft SQL Server Databases script consists of two main components:

1. An [AWS\-signed PowerShell backup script](https://awsec2-server-upgrade-prod.s3.us-west-1.amazonaws.com/MigrateSQLServerToEC2Linux.ps1), which backs up on\-premises Microsoft SQL Server databases to an Amazon S3 storage bucket\. It then invokes the SSM Automation document [AWSEC2\-SQLServerDBRestore](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-sqlserverdbrestore.html) to restore the backups to a Microsoft SQL Server on EC2 Linux instance\.

1. An SSM Automation document named `AWSEC2-SQLServerDBRestore`, which restores database backups to Microsoft SQL Server on EC2 Linux\. This automation restores Microsoft SQL Server database backups stored in Amazon S3 to Microsoft SQL Server 2017 running on an EC2 Linux instance\. You can provide your own EC2 instance running Microsoft SQL Server 2017 Linux, or the automation launches and configures a new EC2 instance with Microsoft SQL Server 2017 on Ubuntu 16\.04\. The automation supports the restoration of full, differential, and transactional log backups, and accepts multiple database backup files\. The automation automatically restores the most recent valid backup of each database in the files provided\. For more information, see [AWSEC2\-SQLServerDBRestore](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awsec2-sqlserverdbrestore.html)\.