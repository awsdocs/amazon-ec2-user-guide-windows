# Migrate to EC2Launch v2<a name="ec2launch-v2-migrate"></a>

The EC2Launch migration tool upgrades the installed launch agent \(EC2Config and EC2Launch v1\) by uninstalling it and installing EC2Launch v2\. Applicable configurations from previous launch services are automatically migrated to the new service\. The migration tool does not detect any scheduled tasks linked to EC2Launch v1 scripts; therefore, it does not automatically set up those tasks in EC2Launch v2\. To configure these tasks, edit the [`agent-config.yml`](ec2launch-v2-settings.md#ec2launch-v2-task-configuration) file, or use the [EC2Launch v2 settings dialog box](ec2launch-v2-settings.md#ec2launch-v2-ui)\. For example, if an instance has a scheduled task that runs `InitializeDisks.ps1`, then after you run the migration tool, you must specify the volumes you want to initialize in the EC2Launch v2 settings dialog box\. See Step 6 of the procedure to [Change settings using the EC2Launch v2 settings dialog box](ec2launch-v2-settings.md#ec2launch-v2-ui)\. 

You can download the migration tool or install with an SSM RunCommand document\. 

You can download the tool from the following locations:

**Note**  
The 32\-bit migration tool link will be deprecated\. We recommend that you use the 64\-bit link to migrate to EC2Launch v2\. If you require a 32\-bit launch agent, use [EC2Config](ec2config-service.md)\.
+ **64Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2\-utils/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool\.zip](https://s3.amazonaws.com/amazon-ec2launch-v2-utils/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool.zip)
+ **32Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2\-utils/MigrationTool/windows/386/latest/EC2LaunchMigrationTool\.zip](https://s3.amazonaws.com/amazon-ec2launch-v2-utils/MigrationTool/windows/386/latest/EC2LaunchMigrationTool.zip)

**Note**  
You must run the EC2Launch v2 migration tool as an Administrator\. EC2Launch v2 is installed as a service after you run the migration tool\. It does not run immediately\. By default, it runs during instance startup and runs if an instance is stopped and later started, or restarted\.

Use the [https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1](https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1) SSM document to migrate to the latest EC2Launch v2 version with SSM Run Command\. The document does not require any parameters\. For more information about using SSM Run Command, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\.

The migration tool applies the following configurations from EC2Config to EC2Launch v2\.
+ If `Ec2DynamicBootVolumeSize` is set to `false`, removes EC2Launch v2 `boot` stage
+ If `Ec2SetPassword` is set to `Enabled`, sets EC2Launch v2 password type to `random` 
+ If `Ec2SetPassword` is set to `Disabled`, sets EC2Launch v2 password type to `donothing` 
+ If `SetDnsSuffixList` is set to `false`, removes EC2Launch v2 `setDnsSuffix` task
+ If `EC2SetComputerName` is set to true, adds EC2Launch v2 `setHostName` task to `yaml` configuration

The migration tool applies the following configurations from EC2Launch v1 to EC2Launch v2\.
+ If `ExtendBootVolumeSize` is set to `false`, removes EC2Launch v2 `boot` stage
+ If `AdminPasswordType` is set to `Random`, sets EC2Launch v2 password type to `random` 
+ If `AdminPasswordType` is set to `Specify`, sets EC2Launch v2password type to `static` and password data to the password specified in `AdminPassword` 
+ If `SetWallpaper` is set to `false`, removes EC2Launch v2 `setWallpaper` task
+ If `AddDnsSuffixList` is set to `false`, removes EC2Launch v2 `setDnsSuffix` task
+ If `SetComputerName` is set to `true`, adds EC2Launch v2 `setHostName` task