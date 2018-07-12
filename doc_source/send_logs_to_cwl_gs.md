# Preliminary Tasks for Configuring Integration with CloudWatch<a name="send_logs_to_cwl_gs"></a>

Complete the following preliminary tasks to configure integration with CloudWatch\. These tasks apply to all methods for configuring instances to send logs, events, and performance counters to CloudWatch\. 

**Important**  
The unified CloudWatch Agent has replaced the SSM Agent as the tool for sending log data to Amazon CloudWatch Logs\. Support for using the SSM Agent to send log data will be deprecated in the near future\. We recommend that you begin using the unified CloudWatch Agent for your log collection processes as soon as possible\. For more information, see the following topics:  
[Send Logs to CloudWatch Logs \(CloudWatch Agent\)](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html) in the *AWS Systems Manager User Guide*
[ Migrate Windows Server Instance Log Collection to the CloudWatch Agent](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html#monitoring-cloudwatch-agent-migrate) in the *AWS Systems Manager User Guide*
[Collect Metrics from Amazon Elastic Compute Cloud Instances and On\-Premises Servers with the CloudWatch Agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*

**Topics**
+ [Download the Sample Configuration File](#configure_cwl_download)
+ [Configure the JSON File for CloudWatch](#send_logs_to_cwl_json)
+ [Create an IAM User and Role for Systems Manager](#iam_permissions)
+ [Verify Systems Manager Prerequisites](#send_logs_cwl_syspre)
+ [Verify Internet Access](#send_logs_cwl_internet)
+ [Next Step](#send_longs_cwl_next_step)

## Download the Sample Configuration File<a name="configure_cwl_download"></a>

Download the following sample file to your computer: [AWS\.EC2\.Windows\.CloudWatch\.json](https://s3.amazonaws.com/ec2-downloads-windows/CloudWatchConfig/AWS.EC2.Windows.CloudWatch.json)\.

## Configure the JSON File for CloudWatch<a name="send_logs_to_cwl_json"></a>

You determine which logs, events, and performance counters are sent to CloudWatch by specifying your choices in a configuration file\. The process of creating this file and specifying your choices can take 30 minutes or more to complete\. After you have completed this task once, you can reuse the configuration file on all of your instances\.

**Important**  
Use UTF\-8 without BOM encoding for the CloudWatch\.json file\. If you don't, CloudWatch might not read the configuration file correctly\.

**Topics**
+ [Step 1: Configure Settings for CloudWatch](#configure_cwl_credentials)
+ [Step 2: Configure the Data to Send](#configure_logs)
+ [Step 3: Configure Flow Control](#configure_log_flow)

### Step 1: Configure Settings for CloudWatch<a name="configure_cwl_credentials"></a>

By specifying credentials, a region, and a metric namespace for CloudWatch, you enable an instance to send performance counter data to CloudWatch\. If you don't want to send performance counter data, you can skip this procedure\. To send the same performance counter data to different locations, add additional sections with unique IDs \(for example, `CloudWatch2` and `CloudWatch3`\) and a different region for each ID\.

**To configure settings to send performance counter data to CloudWatch**

1. In the JSON file, locate the `CloudWatch` section near the bottom of the file\.

   ```
   {
       "Id": "CloudWatch",
       "FullName": "AWS.EC2.Windows.CloudWatch.CloudWatch.CloudWatchOutputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "AccessKey": "",
           "SecretKey": "",
           "Region": "us-west-1",
           "NameSpace": "Windows/Default"
       }
   },
   ```

1. \(Optional\) If you are using a local configuration file with local credentials, type your access key ID for `AccessKey` and your secret access key for `SecretKey`\.
**Important**  
If you use Systems Manager Run Command or State Manager, do not provide credentials in the configuration file, as there is a chance that they could be exposed in log files, including debug log files\. You'll configure credentials using an IAM role\.

1. For `Region`, type the region where you want to send log data \(for example `us-east-2`\)\. Although you can send performance counters to a different region from where you send your log data, we recommend that you set this parameter to the same region where your instance is running\.

1. For `NameSpace`, type the metric namespace where performance counter data will be written\.

Next, specify credentials, region, log group name, and a log stream namespace\. This enables the instance to send log data to CloudWatch Logs\. To send the same log data to different locations, add additional sections with unique IDs \(for example, `CloudWatchLogs2` and `CloudWatchLogs3`\) and a different region for each ID\.

**To configure settings to send log data to CloudWatch Logs**

1. In the JSON file, locate the `CloudWatchLogs` section\.

   ```
   {
       "Id": "CloudWatchLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CloudWatchLogsOutput,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "AccessKey": "",
           "SecretKey": "",
           "Region": "us-east-1",
           "LogGroup": "Default-Log-Group",
           "LogStream": "{instance_id}"
       }
   },
   ```

1. \(Optional\) If you are using a local configuration file with local credentials, type your access key ID for `AccessKey` and your secret access key for `SecretKey`\.
**Important**  
If you use Systems Manager Run Command or State Manager, do not provide credentials in the configuration file, as there is a chance that they could be exposed in log files, including debug log files\. You'll configure credentials using an IAM role\.

1. For `Region`, type the region where you want to send log data \(for example, `us-east-2`\)\.

1. For `LogGroup`, type the name for your log group\. This name will appear on the **Log Groups** screen in the CloudWatch console\.

1. For `LogStream`, type the destination log stream\. This name will appear on the **Log Groups > Streams** screen in the CloudWatch console\.

   If you use `{instance_id}`, the default, the log stream name is the instance ID of this instance\.

   If you specify a log stream name that doesn't already exist, CloudWatch Logs automatically creates it for you\. You can define a log stream name using a literal string, the predefined variables `{instance_id}`, `{hostname}`, and `{ip_address}`, or a combination of these\.

### Step 2: Configure the Data to Send<a name="configure_logs"></a>

You can send performance counters, event log data, Event Tracing for Windows \(ETW\) data, and other log data to Amazon CloudWatch Logs and Amazon CloudWatch Events\.

**To configure the performance counters to send to CloudWatch**

1. Locate the `PerformanceCounter` section\.

   ```
   {
       "Id": "PerformanceCounter",
       "FullName": "AWS.EC2.Windows.CloudWatch.PerformanceCounterComponent.PerformanceCounterInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "CategoryName": "Memory",
           "CounterName": "Available MBytes",
           "InstanceName": "",
           "MetricName": "AvailableMemory",
           "Unit": "Megabytes",
           "DimensionName": "",
           "DimensionValue": ""
       }
   },
   ```

1. You can select any performance counters that are available in Performance Monitor\. You can select different categories to upload to CloudWatch as metrics, such as \.NET CLR Data, ASP\.NET Applications, HTTP Service, Memory, or Process and Processors\.

   For each performance counter, copy the `PerformanceCounter` section and change the `Id` parameter to make it unique \(for example, `PerformanceCounter2`\)\. Update the other parameters as necessary\.

1. For `CategoryName`, type the performance counter category\. To find the available categories and counters, do the following:

   1. Open Performance Monitor\. \(To find Performance Monitor, right\-click the **Start** menu, choose **Search**, and type **perfmon**\.\)

   1. In the navigation pane, choose **Monitoring Tools**, **Performance Monitor**\.

   1. In the results pane, choose the green **\+** \(plus\) button\. The categories and counters are listed in the **Add Counters** dialog box\.

1. For `CounterName`, type the name of the performance counter\.

1. For `InstanceName`, type values from the **Add Counters** dialog box in Performance Monitor, which can be one of the following:
   + Blank, if the selected object has no instances\.
   + A single instance of the selected object\.
   + `_Total` to use the aggregate of all instances\.

   Do not use an asterisk \(\*\) to indicate all instances because each performance counter component only supports one metric\.

1. For `MetricName`, type the CloudWatch metric that you want performance data to appear under\.

1. For `Unit`, type the appropriate unit of measure for the metric\. The possible values are as follows:

   Seconds \| Microseconds \| Milliseconds \| Bytes \| Kilobytes \| Megabytes \| Gigabytes \| Terabytes \| Bits \| Kilobits \| Megabits \| Gigabits \| Terabits \| Percent \| Count \| Bytes/Second \| Kilobytes/Second \| Megabytes/Second \| Gigabytes/Second \| Terabytes/Second \| Bits/Second \| Kilobits/Second \| Megabits/Second \| Gigabits/Second \| Terabits/Second \| Count/Second \| None\.

1. \(Optional\) To specify a dimension for your metric, type a dimension name and value for `DimensionName` and `DimensionValue`\. These parameters provide another view when listing metrics\. You can also use the same dimension for multiple metrics so that you can view all metrics belonging to a specific dimension\.

**Note**  
If the SSM Agent or the CloudWatch plugin is stopped, performance counter data is not logged in CloudWatch\. This behavior is different than custom logs or Windows Event logs\. Custom logs and Windows Event logs preserve performance counter data and upload it to CloudWatch after the SSM Agent or the CloudWatch plugin is available\.

**To send Windows application event log data to CloudWatch Logs**

1. In the JSON file, locate the `ApplicationEventLog` section\.

   ```
   {
       "Id": "ApplicationEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Application",
           "Levels": "1"
       }
   },
   ```

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** – Upload only error messages\.
   + **2** – Upload only warning messages\.
   + **4** – Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send security log data to CloudWatch Logs**

1. In the JSON file, locate the `SecurityEventLog` section\.

   ```
   {
       "Id": "SecurityEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Security",
           "Levels": "7"
       }
   },
   ```

1. For `Levels`, type **7** to upload all messages\.

**To send system event log data to CloudWatch Logs**

1. In the JSON file, locate the `SystemEventLog` section\.

   ```
   {
       "Id": "SystemEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "System",
           "Levels": "7"
       }
   },
   ```

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** – Upload only error messages\.
   + **2** – Upload only warning messages\.
   + **4** – Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send other types of event log data to CloudWatch Logs**

1. In the JSON file, add a new section\.

   ```
   {
       "Id": "",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "",
           "Levels": "7"
       }
   },
   ```

1. For `Id`, type a name for the log to upload \(for example, **WindowsBackup**\)\.

1. For `LogName`, type the name of the log to upload\. You can find the name of the log as follows\.

   1. Open Event Viewer\.

   1. In the navigation pane, choose **Applications and Services Logs**\.

   1. Navigate to the log, and then choose **Actions**, **Properties**\.

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** – Upload only error messages\.
   + **2** – Upload only warning messages\.
   + **4** – Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send Event Tracing for Windows data to CloudWatch Logs**

ETW \(Event Tracing for Windows\) provides an efficient and detailed logging mechanism that applications can write logs to\. Each ETW is controlled by a session manager that can start and stop the logging session\. Each session has a provider and one or more consumers\.

1. In the JSON file, locate the `ETW` section\.

   ```
   {
       "Id": "ETW",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Microsoft-Windows-WinINet/Analytic",
           "Levels": "7"
       }
   },
   ```

1. For `LogName`, type the name of the log to upload\.

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** – Upload only error messages\.
   + **2** – Upload only warning messages\.
   + **4** – Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send custom logs \(any text\-based log file\) to CloudWatch Logs**

1. In the JSON file, locate the `CustomLogs` section\.

   ```
   {
       "Id": "CustomLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CustomLog.CustomLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogDirectoryPath": "C:\\CustomLogs\\",
           "TimestampFormat": "MM/dd/yyyy HH:mm:ss",
           "Encoding": "UTF-8",
           "Filter": "",
           "CultureName": "en-US",
           "TimeZoneKind": "Local",
           "LineCount": "5"
       }
   },
   ```

1. For `LogDirectoryPath`, type the path where logs are stored on your instance\.

1. For `TimestampFormat`, type the timestamp format you want to use\. For a list of supported values, see the [Custom Date and Time Format Strings](https://msdn.microsoft.com/en-us/library/8kb3ddd4.aspx) topic on MSDN\.
**Important**  
Your source log file must have the timestamp at the beginning of each log line and there must be a space following the timestamp\.

1. For `Encoding`, type the file encoding to use \(for example, UTF\-8\)\. For a list of supported values, see the [Encoding Class](https://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) topic on MSDN\.
**Note**  
Use the encoding name, not the display name\.

1. \(Optional\) For `Filter`, type the prefix of log names\. Leave this parameter blank to monitor all files\. For a list of supported values, see the [FileSystemWatcherFilter Property](https://msdn.microsoft.com/en-us/library/system.io.filesystemwatcher.filter.aspx) topic on MSDN\.

1. \(Optional\) For `CultureName`, type the locale where the timestamp is logged, the structure of which is based on RFC 5646\. If `CultureName` is blank, it defaults to the same locale currently used by your Windows instance\. 
**Note**  
The `div`, `div-MV`, `hu`, and `hu-HU` values are not supported\.

1. \(Optional\) For `TimeZoneKind`, type `Local` or `UTC`\. You can set this to provide time zone information when no time zone information is included in your log's timestamp\. If this parameter is left blank and if your timestamp doesn't include time zone information, CloudWatch Logs defaults to the local time zone\. This parameter is ignored if your timestamp already contains time zone information\.

1. \(Optional\) For `LineCount`, type the number of lines in the header to identify the log file\. For example, IIS log files have virtually identical headers\. You could enter **3**, which would read the first three lines of the log file's header to identify it\. In IIS log files, the third line is the date and timestamp, which is different between log files\. For this reason, we recommend including at least one line of actual log data to uniquely fingerprint the log file\.

**To send IIS log data to CloudWatch Logs**

1. In the JSON file, locate the `IISLog` section\.

   ```
   {
       "Id": "IISLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CustomLog.CustomLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogDirectoryPath": "C:\\inetpub\\logs\\LogFiles\\W3SVC1",
           "TimestampFormat": "yyyy-MM-dd HH:mm:ss",
           "Encoding": "UTF-8",
           "Filter": "",
           "CultureName": "en-US",
           "TimeZoneKind": "UTC",
           "LineCount": "5"
       }
   },
   ```

1. For `LogDirectoryPath`, type the folder where IIS logs are stored for an individual site \(for example, `C:\inetpub\logs\LogFiles\W3SVCn`\)\.
**Note**  
Only W3C log format is supported\. IIS, NCSA, and Custom formats are not supported\.

1. For `TimestampFormat`, type the timestamp format you want to use\. For a list of supported values, see the [Custom Date and Time Format Strings](https://msdn.microsoft.com/en-us/library/8kb3ddd4.aspx) topic on MSDN\.

1. For `Encoding`, type the file encoding to use \(for example, UTF\-8\)\. For a list of supported values, see the [Encoding Class](http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) topic on MSDN\.
**Note**  
Use the encoding name, not the display name\.

1. \(Optional\) For `Filter`, type the prefix of log names\. Leave this parameter blank to monitor all files\. For a list of supported values, see the [FileSystemWatcherFilter Property](https://msdn.microsoft.com/en-us/library/system.io.filesystemwatcher.filter.aspx) topic on MSDN\.

1. \(Optional\) For `CultureName`, type the locale where the timestamp is logged, the structure of which is based on RFC 5646\. If `CultureName` is blank, it defaults to the same locale currently used by your Windows instance\.
**Note**  
The `div`, `div-MV`, `hu`, and `hu-HU` values are not supported\.

1. \(Optional\) For `TimeZoneKind`, type `Local` or `UTC`\. You can set this to provide time zone information when no time zone information is included in your log's timestamp\. If this parameter is left blank and if your timestamp doesn't include time zone information, CloudWatch Logs defaults to the local time zone\. This parameter is ignored if your timestamp already contains time zone information\.

1. \(Optional\) For `LineCount`, type the number of lines in the header to identify the log file\. For example, IIS log files have virtually identical headers\. You could enter **3**, which would read the first three lines of the log file's header to identify it\. In IIS log files, the third line is the date and time stamp, which is different between log files\. For this reason, we recommend including at least one line of actual log data to uniquely fingerprint the log file\.

### Step 3: Configure Flow Control<a name="configure_log_flow"></a>

Each data type must have a corresponding destination in the `Flows` section\. For example, to send a performance counter defined in the `PerformanceCounter` section to the destination defined in the `CloudWatch` section, add `PerformanceCounter,CloudWatch` to the `Flows` section\. Similarly, to send the custom log, ETW log, and system log to CloudWatch Logs, add `(CustomLogs,ETW,SystemEventLog),CloudWatchLogs`\.

**Warning**  
Adding a step that is not valid blocks the flow\. For example, if you add a disk metric step, but your instance doesn't have a disk, all steps in the flow are blocked\.

Note that you can send the same performance counter or log file to more than one destination\. For example, to send the application log to two different destinations that you defined in the `CloudWatchLogs` section, add `ApplicationEventLog,(CloudWatchLogs, CloudWatchLogs2)` to the `Flows` section\.

**To configure flow control**

1. In the `AWS.EC2.Windows.CloudWatch.json` file, locate the `Flows` section\.

   ```
   "Flows": {
       "Flows": [
         "PerformanceCounter,CloudWatch",
         "(PerformanceCounter,PerformanceCounter2), CloudWatch2",
         "(CustomLogs, ETW, SystemEventLog),CloudWatchLogs",
         "CustomLogs, CloudWatchLogs2",
         "ApplicationEventLog,(CloudWatchLogs, CloudWatchLogs2)"
       ]
   }
   ```

1. For `Flows`, add each data type that to be uploaded \(for example, `ApplicationEventLog`\) and its destination \(for example, `CloudWatchLogs`\)\.

## Create an IAM User and Role for Systems Manager<a name="iam_permissions"></a>

If you plan to use a local configuration file with local credentials, then you can skip this task\.

An IAM role for instance credentials is required when you use Systems Manager Run Command or State Manager and optional when you use a local configuration file\. This role enables Systems Manager to perform actions on the instance\. You can optionally create a unique IAM user account for configuring and running Systems Manager\. For more information, see [Configuring Access to Systems Manager](http://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-access.html) in the *AWS Systems Manager User Guide*\. For information about how to attach an IAM role to an existing instance, see [Attaching an IAM Role to an Instance](iam-roles-for-amazon-ec2.md#attach-iam-role)\.

## Verify Systems Manager Prerequisites<a name="send_logs_cwl_syspre"></a>

If you plan to use a local configuration file, then you can skip this task\.

Before you use either Systems Manager Run Command or State Manager to configure integration with CloudWatch, verify that your instances meet the minimum requirements\. For more information, see [Systems Manager Prerequisites](http://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

## Verify Internet Access<a name="send_logs_cwl_internet"></a>

Your Amazon EC2 Windows Server instances and managed instances must have outbound internet access in order to send log and event data to CloudWatch\. For more information about how to configure internet access, see [Internet Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

## Next Step<a name="send_longs_cwl_next_step"></a>

After you complete the preliminary tasks for configuring integration with CloudWatch, you can perform the procedure required to complete the integration\. For more information, see [Configure Instances for CloudWatch](send_logs_to_cwl_instances.md)\.