# Configure a Windows instance using EC2Launch<a name="ec2launch"></a>

EC2Launch is a set of Windows PowerShell scripts that replaced the EC2Config service on Windows Server 2016 and 2019 AMIs\. Windows Server 2022 uses [EC2Launch v2](ec2launch-v2.md), the latest launch service for all supported Windows versions, which replaces both EC2Config and EC2Launch\.

**Topics**
+ [EC2Launch tasks](#ec2launch-tasks)
+ [Telemetry](#ec2launch-telemetry)
+ [Install the latest version of EC2Launch](ec2launch-download.md)
+ [Verify the EC2Launch version](#ec2launch-verify-version)
+ [EC2Launch directory structure](#ec2launch-directories)
+ [Configure EC2Launch](#ec2launch-config)
+ [EC2Launch version history](ec2launch-version-details.md)

## EC2Launch tasks<a name="ec2launch-tasks"></a>

EC2Launch performs the following tasks by default during the initial instance boot:
+ Sets up new wallpaper that renders information about the instance\.
+ Sets the computer name\.
+ Sends instance information to the Amazon EC2 console\.
+ Sends the RDP certificate thumbprint to the EC2 console\. 
+ Sets a random password for the administrator account\.
+ Adds DNS suffixes\.
+ Dynamically extends the operating system partition to include any unpartitioned space\.
+ Executes user data \(if specified\)\. For more information about specifying user data, see [Work with instance user data](instancedata-add-user-data.md)\.
+  Sets persistent static routes to reach the metadata service and AWS KMS servers\. 
**Important**  
 If a custom AMI is created from this instance, these routes are captured as part of the OS configuration and any new instances launched from the AMI will retain the same routes, regardless of subnet placement\. In order to update the routes, see [Update metadata/KMS routes for Server 2016 and later when launching a custom AMI](Creating_EBSbacked_WinAMI.md#update-metadata-KMS)\. 

The following tasks help to maintain backward compatibility with the EC2Config service\. You can also configure EC2Launch to perform these tasks during startup:
+ Initialize secondary EBS volumes\.
+ Send Windows Event logs to the EC2 console logs\.
+ Send the *Windows is ready to use* message to the EC2 console\.

For more information about Windows Server 2019, see [Compare Features in Windows Server Versions](https://www.microsoft.com/en-us/cloud-platform/windows-server-comparison) on Microsoft\.com\.

## Telemetry<a name="ec2launch-telemetry"></a>

Telemetry is additional information that helps AWS to better understand your requirements, diagnose issues, and deliver features to improve your experience with AWS services\.

EC2Launch version `1.3.2003463` and later collect telemetry, such as usage metrics and errors\. This data is collected from the Amazon EC2 instance on which EC2Launch runs\. This includes all Windows AMIs owned by AWS\.

The following types of telemetry are collected by EC2Launch:
+ **Usage information** — agent commands, install method, and scheduled run frequency\.
+ **Errors and diagnostic information** — agent installation and run error codes\.

Examples of collected data:

```
2021/07/15 21:44:12Z: EC2LaunchTelemetry: IsAgentScheduledPerBoot=true
2021/07/15 21:44:12Z: EC2LaunchTelemetry: IsUserDataScheduledPerBoot=true
2021/07/15 21:44:12Z: EC2LaunchTelemetry: AgentCommandCode=1
2021/07/15 21:44:12Z: EC2LaunchTelemetry: AgentCommandErrorCode=5
2021/07/15 21:44:12Z: EC2LaunchTelemetry: AgentInstallCode=2
2021/07/15 21:44:12Z: EC2LaunchTelemetry: AgentInstallErrorCode=0
```

Telemetry is enabled by default\. You can disable telemetry collection at any time\. If telemetry is enabled, EC2Launch sends telemetry data without additional customer notifications\.

Your choice to enable or disable telemetry is collected\.

You can opt in or out of telemetry collection\. Your selection to opt in or out of telemetry is collected to ensure that we adhere to your telemetry option\.

**Telemetry visibility**  
When telemetry is enabled, it appears in the Amazon EC2 console output as follows:

```
2021/07/15 21:44:12Z: Telemetry: <Data>
```

**Disable telemetry on an instance**  
To disable telemetry by setting a system environment variable, run the following command as an administrator:

```
setx /M EC2LAUNCH_TELEMETRY 0
```

To disable telemetry during installation, run `install.ps1` as follows:

```
. .\install.ps1 -EnableTelemetry:$false
```

## Verify the EC2Launch version<a name="ec2launch-verify-version"></a>

Use the following Windows PowerShell command to verify the installed version of EC2Launch\.

```
PS C:\> Test-ModuleManifest -Path "C:\ProgramData\Amazon\EC2-Windows\Launch\Module\Ec2Launch.psd1" | Select Version
```

## EC2Launch directory structure<a name="ec2launch-directories"></a>

EC2Launch is installed by default on Windows Server 2016 and later AMIs in the root directory `C:\ProgramData\Amazon\EC2-Windows\Launch`\.

**Note**  
By default, Windows hides files and folders under `C:\ProgramData`\. To view EC2Launch directories and files, you must either type the path in Windows Explorer or change the folder properties to show hidden files and folders\.

The `Launch` directory contains the following subdirectories\.
+ `Scripts` — Contains the PowerShell scripts that make up EC2Launch\.
+ `Module` — Contains the module for building scripts related to Amazon EC2\. 
+ `Config` — Contains script configuration files that you can customize\.
+ `Sysprep` — Contains Sysprep resources\.
+ `Settings` — Contains an application for the Sysprep graphical user interface\.
+ `Library` — Contains shared libraries for EC2 launch agents\.
+ `Logs` — Contains log files generated by scripts\.

All EC2Launch directories inherit their permissions from `C:\ProgramData`, with the exception of the following:
+ `C:\ProgramData\Amazon\EC2-Windows\Launch\Module\Scripts` — This folder inherits all initial permissions from `C:\ProgramData` when it is created, but removes access for normal users to `CreateFiles` in the directory\.

## Configure EC2Launch<a name="ec2launch-config"></a>

After your instance has been initialized the first time, you can configure EC2Launch to run again and perform different start\-up tasks\.

**Topics**
+ [Configure initialization tasks](#ec2launch-inittasks)
+ [Schedule EC2Launch to run on every boot](#run-on-every-boot)
+ [Initialize drives and map drive letters](#ec2launch-mapping)
+ [Send Windows event logs to the EC2 console](#ec2launch-sendlogs)
+ [Send Windows is ready message after a successful boot](#ec2launch-sendwinisready)

### Configure initialization tasks<a name="ec2launch-inittasks"></a>

Specify settings in the `LaunchConfig.json` file to enable or disable the following initialization tasks:
+ Set the computer name\.
+ Set the monitor to always stay on\.
+ Set up new wallpaper\.
+ Add DNS suffix list\.
+ Extend the boot volume size\.
+ Set the administrator password\.

**To configure initialization settings**

1. On the instance to configure, open the following file in a text editor: `C:\ProgramData\Amazon\EC2-Windows\Launch\Config\LaunchConfig.json`\.

1. Update the following settings as needed and save your changes\. Provide a password in `adminPassword` only if `adminPasswordtype` is `Specify`\.

   ```
   {
     "setComputerName": false,
     "setMonitorAlwaysOn": true,
     "setWallpaper": true,
     "addDnsSuffixList": true,
     "extendBootVolumeSize": true,
     "handleUserData": true,                      
     "adminPasswordType": "Random | Specify | DoNothing",
     "adminPassword":  "password that adheres to your security policy (optional)"
   }
   ```

   The password types are defined as follows:  
`Random`  
EC2Launch generates a password and encrypts it using the user's key\. The system disables this setting after the instance is launched so that this password persists if the instance is rebooted or stopped and started\.  
`Specify`  
EC2Launch uses the password you specify in `adminPassword`\. If the password does not meet the system requirements, EC2Launch generates a random password instead\. The password is stored in `LaunchConfig.json` as clear text and is deleted after Sysprep sets the administrator password\. EC2Launch encrypts the password using the user's key\.  
`DoNothing`  
EC2Launch uses the password you specify in the `unattend.xml` file\. If you don't specify a password in `unattend.xml`, the administrator account is disabled\.

1. In Windows PowerShell, run the following command to schedule the script to run as a Windows Scheduled Task\. The script runs one time during the next boot and then disables these tasks from running again\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -Schedule
   ```

### Schedule EC2Launch to run on every boot<a name="run-on-every-boot"></a>

You can schedule EC2Launch to run on every boot instead of only the initial boot\.

To enable EC2Launch to run on every boot: 

1. Open Windows PowerShell and run the following command:

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -SchedulePerBoot
   ```

1. Or, run the executable with the following command:

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Settings\Ec2LaunchSettings.exe
   ```

   Then select `Run EC2Launch on every boot`\. You can specify that your EC2 instance `Shutdown without Sysprep` or `Shutdown with Sysprep`\.

**Note**  
When you enable EC2Launch to run on every boot, the following happens the next time EC2Launch runs:  
If `AdminPasswordType` is still set to `Random`, EC2Launch will generate a new password at the next boot\. After that boot, `AdminPasswordType` is automatically set to `DoNothing` to prevent EC2Launch from generating new passwords on subsequent boots\. To prevent EC2Launch from generating a new password on the first boot, manually set `AdminPasswordType` to `DoNothing` before you reboot\.
`HandleUserData` will be set back to `false` unless the user data has `persist` set to `true`\. For more information about user data scripts, see [ User Data Scripts](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-user-data.html#user-data-scripts) in the Amazon EC2 User Guide\.

### Initialize drives and map drive letters<a name="ec2launch-mapping"></a>

Specify settings in the `DriveLetterMappingConfig.json` file to map drive letters to volumes on your EC2 instance\. The script initializes drives that are not already initialized and partitioned\.

**To map drive letters to volumes**

1. Open the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config\DriveLetterMappingConfig.json` file in a text editor\.

1. Specify the following volume settings and save your changes:

   ```
   {
     "driveLetterMapping": [
       {
         "volumeName": "sample volume",
         "driveLetter": "H"
       }
     ]
   }
   ```

1. Open Windows PowerShell and use the following command to run the EC2Launch script that initializes the disks:

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeDisks.ps1
   ```

   To initialize the disks each time the instance boots, add the `-Schedule` flag as follows:

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeDisks.ps1 -Schedule
   ```

### Send Windows event logs to the EC2 console<a name="ec2launch-sendlogs"></a>

Specify settings in the `EventLogConfig.json` file to send Windows Event logs to EC2 console logs\.

**To configure settings to send Windows Event logs**

1. On the instance, open the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config\EventLogConfig.json` file in a text editor\.

1. Configure the following log settings and save your changes:

   ```
   {
     "events": [
       {
         "logName": "System",
         "source": "An event source (optional)",
         "level": "Error | Warning | Information",
         "numEntries": 3
       }
     ]
   }
   ```

1. In Windows PowerShell, run the following command so that the system schedules the script to run as a Windows Scheduled Task each time the instance boots\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\SendEventLogs.ps1 -Schedule
   ```

   The logs can take three minutes or more to appear in the EC2 console logs\.

### Send Windows is ready message after a successful boot<a name="ec2launch-sendwinisready"></a>

The EC2Config service sent the "Windows is ready" message to the EC2 console after every boot\. EC2Launch sends this message only after the initial boot\. For backwards compatibility with the EC2Config service, you can schedule EC2Launch to send this message after every boot\. On the instance, open Windows PowerShell and run the following command\. The system schedules the script to run as a Windows Scheduled Task\.

```
PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\SendWindowsIsReady.ps1 -Schedule
```