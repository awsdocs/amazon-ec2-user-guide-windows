# Use Sysprep to create a standard Amazon Machine Image<a name="ami-create-standard"></a>

The Microsoft System Preparation \(Sysprep\) tool simplifies the process of duplicating a customized installation of Windows\. You can use Sysprep to create a standardized Amazon Machine Image \(AMI\)\. You can then create new Amazon EC2 instances for Windows from this standardized image\.

We recommend that you use [EC2 Image Builder](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html) to automate the creation, management, and deployment of customized, secure, and up\-to\-date "golden" server images that are pre\-installed and preconfigured with software and settings\.

If you use Sysprep to create a standardized AMI, we recommend that you run Sysprep with [EC2Launch v2](ec2launch-v2.md)\. If you are still using the EC2Config \(Windows Server 2012 R2 and earlier\) or EC2Launch \(Windows Server 2016 and later\) agents, see the documentation for using Sysprep with EC2Config and EC2Launch below\.

**Important**  
Do not use Sysprep to create an instance backup\. Sysprep removes system\-specific information; removing this information might have unintended consequences for an instance backup\.

To troubleshoot Sysprep, see [Troubleshooting Sysprep](sysprep-troubleshoot.md)\.

**Topics**
+ [Before you begin](#sysprep-begin)
+ [Using Sysprep with EC2Launch v2](#sysprep-using-ec2launchv2)
+ [Using Sysprep with EC2Launch](#ec2launch-sysprep)
+ [Using Sysprep with EC2Config](#sysprep-using)

## Before you begin<a name="sysprep-begin"></a>
+ Before performing Sysprep, we recommend that you remove all local user accounts and all account profiles other than a single administrator account under which Sysprep will be executed\. If you perform Sysprep with additional accounts and profiles, unexpected behavior could result, including loss of profile data or failure to complete Sysprep\.
+ Learn more about [Sysprep](https://technet.microsoft.com/en-us/library/cc721940.aspx) on Microsoft TechNet\.
+ Learn which [server roles are supported for Sysprep](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)\.

## Using Sysprep with EC2Launch v2<a name="sysprep-using-ec2launchv2"></a>

This section contains details about the different Sysprep execution phases and the tasks performed by the EC2Launch v2 service as the image is prepared\. It also includes the steps to create a standardized AMI using Sysprep with the EC2Launch v2 service\.

**Topics**
+ [Sysprep phases](#sysprep-phases)
+ [Sysprep actions](#sysprep-actions-ec2launchv2)
+ [Post Sysprep](#sysprep-post-ec2launchv2)
+ [Run Sysprep with EC2Launch v2](#sysprep-gui-procedure-ec2launchv2)

### Sysprep phases<a name="sysprep-phases"></a>

Sysprep runs through the following phases:
+ **Generalize**: The tool removes image\-specific information and configurations\. For example, Sysprep removes the security identifier \(SID\), the computer name, the event logs, and specific drivers, to name a few\. After this phase is completed, the operating system \(OS\) is ready to create an AMI\. 
**Note**  
When you run Sysprep with the EC2Launch v2 service, the system prevents drivers from being removed because the `PersistAllDeviceInstalls` setting is set to true by default\.
+ **Specialize**: Plug and Play scans the computer and installs drivers for any detected devices\. The tool generates OS requirements, like the computer name and SID\. Optionally, you can execute commands in this phase\. 
+ **Out\-of\-Box Experience \(OOBE\)**: The system runs an abbreviated version of Windows Setup and asks you to enter information such as system language, time zone, and registered organization\. When you run Sysprep with EC2Launch v2, the answer file automates this phase\. 

### Sysprep actions<a name="sysprep-actions-ec2launchv2"></a>

Sysprep and EC2Launch v2 perform the following actions when preparing an image\.

1. When you choose **Shutdown with Sysprep** in the **EC2Launch settings** dialog box, the system runs the `ec2launch sysprep` command\.

1. EC2Launch v2 edits the content of the `unattend.xml` file by reading the registry value at `HKEY_USERS\.DEFAULT\Control Panel\International\LocaleName`\. This file is located in the following directory: `C:\ProgramData\Amazon\EC2Launch\sysprep`\.

1. The system executes the `BeforeSysprep.cmd`\. This command creates a registry key as follows:

   reg add "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Terminal Server" /v fDenyTSConnections /t REG\_DWORD /d 1 /f

   The registry key disables RDP connections until they are re\-enabled\. Disabling RDP connections is a necessary security measure because, during the first boot session after Sysprep has run, there is a short period of time where RDP allows connections and the Administrator password is blank\.

1. The EC2Launch v2 service calls Sysprep by running the following command:

   sysprep\.exe /oobe /generalize /shutdown /unattend: "C:\\ProgramData\\Amazon\\EC2Launch\\sysprep\\unattend\.xml"

#### Generalize phase<a name="sysprep-generalize-ec2launchv2"></a>
+ EC2Launch v2 removes image\-specific information and configurations, such as the computer name and the SID\. If the instance is a member of a domain, it is removed from the domain\. The `unattend.xml` answer file includes the following settings that affect this phase: 
  + **PersistAllDeviceInstalls**: This setting prevents Windows Setup from removing and reconfiguring devices, which speeds up the image preparation process because Amazon AMIs require certain drivers to run and re\-detection of those drivers would take time\.
  + **DoNotCleanUpNonPresentDevices**: This setting retains Plug and Play information for devices that are not currently present\.
+ Sysprep shuts down the OS as it prepares to create the AMI\. The system either launches a new instance or starts the original instance\.

#### Specialize phase<a name="sysprep-specialize-ec2launchv2"></a>

The system generates OS\-specific requirements, such as a computer name and an SID\. The system also performs the following actions based on configurations that you specify in the `unattend.xml` answer file\.
+ **CopyProfile**: Sysprep can be configured to delete all user profiles, including the built\-in Administrator profile\. This setting retains the built\-in Administrator account so that any customizations you make to that account are carried over to the new image\. The default value is `True`\.

  **CopyProfile** replaces the default profile with the existing local administrator profile\. All accounts that you log in to after running Sysprep receive a copy of that profile and its contents at first login\. 

  If you don’t have specific user\-profile customizations that you want to carry over to the new image, then change this setting to `False`\. Sysprep will remove all user profiles \(this saves time and disk space\)\. 
+ **TimeZone**: The time zone is set to Coordinate Universal Time \(UTC\) by default\.
+ **Synchronous command with order 1**: The system executes the following command, which enables the administrator account and specifies the password requirement:

  net user Administrator /ACTIVE:YES /LOGONPASSWORDCHG:NO /EXPIRES:NEVER /PASSWORDREQ:YES
+ **Synchronous command with order 2**: The system scrambles the administrator password\. This security measure is designed to prevent the instance from being accessible after Sysprep completes if you did not enable the `ec2setpassword` setting\. 

  C:\\Program Files\\Amazon\\Ec2ConfigService\\ScramblePassword\.exe" \-u Administrator
+ **Synchronous command with order 3**: The system executes the following command:

  C:\\Program Files\\Amazon\\Ec2ConfigService\\Scripts\\SysprepSpecializePhase\.cmd

   This command adds the following registry key, which re\-enables RDP:

  reg add "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Terminal Server" /v fDenyTSConnections /t REG\_DWORD /d 0 /f

#### OOBE phase<a name="sysprep-oobe-ec2launchv2"></a>

1. The system specifies the following configurations using the EC2Launch v2 answer file:
   + `<InputLocale>en-US</InputLocale>`
   + `<SystemLocale>en-US</SystemLocale>`
   + `<UILanguage>en-US</UILanguage>`
   + `<UserLocale>en-US</UserLocale>`
   + `<HideEULAPage>true</HideEULAPage>`
   + `<HideWirelessSetupInOOBE>true</HideWirelessSetupInOOBE>`
   + `<ProtectYourPC>3</ProtectYourPC>`
   + `<BluetoothTaskbarIconEnabled>false</BluetoothTaskbarIconEnabled>`
   + `<TimeZone>UTC</TimeZone>`
   + `<RegisteredOrganization>Amazon.com</RegisteredOrganization>`
   + `<RegisteredOwner>EC2</RegisteredOwner>`
**Note**  
During the generalize and specialize phases, EC2Launch v2 monitors the status of the OS\. If EC2Launch v2 detects that the OS is in a Sysprep phase, then it publishes the following message to the system log:  
Windows is being configured\. SysprepState=IMAGE\_STATE\_UNDEPLOYABLE

1. The system runs EC2Launch v2\.

### Post Sysprep<a name="sysprep-post-ec2launchv2"></a>

After Sysprep completes, EC2Launch v2 sends the following message to the console output:

```
Windows sysprep configuration complete.
```

EC2Launch v2 then performs the following actions:

1. Reads the content of the `agent-config.yml` file and runs configured tasks\. 

1. Executes all tasks in the `preReady` stage\.

1. After it is finished, sends a `Windows is ready` message to the instance system logs\.

1. Executes all tasks in the `PostReady` stage\.

For more information about EC2Launch v2 , see [Configuring a Windows instance using EC2Launch v2](ec2launch-v2.md)\.

### Run Sysprep with EC2Launch v2<a name="sysprep-gui-procedure-ec2launchv2"></a>

Use the following procedure to create a standardized AMI using Sysprep with EC2Launch v2\.

1. In the Amazon EC2 console, locate or [create](Creating_EBSbacked_WinAMI.md) an AMI that you want to duplicate\.

1. Launch and connect to your Windows instance\.

1. Customize it\.

1. From the Windows **Start** menu, search for and choose **Amazon EC2Launch settings**\. For more information about the options and settings in the Amazon **EC2Launch settings** dialog box, see [EC2Launch v2 settings](ec2launch-v2-settings.md)\.

1. Select **Shutdown with Sysprep** or **Shutdown without Sysprep**\.

When you are asked to confirm that you want to run Sysprep and shut down the instance, click **Yes**\. EC2Launch v2 runs Sysprep\. Next, you are logged off the instance, and the instance shuts down\. If you check the **Instances** page in the Amazon EC2 console, the instance state changes from `running` to `stopping` to `stopped`\. At this point, it's safe to create an AMI from this instance\.

You can manually invoke the Sysprep tool from the command line using the following command:

"%programfiles%\\amazon\\ec2launch\\ec2launch\.exe" sysprep

## Using Sysprep with EC2Launch<a name="ec2launch-sysprep"></a>

EC2Launch offers a default answer file and batch files for Sysprep that automate and secure the image\-preparation process on your AMI\. Modifying these files is optional\. These files are located in the following directory by default: `C:\ProgramData\Amazon\EC2-Windows\Launch\Sysprep`\.

**Important**  
Do not use Sysprep to create an instance backup\. Sysprep removes system\-specific information\. If you remove this information there might be unintended consequences for an instance backup\.

**Topics**
+ [EC2Launch answer and batch files for Sysprep](#ec2launch-sysprep-answer-batch)
+ [Running Sysprep with EC2Launch](#ec2launch-sysprep-running)
+ [Updating metadata/KMS routes for Server 2016 and later when launching a custom AMI](#update-metadata-KMS)

### EC2Launch answer and batch files for Sysprep<a name="ec2launch-sysprep-answer-batch"></a>

The EC2Launch answer file and batch files for Sysprep include the following:

`Unattend.xml`  
This is the default answer file\. If you run `SysprepInstance.ps1` or choose **ShutdownWithSysprep** in the user interface, the system reads the setting from this file\.

`BeforeSysprep.cmd`  
Customize this batch file to run commands before EC2Launch runs Sysprep\.

`SysprepSpecialize.cmd`  
Customize this batch file to run commands during the Sysprep specialize phase\.

### Running Sysprep with EC2Launch<a name="ec2launch-sysprep-running"></a>

On the full installation of Windows Server 2016 and later \(with a desktop experience\), you can run Sysprep with EC2Launch manually or by using the **EC2 Launch Settings** application\.

**To run Sysprep using the EC2Launch Settings application**

1. In the Amazon EC2 console, locate or create a Windows Server 2016 or later AMI\.

1. Launch a Windows instance from the AMI\.

1. Connect to your Windows instance and customize it\.

1. Search for and run the **EC2LaunchSettings** application\. It is located in the following directory by default: `C:\ProgramData\Amazon\EC2-Windows\Launch\Settings`\.  
![\[EC2 Launch Settings application\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launch-sysprep.png)

1. Select or clear options as needed\. These settings are stored in the `LaunchConfig.json` file\.

1. For **Administrator Password**, do one of the following:
   + Choose **Random**\. EC2Launch generates a password and encrypts it using the user's key\. The system disables this setting after the instance is launched so that this password persists if the instance is rebooted or stopped and started\.
   + Choose **Specify** and type a password that meets the system requirements\. The password is stored in `LaunchConfig.json` as clear text and is deleted after Sysprep sets the administrator password\. If you shut down now, the password is set immediately\. EC2Launch encrypts the password using the user's key\.
   + Choose **DoNothing** and specify a password in the `unattend.xml` file\. If you don't specify a password in `unattend.xml`, the administrator account is disabled\.

1. Choose **Shutdown with Sysprep**\.

**To manually run Sysprep using EC2Launch**

1. In the Amazon EC2 console locate or create a Windows Server 2016 or later Datacenter edition AMI that you want to duplicate\.

1. Launch and connect to your Windows instance\.

1. Customize the instance\.

1. Specify settings in the `LaunchConfig.json` file\. This file is located in the `C:\ProgramData\Amazon\EC2-Windows\Launch\Config` directory by default\.

   For `adminPasswordType`, specify one of the following values:  
`Random`  
EC2Launch generates a password and encrypts it using the user's key\. The system disables this setting after the instance is launched so that this password persists if the instance is rebooted or stopped and started\.  
`Specify`  
EC2Launch uses the password you specify in `adminPassword`\. If the password does not meet the system requirements, EC2Lauch generates a random password instead\. The password is stored in `LaunchConfig.json` as clear text and is deleted after Sysprep sets the administrator password\. EC2Launch encrypts the password using the user's key\.  
`DoNothing`  
EC2Launch uses the password you specify in the `unattend.xml` file\. If you don't specify a password in `unattend.xml`, the administrator account is disabled\.

1. \(Optional\) Specify settings in `unattend.xml` and other configuration files\. If plan to attend to the installation, then you don't need to make changes in these files\. The files are located in the following directory by default: `C:\ProgramData\Amazon\EC2-Windows\Launch\Sysprep`\.

1. In Windows PowerShell, run `./InitializeInstance.ps1 -Schedule`\. The script is located in the following directory, by default: `C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts`\. This script schedules the instance to initialize during the next boot\. You must run this script before you run the `SysprepInstance.ps1` script in the next step\.

1. In Windows PowerShell, run `./SysprepInstance.ps1`\. The script is located in the following directory by default: `C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts`\. 

You are logged off the instance and the instance shuts down\. If you check the **Instances** page in the Amazon EC2 console, the instance state changes from `running` to `stopping`, and then to `stopped`\. At this point, it is safe to create an AMI from this instance\.

### Updating metadata/KMS routes for Server 2016 and later when launching a custom AMI<a name="update-metadata-KMS"></a>

To update metadata/KMS routes for Server 2016 and later when launching a custom AMI, do one of the following:
+ Run the EC2LaunchSettings GUI \(C:\\ProgramData\\Amazon\\EC2\-Windows\\Launch\\Settings\\Ec2LaunchSettings\.exe\) and select the option to shut down with Sysprep\.
+ Run EC2LaunchSettings and shut down without Sysprep before creating the AMI\. This sets the EC2 Launch Initialize tasks to run at the next boot, which will set routes based on the subnet for the instance\.
+ Manually reschedule EC2 Launch initialize tasks before creating an AMI from [PowerShell](ec2launch.md#ec2launch-inittasks)\. 
**Important**  
Take note of the default password reset behavior before rescheduling tasks\.
+ To update the routes on a running instance that is experiencing Windows activation or communication with instance metadata failures, see ["Unable to activate Windows"](common-messages.md#activate-windows)\.

## Using Sysprep with EC2Config<a name="sysprep-using"></a>

This section contains details about the different Sysprep execution phases and the tasks performed by the EC2Config service as the image is prepared\. It also includes the steps to create a standardized AMI using Sysprep with the EC2Config service\.

**Topics**
+ [Sysprep phases](#sysprep-phases)
+ [Sysprep actions](#sysprep-actions)
+ [Post Sysprep](#sysprep-post)
+ [Run Sysprep with the EC2Config service](#sysprep-gui-procedure)

### Sysprep phases<a name="sysprep-phases"></a>

Sysprep runs through the following phases:
+ **Generalize**: The tool removes image\-specific information and configurations\. For example, Sysprep removes the security identifier \(SID\), the computer name, the event logs, and specific drivers, to name a few\. After this phase is completed, the operating system \(OS\) is ready to create an AMI\. 
**Note**  
When you run Sysprep with the EC2Config service, the system prevents drivers from being removed because the PersistAllDeviceInstalls setting is set to true by default\.
+ **Specialize**: Plug and Play scans the computer and installs drivers for any detected devices\. The tool generates OS requirements like the computer name and SID\. Optionally, you can execute commands in this phase\. 
+ **Out\-of\-Box Experience \(OOBE\)**: The system runs an abbreviated version of Windows Setup and asks the user to enter information such as a system language, the time zone, and a registered organization\. When you run Sysprep with EC2Config, the answer file automates this phase\. 

### Sysprep actions<a name="sysprep-actions"></a>

Sysprep and the EC2Config service perform the following actions when preparing an image\.

1. When you choose **Shutdown with Sysprep** in the **EC2 Service Properties** dialog box, the system runs the **ec2config\.exe –sysprep** command\.

1. The EC2Config service reads the content of the `BundleConfig.xml` file\. This file is located in the following directory, by default: `C:\Program Files\Amazon\Ec2ConfigService\Settings`\.

    The `BundleConfig.xml` file includes the following settings\. You can change these settings:
   + **AutoSysprep**: Indicates whether to use Sysprep automatically\. You do not need to change this value if you are running Sysprep from the EC2 Service Properties dialog box\. The default value is `No`\.
   + **SetRDPCertificate**: Sets a self\-signed certificate for the Remote Desktop server\. This enables you to securely use the Remote Desktop Protocol \(RDP\) to connect to the instance\. Change the value to `Yes` if new instances should use a certificate\. This setting is not used with Windows Server 2008 or Windows Server 2012 instances because these operating systems can generate their own certificates\. The default value is `No`\.
   + **SetPasswordAfterSysprep**: Sets a random password on a newly launched instance, encrypts it with the user launch key, and outputs the encrypted password to the console\. Change the value to `No` if new instances should not be set to a random encrypted password\. The default value is `Yes`\.
   +  **PreSysprepRunCmd**: The location of the command to run\. The command is located in the following directory, by default: `C:\Program Files\Amazon\Ec2ConfigService\Scripts\BeforeSysprep.cmd`

1. The system executes `BeforeSysprep.cmd`\. This command creates a registry key as follows:

   ```
   reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 1 /f
   ```

   The registry key disables RDP connections until they are re\-enabled\. Disabling RDP connections is a necessary security measure because, during the first boot session after Sysprep has run, there is a short period of time where RDP allows connections and the Administrator password is blank\.

1. The EC2Config service calls Sysprep by running the following command:

   ```
   sysprep.exe /unattend: "C:\Program Files\Amazon\Ec2ConfigService\sysprep2008.xml" /oobe /generalize /shutdown
   ```

#### Generalize phase<a name="sysprep-generalize"></a>
+ The tool removes image\-specific information and configurations such as the computer name and the SID\. If the instance is a member of a domain, it is removed from the domain\. The `sysprep2008.xml` answer file includes the following settings that affect this phase: 
  + **PersistAllDeviceInstalls**: This setting prevents Windows Setup from removing and reconfiguring devices, which speeds up the image preparation process because Amazon AMIs require certain drivers to run and re\-detection of those drivers would take time\.
  + **DoNotCleanUpNonPresentDevices**: This setting retains Plug and Play information for devices that are not currently present\.
+ Sysprep shuts down the OS as it prepares to create the AMI\. The system either launches a new instance or starts the original instance\.

#### Specialize phase<a name="sysprep-specialize"></a>

The system generates OS specific requirements such as a computer name and a SID\. The system also performs the following actions based on configurations that you specify in the sysprep2008\.xml answer file\.
+ **CopyProfile**: Sysprep can be configured to delete all user profiles, including the built\-in Administrator profile\. This setting retains the built\-in Administrator account so that any customizations you made to that account are carried over to the new image\. The default value is True\.

  **CopyProfile** replaces the default profile with the existing local administrator profile\. All accounts logged into after running Sysprep will receive a copy of that profile and its contents at first login\. 

  If you don’t have specific user\-profile customizations that you want to carry over to the new image then change this setting to False\. Sysprep will remove all user profiles; this saves time and disk space\. 
+ **TimeZone**: The time zone is set to Coordinate Universal Time \(UTC\) by default\.
+ **Synchronous command with order 1**: The system executes the following command that enables the administrator account and specifies the password requirement\.

  net user Administrator /ACTIVE:YES /LOGONPASSWORDCHG:NO /EXPIRES:NEVER /PASSWORDREQ:YES
+ **Synchronous command with order 2**: The system scrambles the administrator password\. This security measure is designed to prevent the instance from being accessible after Sysprep completes if you did not enable the ec2setpassword setting\. 

  C:\\Program Files\\Amazon\\Ec2ConfigService\\ScramblePassword\.exe" \-u Administrator
+ **Synchronous command with order 3**: The system executes the following command:

  C:\\Program Files\\Amazon\\Ec2ConfigService\\Scripts\\SysprepSpecializePhase\.cmd

   This command adds the following registry key, which re\-enables RDP:

  reg add "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Terminal Server" /v fDenyTSConnections /t REG\_DWORD /d 0 /f

#### OOBE phase<a name="sysprep-oobe"></a>

1. Using the EC2Config service answer file, the system specifies the following configurations:
   + <InputLocale>en\-US</InputLocale>
   + <SystemLocale>en\-US</SystemLocale>
   + <UILanguage>en\-US</UILanguage>
   + <UserLocale>en\-US</UserLocale>
   + <HideEULAPage>true</HideEULAPage>
   + <HideWirelessSetupInOOBE>true</HideWirelessSetupInOOBE>
   + <NetworkLocation>Other</NetworkLocation>
   + <ProtectYourPC>3</ProtectYourPC>
   + <BluetoothTaskbarIconEnabled>false</BluetoothTaskbarIconEnabled>
   + <TimeZone>UTC</TimeZone>
   + <RegisteredOrganization>Amazon\.com</RegisteredOrganization>
   + <RegisteredOwner>Amazon</RegisteredOwner>
**Note**  
During the generalize and specialize phases the EC2Config service monitors the status of the OS\. If EC2Config detects that the OS is in a Sysprep phase, then it publishes the following message to the system log:  
EC2ConfigMonitorState: 0 Windows is being configured\. SysprepState=IMAGE\_STATE\_UNDEPLOYABLE

1. After the OOBE phase completes, the system executes the SetupComplete\.cmd from the following location: C:\\Windows\\Setup\\Scripts\\SetupComplete\.cmd\. In Amazon public AMIs before April 2015 this file was empty and executed nothing on the image\. In public AMIs dated after April 2015, the file includes the following value: **call "C:\\Program Files\\Amazon\\Ec2ConfigService\\Scripts\\PostSysprep\.cmd"**\.

1. The system executes the PostSysprep\.cmd, which performs the following operations:
   + Sets the local Administrator password to not expire\. If the password expired, Administrators might not be able to log on\.
   + Sets the MSSQLServer machine name \(if installed\) so that the name will be in sync with the AMI\.

### Post Sysprep<a name="sysprep-post"></a>

After Sysprep completes, the EC2Config services sends the following message to the console output:

```
Windows sysprep configuration complete.
			Message: Sysprep Start
			Message: Sysprep End
```

EC2Config then performs the following actions:

1. Reads the content of the config\.xml file and lists all enabled plug\-ins\. 

1. Executes all “Before Windows is ready” plug\-ins at the same time\.
   + Ec2SetPassword
   + Ec2SetComputerName
   + Ec2InitializeDrives
   + Ec2EventLog
   + Ec2ConfigureRDP
   + Ec2OutputRDPCert
   + Ec2SetDriveLetter
   + Ec2WindowsActivate
   + Ec2DynamicBootVolumeSize

1. After it is finished, sends a “Windows is ready” message to the instance system logs\.

1. Runs all “After Windows is ready” plug\-ins at the same time\.
   + AWS CloudWatch logs 
   + UserData
   + AWS Systems Manager \(Systems Manager\) 

For more information about Windows plug\-ins, see [Configuring a Windows Instance Using the EC2Config Service](ec2config-service.md)\.

### Run Sysprep with the EC2Config service<a name="sysprep-gui-procedure"></a>

Use the following procedure to create a standardized AMI using Sysprep and the EC2Config service\.

1. In the Amazon EC2 console, locate or [create](Creating_EBSbacked_WinAMI.md) an AMI that you want to duplicate\.

1. Launch and connect to your Windows instance\.

1. Customize it\.

1. Specify configuration settings in the EC2Config service answer file:

   `C:\Program Files\Amazon\Ec2ConfigService\sysprep2008.xml`

1. From the Windows **Start** menu, choose **All Programs**, and then choose **EC2ConfigService Settings**\. 

1. Choose the **Image** tab in the **Ec2 Service Properties** dialog box\. For more information about the options and settings in the Ec2 Service Properties dialog box, see [Ec2 Service Properties](ec2config-service.md)\.

1. Select an option for the Administrator password, and then select **Shutdown with Sysprep** or **Shutdown without Sysprep**\. EC2Config edits the settings files based on the password option that you selected\.
   + **Random**: EC2Config generates a password, encrypts it with user's key, and displays the encrypted password to the console\. We disable this setting after the first launch so that this password persists if the instance is rebooted or stopped and started\.
   + **Specify**: The password is stored in the Sysprep answer file in unencrypted form \(clear text\)\. When Sysprep runs next, it sets the Administrator password\. If you shut down now, the password is set immediately\. When the service starts again, the Administrator password is removed\. It's important to remember this password, as you can't retrieve it later\.
   + **Keep Existing**: The existing password for the Administrator account doesn't change when Sysprep is run or EC2Config is restarted\. It's important to remember this password, as you can't retrieve it later\.

1. Choose **OK**\.

When you are asked to confirm that you want to run Sysprep and shut down the instance, click **Yes**\. You'll notice that EC2Config runs Sysprep\. Next, you are logged off the instance, and the instance is shut down\. If you check the **Instances** page in the Amazon EC2 console, the instance state changes from `running` to `stopping`, and then finally to `stopped`\. At this point, it's safe to create an AMI from this instance\.

You can manually invoke the Sysprep tool from the command line using the following command:

```
"%programfiles%\amazon\ec2configservice\"ec2config.exe -sysprep"" 
```

**Note**  
The double quotation marks in the command are not required if your CMD shell is already in the C:\\Program Files\\Amazon\\EC2ConfigService\\ directory\.

However, you must be very careful that the XML file options specified in the `Ec2ConfigService\Settings` folder are correct; otherwise, you might not be able to connect to the instance\. For more information about the settings files, see [EC2Config Settings Files](ec2config-service.md#UsingConfigXML_WinAMI)\. For an example of configuring and then running Sysprep from the command line, see `Ec2ConfigService\Scripts\InstallUpdates.ps1`\.