# Create a standard Amazon Machine Image using Sysprep<a name="ami-create-standard"></a>

The Microsoft System Preparation \(Sysprep\) tool simplifies the process of duplicating a customized installation of Windows\. We recommend that you use Sysprep to create a standardized Amazon Machine Image \(AMI\)\. You can then create new Amazon EC2 instances for Windows from this standardized image\.

We also recommend that you run Sysprep with EC2Launch \(Windows Server 2016 and later\) or the EC2Config service \(prior to Windows Server 2016\)\.

**Important**  
Don't use Sysprep to create an instance backup\. Sysprep removes system\-specific information; removing this information might have unintended consequences for an instance backup\.

**Topics**
+ [Before you begin](#sysprep-begin)
+ [Using Sysprep with the EC2Config service](#sysprep-using)
+ [Run Sysprep with the EC2Config service](#sysprep-gui-procedure)
+ [Troubleshooting Sysprep](#sysprep-troubleshoot)

## Before you begin<a name="sysprep-begin"></a>
+ Before performing Sysprep, we recommend that you remove all local user accounts and all account profiles other than a single administrator account under which Sysprep will be executed\. If you perform Sysprep with additional accounts and profiles, unexpected behavior could result, including loss of profile data or failure to complete Sysprep\.
+ Learn more about [Sysprep](https://technet.microsoft.com/en-us/library/cc721940.aspx) on Microsoft TechNet\.
+ Learn which [server roles are supported for Sysprep](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)\.
+ The procedures on this page apply to E2Config\. With Windows Server 2016 and later, see [Using Sysprep with EC2Launch](ec2launch.md#ec2launch-sysprep)\.

## Using Sysprep with the EC2Config service<a name="sysprep-using"></a>

Learn the details of the different Sysprep execution phases and the tasks performed by the EC2Config service as the image is prepared\. 

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

    The BundleConfig\.xml file includes the following settings\. You can change these settings:
   + **AutoSysprep**: Indicates whether to use Sysprep automatically\. You do not need to change this value if you are running Sysprep from the EC2 Service Properties dialog box\. The default value is No\.
   + **SetRDPCertificate**: Sets a self\-signed certificate for the Remote Desktop server\. This enables you to securely use the Remote Desktop Protocol \(RDP\) to connect to the instance\. Change the value to **Yes** if new instances should use a certificate\. This setting is not used with Windows Server 2008 or Windows Server 2012 instances because these operating systems can generate their own certificates\. The default value is **No**\.
   + **SetPasswordAfterSysprep**: Sets a random password on a newly launched instance, encrypts it with the user launch key, and outputs the encrypted password to the console\. Change the value to No if new instances should not be set to a random encrypted password\. The default value is Yes\.
   +  **PreSysprepRunCmd**: The location of the command to run\. The command is located in the following directory, by default: C:\\Program Files\\Amazon\\Ec2ConfigService\\Scripts\\BeforeSysprep\.cmd

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
+ The tool removes image\-specific information and configurations such as the computer name and the SID\. If the instance is a member of a domain, it is removed from the domain\. The `sysprep2008.xml` answer file includes the following settings which affect this phase: 
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
During the generalize and specialize phases the EC2Config service monitors the status of of the OS\. If EC2Config detects that the OS is in a Sysprep phase, then it publishes the following message the system log:

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

## Run Sysprep with the EC2Config service<a name="sysprep-gui-procedure"></a>

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

## Troubleshooting Sysprep<a name="sysprep-troubleshoot"></a>

If you experience problems or receive error messages during image preparations, review the following logs: 
+ %WINDIR%\\Panther\\Unattendgc
+ %WINDIR%\\System32\\Sysprep\\Panther
+ "C:\\Program Files\\Amazon\\Ec2ConfigService\\Logs\\Ec2ConfigLog\.txt"

If you receive an error message during image preparation with Sysprep, the OS might not be reachable\. To review the log files, you must stop the instance, attach its root volume to another healthy instance as a secondary volume, and then review the logs mentioned earlier on the secondary volume\. For more information about the purpose of the log files by name, see [Windows Setup\-Related Log Files](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/deployment-troubleshooting-and-log-files#windows-setup-related-log-files) in the Microsoft documentation\.

If you locate errors in the Unattendgc log file, use the [Microsoft Error Lookup Tool](https://www.microsoft.com/en-us/download/details.aspx?id=100432) to get more details about the error\. The following issue reported in the Unattendgc log file is typically the result of one or more corrupted user profiles on the instance:

```
Error [Shell Unattend] _FindLatestProfile failed (0x80070003) [gle=0x00000003]
		Error [Shell Unattend] CopyProfile failed (0x80070003) [gle=0x00000003]
```

There are two options for resolving this issue:

**Option 1:** Use Regedit on the instance to search for the following key\. Verify that there are no profile registry keys for a deleted user:

\[HKEY\_LOCAL\_MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\ProfileList\\

**Option 2:** Edit the EC2Config answer file \(`C:\Program Files\Amazon\Ec2ConfigService\sysprep2008.xml`\) and change <CopyProfile>true</CopyProfile> to <CopyProfile>false</CopyProfile>\. Run Sysprep again\. Note that this configuration change will delete the built\-in administrator user profile after Sysprep completes\.