# EC2Launch v2 settings<a name="ec2launch-v2-settings"></a>

This section contains information about how to configure settings for EC2Launch v2\. 

**Topics**
+ [Change settings using the EC2Launch v2 settings dialog box](#ec2launch-v2-ui)
+ [EC2Launch v2 directory structure](#ec2launch-v2-directory)
+ [Configure EC2Launch v2 using the CLI](#ec2launch-v2-cli)
+ [EC2Launch v2 task configuration](#ec2launch-v2-task-configuration)
+ [EC2Launch v2 exit codes and reboots](#ec2launch-v2-exit-codes-reboots)
+ [EC2Launch v2 and Sysprep](#ec2launch-v2-sysprep)

## Change settings using the EC2Launch v2 settings dialog box<a name="ec2launch-v2-ui"></a>

The following procedure describes how to use the EC2Launch v2 settings dialog box to enable or disable settings\.
**Note**  
If you improperly configure custom tasks in the agent\-config\.yml file, and you attempt to open the Amazon EC2Launch settings dialog box, you will receive an error\. For example schema, see [Example: `agent-config.yml`](#ec2launch-v2-example-agent-config)\.

1. Launch and connect to your Windows instance\.

1. From the Start menu, choose **All Programs**, and then navigate to **EC2Launch settings**\.   
![\[EC2 Launch settings application\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launchv2-settings.png)

1. On the **General** tab of the **EC2Launch settings** dialog box, you can enable or disable the following settings\.

   1. **Set Computer Name**

      If this setting is enabled \(it is disabled by default\), the current host name is compared to the desired host name at each boot\. If the host names do not match, the host name is reset, and the system then optionally reboots to pick up the new host name\. If a custom host name is not specified, it is generated using the hexadecimal\-formatted private IPv4 address, for example, `ip-AC1F4E6`\. To prevent your existing host name from being modified, do not enable this setting\.

   1. **Extend Boot Volume**

      This setting dynamically extends `Disk 0`/`Volume 0` to include any unpartitioned space\. This can be useful when the instance is booted from a root device volume that has a custom size\.

   1. **Set Administrator Account**

      When enabled, you can set the username and password attributes for the administrator account that is created on your local machine\. If this feature is not enabled, an administrator account is not created on the system following Sysprep\. Provide a password in `adminPassword` only if `adminPasswordtype` is `Specify`\.

      The password types are defined as follows:

      1. `Random`

         EC2Launch generates a password and encrypts it using the user's key\. The system disables this setting after the instance is launched so that this password persists if the instance is rebooted or stopped and started\.

      1. `Specify`

         EC2Launch uses the password that you specify in `adminPassword`\. If the password does not meet the system requirements, EC2Launch generates a random password instead\. The password is stored in `agent-config.yml` as clear text and is deleted after Sysprep sets the administrator password\. EC2Launch encrypts the password using the user's key\.

      1. `DoNothing`

         EC2Launch uses the password that you specify in the unattend\.xml file\. If you don't specify a password in unattend\.xml, the administrator account is disabled\.

   1. **Start SSM Service**

      When selected, the Systems Manager service is enabled to start following Sysprep\. EC2Launch v2 performs all of the tasks described [earlier](ec2launch-v2-overview.md#ec2launch-v2-tasks), and the SSM Agent processes requests for Systems Manager capabilities, such as Run Command and State Manager\.

      You can use Run Command to upgrade your existing instances to use the latest version of the EC2Launch v2 service and SSM Agent\. For more information, see [Update SSM Agent by using Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/rc-console.html#rc-console-agentexample) in the *AWS Systems Manager User Guide*\.

   1. **Optimize ENA**

      When selected, ENA settings are configured to ensure that ENA Receive Side Scaling and Receive Queue Depth settings are optimized for AWS\. For more information, see [Configure RSS CPU affinity](enhanced-networking-os.md#windows-rss-cpu-affinity)\.

   1. **Enable SSH**

      This setting enables OpenSSH for later Windows versions to allow for remote system administration\.

   1. **Enable Jumbo Frames**

      Select to enable Jumbo Frames\. Jumbo Frames can have unintended effects on your network communications, so ensure you understand how Jumbo Frames will impact your system before enabling\. For more information about Jumbo Frames, see [Jumbo frames \(9001 MTU\)](network_mtu.md#jumbo_frame_instances)\.

   1. **Prepare for Imaging**

      Select whether you want your EC2 instance to shut down with or without Sysprep\. When you want to run Sysprep with EC2Launch v2, choose **Shutdown with Sysprep**\.

1. On the **DNS Suffix** tab, you can select whether you want to add a DNS suffix list for DNS resolution of servers running in EC2, without providing the fully qualified domain name\. DNS suffixes can contain the variables `$REGION` and `$AZ`\. Only suffixes that do not already exist will be added to the list\.   
![\[EC2 Launch settings application\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launchv2-dns.png)

1. On the **Wallpaper** tab, you can enable the display of selected instance details on the wallpaper\. You also have the option of choosing a custom image\. The details are generated each time that you log in\. Clear the check box to remove instance details from the wallpaper\.  
![\[EC2 Launch settings application\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launchv2-wallpaper-02.png)

1. On the **Volumes** tab, select whether you want to initialize the volumes that are attached to the instance\. Enabling sets drive letters for any additional volumes and extends them to use available space\. If you select **All**, all of the storage volumes are initialized\. If you select **Devices**, only devices that are specified in the list are initialized\. You must enter the device for each device to be initialized\. Use the devices listed on the EC2 console, for example, `xvdb` or `/dev/nvme0n1`\. The dropdown list displays the storage volumes that are attached to the instance\. To enter a device that is not attached to the instance, enter it in the text field\.

   **Name**, **Letter**, and **Partition** are optional fields\. If no value is specified for **Partition**, storage volumes larger than 2 TB are initialized with the GPT partition type, and those smaller than 2 TB are initialized with the MBR partition type\. If devices are configured, and a non\-NTFS device either contains a partition table, or the first 4 KB of the disk contain data, then the disk is skipped and the action logged\.   
![\[EC2 Launch settings application\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launchv2-volumes.png)

The following is an example configuration YAML file created from the settings entered in the EC2Launch dialog\.

```
version: 1.0
config:
  - stage: boot
    tasks:
      - task: extendRootPartition
  - stage: preReady
    tasks:
      - task: activateWindows
        inputs:
          activation:
            type: amazon
      - task: setDnsSuffix
        inputs:
          suffixes:
            - $REGION.ec2-utilities.amazonaws.com
      - task: setAdminAccount
        inputs:
          password:
            type: random
      - task: setWallpaper
        inputs:
          path: C:\ProgramData\Amazon\EC2Launch\wallpaper\Ec2Wallpaper.jpg
          attributes:
            - hostName
            - instanceId
            - privateIpAddress
            - publicIpAddress
            - instanceSize
            - availabilityZone
            - architecture
            - memory
            - network
  - stage: postReady
    tasks:
      - task: startSsm
```

## EC2Launch v2 directory structure<a name="ec2launch-v2-directory"></a>

EC2Launch v2 should be installed in the following directories:
+ Service binaries: `%ProgramFiles%\Amazon\EC2Launch`
+ Service data \(settings, log files, and state files\): `%ProgramData%\Amazon\EC2Launch`

**Note**  
By default, Windows hides files and folders under `C:\ProgramData`\. To view EC2Launch v2 directories and files, you must either enter the path in Windows Explorer or change the folder properties to show hidden files and folders\.

The `%ProgramFiles%\Amazon\EC2Launch` directory contains binaries and supporting libraries\. It includes the following subdirectories:
+ `settings`
  + `EC2LaunchSettingsUI.exe` — user interface for modifying the `agent-config.yml` file
  + `YamlDotNet.dll` — DLL for supporting some operations in the user interface
+ `tools`
  + `ebsnvme-id.exe` — tool for examining the metadata of the EBS volumes on the instance
  + `AWSAcpiSpcrReader.exe` — tool for determining the correct COM port to use
  + `EC2LaunchEventMessage.dll` — DLL for supporting the Windows event logging for EC2Launch\.
+ `service`
  + `EC2LaunchService.exe` — Windows service executable that is launched when the launch agent runs as a service\.
+ `EC2Launch.exe` — main EC2Launch executable
+ `EC2LaunchAgentAttribution.txt` — attribution for code used within EC2 Launch

The `%ProgramData%\Amazon\EC2Launch` directory contains the following subdirectories\. All of the data produced by the service, including logs, configuration, and state, is stored in this directory\.
+ `config` — Configuration

  The service configuration file is stored in this directory as `agent-config.yml`\. This file can be updated to modify, add, or remove default tasks run by the service\. Permission to create files in this directory is restricted to the administrator account to prevent privilege escalation\.
+ `log` — Instance logs

  Logs for the service \(`agent.log`\), console \(`console.log`\), performance \(`bench.log`\), and errors \(`error.log`\) are stored in this directory\. Log files are appended to on subsequent executions of the service\.
+ `state` — Service state data

  The state that the service uses to determine which tasks should run is stored here\. There is a `.run-once` file that indicates whether the service has already run after Sysprep \(so tasks with a frequency of once will be skipped on the next run\)\. This subdirectory includes a `state.json` and `previous-state.json` to track the status of each task\.
+ `sysprep` — Sysprep

  This directory contains files that are used to determine which operations to perform by Sysprep when it creates a customized Windows AMI that can be reused\.

## Configure EC2Launch v2 using the CLI<a name="ec2launch-v2-cli"></a>

You can use the Command Line Interface \(CLI\) to configure your EC2Launch settings and manage the service\. The following section contains descriptions and usage information for the CLI commands that you can use to manage EC2Launch v2\.

**Topics**
+ [collect\-logs](#ec2launch-v2-collect-logs)
+ [get\-agent\-config](#ec2launch-v2-get-agent-config)
+ [list\-volumes](#ec2launch-v2-list-volumes)
+ [reset](#ec2launch-v2-reset)
+ [run](#ec2launch-v2-run)
+ [status](#ec2launch-v2-settings-status)
+ [sysprep](#ec2launch-v2-settings-sysprep)
+ [validate](#ec2launch-v2-validate)
+ [version](#ec2launch-v2-version)
+ [wallpaper](#ec2launch-v2-wallpaper)

### collect\-logs<a name="ec2launch-v2-collect-logs"></a>

Collects log files for EC2Launch, zips the files, and places them in a specified directory\.

**Example**

```
ec2launch collect-logs -o C:\Mylogs.zip
```

**Usage**

`ec2launch collect-logs [flags]`

**Flags**

`-h`, `--help`

help for `collect-logs`

`-o`, `--output string`

path to zipped output log files

### get\-agent\-config<a name="ec2launch-v2-get-agent-config"></a>

Prints `agent-config.yml` in the format specified \(JSON or YAML\)\. If no format is specified, `agent-config.yml` is printed in the format previously specified\. 

**Example**

```
ec2launch get-agent-config -f json
```

**Example 2**

The following PowerShell commands show how to edit and save the `agent-config` file in JSON format\.

```
$config = ec2launch get-agent-config --format json | ConvertFrom-Json
$jumboFrame =@"
{
   "task": "enableJumboFrames"
}
"@
$config.config | %{if($_.stage -eq 'postReady'){$_.tasks += (ConvertFrom-Json -InputObject $jumboFrame)}}
$config | ConvertTo-Json -Depth 6 | Out-File -encoding UTF8 $env:ProgramData/Amazon/EC2Launch/config/agent-config.yml
```

**Usage**

`ec2launch get-agent-config [flags]`

**Flags**

`-h`, `--help`

help for `get-agent-config`

`-f`, `--format string`

output format of `agent-config` file: `json`, `yaml`

### list\-volumes<a name="ec2launch-v2-list-volumes"></a>

Lists all of the storage volumes attached to the instance, including ephemeral and EBS volumes\.

**Example**

```
ec2launch list-volumes
```

**Usage**

`ec2launch list-volumes`

**Flags**

`-h`, `--help`

help for `list-volumes`

### reset<a name="ec2launch-v2-reset"></a>

Deletes the `.runonce` file so that tasks specified to run once will run on the next execution; optionally deletes the service and sysprep logs\.

**Example**

```
ec2launch reset -c
```

**Usage**

`ec2launch reset [flags]`

**Flags**

`-b`, `--block`

blocks the `reset` command until the service stops\. If the reset command is run with the `--block` flag as part of the `executeScript` task, the `detach` argument must be set to true\. For more information, see Example 4 under [executeScript ](#ec2launch-v2-executescript)\. 

`-c`, `--clean`

cleans instance logs before `reset`

`-h`, `--help`

help for `reset`

### run<a name="ec2launch-v2-run"></a>

Runs EC2Launch v2\.

**Example**

```
ec2launch run
```

**Usage**

`ec2launch run [flags]`

**Flags**

`-h`, `--help`

help for `run`

### status<a name="ec2launch-v2-settings-status"></a>

Gets the status of the EC2Launch service\. Optionally blocks the process until the service is finished\. The process exit code determines the service state:
+ `0` — the service ran and was successful\.
+ `1` — the service ran and failed\.
+ `2` — the service is still running\.
+ `3` — the service is in an unknown state\. The service state is not running or stopped\.
+ `4` — an error occurred when attempting to retrieve the service state\.
+ `5` — the service is not running and the status of the last known run is unknown\. This could mean one of the following:
  + both the `state.json` and `previous-state.json` are deleted\.
  + the `previous-state.json` is corrupted\.

  This is the service state after running the [`reset`](#ec2launch-v2-reset) command\.

**Example:**

```
ec2launch status -b
```

**Usage**

`ec2launch status [flags]`

**Flags**

`-b`,`--block`

blocks the process until the services finishes running

`-h`,`--help`

help for `status`

### sysprep<a name="ec2launch-v2-settings-sysprep"></a>

Resets the service state, updates `unattend.xml`, disables RDP, and runs Sysprep\.

**Example:**

```
ec2launch sysprep
```

**Usage**

`ec2launch sysprep [flags]`

**Flags**

`-b`,`--block`

blocks the `sysprep` command until the service stops\. If the reset command is run with the `--block` flag as part of the `executeScript` task, the `detach` argument must be set to true\. For more information, see Example 4 under [executeScript ](#ec2launch-v2-executescript)\. 

`-c`,`--clean`

cleans instance logs before `sysprep`

`-h`,`--help`

help for Sysprep

`-s`,`--shutdown`

shuts down the instance after `sysprep`

### validate<a name="ec2launch-v2-validate"></a>

Validates the `agent-config` file `C:\ProgramData\Amazon\EC2Launch\config\agent-config.yml`\.

**Example**

```
ec2launch validate
```

**Usage**

`ec2launch validate [flags]`

**Flags**

\-h` `, `--help`

help for `validate`

### version<a name="ec2launch-v2-version"></a>

Gets the executable version\.

**Example**

```
ec2launch version
```

**Usage**

`ec2launch version [flags]`

**Flags**

`-h`, `--help`

help for `version`

### wallpaper<a name="ec2launch-v2-wallpaper"></a>

Sets new wallpaper to the wallpaper path that is provided \(\.jpg file\), and displays the selected instance details\.

**Example**

```
ec2launch wallpaper ^
--path="C:\ProgramData\Amazon\EC2Launch\wallpaper\Ec2Wallpaper.jpg" ^
--attributes=hostName,instanceId,privateIpAddress,publicIpAddress,instanceSize,availabilityZone,architecture,memory,network
```

**Usage**

`ec2launch wallpaper [flags]`

**Flags**

`--attributes strings`

`wallpaper` attributes

`-h`, `--help`

help for `wallpaper`

`-p`, `--path string`

`wallpaper` file path

## EC2Launch v2 task configuration<a name="ec2launch-v2-task-configuration"></a>

This section includes the configuration tasks, details, and examples for `agent-config.yml` and user data\.

**Topics**
+ [activateWindows](#ec2launch-v2-activatewindows)
+ [enableJumboFrames](#ec2launch-v2-enablejumboframes)
+ [enableOpenSsh](#ec2launch-v2-enableopenssh)
+ [executeProgram](#ec2launch-v2-executeprogram)
+ [executeScript](#ec2launch-v2-executescript)
+ [extendRootPartition](#ec2launch-v2-extendrootpartition)
+ [initializeVolume](#ec2launch-v2-initializevolume)
+ [optimizeEna](#ec2launch-v2-optimizeena)
+ [setAdminAccount](#ec2launch-v2-setadminaccount)
+ [setDnsSuffix](#ec2launch-v2-setdnssuffix)
+ [setHostName](#ec2launch-v2-sethostname)
+ [setWallpaper](#ec2launch-v2-setwallpaper)
+ [startSsm](#ec2launch-v2-startssm)
+ [sysprep](#ec2launch-v2-task-sysprep)
+ [writeFile](#ec2-launch-v2-writefile)
+ [Example: `agent-config.yml`](#ec2launch-v2-example-agent-config)
+ [Example: user data](#ec2launch-v2-example-user-data)

### activateWindows<a name="ec2launch-v2-activatewindows"></a>

Activates Windows against a set of AWS KMS servers\.

*Frequency* — once

*AllowedStages* — `[PreReady]`

*Inputs* — 

`activation`: \(map\)

`type`: \(string\) activation type to use, set to `amazon`

*Example*

```
task: activateWindows
inputs:
  activation:
    type: amazon
```

### enableJumboFrames<a name="ec2launch-v2-enablejumboframes"></a>

Enables Jumbo Frames, which increase the maximum transmission unit \(MTU\) of the network adapter\. For more information, see [Jumbo frames \(9001 MTU\)](network_mtu.md#jumbo_frame_instances)\.

*Frequency* — always

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — none

*Example*

```
task: enableJumboFrames
```

### enableOpenSsh<a name="ec2launch-v2-enableopenssh"></a>

Enables Windows OpenSSH and adds the public key for the instance to the authorized keys folder\.

*Frequency* — once

*AllowedStages* — `[PreReady, UserData]`

*Inputs* — none

*Example*

The following example shows how to enable OpenSSH on an instance, and to add the public key for the instance to the authorized keys folder\. This configuration works only on instances running Windows Server 2019 and later versions\.

```
task: enableOpenSsh
```

### executeProgram<a name="ec2launch-v2-executeprogram"></a>

Executes a program with optional arguments and a specified frequency\.

*Frequency* — see *Inputs*

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — 

`frequency`: \(string\) one of `once` or `always`

`path`: \(string\) path to the executable

`arguments`: \(list of strings\) list of string arguments to pass to the executable

`runAs`: \(string\) must be set to `localSystem`

*Example*

The following example shows how to run an executable file that is already on an instance\. 

```
task: executeProgram
inputs:
- frequency: always
  path: C:\Users\Administrator\Desktop\setup.exe
  arguments: [‘—quiet']
```

*Example 2*

The following example shows how to run an executable file that is already on an instance\. This configuration installs a VLC `.exe` file that is present on the `C:` drive of the instance\. `/L=1033` and `/S` are VLC arguments passed as a string list with the VLC `.exe` file\.

```
task: executeProgram
inputs:
- frequency: always
  path: C:\vlc-3.0.11-win64.exe 
  arguments: ['/L=1033','/S']
  runAs: localSystem
```

### executeScript<a name="ec2launch-v2-executescript"></a>

Runs a script with optional arguments and a specified frequency\.

*Frequency* — see *Inputs*

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — 

`frequency`: \(string\) one of `once` or `always`

`type`: \(string\) one of `batch` or `powershell`

`arguments`: \(list of strings\) list of string arguments to pass to the shell\. This parameter is not supported when `type` is set to `batch`\.

`content`: \(string\) contents of the script

`runAs`: \(string\) one of `admin` or `localSystem`

`detach`: \(Boolean\) defaults to `false`\. Set to `true` if the script should run in detached mode, where EC2Launch runs it concurrently with other tasks\. Script exit codes \(including `3010`\) have no effect when `detach` is set to `true`\.

*Example*

```
task: executeScript
inputs:
- frequency: always
  type: powershell
  content: |
    Get-Process | Out-File -FilePath .\Process.txt
  runAs: localSystem
```

*Example 2*

The following example shows how to run a PowerShell script on an EC2 instance\. This configuration creates a text file in the `C:` drive\.

```
task: executeScript
inputs:
- frequency: always
  type: powershell
  runAs: admin
  content: |-
    New-Item -Path 'C:\PowerShellTest.txt' -ItemType File
    Set-Content 'C:\PowerShellTest.txt' "hello world"
```

*Example 3*

The following example shows an idempotent script that reboots an instance multiple times\.

```
task: executeScript
inputs:
- frequency: always
  type: powershell
  runAs: localSystem
  content: |-
    $name = $env:ComputerName
     if ($name -ne $desiredName) {
       Rename-Computer -NewName $desiredName
       exit 3010
     }
     $domain = Get-ADDomain
     if ($domain -ne $desiredDomain) 
     {
       Add-Computer -DomainName $desiredDomain
       exit 3010
     }
    $telnet = Get-WindowsFeature -Name Telnet-Client
    if (-not $telnet.Installed)
    {
      Install-WindowsFeature -Name "Telnet-Client"
      exit 3010 
    }
```

*Example 4*

You can run EC2Launch v2 CLI commands as part of scripts\. `reset` and `sysprep` commands must include the `--block` flag because they depend on the agent finishing first\. When the `--block` flag is used, the `detach` argument for this task must be set to true\. A deadlock results when you use the `--block` flag in a non\-detached script\. The commands detect the potential deadlock and exit with an error\. The following example shows a script that resets the agent state after the agent finishes running\.

```
task: executeScript
inputs:
- frequency: always
  type: powershell
  runAs: localSystem
  detach: true
  content: |-
    & 'C:\Program Files\Amazon\EC2Launch\ec2launch.exe' reset -c -b
```

### extendRootPartition<a name="ec2launch-v2-extendrootpartition"></a>

Extends the root volume to use all of the available space on the disk\.

*Frequency* — once

*AllowedStages* — `[Boot]`

*Inputs* — none

*Example* 

```
task: extendRootParitition
```

### initializeVolume<a name="ec2launch-v2-initializevolume"></a>

Initializes volumes attached to the instance so that they are activated and partitioned\. Any volumes that are detected as not empty are not initialized\. A volume is considered empty if the first 4 KiB of a volume are empty, or if a volume does not have a [Windows\-recognizable drive layout](https://docs.microsoft.com/en-us/windows/win32/api/winioctl/ns-winioctl-drive_layout_information_ex)\. The volume `letter` field is always applied when this task runs, regardless of whether the drive is already initialized\.

*Frequency* — always

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — 

`initialize`: \(string\) type of initialization strategy to use; one of `all` or `devices`

`devices`: \(list of maps\)

`device`: device identifier used when creating the instance; some examples are `xvdb`, `xvdf`, or `/dev/nvme0n1`

`name`: \(string\) drive name to assign

`letter`: \(string\) drive letter to assign

`partition`: \(string\) partitioning type to use; one of mbr or gpt

*Example 1* 

The following example shows inputs for the `InitializeVolume` task to set selected volumes to be initialized\.

```
task: initializeVolume
inputs:
  initialize: devices
  devices:
  - device: xvdb
    name: MyVolumeOne
    letter: D
    partition: mbr
  - device: /dev/nvme0n1
    name: MyVolumeTwo
    letter: E
    partition: gpt
```

*Example 2*

The following example shows how to initialize EBS volumes that are attached to an instance\. This configuration will initialize all empty EBS volumes that are attached to the instance\. If a volume is not empty, then it will not be initialized\.

```
task: initializeVolume
inputs:
  initialize: all
```

### optimizeEna<a name="ec2launch-v2-optimizeena"></a>

Optimizes ENA settings based on the current instance type; might reboot the instance\.

*Frequency* — always

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — none

*Example* 

```
task: optimizeEna
```

### setAdminAccount<a name="ec2launch-v2-setadminaccount"></a>

Sets attributes for the default administrator account that is created on the local machine\.

*Frequency* — once

*AllowedStages* — `[PreReady]`

*Inputs* — 

`name`: \(string\) name of the administrator account

`password`: \(map\)

`type`: \(string\) strategy to set the password, either as `static`, `random`, or `doNothing`

`data`: \(string\) stores data if the `type` field is static

*Example*

```
task: setAdminAccount
inputs:
  name: Administrator
  password:
    type: random
```

### setDnsSuffix<a name="ec2launch-v2-setdnssuffix"></a>

Adds DNS suffixes to the list of search suffixes\. Only suffixes that do not already exist are added to the list\.

*Frequency* — always

*AllowedStages* — `[PreReady]`

*Inputs* — 

`suffixes`: \(list of strings\) list of one or more valid DNS suffixes; valid substitution variables are `$REGION` and `$AZ`

*Example*

```
task: setDnsSuffix
inputs:
  suffixes:
  - $REGION.ec2-utilities.amazonaws.com
```

### setHostName<a name="ec2launch-v2-sethostname"></a>

Sets the hostname of the computer to a custom string or, if `hostName` is not specified, the private IPv4 address\.

*Frequency* — always

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — 

`hostName`: \(string\) optional host name, which must be formatted as follows\.
+ Must be 15 characters or less
+ Must contain only alphanumeric \(a\-z, A\-Z, 0\-9\) and hyphen \(\-\) characters\.
+ Must not consist entirely of numerical characters\.

`reboot`: \(boolean\) denotes whether a reboot is permitted when the hostname is changed

*Example*

```
task: setHostName
inputs:
  reboot: true
```

### setWallpaper<a name="ec2launch-v2-setwallpaper"></a>

Creates the `setwallpaper.lnk` shortcut file in the startup folder of each existing user except for `Default User`\. This shortcut file runs when the user logs in for the first time after instance boot\. It sets up the instance with a custom wallpaper that displays the instance attributes\. 

The shortcut path is:

```
$env:SystemDrive/Users/<user>/AppData/Roaming/Microsoft/Windows/Start Menu/Programs/Startup/setwallpaper.lnk
```

 Note that removing the `setWallpaper` task does not delete this shortcut file\. For more information, see [`setWallpaper` task is not enabled but the wallpaper resets at reboot](ec2launchv2-troubleshooting.md#ec2launchv2-troubleshooting-wallpaper-resets)\.

*Frequency* — always

*AllowedStages* — `[PreReady, UserData]`

*Inputs* — 

`path`: \(string\) path to a local \.jpg file to use as the wallpaper image

`attributes`: \(list of strings\) list of attributes to add to the wallpaper; one of `hostName`, `instanceId`, `privateIpAddress`, `publicIpAddress`, `instanceSize`, `availabilityZone`, `architecture`, `memory`, or `network`

*Example*

```
task: setWallpaper
inputs:
  path: C:\ProgramData\Amazon\EC2Launch\wallpaper\Ec2Wallpaper.jpg
  attributes:
  - hostName
  - instanceId
  - privateIpAddress
  - publicIpAddress
```

### startSsm<a name="ec2launch-v2-startssm"></a>

Starts the Systems Manager \(SSM\) service following Sysprep\.

*Frequency* — always

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — none

*Example*

```
task: startSsm
```

### sysprep<a name="ec2launch-v2-task-sysprep"></a>

Resets the service state, updates `unattend.xml`, disables RDP, and runs Sysprep\. This task runs only after all other tasks are completed\.

*Frequency* — once

*AllowedStages* — `[UserData]`

*Inputs* — 

`clean`: \(boolean\) cleans instance logs before running Sysprep

`shutdown`: \(boolean\) shuts down the instance after running Sysprep

*Example*

```
task: sysprep
inputs:
  clean: true
  shutdown: true
```

### writeFile<a name="ec2-launch-v2-writefile"></a>

Writes a file to a destination\.

*Frequency* — see *Inputs*

*AllowedStages* — `[PostReady, UserData]`

*Inputs* — 

`frequency`: \(string\) one of `once` or `always`

`destination`: \(string\) path to which to write the content

`content`: \(string\) text to write to the destination

*Example*

```
task: writeFile
inputs:
- frequency: once
  destination: C:\Users\Administrator\Desktop\booted.txt
  content: Windows Has Booted
```

### Example: `agent-config.yml`<a name="ec2launch-v2-example-agent-config"></a>

The following example shows settings for the `agent-config.yml` configuration file\.

```
version: 1.0
config:
- stage: boot
  tasks:
  - task: extendRootPartition
- stage: preReady
  tasks:
  - task: activateWindows
    inputs:
      activation:
        type: amazon
  - task: setDnsSuffix
    inputs:
      suffixes:
      - $REGION.ec2-utilities.amazonaws.com
  - task: setAdminAccount
    inputs:
      password:
        type: random
  - task: setWallpaper
    inputs:
      path: C:\ProgramData\Amazon\EC2Launch\wallpaper\Ec2Wallpaper.jpg
      attributes:
      - hostName
      - instanceId
      - privateIpAddress
      - publicIpAddress
      - instanceSize
      - availabilityZone
      - architecture
      - memory
      - network
- stage: postReady
  tasks:
  - task: startSsm
```

### Example: user data<a name="ec2launch-v2-example-user-data"></a>

For more information about user data, see [Run commands on your Windows instance at launch](ec2-windows-user-data.md)\.

The following example shows settings for user data\.

```
version: 1.0
tasks:
- task: executeScript
  inputs:
  - frequency: always
    type: powershell
    runAs: localSystem
    content: |-
      New-Item -Path 'C:\PowerShellTest.txt' -ItemType File
```

The following format is compatible with the previous version of this service\. It is run as an `executeScript` task in the `UserData` stage\. To mimic the behavior of the previous version, it will be set to run as a detached process\.

```
<powershell>
  $file = $env:SystemRoot + "\Temp" + (Get-Date).ToString("MM-dd-yy-hh-mm")
  New-Item $file -ItemType file
</powershell>
<persist>true</persist>
```

## EC2Launch v2 exit codes and reboots<a name="ec2launch-v2-exit-codes-reboots"></a>

You can use EC2Launch v2 to define how exit codes are handled by your scripts\. By default, the exit code of the last command that is run in a script is reported as the exit code for the entire script\. For example, if a script includes three commands and the first command fails but the following ones succeed, the run status is reported as `success` because the final command succeeded\.

If you want a script to reboot an instance, then you must specify `exit 3010` in your script, even when the reboot is the last step in your script\. `exit 3010` instructs EC2Launch v2 to reboot the instance and call the script again until it returns an exit code that is not `3010`, or until the maximum reboot count has been reached\. EC2Launch v2 permits a maximum of 5 reboots per task\. If you attempt to reboot an instance from a script by using a different mechanism, such as `Restart-Computer`, then the script run status will be inconsistent\. For example, it may get stuck in a restart loop or not perform the restart\.

If you are using a legacy user data format that is compatible with older agents, the user data may run more times than you intend it to\. For more information, see [Service runs user data more than once](ec2launchv2-troubleshooting.md#ec2launchv2-troubleshooting-user-data-more-than-once) in the Troubleshooting section\.

## EC2Launch v2 and Sysprep<a name="ec2launch-v2-sysprep"></a>

The EC2Launch v2 service runs Sysprep, a Microsoft tool that enables you to create a customized Windows AMI that can be reused\. When EC2Launch v2 calls Sysprep, it uses the files in `%ProgramData%\Amazon\EC2Launch` to determine which operations to perform\. You can edit these files indirectly using the **EC2Launch settings** dialog box, or directly using a YAML editor or a text editor\. However, there are some advanced settings that aren't available in the **EC2Launch settings** dialog box, so you must edit those entries directly\.

If you create an AMI from an instance after updating its settings, the new settings are applied to any instance that's launched from the new AMI\. For information about creating an AMI, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.