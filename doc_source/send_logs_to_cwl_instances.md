# Configure Instances for CloudWatch<a name="send_logs_to_cwl_instances"></a>

**Important**  
The unified CloudWatch Agent has replaced the SSM Agent as the tool for sending log data to Amazon CloudWatch Logs\. Support for using the SSM Agent to send log data will be deprecated in the near future\. We recommend that you begin using the unified CloudWatch Agent for your log collection processes as soon as possible\. For more information, see the following topics:  
[Send Logs to CloudWatch Logs \(CloudWatch Agent\)](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html) in the *AWS Systems Manager User Guide*
[ Migrate Windows Server Instance Log Collection to the CloudWatch Agent](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html#monitoring-cloudwatch-agent-migrate) in the *AWS Systems Manager User Guide*
[Collect Metrics from Amazon Elastic Compute Cloud Instances and On\-Premises Servers with the CloudWatch Agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*

Choose from the following methods to configure integration with CloudWatch:
+ [Use Run Command](#remote-commands-cloudwatch)
+ [Use State Manager](#ec2-configuration-cwl)
+ [Use a Local Configuration File](#send_logs_cwl_configfile)

## Before You Begin<a name="send_logs_to_cwl_prerequisites"></a>

Verify that you have completed the prerequisites\. For more information, see [Preliminary Tasks for Configuring Integration with CloudWatch](send_logs_to_cwl_gs.md)\.

## Use Systems Manager Run Command<a name="remote-commands-cloudwatch"></a>

Run Command enables you to manage the configuration of your instances on demand\. You specify a Systems Manager document, specify parameters, and execute the command on one or more instance\. The SSM agent on the instance processes the command and configures the instance as specified\.

You can use Run Command to configure integration with CloudWatch\. After you configure integration, the SSM Agent sends all the logs you configured in your JSON file to CloudWatch\. The time frame varies for when the information is sent\. For the application, system, security, and event tracing \(Windows\) logs, the system sends all information generated within the first minute of integration being enabled\. Logs that occurred before this time are not included\. For any custom log files and Internet Information Services \(IIS\) logs, State Manager reads the log files from the beginning\.

If you previously enabled CloudWatch integration by using the EC2Config service, Run Command settings override the EC2Config settings stored on the instance\. By default, these settings are stored in the following file on the instance: `C:\Program Files\Amazon\Ec2ConfigService\Settings\AWS.EC2.Windows.CloudWatch.json`\.

**To configure integration with CloudWatch using Run Command**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Systems Manager Services**, **Run Command**\.

1. Choose **Run a command**\.

1. For **Command document**, choose **AWS\-ConfigureCloudWatch**\.

1. For **Target instances**, choose the instances to integrate with CloudWatch\. If you do not see an instance in this list, it might not be configured for Run Command\. For more information, see [Systems Manager Prerequisites](http://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

1. For **Status**, choose **Enabled**\.

1. For **Properties**, copy and paste your JSON content\.

1. Complete the remaining optional fields and choose **Run**\.

Use the following procedure to view the results of command execution in the Amazon EC2 console\.

**To view command output in the console**

1. Select a command\.

1. Choose the **Output** tab\.

1. Choose **View Output**\. The command output page shows the results of your command execution\.

## Use Systems Manager State Manager<a name="ec2-configuration-cwl"></a>

State Manager enables you to manage the configuration of your Windows instances while they are running\. You create a configuration document, which describes configuration tasks \(for example, sending performance counters to CloudWatch and logs to CloudWatch Logs\), and associate the configuration document with one or more running Windows instances\. The SSM agent on the instance processes the configuration document and configures the instance as specified\.

You can use Systems Manager State Manager \(formerly called SSM Config\) to configure integration with CloudWatch\. After you configure integration, the SSM Agent sends all the logs you configured in your JSON file to CloudWatch\. The time frame varies for when the information is sent\. For the application, system, security, and event tracing \(Windows\) logs, the system sends all information generated within the first minute of integration being enabled\. Logs that occurred before this time are not included\. If you disable logging and then later re\-enable logging, State Manager sends logs from where it left off\. For any custom log files and Internet Information Services \(IIS\) logs, State Manager reads the log files from the beginning\.

If you previously enabled CloudWatch integration by using the EC2Config service, State Manager settings override the EC2Config settings stored on the instance\. By default, these settings are stored in the following file on the instance: `C:\Program Files\Amazon\Ec2ConfigService\Settings\AWS.EC2.Windows.CloudWatch.json`\.

**To configure integration with CloudWatch using State Manager**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Systems Manager Services**, **State Manager**\.

1. Choose **Create an association**\.

1. For **Document**, choose **AWS\-ConfigureCloudWatch**\.

1. For **Document Version**, choose **Default version at runtime**\.

1. For **Targets**, choose the instances to integrate with CloudWatch\. If you do not see an instance in this list, it might not be configured for Run Command\. For more information, see [Systems Manager Prerequisites](http://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-seting-up.html) in the *AWS Systems Manager User Guide*\.

1. For **Schedule**, choose how often you want Systems Manager to apply this policy\. This indicates how often you want Systems Manager to ensure the integration with CloudWatch is still valid\. This does not affect the frequency when the SSM Agent sends data to CloudWatch\.

1. For **Parameters**, **Status**, choose **Enabled**\. For **Properties**, copy and paste your JSON content\.

1. \(Optional\) To send command output to an Amazon S3 bucket, choose **Advanced**, **Write to S3**\.
**Important**  
The Amazon EC2 console truncates output after 2500 characters\. Configure an Amazon S3 bucket before executing commands using Systems Manager so that you can view the full output, beyond 2500 characters, in your bucket\. For more information, see [Create a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

1. Choose **Create Association**\.

1. Choose the association you just created, and then choose **Apply Association Now**\.

## Use a Local Configuration File<a name="send_logs_cwl_configfile"></a>

The procedure to configure your instances using a local configuration file depends on the agent and the version of the agent that you're using\. The SSM Agent is the only agent compatible with Windows Server 2016\. With Windows Server 2008 to Windows Server 2012 R2, you must determine which version of EC2Config is running on your instance\. For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

**Topics**
+ [Use SSM Agent to Configure CloudWatch](#configure-ssm-agent)
+ [Use EC2Config 4\.x to Configure CloudWatch](#ec2config-4x)
+ [Use EC2Config 3\.x or Earlier to Configure CloudWatch](#ec2config-3x)

### Use SSM Agent to Configure CloudWatch<a name="configure-ssm-agent"></a>

The following procedure describes how to configure CloudWatch using the SSM Agent on Amazon EC2 Windows Server 2016 instances\.

**To configure CloudWatch using SSM Agent**

1. Download the latest version of the SSM Agent to your instance\. For more information, see [Installing SSM Agent on Windows](http://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html#sysman-install-ssm-win)\.

1. Open the `AWS.EC2.Windows.CloudWatch.json` file, and change `IsEnabled` to `true`\. This tells the agent to start sending data to CloudWatch immediately after it is started or restarted\.

1. Save the file with the same name in the following folder on your Windows Server 2016 instance: `C:\Program Files\Amazon\SSM\Plugins\awsCloudWatch\`\.

1. Start or restart the SSM agent \(`AmazonSSMAgent.exe`\) using the Windows Services control panel or by sending the following command in PowerShell: 

   ```
   PS C:\> Restart-Service AmazonSSMAgent
   ```

After the SSM agent restarts, it detects the local configuration file and configures the instance for CloudWatch integration\. If you change parameters and settings in the local configuration file, you need to restart the SSM agent to pick up the changes\. If you want to disable CloudWatch integration on the instance, change `IsEnabled` to `false` and save your changes in the configuration file\.

### Use EC2Config 4\.x to Configure CloudWatch<a name="ec2config-4x"></a>

**To configure CloudWatch using EC2Config 4\.x**

1. Download the latest version of EC2Config to your instance\. For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

1. \(Optional\) If you have an existing JSON file from an EC2Config 3\.x integration with CloudWatch, open the file, and add the `IsEnabled` section\. This tells the agent to start sending data to CloudWatch immediately after it is started or restarted\.

   The `IsEnabled` section must be located on the same level as the `EngineConfiguration` section\. The following example illustrates this:

   ```
   {
       "IsEnabled": true,
       "EngineConfiguration": { 
       "PollInterval": "00:00:15",
       "Components": [ 
       {
          "Id":"OsCpuUtilization",
          "FullName":"AWS.EC2.Windows.CloudWatch.PerformanceCounterComponent.PerformanceCounterInputComponent,AWS.EC2.Windows.CloudWatch",
          "Parameters":{ 
              "CategoryName":"Process",
          ...          
   }
   ```

1. Save the file with the same name in the following folder on your Windows Server 2008 through Windows Server 2012 R2 instance: `C:\Program Files\Amazon\SSM\Plugins\awsCloudWatch\`\.

1. Start or restart the SSM agent \(`AmazonSSMAgent.exe`\) using the Windows Services control panel or using the following PowerShell command:

   ```
   PS C:\> Restart-Service AmazonSSMAgent
   ```

After the SSM agent restarts, it detects the local configuration file and configures the instance for CloudWatch integration\. If you change parameters and settings in the local configuration file, you need to restart the SSM agent to pick up the changes\. If you want to disable CloudWatch integration on the instance, change `IsEnabled` to `false` and save your changes in the configuration file\.

### Use EC2Config 3\.x or Earlier to Configure CloudWatch<a name="ec2config-3x"></a>

Use the following procedure if you need to run an older version of EC2Config on your instances and continue to integrate with CloudWatch\.

**To configure CloudWatch using EC2Config 3\.x or earlier**

1. Connect to your Windows instance\.

1. From the **Start** menu, choose **All Programs**, and then choose **EC2ConfigService Settings**\.

1. On the **General** tab of the **Ec2 Service Properties** dialog box, under **CloudWatch Logs**, choose **Enable CloudWatch Logs integration**, and then choose **OK**\.

1. If you made changes to the `AWS.EC2.Windows.CloudWatch.json` file, then you must restart the EC2Config service\. For more information, see [Stopping, Restarting, Deleting, or Uninstalling EC2Config](ec2config-service.md#UsingConfig_StopDelete)\.

**To enable CloudWatch Logs using user data**  
You can enable CloudWatch Logs by adding the following script to the user data field when you launch an instance\. EC2Config will run this script every time your instance is restarted to make sure that CloudWatch Logs integration is enabled\. To run this script only when an instance is first launched, remove `<persist>true</persist>` from the script\.

```
<powershell>
$EC2SettingsFile="C:\Program Files\Amazon\Ec2ConfigService\Settings\Config.xml"
$xml = [xml](get-content $EC2SettingsFile)
$xmlElement = $xml.get_DocumentElement()
$xmlElementToModify = $xmlElement.Plugins

foreach ($element in $xmlElementToModify.Plugin)
{
    if ($element.name -eq "AWS.EC2.Windows.CloudWatch.PlugIn")
    {
        $element.State="Enabled"
    }
}
$xml.Save($EC2SettingsFile)
</powershell>
<persist>true</persist>
```