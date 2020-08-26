# Common messages troubleshooting Windows instances<a name="common-messages"></a>

This section includes tips to help you troubleshoot issues based on common messages\.

**Topics**
+ ["Password is not available"](#password-not-available)
+ ["Password not available yet"](#password-not-ready)
+ ["Cannot retrieve Windows password"](#cannot-retrieve-password)
+ ["Waiting for the metadata service"](#metadata-unavailable)
+ ["Unable to activate Windows"](#activate-windows)
+ ["Windows is not genuine \(0x80070005\)"](#windows-not-genuine)
+ ["No Terminal Server License Servers available to provide a license"](#no-license-servers)
+ ["Some settings are managed by your organization" \(Windows Server 2019\)](#some-settings-managed-by-org)

## "Password is not available"<a name="password-not-available"></a>

To connect to a Windows instance using Remote Desktop, you must specify an account and password\. The accounts and passwords provided are based on the AMI that you used to launch the instance\. You can either retrieve the auto\-generated password for the Administrator account, or use the account and password that were in use in the original instance from which the AMI was created\.

If your Windows instance isn't configured to generate a random password, you'll receive the following message when you retrieve the auto\-generated password using the console:

```
Password is not available.
The instance was launched from a custom AMI, or the default password has changed. A
password cannot be retrieved for this instance. If you have forgotten your password, you can
reset it using the Amazon EC2 configuration service. For more information, see Passwords for a
Windows Server instance.
```

Check the console output for the instance to see whether the AMI that you used to launch it was created with password generation disabled\. If password generation is disabled, the console output contains the following:

```
Ec2SetPassword: Disabled
```

If password generation is disabled and you don't remember the password for the original instance, you can reset the password for this instance\. For more information, see [Resetting a lost or expired Windows administrator password](ResettingAdminPassword.md)\.

## "Password not available yet"<a name="password-not-ready"></a>

To connect to a Windows instance using Remote Desktop, you must specify an account and password\. The accounts and passwords provided are based on the AMI that you used to launch the instance\. You can either retrieve the auto\-generated password for the Administrator account, or use the account and password that were in use in the original instance from which the AMI was created\.

Your password should be available within a few minutes\. If the password isn't available, you'll receive the following message when you retrieve the auto\-generated password using the console:

```
Password not available yet.
Please wait at least 4 minutes after launching an instance before trying to retrieve the 
auto-generated password.
```

If it's been longer than four minutes and you still can't get the password, it's possible that EC2Config is disabled\. Verify by checking whether the console output is empty\. For more information, see [Unable to get console output](common-issues.md#no-console-output)\.

Also verify that the AWS Identity and Access Management \(IAM\) account being used to access the Management Portal has the `ec2:GetPasswordData` action allowed\. For more information about IAM permissions, see [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\.

## "Cannot retrieve Windows password"<a name="cannot-retrieve-password"></a>

To retrieve the auto\-generated password for the Administrator account, you must use the private key for the key pair that you specified when you launched the instance\. If you didn't specify a key pair when you launched the instance, you'll receive the following message\.

```
Cannot retrieve Windows password
```

You can terminate this instance and launch a new instance using the same AMI, making sure to specify a key pair\.

## "Waiting for the metadata service"<a name="metadata-unavailable"></a>

A Windows instance must obtain information from its instance metadata before it can activate itself\. By default, the `WaitForMetaDataAvailable` setting ensures that the EC2Config service waits for the instance metadata to be accessible before continuing with the boot process\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md)\.

If the instance is failing the instance reachability test, try the following to resolve this issue\.
+ Check the CIDR block for your VPC\. A Windows instance cannot boot correctly if it's launched into a VPC that has an IP address range from `224.0.0.0` to `255.255.255.255` \(Class D and Class E IP address ranges\)\. These IP address ranges are reserved, and should not be assigned to host devices\. We recommend that you create a VPC with a CIDR block from the private \(non\-publicly routable\) IP address ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.
+ It's possible that the system has been configured with a static IP address\. Try [creating a network interface](using-eni.md#create_eni) and [attaching it to the instance](using-eni.md#attach_eni_running_stopped)\.
+ 

**To enable DHCP on a Windows instance that you can't connect to**

  1. Stop the affected instance and detach its root volume\.

  1. Launch a temporary instance in the same Availability Zone as the affected instance\.
**Warning**  
If your temporary instance is based on the same AMI that the original instance is based on, you must complete additional steps or you won't be able to boot the original instance after you restore its root volume because of a disk signature collision\. Alternatively, select a different AMI for the temporary instance\. For example, if the original instance uses the AWS Windows AMI for Windows Server 2008 R2, launch the temporary instance using the AWS Windows AMI for Windows Server 2012\.

  1. Attach the root volume from the affected instance to this temporary instance\. Connect to the temporary instance, open the **Disk Management** utility, and bring the drive online\.

  1. From the temporary instance, open **Regedit** and select **HKEY\_LOCAL\_MACHINE**\. From the **File** menu, choose **Load Hive**\. Select the drive, open the file `Windows\System32\config\SYSTEM`, and specify a key name when prompted \(you can use any name\)\.

  1. Select the key that you just loaded and navigate to `ControlSet001\Services\Tcpip\Parameters\Interfaces`\. Each network interface is listed by a GUID\. Select the correct network interface\. If DHCP is disabled and a static IP address assigned, `EnableDHCP` is set to 0\. To enable DHCP, set `EnableDHCP` to 1, and delete the following keys if they exist: `NameServer`, `SubnetMask`, `IPAddress`, and `DefaultGateway`\. Select the key again, and from the **File** menu, choose **Unload Hive**\.
**Note**  
If you have multiple network interfaces, you'll need to identify the correct interface to enable DHCP\. To identify the correct network interface, review the following key values `NameServer`, `SubnetMask`, `IPAddress`, and `DefaultGateway`\. These values display the static configuration of the previous instance\. 

  1. \(Optional\) If DHCP is already enabled, it's possible that you don't have a route to the metadata service\. Updating EC2Config can resolve this issue\.

     1. [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information about installing this service, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\. 

     1. Extract the files from the `.zip` file to the `Temp` directory on the drive you attached\.

     1. Open **Regedit** and select **HKEY\_LOCAL\_MACHINE**\. From the **File** menu, choose **Load Hive**\. Select the drive, open the file `Windows\System32\config\SOFTWARE`, and specify a key name when prompted \(you can use any name\)\.

     1. Select the key that you just loaded and navigate to `Microsoft\Windows\CurrentVersion`\. Select the `RunOnce` key\. \(If this key doesn't exist, right\-click `CurrentVersion`, point to **New**, select **Key**, and name the key `RunOnce`\.\) Right\-click, point to **New**, and select **String Value**\. Enter `Ec2Install` as the name and `C:\Temp\Ec2Install.exe -q` as the data\.

     1. Select the key again, and from the **File** menu, choose **Unload Hive**\.

  1. \(Optional\) If your temporary instance is based on the same AMI that the original instance is based on, you must complete the following steps or you won't be able to boot the original instance after you restore its root volume because of a disk signature collision\.
**Warning**  
The following procedure describes how to edit the Windows Registry using Registry Editor\. If you are not familiar with the Windows Registry or how to safely make changes using Registry Editor, see [Configure the Registry](https://technet.microsoft.com/en-us/library/cc725612.aspx)\.

     1. Open a command prompt, type regedit\.exe, and press Enter\.

     1. In the **Registry Editor**, choose **HKEY\_LOCAL\_MACHINE** from the context menu \(right\-click\), and then choose **Find**\.

     1. Type Windows Boot Manager and then choose **Find Next**\.

     1. Choose the key named `11000001`\. This key is a sibling of the key you found in the previous step\.

     1. In the right pane, choose `Element` and then choose **Modify** from the context menu \(right\-click\)\.

     1. Locate the four\-byte disk signature at offset 0x38 in the data\. Reverse the bytes to create the disk signature, and write it down\. For example, the disk signature represented by the following data is `E9EB3AA5`:

        ```
        ...
        0030  00 00 00 00 01 00 00 00
        0038  A5 3A EB E9 00 00 00 00
        0040  00 00 00 00 00 00 00 00
        ...
        ```

     1. In a Command Prompt window, run the following command to start Microsoft DiskPart\.

        ```
        diskpart
        ```

     1. Run the following DiskPart command to select the volume\. \(You can verify that the disk number is 1 using the **Disk Management** utility\.\)

        ```
        DISKPART> select disk 1
        
        Disk 1 is now the selected disk.
        ```

     1. Run the following DiskPart command to get the disk signature\.

        ```
        DISKPART>  uniqueid disk
        
        Disk ID: 0C764FA8
        ```

     1. If the disk signature shown in the previous step doesn't match the disk signature from BCD that you wrote down earlier, use the following DiskPart command to change the disk signature so that it matches:

        ```
        DISKPART> uniqueid disk id=E9EB3AA5
        ```

  1. Using the **Disk Management** utility, bring the drive offline\.
**Note**  
The drive is automatically offline if the temporary instance is running the same operating system as the affected instance, so you won't need to bring it offline manually\.

  1. Detach the volume from the temporary instance\. You can terminate the temporary instance if you have no further use for it\.

  1. Restore the root volume of the affected instance by attaching the volume as `/dev/sda1`\.

  1. Start the affected instance\.

If you are connected to the instance, open an Internet browser from the instance and enter the following URL for the metadata server:

```
http://169.254.169.254/latest/meta-data/
```

If you can't contact the metadata server, try the following to resolve the issue:
+ [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information about installing this service, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\. 
+ Check whether the Windows instance is running RedHat PV drivers\. If so, update to Citrix PV drivers\. For more information, see [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)\.
+ Verify that the firewall, IPSec, and proxy settings do not block outgoing traffic to the metadata service \(`169.254.169.254`\) or the KMS servers \(the addresses are specified in `TargetKMSServer` elements in `C:\Program Files\Amazon\Ec2ConfigService\Settings\ActivationSettings.xml`\)\.
+ Verify that you have a route to the metadata service \(`169.254.169.254`\) using the following command\.

  ```
  route print
  ```
+ Check for network issues that might affect the Availability Zone for your instance\. Go to [http://status\.aws\.amazon\.com/](http://status.aws.amazon.com/)\.

## "Unable to activate Windows"<a name="activate-windows"></a>

Windows instances use Windows KMS activation\. You can receive this message: `A problem occurred when Windows tried to activate. Error Code 0xC004F074`, if your instance can't reach the KMS server\. Windows must be activated every 180 days\. EC2Config attempts to contact the KMS server before the activation period expires to ensure that Windows remains activated\.

If you encounter a Windows activation issue, use the following procedure to resolve the issue\.

**For EC2Config \(Windows Server 2012 R2 AMIs and earlier\)**

1. [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information about installing this service, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\. 

1. Log onto the instance and open the following file: `C:\Program Files\Amazon\Ec2ConfigService\Settings\config.xml`\.

1. Locate the **Ec2WindowsActivate** plugin in the `config.xml` file\. Change the state to **Enabled** and save your changes\.

1. In the Windows Services snap\-in, restart the EC2Config service or reboot the instance\.

If this does not resolve the activation issue, follow these additional steps\.

1. Set the KMS target: C:\\> slmgr\.vbs /skms 169\.254\.169\.250:1688

1. Activate Windows: C:\\> slmgr\.vbs /ato

**For EC2Launch \(Windows Server 2016 AMIs and later\)**

1. Import the EC2Launch module:

   ```
   PS C:\> Import-Module "C:\ProgramData\Amazon\EC2-Windows\Launch\Module\Ec2Launch.psd1"
   ```

1. Call the Add\-Routes function:

   ```
   PS C:\> Add-Routes
   ```

1. Call the Set\-ActivationSettings function:

   ```
   PS C:\> Set-Activationsettings
   ```

1. Then, run the following script to activate Windows:

   ```
   PS C:\> cscript "${env:SYSTEMROOT}\system32\slmgr.vbs" /ato
   ```

 For both EC2Config and EC2Launch, if you are still receiving an activation error, verify the following information\.
+ Verify that you have routes to the KMS servers\. Open `C:\Program Files\Amazon\Ec2ConfigService\Settings\ActivationSettings.xml` and locate the `TargetKMSServer` elements\. Run the following command and check whether the addresses for these KMS servers are listed\.

  ```
  route print
  ```
+ Verify that the KMS client key is set\. Run the following command and check the output\.

  ```
  C:\Windows\System32\slmgr.vbs /dlv
  ```

  If the output contains Error: product key not found, the KMS client key isn't set\. If the KMS client key isn't set, look up the client key as described in this Microsoft article: [KMS Client Setup Keys](https://docs.microsoft.com/en-us/windows-server/get-started/kmsclientkeys), and then run the following command to set the KMS client key\.

  ```
  C:\Windows\System32\slmgr.vbs /ipk client_key
  ```
+ Verify that the system has the correct time and time zone\. If you are using Windows Server 2008 or later and a time zone other than UTC, add the following registry key and set it to `1` to ensure that the time is correct: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\RealTimeIsUniversal`\.
+ If Windows Firewall is enabled, temporarily disable it using the following command\.

  ```
  netsh advfirewall set allprofiles state off
  ```

## "Windows is not genuine \(0x80070005\)"<a name="windows-not-genuine"></a>

Windows instances use Windows KMS activation\. If an instance is unable to complete the activation process, it reports that the copy of Windows is not genuine\.

Try the suggestions for ["Unable to activate Windows"](#activate-windows)\.

## "No Terminal Server License Servers available to provide a license"<a name="no-license-servers"></a>

By default, Windows Server is licensed for two simultaneous users through Remote Desktop\. If you need to provide more than two users with simultaneous access to your Windows instance through Remote Desktop, you can purchase a Remote Desktop Services client access license \(CAL\) and install the Remote Desktop Session Host and Remote Desktop Licensing Server roles\.

Check for the following issues:
+ You've exceeded the maximum number of concurrent RDP sessions\.
+ You've installed the Windows Remote Desktop Services role\.
+ Licensing has expired\. If the licensing has expired, you can't connect to your Windows instance as a user\. You can try the following: 
  + Connect to the instance from the command line using an `/admin` parameter, for example: 

    ```
    mstsc /v:instance /admin
    ```

    For more information, see the following Microsoft article: [Access Remote Desktop Via Command Line](https://social.technet.microsoft.com/wiki/contents/articles/4487.access-remote-desktop-via-commandline.aspx)\.
  + Stop the instance, detach its Amazon EBS volumes, and attach them to another instance in the same Availability Zone to recover your data\.

## "Some settings are managed by your organization" \(Windows Server 2019\)<a name="some-settings-managed-by-org"></a>

Instances launched from the latest Windows Server 2019 AMIs may show a Windows Update dialog message stating "Some settings are managed by your organization\." This message appears as a result of changes in Windows Server 2019 and does not impact the behavior of Windows Update or your ability to manage update settings\.

To remove this warning, follow these steps: 

1. In gpedit\.msc, navigate to **Computer Configuration** > **Administrative Templates** > **Windows Components** > **Windows updates**\. Edit **Configure Automatic Update**, and switch this to **enabled**\.

1. In a command prompt, update group policy with **gpupdate /force**\.

1. Close and reopen the Windows Update Settings\. You will see the above message about your settings being managed by your organization, followed by "We'll automatically download updates, except on metered connections \(where charges may apply\)\. In that case, we'll automatically download those updates required to keep Windows running smoothly\.

1. Return to gpedit\.msc in Step 1 and set the group policy back to **not configured**\. Do another **gpupdate /force**\.

1. Close the command prompt and wait a few minutes\.

1. Reopen the Windows Update Settings\. No message stating "Some settings are managed by your organization" should appear\.