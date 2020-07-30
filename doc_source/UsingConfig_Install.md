# Installing the Latest Version of EC2Config<a name="UsingConfig_Install"></a>

By default, the EC2Config service is included in AMIs prior to Windows Server 2016\. When the EC2Config service is updated, new Windows AMIs from AWS include the latest version of the service\. However, you need to update your own Windows AMIs and instances with the latest version of EC2Config\.

**Note**  
EC2Launch replaces EC2Config on Windows Server 2016 and later AMIs\. For more information, see [Configuring a Windows instance using EC2Launch](ec2launch.md)\. The latest launch service for all supported Windows Server versions is [EC2Launch v2](ec2launch-v2.md), which replaces both EC2Config and EC2Launch\.

For information about how to receive notifications for EC2Config updates, see [Subscribing to EC2Config Service Notifications](ec2config-version-details.md#ec2-subscribe-notifications)\. For information about the changes in each version, see the [EC2Config Version History](ec2config-version-details.md)\.

## Before You Begin<a name="ec2config-prereqs"></a>
+ Verify that you have \.NET framework 3\.5 SP1 or greater\.
+ By default, Setup replaces your settings files with default settings files during installation and restarts the EC2Config service when the installation is completed\. If you changed EC2Config service settings, copy the `config.xml` file from the `%Program Files%\Amazon\Ec2ConfigService\Settings` directory\. After you update the EC2Config service, you can restore this file to retain your configuration changes\.
+ If your version of EC2Config is earlier than version 2\.1\.19 and you are installing version 2\.2\.12 or earlier, you must first install version 2\.1\.19\. To install version 2\.1\.19, download [EC2Install\_2\.1\.19\.zip](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install_2.1.19.zip), unzip the file, and then run `EC2Install.exe`\.
**Note**  
If your version of EC2Config is earlier than version 2\.1\.19 and you are installing version 2\.3\.313 or later, you can install it directly without installing version 2\.1\.19 first\.

## Verify the EC2Config Version<a name="ec2config-verify-version"></a>

Use the following procedure to verify the version of EC2Config that is installed on your instances\.

**To verify the installed version of EC2Config**

1. Launch an instance from your AMI and connect to it\.

1. In Control Panel, select **Programs and Features**\.

1. In the list of installed programs, look for `Ec2ConfigService`\. Its version number appears in the **Version** column\.

## Update EC2Config<a name="ec2config-update-version"></a>

Use the following procedure to download and install the latest version of EC2Config on your instances\.

**To download and install the latest version of EC2Config**

1. Download and unzip the [EC2Config installer](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip)\.

1. Run `EC2Install.exe`\. For a complete list of options, run `EC2Install` with the `/?` option\. By default, setup displays prompts\. To run the command with no prompts, use the `/quiet` option\.
**Important**  
To keep the custom settings from the `config.xml` file that you saved, run `EC2Install` with the `/norestart` option, restore your settings, and then restart the EC2Config service manually\.

1. If you are running EC2Config version 4\.0 or later, you must restart SSM Agent on the instance from the Microsoft Services snap\-in\.
**Note**  
The updated EC2Config version information will not appear in the instance System Log or Trusted Advisor check until you reboot or stop and start your instance\.