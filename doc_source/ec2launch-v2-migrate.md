# Migrate to EC2Launch v2<a name="ec2launch-v2-migrate"></a>

The EC2Launch migration tool includes an option to upgrade an earlier version of the service by uninstalling it and installing EC2Launch v2\. If you choose that option, all of the applicable configurations from prior launch services are automatically migrated to the new service\. If you are migrating from EC2Config, the [AWS Systems Manager Agent \(SSM Agent\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) is uninstalled when EC2Config is uninstalled\. The EC2Launch v2 upgrade reinstalls a stable version of the SSM Agent\. We recommend that you update the SSM Agent to the latest version using your preferred change control process\.

 You can download the migration tool or install with an SSM RunCommand document\. 

You can download the tool from the following locations:
+ **64Bit** — [https://amazon\-ec2launch\-v2\-utils\.s3\.amazonaws\.com/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool\.zip](https://amazon-ec2launch-v2-utils.s3.amazonaws.com/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool.zip)
+ **32Bit** — [https://amazon\-ec2launch\-v2\-utils\.s3\.amazonaws\.com/MigrationTool/windows/386/latest/EC2LaunchMigrationTool\.zip](https://amazon-ec2launch-v2-utils.s3.amazonaws.com/MigrationTool/windows/386/latest/EC2LaunchMigrationTool.zip)

Use the [https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1](https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1) SSM document to migrate to the latest EC2Launch version with SSM Run Command\. The document does not require any parameters\. For more information about using SSM Run Command, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\.

## EC2Launch v2 migration tool version history<a name="ec2launchv2-migration-tool-version-history"></a>

The following table describes the released versions of the EC2Launch v2 migration tool\.


| Version | Details | Release date | 
| --- | --- | --- | 
| 1\.0\.0 | Initial release | June 30, 2020 | 
