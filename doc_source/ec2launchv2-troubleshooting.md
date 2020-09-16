# Troubleshooting EC2Launch v2<a name="ec2launchv2-troubleshooting"></a>

This section shows common troubleshooting scenarios for EC2Launch v2, information about viewing Windows event logs, and console log output and messages\.

**Topics**
+ [Common troubleshooting scenarios](#ec2launchv2-troubleshooting-scenarios)
+ [Windows event logs](#ec2launchv2-windows-event-logs)
+ [EC2Launch v2 console log output](#ec2launchv2-console-output)

## Common troubleshooting scenarios<a name="ec2launchv2-troubleshooting-scenarios"></a>

This section shows common troubleshooting scenarios and steps for resolution\.

**Topics**
+ [Service fails to set the wallpaper](#ec2launchv2-troubleshooting-wallpaper)
+ [Service fails to run user data](#ec2launchv2-troubleshooting-user-data)
+ [Service executes a task only one time](#ec2launchv2-troubleshooting-task-once)
+ [Service fails to run a task](#ec2launchv2-troubleshooting-task-failed)

### Service fails to set the wallpaper<a name="ec2launchv2-troubleshooting-wallpaper"></a>

**Resolution**

1. Check that `%AppData%\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\setwallpaper.lnk` exists\.

1. Check `%ProgramData%\Amazon\EC2Launch\log\agent.log` to see if any errors occurred\.

### Service fails to run user data<a name="ec2launchv2-troubleshooting-user-data"></a>

**Possible cause**: Service may have failed before running user data\.

**Resolution**

1. Check `%ProgramData%\Amazon\EC2Launch\state\previous-state.json`\.

1. See if `boot`, `network`, `preReady`, and `postReadyLocalData` have all been marked as success\.

1. If one of the stages failed, check `%ProgramData%\Amazon\EC2Launch\log\agent.log` for specific errors\.

### Service executes a task only one time<a name="ec2launchv2-troubleshooting-task-once"></a>

**Resolution**

1. Check the frequency of the task\.

1. If the service already ran after Sysprep, and the task frequency is set to `once`, the task will not run again\.

1. Set the frequency of the task to `always` if you want it to run the task every time EC2Launch v2 runs\.

### Service fails to run a task<a name="ec2launchv2-troubleshooting-task-failed"></a>

**Resolution**

1. Check the latest entries in `%ProgramData%\Amazon\EC2Launch\log\agent.log`\.

1. If no errors occurred, try running the service manually from `"%ProgramFiles%\Amazon\EC2Launch\EC2Launch.exe" run` to see if the tasks succeed\.

## Windows event logs<a name="ec2launchv2-windows-event-logs"></a>

EC2Launch v2 publishes Windows event logs for important events, such as service starting, Windows is ready, and task success and failure\. Event identifiers uniquely identify a particular event\. Each event contains stage, task, and level information, and a description\. You can set triggers for specific events using the event identifier\.

**Topics**
+ [Event ID format](#ec2launchv2-windows-event-logs-format)
+ [Event ID examples](#ec2launchv2-windows-event-logs-id-examples)
+ [Windows event log schema](#ec2launch-v2-windows-event-logs-schema)

### Event ID format<a name="ec2launchv2-windows-event-logs-format"></a>

The following table shows the format of an EC2Launch v2 event identifier\.


| 3 | 2 1 | 0 | 
| --- | --- | --- | 
|  S  |  T  |  L  | 

The letters and numbers in the table represent the following event type and definitions\.


| Event type | Definition | 
| --- | --- | 
|  S \(Stage\)  |  0 \- Service\-level message 1 \- Boot 2 \- Network 3 \- PreReady 5 \- Windows is Ready 6 \- PostReady 7 \- User Data  | 
|  T \(Task\)  |  The tasks represented by the corresponding two values are different for each stage\. To view the complete list of events, see [Windows Event log schema](#ec2launch-v2-windows-event-logs-schema)\.  | 
| L \(Level of the event\) |  0 \- Success 1 \- Informational 2 \- Warning 3 \- Error  | 

### Event ID examples<a name="ec2launchv2-windows-event-logs-id-examples"></a>

The following are example event IDs\.
+ `5000` \- Windows is ready to use
+ `3010` \- Activate windows task in PreReady stage was successful
+ `6013` \- Set wallpaper task in PostReady Local Data stage encountered an error

### Windows event log schema<a name="ec2launch-v2-windows-event-logs-schema"></a>


| MessageId/Event Id | Event message | 
| --- | --- | 
|  `. . .0`  | Success | 
|  `. . .1`  | Informational | 
|  `. . .2`  | Warning | 
|  `. . .3`  | Error | 
|  `x`  | EC2Launch service\-level logs | 
|  `0`  | EC2Launch Service exited successfully | 
|  `1`  | EC2Launch Service starting | 
|  `2`  | Error stopping EC2Launch service | 
|  `10`  | Replace state\.json with previous\-state\.json | 
| 100 | Serial Port | 
| 200 | Sysprep | 
| 300 | PrimaryNic | 
| 400 | Metadata | 
|  `x000`  | Stage \(1 digit\), Task \(2 digits\), Status \(1 digit\) | 
|  `1000`  | Boot | 
|  `1010`  | Boot \- extend\_root\_partition | 
| 2000 | Network | 
|  `2010`  | Network \- add\_routes | 
|  `3000`  | PreReady | 
|  `3010`  | PreReady \- activate\_windows | 
|  `3020`  | PreReady \- install\_egpu\_manager | 
|  `3030`  | PreReady \- set\_monitor\_on | 
|  `3040`  | PreReady \- set\_hibernation | 
|  `3050`  | PreReady \- set\_admin\_account | 
|  `3060`  | PreReady \- set\_dns\_suffix | 
|  `3070`  | PreReady \- set\_wallpaper | 
|  `3080`  | PreReady \- set\_update\_schedule | 
|  `3090`  | PreReady \- output\_log | 
|  `3100`  | PreReady \- enable\_open\_ssh | 
|  `5000`  | Windows is Ready to use | 
|  `6000`  | PostReadyLocalData | 
| 7000 | PostReadyUserData | 
|  `6010/7010`  | PostReadyLocal/UserData \- set\_wallpaper | 
|  `6020/7020`  | PostReadyLocal/UserData \- set\_update\_schedule | 
|  `6030/7030`  | PostReadyLocal/UserData \- set\_hostname | 
|  `6040/7040`  | PostReadyLocal/UserData \- execute\_program | 
|  `6050/7050`  | PostReadyLocal/UserData \- execute\_script | 
|  `6060/7060`  | PostReadyLocal/UserData \- manage\_package | 
|  `6070/7070`  | PostReadyLocal/UserData \- initialize\_volume | 
|  `6080/7080`  | PostReadyLocal/UserData \- write\_file | 
|  `6090/7090`  | PostReadyLocal/UserData \- start\_ssm | 
|  `7100`  | PostReadyUserData \- enable\_open\_ssh | 
|  `6110/7110`  | PostReadyLocal/UserData \- enable\_jumbo\_frames | 

## EC2Launch v2 console log output<a name="ec2launchv2-console-output"></a>

This section contains sample console log output for EC2Launch v2 and lists all of the EC2Launch v2 console log error messages to help you to troubleshoot issues\.

**Topics**
+ [EC2Launch v2 console log output](#ec2launchv2-console-log-output)
+ [EC2Launch v2 console log messages](#ec2launchv2-console-log-messages)

### EC2Launch v2 console log output<a name="ec2launchv2-console-log-output"></a>

The following is sample console log output for EC2Launch v2\.

```
2020/08/13 17:25:12Z: Windows is being configured. SysprepState=IMAGE_STATE_UNDEPLOYABLE
2020/08/13 17:27:44Z: Windows is being configured. SysprepState=IMAGE_STATE_UNDEPLOYABLE
2020/08/13 17:28:02Z: Windows sysprep configuration complete.
2020/08/13 17:28:03Z: Message: Waiting for meta-data accessibility...
2020/08/13 17:28:03Z: Message: Meta-data is now available.
2020/08/13 17:28:03Z: AMI Origin Version: 2020.07.15
2020/08/13 17:28:03Z: AMI Origin Name: EC2LaunchV2_Preview-Windows_Server-2012_R2_RTM-English-Full-Base
2020/08/13 17:28:03Z: OS: Microsoft Windows NT 6.3.9600
2020/08/13 17:28:03Z: OsVersion: 6.3
2020/08/13 17:28:03Z: OsProductName: Windows Server 2012 R2 Standard
2020/08/13 17:28:03Z: OsBuildLabEx: 9600.19761.amd64fre.winblue_ltsb.200610-0600
2020/08/13 17:28:03Z: OsCurrentBuild: 9600
2020/08/13 17:28:03Z: Language: en-US
2020/08/13 17:28:03Z: TimeZone: GMT
2020/08/13 17:28:03Z: Offset: UTC +0000
2020/08/13 17:28:03Z: Launch: EC2 Launch v2.0.0
2020/08/13 17:28:03Z: AMI-ID: ami-1a2b3c4d
2020/08/13 17:28:03Z: Instance-ID: i-1234567890abcdef0
2020/08/13 17:28:03Z: Instance Type: t2.nano
2020/08/13 17:28:07Z: Driver: AWS PV Driver Package v8.3.3
2020/08/13 17:28:07Z: RDPCERTIFICATE-SUBJECTNAME: EC2AMAZ-A1B2C3D
2020/08/13 17:28:07Z: RDPCERTIFICATE-THUMBPRINT: A1B2C3D4E5
2020/08/13 17:28:12Z: SSM: Amazon SSM Agent v2.3.842.0
2020/08/13 17:28:13Z: Username: Administrator
2020/08/13 17:28:13Z: Password: <Password>
A1B2C3D4E5F6G7H8I9J10K11L12M13N14O15P16Q17
</Password>
2020/08/13 17:28:13Z: Message: Windows is Ready to use
```

### EC2Launch v2 console log messages<a name="ec2launchv2-console-log-messages"></a>

The following is a list of all of the EC2Launch v2 console log messages\.

```
Message: Error EC2Launch service is stopping. {error message}
 Error setting up EC2Launch agent folders
 See instance logs for detail
 Error stopping service
 Error initializing service
Message: Windows sysprep configuration complete
Message: Invalid administrator username: {invalid username}
Message: Invalid administrator password
Username: {username}
Password: <Password>{encrypted password}</Password>
AMI Origin Version: {amiVersion}
AMI Origin Name: {amiName}
Microsoft Windows NT {currentVersion}.{currentBuildNumber}
OsVersion: {currentVersion}
OsProductName: {productName}
OsBuildLabEx: {buildLabEx}
OsCurrentBuild: {currentBuild}
OsReleaseId: {releaseId}
Language: {language}
TimeZone: {timeZone}
Offset: UTC {offset}
Launch agent: EC2Launch {BuildVersion}
AMI-ID: {amiId}
Instance-ID: {instanceId}
Instance Type: {instanceType}
RDPCERTIFICATE-SUBJECTNAME: {certificate subject name}
RDPCERTIFICATE-THUMBPRINT: {thumbprint hash}
SqlServerBilling: {sql billing}
SqlServerInstall: {sql patch leve, edition type}
Driver: AWS NVMe Driver {version}
Driver: Inbox NVMe Driver {version}
Driver: AWS PV Driver Package {version}
Microsoft-Hyper-V is installed.
Unable to get service status for vmms
Microsoft-Hyper-V is {status}
SSM: Amazon SSM Agent {version}
AWS VSS Version: {version}
Message: Windows sysprep configuration complete
Message: Windows is being configured. SysprepState is {state}
Windows is still being configured. SysprepState is {state}
Message: Windows is Ready to use
Message: Waiting for meta-data accessibility...
Message: Meta-data is now available.
Message: Still waiting for meta-data accessibility...
Message: Failed to find primary network interface...retrying...
```