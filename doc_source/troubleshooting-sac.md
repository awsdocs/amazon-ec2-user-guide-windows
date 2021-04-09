# Use SAC to troubleshoot your Windows instance<a name="troubleshooting-sac"></a>

The Special Admin Console \(SAC\) capability of Windows provides a way to troubleshoot a Windows instance\. By connecting to the instance's serial console and using SAC, you can interrupt the boot process and boot Windows in safe mode\.

**Topics**
+ [Limitations](#sac-limitations)
+ [Prerequisites](#sac-prerequisites)
+ [Use SAC](#use-sac)
+ [Use the boot menu](#use-boot-menu)

## Limitations<a name="sac-limitations"></a>

If you launch an instance with an AMI that comes preconfigured with SAC, the EC2 services that rely on password retrieval will not work from the console\.

## Prerequisites<a name="sac-prerequisites"></a>

**To use SAC for troubleshooting a Windows instance, you must first complete the following prerequisites:**

1. Grant access to the serial console\. For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.

1. Connect to the serial console\. For more information, see [Connect to the EC2 Serial Console](connect-to-serial-console.md)\.

1. Enable SAC and the boot menu\. For more information, see [Enable SAC and the boot menu](#configure-sac-bootmenu)\.

### Enable SAC and the boot menu<a name="configure-sac-bootmenu"></a>

Use one of the following methods to enable SAC and the boot menu on an instance\.

------
#### [ PowerShell ]

**To enable SAC and the boot menu on a Windows instance**

1. [Connect](connecting_to_windows_instance.md) to your instance and perform the following steps from the PowerShell command line\.

1. Enable SAC\.

   ```
   bcdedit /ems '{current}' on
   bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Enable the boot menu\.

   ```
   bcdedit /set '{bootmgr}' displaybootmenu yes
   bcdedit /set '{bootmgr}' timeout 15
   bcdedit /set '{bootmgr}' bootems yes
   ```

1. Apply the updated configuration by rebooting the instance\.

   ```
   shutdown -r -t 0
   ```

------
#### [ Command prompt ]

**To enable SAC and the boot menu on a Windows instance**

1. [Connect](connecting_to_windows_instance.md) to your instance and perform the following steps from the command prompt\.

1. Enable SAC\.

   ```
   bcdedit /ems {current} on
   bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Enable the boot menu\.

   ```
   bcdedit /set {bootmgr} displaybootmenu yes
   bcdedit /set {bootmgr} timeout 15
   bcdedit /set {bootmgr} bootems yes
   ```

1. Apply the updated configuration by rebooting the instance\.

   ```
   shutdown -r -t 0
   ```

------

## Use SAC<a name="use-sac"></a>

If SAC has been enabled on the instance, the serial console displays the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-3.png)

**To display the available SAC commands**  
Enter `?` and then press **Enter**\.

The output is as follows\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-4.png)

**To switch to channels created by cmd**  
Press **ESC\+TAB\+channel number** together\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-5.png)

The command prompt requires credentials\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-6.png)

The command prompt is the same full\-featured command shell that you get on a desktop, but with the exception that it does not allow the reading of characters that were already output\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-7.png)

**PowerShell can also be used from the command prompt\.**

Note that you might need to set the progress preference to silent mode\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-8.png)

## Use the boot menu<a name="use-boot-menu"></a>

If the instance has the boot menu enabled and is restarted after connecting via SSH, you should see the boot menu, as follows\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-1.png)

**Boot menu commands**

ENTER  
Starts the selected entry of the operating system\.

TAB  
Switches to the Tools menu\.

ESC  
Cancels and restarts the instance\.

ESC followed by 8  
Equivalent to pressing **F8**\. Shows advanced options for the selected item\.

ESC key \+ left arrow  
Goes back to the initial boot menu\.  
The ESC key alone does not take you back to the main menu because Windows is waiting to see if an escape sequence is in progress\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/win-boot-2.png)