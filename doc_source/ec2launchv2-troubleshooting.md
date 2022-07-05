# Troubleshoot EC2Launch v2<a name="ec2launchv2-troubleshooting"></a>

This section shows common troubleshooting scenarios for EC2Launch v2, information about viewing Windows event logs, and console log output and messages\.

**Topics**
+ [Common scenarios](#ec2launchv2-troubleshooting-scenarios)
+ [Windows event logs](#ec2launchv2-windows-event-logs)
+ [EC2Launch v2 console log output](#ec2launchv2-console-output)

## Common troubleshooting scenarios<a name="ec2launchv2-troubleshooting-scenarios"></a>

This section shows common troubleshooting scenarios and steps for resolution\.

**Topics**
+ [Service fails to set the wallpaper](#ec2launchv2-troubleshooting-wallpaper)
+ [Service fails to run user data](#ec2launchv2-troubleshooting-user-data)
+ [Service runs a task only one time](#ec2launchv2-troubleshooting-task-once)
+ [Service fails to run a task](#ec2launchv2-troubleshooting-task-failed)
+ [Service runs user data more than once](#ec2launchv2-troubleshooting-user-data-more-than-once)
+ [Scheduled tasks from EC2Launch v1 fail to run after migration to EC2Launch v2](#ec2launchv2-troubleshooting-scheduled-tasks-migration)
+ [Service initializes an EBS volume that is not empty](#ec2launchv2-troubleshooting-ebs-initialize)
+ [`setWallpaper` task is not enabled but the wallpaper resets at reboot](#ec2launchv2-troubleshooting-wallpaper-resets)
+ [Service stuck in running status](#ec2launchv2-troubleshooting-service-stuck-running)
+ [Invalid `agent-config.yml` prevents opening EC2Launch v2 settings dialog box](#ec2launchv2-troubleshooting-invalid-agent-config)
+ [`task:executeScript should be unique and only invoked once`](#ec2launchv2-troubleshooting-executescript)

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

### Service runs a task only one time<a name="ec2launchv2-troubleshooting-task-once"></a>

**Resolution**

1. Check the frequency of the task\.

1. If the service already ran after Sysprep, and the task frequency is set to `once`, the task will not run again\.

1. Set the frequency of the task to `always` if you want it to run the task every time EC2Launch v2 runs\.

### Service fails to run a task<a name="ec2launchv2-troubleshooting-task-failed"></a>

**Resolution**

1. Check the latest entries in `%ProgramData%\Amazon\EC2Launch\log\agent.log`\.

1. If no errors occurred, try running the service manually from `"%ProgramFiles%\Amazon\EC2Launch\EC2Launch.exe" run` to see if the tasks succeed\.

### Service runs user data more than once<a name="ec2launchv2-troubleshooting-user-data-more-than-once"></a>

**Resolution**  
User data is handled differently between EC2Launch v1 and EC2Launch v2\. EC2Launch v1 runs user data as a scheduled task on the instance when `persist` is set to `true`\. If `persist` is set to `false`, the task is not scheduled even when it exits with a reboot or is interrupted while running\. 

EC2Launch v2 runs user data as an agent task and tracks its run state\. If user data issues a computer restart or if user data was interrupted while running, the run state persists as `pending` and the user data will run again at the next instance boot\. If you want to prevent the user data script from running more than once, make the script idempotent\. 

The following example idempotent script sets the computer name and joins a domain\.

```
<powershell>
  $name = $env:computername
  if ($name -ne $desiredName) {
    Rename-Computer -NewName $desiredName
  }
  $domain = Get-ADDomain
  if ($domain -ne $desiredDomain) 
  {
    Add-Computer -DomainName $desiredDomain
  }
  $telnet = Get-WindowsFeature -Name Telnet-Client
  if (-not $telnet.Installed)
  {
    Install-WindowsFeature -Name "Telnet-Client"
  }
</powershell>
<persist>false</persist>
```

### Scheduled tasks from EC2Launch v1 fail to run after migration to EC2Launch v2<a name="ec2launchv2-troubleshooting-scheduled-tasks-migration"></a>

**Resolution**  
The migration tool does not detect any scheduled tasks linked to EC2Launch v1 scripts; therefore, it does not automatically set up those tasks in EC2Launch v2\. To configure these tasks, edit the [`agent-config.yml`](ec2launch-v2-settings.md#ec2launch-v2-task-configuration) file, or use the [EC2Launch v2 settings dialog box](ec2launch-v2-settings.md#ec2launch-v2-ui)\. For example, if an instance has a scheduled task that runs `InitializeDisks.ps1`, then after you run the migration tool, you must specify the volumes you want to initialize in the EC2Launch v2 settings dialog box\. See Step 6 of the procedure to [Change settings using the EC2Launch v2 settings dialog box](ec2launch-v2-settings.md#ec2launch-v2-ui)\. 

### Service initializes an EBS volume that is not empty<a name="ec2launchv2-troubleshooting-ebs-initialize"></a>

**Resolution**  
Before it initializes a volume, EC2Launch v2 attempts to detect whether it is empty\. If a volume is not empty, it skips the initialization\. Any volumes that are detected as not empty are not initialized\. A volume is considered empty if the first 4 KiB of a volume are empty, or if a volume does not have a [Windows\-recognizable drive layout](https://docs.microsoft.com/en-us/windows/win32/api/winioctl/ns-winioctl-drive_layout_information_ex)\. A volume that was initialized and formatted on a Linux system does not have a Windows\-recognizable drive layout, for example MBR or GPT\. Therefore, it will be considered as empty and initialized\. If you want to preserve this data, do not rely on EC2Launch v2 empty drive detection\. Instead, specify volumes that you would like to initialize in the [EC2Launch v2 settings dialog box](ec2launch-v2-settings.md#ec2launch-v2-ui) \(see step 6\) or in the [`agent-config.yml`](ec2launch-v2-settings.md#ec2launch-v2-initializevolume)\.

### `setWallpaper` task is not enabled but the wallpaper resets at reboot<a name="ec2launchv2-troubleshooting-wallpaper-resets"></a>

The `setWallpaper` task creates the `setwallpaper.lnk` shortcut file in the startup folder of each existing user except for `Default User`\. This shortcut file runs when the user logs in for the first time after instance boot\. It sets up the instance with a custom wallpaper that displays the instance attributes\. Removing the `setWallpaper` task does not delete this shortcut file\. You must manually delete this file or delete it using a script\.

The shortcut path is:

```
$env:SystemDrive/Users/<user>/AppData/Roaming/Microsoft/Windows/Start Menu/Programs/Startup/setwallpaper.lnk
```

**Resolution**  
Manually delete this file, or delete it using a script\.

**Example PowerShell script to delete shortcut file**

```
foreach ($userDir in (Get-ChildItem "C:\Users" -Force -Directory).FullName)
{
    $startupPath = Join-Path $userDir -ChildPath "AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup"
    if (Test-Path $startupPath)
    {
        $wallpaperSetupPath = Join-Path $startupPath -ChildPath "setwallpaper.lnk"
        if (Test-Path $wallpaperSetupPath)
        {
            Remove-Item $wallpaperSetupPath -Force -Confirm:$false
        }
    }
}
```

### Service stuck in running status<a name="ec2launchv2-troubleshooting-service-stuck-running"></a>

**Description**
+ Commands that run in blocking mode, such as [sysprep](ec2launch-v2-settings.md#ec2launch-v2-settings-sysprep) and [reset](ec2launch-v2-settings.md#ec2launch-v2-reset), are blocked with output similar to the following:

  ```
  PS C:\Program Files\Amazon\EC2Launch> .\EC2Launch.exe sysprep -c -s
  'sysprep' command running in blocking mode.
  service is still running...checking again in '5.000000' seconds
  service is still running...checking again in '5.000000' seconds
  service is still running...checking again in '5.000000' seconds
  service is still running...checking again in '5.000000' seconds
  ```
+ EC2Launch is blocked with logs \(`agent.log`\) similar to the following:

  ```
  2022-02-24 08:08:58 Info: *****************************************************************************************
  2022-02-24 08:08:58 Info: EC2Launch Service starting
  2022-02-24 08:08:58 Info: Windows event custom log exists: Amazon EC2Launch
  2022-02-24 08:08:58 Info: ACPI SPCR table not supported. Bailing Out
  2022-02-24 08:08:58 Info: Serial port is in use. Waiting for Serial Port...
  2022-02-24 08:09:00 Info: ACPI SPCR table not supported. Bailing Out
  2022-02-24 08:09:02 Info: ACPI SPCR table not supported. Bailing Out
  2022-02-24 08:09:04 Info: ACPI SPCR table not supported. Bailing Out
  2022-02-24 08:09:06 Info: ACPI SPCR table not supported. Bailing Out
  ```

**Possible cause**  
SAC is enabled and using the serial port\. For more information, see [Use SAC to troubleshoot your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-sac.html#troubleshooting-sac)\.

**Resolution**  
Try the following steps to resolve this issue:
+ Disable the service that is using the serial port\.
+ If you want the service to continue to use the serial port, write custom scripts to perform launch agent tasks and invoke them as scheduled tasks\. 

### Invalid `agent-config.yml` prevents opening EC2Launch v2 settings dialog box<a name="ec2launchv2-troubleshooting-invalid-agent-config"></a>

**Description**  
EC2Launch v2 settings attempts to parse the `agent-config.yml` file before it opens the dialog box\. If the YAML configuration file does not follow the supported schema, the dialog box will show the following error:

`Unable to parse configuration file agent-config.yml. Review configuration file. Exiting application.`

**Resolution**

1. Verify that the configuration file follows the [supported schema](ec2launch-v2-settings.md#ec2launch-v2-schema-agent-config)\.

1. If you want to start from scratch, copy the default configuration file into `agent-config.yml`\. You can use the [example `agent-config.yml`](ec2launch-v2-settings.md#ec2launch-v2-example-agent-config) provided in the Task Configuration section\.

1. You can also start over by deleting `agent-config.yml`\. EC2Launch v2 settings generates an empty configuration file\.

### `task:executeScript should be unique and only invoked once`<a name="ec2launchv2-troubleshooting-executescript"></a>

**Description**  
A task cannot be repeated in the same stage\.

**Resolution**  
Some tasks must be input as an array, such as [**executeScript**](ec2launch-v2-settings.md#ec2launch-v2-executescript) and [**executeProgram**](ec2launch-v2-settings.md#ec2launch-v2-executeprogram)\. For an example of how to write the script as an array, see [**executeScript**](ec2launch-v2-settings.md#ec2launch-v2-executescript)\.

## Windows event logs<a name="ec2launchv2-windows-event-logs"></a>

EC2Launch v2 publishes Windows event logs for important events, such as service starting, Windows is ready, and task success and failure\. Event identifiers uniquely identify a particular event\. Each event contains stage, task, and level information, and a description\. You can set triggers for specific events using the event identifier\.

Event IDs provide information about an event and uniquely identify some events\. The least significant digit of an event ID indicates the severity of an event\. 


| Event | Least significant digit | 
| --- | --- | 
|  Success  | \. \. \.0 | 
| Informational | \. \. \.1 | 
| Warning | \. \. \.2 | 
| Error | \. \. \.3 | 

Service\-related events that are generated when the service starts or stops include a single digit event identifier\.


| Event | Single digit identifier | 
| --- | --- | 
|  Success  | 0 | 
| Informational | 1 | 
| Warning | 2 | 
| Error | 3 | 

The event messages for `EC2LaunchService.exe` events begin with `Service:`\. The event messages for `EC2Launch.exe` events do not begin with `Service:`\.

Four digit event IDs include information about the stage, task, and severity of an event\.

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
|  \. \. \.0  | Success | 
|  \. \. \.1  | Informational | 
|  \. \. \.2  | Warning | 
|  \. \. \.3  | Error | 
|  x  | EC2Launch service\-level logs | 
|  0  | EC2Launch service exited successfully | 
|  1  |  EC2Launch service informational logs | 
|  2  |  EC2Launch service warning logs | 
| 3 | EC2Launch service error logs | 
|  10  | Replace state\.json with previous\-state\.json | 
| 100 | Serial Port | 
| 200 | Sysprep | 
| 300 | PrimaryNic | 
| 400 | Metadata | 
|  x000  | Stage \(1 digit\), Task \(2 digits\), Status \(1 digit\) | 
|  1000  | Boot | 
|  1010  | Boot \- extend\_root\_partition | 
| 2000 | Network | 
|  2010  | Network \- add\_routes | 
|  3000  | PreReady | 
|  3010  | PreReady \- activate\_windows | 
|  3020  | PreReady \- install\_egpu\_manager | 
|  3030  | PreReady \- set\_monitor\_on | 
|  3040  | PreReady \- set\_hibernation | 
|  3050  | PreReady \- set\_admin\_account | 
|  3060  | PreReady \- set\_dns\_suffix | 
|  3070  | PreReady \- set\_wallpaper | 
|  3080  | PreReady \- set\_update\_schedule | 
|  3090  | PreReady \- output\_log | 
|  3100  | PreReady \- enable\_open\_ssh | 
|  5000  | Windows is Ready to use | 
|  6000  | PostReadyLocalData | 
| 7000 | PostReadyUserData | 
|  6010/7010  | PostReadyLocal/UserData \- set\_wallpaper | 
|  6020/7020  | PostReadyLocal/UserData \- set\_update\_schedule | 
|  6030/7030  | PostReadyLocal/UserData \- set\_hostname | 
|  6040/7040  | PostReadyLocal/UserData \- execute\_program | 
|  6050/7050  | PostReadyLocal/UserData \- execute\_script | 
|  6060/7060  | PostReadyLocal/UserData \- manage\_package | 
|  6070/7070  | PostReadyLocal/UserData \- initialize\_volume | 
|  6080/7080  | PostReadyLocal/UserData \- write\_file | 
|  6090/7090  | PostReadyLocal/UserData \- start\_ssm | 
|  7100  | PostReadyUserData \- enable\_open\_ssh | 
|  6110/7110  | PostReadyLocal/UserData \- enable\_jumbo\_frames | 

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