# Getting started<a name="replatform-sql-server-getting-started"></a>

This section contains the PowerShell parameter definitions and scripts for replatforming your databases\. For more information about how to use PowerShell scripts, see [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview)\.

**Topics**
+ [Running the Windows to Linux replatforming assistant for Microsoft SQL Server script](#replatform-sql-server-script)
+ [Parameters](#replatform-sql-server-parameters)

## Running the Windows to Linux replatforming assistant for Microsoft SQL Server script<a name="replatform-sql-server-script"></a>

The following common scenarios and example PowerShell scripts demonstrate how to replatform your Microsoft SQL Server databases using Windows to Linux replatforming assistant for Microsoft SQL Server Databases\.

**Important**  
The Windows to Linux Replatforming Assistant for Microsoft SQL Server Databases resets the SQL Server server administrator \(SA\) user password on the target instance every time that it is run\. After the replatform process is complete, you must set your own SA user password before you can connect to the target SQL Server instance\.

**Syntax**  
The Windows to Linux replatforming assistant for Microsoft SQL Server Databases script adheres to the syntax shown in the following example\.

```
PS C:\> C:\MigrateSQLServerToEC2Linux.ps1 [[-SqlServerInstanceName] <String>] [[-DBNames]<Object[]>] [-
MigrateAllDBs] [PathForBackup] <String> [-SetSourceDBModeReadOnly] [-IamInstanceProfileName] <String>[-
AWSRegion] <String> [[-EC2InstanceId] <String>] [[-EC2InstanceType] <String>] [[-EC2KeyPair] <String>] [[-
SubnetId] <String>] [[-AWSProfileName] <String>] [[-AWSProfileLocation] <String>] [-GeneratePresignedUrls]
[<CommonParameters>]
```

**Example 1: Move a database to an EC2 instance**  
The following example shows how to move a database named `AdventureDB` to an EC2 Microsoft SQL Server on Linux instance, with an instance ID of `i-024689abcdef`, from the Microsoft SQL Server Instance named `MSSQLSERVER`\. The backup directory to be used is `D:\\Backup` and the AWS Region is `us-east-2`\.

```
PS C:\> ./MigrateSQLServerToEC2Linux.ps1 - SQLServerInstanceName MSSQLSERVER -EC2InstanceId i-
024689abcdef -DBNames AdventureDB -PathForBackup D:\\Backup -AWSRegion us-east-2 -
IamInstanceProfileName AmazonSSMManagedInstanceCore
```

**Example 2: Move a database to an EC2 instance using the AWS credentials profile**  
The following example shows how to move the database in Example 1 using the AWS credentials profile: `DBMigration`\.

```
PS C:\> ./MigrateSQLServerToEC2Linux.ps1 - SQLServerInstanceName MSSQLSERVER -EC2InstanceId i-
024689abcdef -DBNames AdventureDB -PathForBackup D:\\Backup -AWSRegion us-east-2 -AWSProfileName
DBMigration -IamInstanceProfileName AmazonSSMManagedInstanceCore
```

**Example 3: Move a database to a new m5\.large type instance**  
The following example shows how to create an `m5.large` type EC2 Linux instance in `subnet-abc127` using the Key Pair `customer-ec2-keypair` and then moving `AdventureDB` and `TestDB` to the new instance from the database used in Examples 1 and 2\.

```
PS C:\> ./MigrateSQLServerToEC2Linux.ps1 -EC2InstanceType m5.large -SubnetId subnet-abc127 -EC2KeyPair
customer-ec2-keypair -DBNames AdventureDB,TestDB -PathForBackup D:\\Backup -AWSRegion us-east-2 -
AWSProfileName DBMigration -IamInstanceProfileName AmazonSSMManagedInstanceCore
```

**Example 4: Move all databases to a new m5\.large type instance**  
The following example shows how to create an `m5.large` type EC2 Linux instance in `subnet-abc127` using the Key Pair `customer-ec2-keypair` and then migrating all databases to the instance from databases used in Examples 1 and 2\.

```
PS C:\> ./MigrateSQLServerToEC2Linux.ps1 -EC2InstanceType m5.large -SubnetId subnet-abc127 -EC2KeyPair
customer-ec2-keypair -MigrateAllDBs -PathForBackup D:\\Backup -AWSRegion us-east-2 -AWSProfileName
DBMigration -IamInstanceProfileName AmazonSSMManagedInstanceCore
```

## Parameters<a name="replatform-sql-server-parameters"></a>

The following parameters are used by the PowerShell script to replatform your Microsoft SQL Server databases\.

**`-SqlServerInstanceName`**  
The name of the Microsoft SQL Server instance to be backed up\. If a value for `SqlServerInstanceName` is not provided, `$env:ComputerName` is used by default\.

Type: String

Required: No

**`-DBNames`**  
The names of the databases to be backed up and restored\. Specify the names of the databases in a comma\-separated list \(for example, `adventureDB`,`universityDB`\)\. Either the `DBNames` or `MigrateAllDBs` parameter is required\.

Type: Object

Required: No

**`-MigrateAllDBs`**  
This switch is disabled by default\. If this switch is enabled, the automation migrates all databases except for the system databases \(master, msdb, tempdb\)\. Either the `DBNames` or `MigrateAllDBs` parameter is required\.

Type: SwitchParameter

Required: No

**`-PathForBackup`**  
The path where the full backup is stored\.

Type: String

Required: Yes

**`-SetSourceDBModeReadOnly`**  
This switch is disabled by default\. If this switch is enabled, it makes the database read\-only during migration\.

Type: SwitchParameter

Required: No

**`-IamInstanceProfileName`**  
Enter the AWS IAM instance role with permissions to run Systems Manager Automation on your behalf\. See [Getting Started with Automation](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-setup.html) in the *AWS Systems Manager User Guide*\.

Type: String

Required: Yes

**`-AWSRegion`**  
Enter the AWS Region where your Amazon S3 buckets are created to store database backups\.

Type: String

Required: Yes

**`-EC2InstanceId`**  
To restore Microsoft SQL Server databases to an existing EC2 instance running Microsoft SQL Server Linux, enter the instance ID of the instance\. Make sure that the EC2 instance already has the AWS Systems Manager SSM Agent installed and running\.

Type: String

Required: No

**`-EC2InstanceType`**  
To restore Microsoft SQL Server databases to a new EC2 Linux instance, enter the instance type of the instance to be launched\.

Type: String

Required: No

**`-EC2KeyPair`**  
To restore Microsoft SQL Server databases to a new EC2 Linux instance, enter the name of the EC2 Key Pair to be used to access the instance\. This parameter is recommended if you are creating a new EC2 Linux instance\.

Type: String

Required: No

**`-SubnetId`**  
This parameter is required when creating a new EC2 Linux instance\. When creating a new EC2 Linux instance, if `SubnetId` is not provided, the AWS user default subnet is used to launch the EC2 Linux instance\.

Type: String

Required: No

**`-AWSProfileName`**  
The name of the AWS profile that the automation uses when connecting to AWS services\. For more information on the required IAM user permissions, see [Getting Started with Automation](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-setup.html) in the *AWS Systems Manager User Guide*\. If a profile is not entered, the automation uses your default AWS profile\.

Type: String

Required: No

**`-AWSProfileLocation`**  
The location of the AWS Profile if the AWS Profile is not stored in the default location\.

Type: String

Required: No

**`-GeneratePresignedUrls`**  
This parameter is only used when replatforming to non\-EC2 instances, such as to VMware Cloud on AWS or on\-premises VMs\.

Type: SwitchParameter

Required: No

**`<CommonParameters>`**  
This cmdlet supports the common parameters: `Verbose`, `Debug`, `ErrorAction`, `ErrorVariable`, `WarningAction`, `WarningVariable`, `OutBuffer`, `PipelineVariable`, and `OutVariable`\. For more information, see [About Common Parameters](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_commonparameters?view=powershell-6) in the Microsoft PowerShell documentation\.

Required: No