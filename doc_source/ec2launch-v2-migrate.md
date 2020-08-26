# Migrate to EC2Launch v2<a name="ec2launch-v2-migrate"></a>

The EC2Launch migration tool includes an option to upgrade an earlier version of the service by uninstalling it and installing EC2Launch v2\. If you choose that option, all of the applicable configurations from prior launch services are automatically migrated to the new service\. If you are migrating from EC2Config, the [AWS Systems Manager Agent \(SSM Agent\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) is uninstalled when EC2Config is uninstalled\. The EC2Launch v2 upgrade reinstalls a stable version of the SSM Agent\. We recommend that you update the SSM Agent to the latest version using your preferred change control process\.

You can download the migration tool or install with an SSM RunCommand document\. 

You can download the tool from the following locations:

**Note**  
You must run the EC2Launch v2 migration tool as an Administrator\.
+ **64Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2\-utils/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool\.zip](https://s3.amazonaws.com/amazon-ec2launch-v2-utils/MigrationTool/windows/amd64/latest/EC2LaunchMigrationTool.zip)
+ **32Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2\-utils/MigrationTool/windows/386/latest/EC2LaunchMigrationTool\.zip](https://s3.amazonaws.com/amazon-ec2launch-v2-utils/MigrationTool/windows/386/latest/EC2LaunchMigrationTool.zip)

Use the [https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1](https://console.aws.amazon.com/systems-manager/documents/AWSEC2Launch-RunMigration/description?region=us-east-1) SSM document to migrate to the latest EC2Launch version with SSM Run Command\. The document does not require any parameters\. For more information about using SSM Run Command, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\.