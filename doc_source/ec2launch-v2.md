# Configure a Windows instance using EC2Launch v2<a name="ec2launch-v2"></a>

All supported instances of Amazon EC2 running Windows Server 2022 include the EC2Launch v2 launch agent \(`EC2Launch.exe`\) by default\. We also provide Windows Server 2016 and 2019 AMIs with EC2Launch v2 installed as the default launch agent\. These AMIs are provided in addition to the Windows Server 2016 and 2019 AMIs that include EC2Launch v1\. You can search for Windows AMIs that include EC2Launch v2 by default by entering the following prefix in your search from the **AMIs** page in the Amazon EC2 console: `EC2LaunchV2-Windows_Server-*`\.

 EC2Launch v2 performs tasks during instance startup and runs if an instance is stopped and later started, or restarted\. EC2Launch v2 can also perform tasks on demand\. Some of these tasks are automatically enabled, while others must be enabled manually\. The EC2Launch v2 service supports all EC2Config and EC2Launch features\. 

This service uses a configuration file to control its operation\. You can update the configuration file using either a graphical tool or by directly editing it as a single \.yml file \(`agent-config.yml`\)\. The service binaries are located in the `%ProgramFiles%\Amazon\EC2Launch` directory\.

EC2Launch v2 publishes Windows event logs to help you troubleshoot errors and set triggers\. For more information, see [Windows event logs](ec2launchv2-troubleshooting.md#ec2launchv2-windows-event-logs)\. 

**Supported operating systems**
+ Windows Server 2022
+ Windows Server 2019 \(Long\-Term Servicing Channel and Semi\-Annual Channel\)
+ Windows Server 2016
+ Windows Server 2012 and 2012 R2
+ Windows Server 2008 SP2 and 2008 R2

**Topics**
+ [EC2Launch v2 overview](ec2launch-v2-overview.md)
+ [Install the latest version of EC2Launch v2](ec2launch-v2-install.md)
+ [Migrate to EC2Launch v2](ec2launch-v2-migrate.md)
+ [Stop, restart, delete, or uninstall EC2Launch v2](ec2-launch-v2-manage.md)
+ [Verify the EC2Launch v2 version](ec2launch-v2-verify-version.md)
+ [Subscribe to EC2Launch v2 service notifications](ec2launch-v2-sns.md)
+ [EC2Launch v2 settings](ec2launch-v2-settings.md)
+ [Troubleshoot EC2Launch v2](ec2launchv2-troubleshooting.md)
+ [EC2Launch v2 version histories](ec2launchv2-versions.md)