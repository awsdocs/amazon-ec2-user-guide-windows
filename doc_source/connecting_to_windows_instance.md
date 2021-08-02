# Connect to your Windows instance<a name="connecting_to_windows_instance"></a>

You can connect to Amazon EC2 instances created from most Windows Amazon Machine Images \(AMIs\) using Remote Desktop\. Remote Desktop uses the [Remote Desktop Protocol \(RDP\)](https://docs.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) to connect to and use your instance in the same way you use a computer sitting in front of you \(local computer\)\. It is available on most editions of Windows and is also available for Mac OS\.

The license for the Windows Server operating system allows two simultaneous remote connections for administrative purposes\. The license for Windows Server is included in the price of your Windows instance\. If you require more than two simultaneous remote connections, you must purchase a Remote Desktop Services \(RDS\) license\. If you attempt a third connection, an error occurs\.

For information about connecting to a Linux instance, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Prerequisites](#rdp-prereqs)
+ [Connect to your Windows instance using RDP](#connect-rdp)
+ [Connect to a Windows instance using its IPv6 address](#connecting-to-windows-ipv6)
+ [Connect to a Windows instance using Session Manager](#session-manager)
+ [Configure your accounts](#configure-admin-accounts)
+ [Transfer files to Windows instances](#AccessingInstancesWindowsFileTransfer)

## Prerequisites<a name="rdp-prereqs"></a>
+ **Install an RDP client**
  + \[Windows\] Windows includes an RDP client by default\. To verify, type mstsc at a Command Prompt window\. If your computer doesn't recognize this command, see the [Windows home page](https://windows.microsoft.com) and search for the download for the Microsoft Remote Desktop app\.
  + \[Mac OS X\] Download the [Microsoft Remote Desktop app](https://apps.apple.com/us/app/microsoft-remote-desktop/id1295203466?mt=12) from the Mac App Store\.
  + \[Linux\] Use [Remmina](https://remmina.org/)\.
+ **Locate the private key**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. For more information about how you created your key pair, see [Creating a Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\.
+ **Enable inbound RDP traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming RDP traffic \(`port 3389`\) from your IP address\. The default security group does not allow incoming RDP traffic by default\. For more information, see [Authorize inbound traffic for your Windows instances](authorizing-access-to-an-instance.md)\.

## Connect to your Windows instance using RDP<a name="connect-rdp"></a>

To connect to a Windows instance, you must retrieve the initial administrator password and then enter this password when you connect to your instance using Remote Desktop\. It takes a few minutes after instance launch before this password is available\.

The name of the administrator account depends on the language of the operating system\. For example, for English, it's `Administrator`, for French it's `Administrateur`, and for Portuguese it's `Administrador`\. For more information, see [Localized Names for Administrator Account in Windows](http://social.technet.microsoft.com/wiki/contents/articles/13813.localized-names-for-administrator-account-in-windows.aspx) in the Microsoft TechNet Wiki\.

If you've joined your instance to a domain, you can connect to your instance using domain credentials you've defined in AWS Directory Service\. On the Remote Desktop login screen, instead of using the local computer name and the generated password, use the fully\-qualified user name for the administrator \(for example, **corp\.example\.com\\Admin**\), and the password for this account\.

If you receive an error while attempting to connect to your instance, see [Remote Desktop can't connect to the remote computer](troubleshoot-connect-windows-instance.md#rdp-issues)\.

------
#### [ New console ]

**To connect to your Windows instance using an RDP client**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, select **Instances**\. Select the instance and then choose **Connect**\.

1. On the **Connect to instance** page, choose the **RDP client** tab, and then choose **Get password**\.  
![\[Get password for RDP.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-get-password.png)

1. Choose **Browse** and navigate to the private key \(`.pem`\) file you created when you launched the instance\. Select the file and choose **Open** to copy the entire contents of the file to this window\.

1. Choose **Decrypt Password**\. The console displays the default administrator password for the instance under **Password**, replacing the **Get password** link shown previously\. Save the password in a safe place\. This password is required to connect to the instance\.  
![\[Password location for RDP.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-password.png)

1. Choose **Download remote desktop file**\. Your browser prompts you to either open or save the RDP shortcut file\. When you have finished downloading the file, choose **Cancel** to return to the **Instances** page\.
   + If you opened the RDP file, you'll see the **Remote Desktop Connection** dialog box\.
   + If you saved the RDP file, navigate to your downloads directory, and open the RDP file to display the dialog box\.

1. You may get a warning that the publisher of the remote connection is unknown\. Choose **Connect** to continue to connect to your instance\.  
![\[Unknown publisher message.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-unknown-publisher.png)

1. The administrator account is chosen by default\. Copy and paste the password that you saved previously\.
**Tip**  
If you receive a "Password Failed" error, try entering the password manually\. Copying and pasting content can corrupt it\.

1. Due to the nature of self\-signed certificates, you may get a warning that the security certificate could not be authenticated\. Use the following steps to verify the identity of the remote computer, or simply choose **Yes** \(Windows\) or **Continue** \(Mac OS X\) if you trust the certificate\.  
![\["Identity cannot be verified" window.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-identity.png)

   1. If you are using **Remote Desktop Connection** on a Windows computer, choose **View certificate**\. If you are using **Microsoft Remote Desktop** on a Mac, choose **Show Certificate**\.

   1. Choose the **Details** tab, and scroll down to **Thumbprint** \(Windows\) or **SHA1 Fingerprints** \(Mac OS X\)\. This is the unique identifier for the remote computer's security certificate\.

   1. In the Amazon EC2 console, select the instance, choose **Actions**, **Monitor and troubleshoot**, **Get system log**\.

   1. In the system log output, look for `RDPCERTIFICATE-THUMBPRINT`\. If this value matches the thumbprint or fingerprint of the certificate, you have verified the identity of the remote computer\.

   1. If you are using **Remote Desktop Connection** on a Windows computer, return to the **Certificate** dialog box and choose **OK**\. If you are using **Microsoft Remote Desktop** on a Mac, return to the **Verify Certificate** and choose **Continue**\.

   1. \[Windows\] Choose **Yes** in the **Remote Desktop Connection** window to connect to your instance\.

      \[Mac OS X\] Log in as prompted, using the default administrator account and the default administrator password that you recorded or copied previously\. Note that you might need to switch spaces to see the login screen\. For more information, see [Add spaces and switch between them](https://support.apple.com/en-us/HT204100)\.

------
#### [ Old console ]

**To connect to your Windows instance using an RDP client**

1. In the Amazon EC2 console, select the instance, and then choose **Connect**\.

1. In the **Connect To Your Instance** dialog box, choose **Get Password** \(it will take a few minutes after the instance is launched before the password is available\)\.

1. Choose **Browse** and navigate to the private key \(\.pem\) file you created when you launched the instance\. Select the file and choose **Open** to copy the entire contents of the file into the **Contents** field\.

1. Choose **Decrypt Password**\. The console displays the default administrator password for the instance in the **Connect To Your Instance** dialog box, replacing the link to **Get Password** shown previously with the actual password\.

1. Record the default administrator password, or copy it to the clipboard\. You need this password to connect to the instance\.

1. Choose **Download Remote Desktop File**\. Your browser prompts you to either open or save the \.rdp file\. Either option is fine\. When you have finished, you can choose **Close** to dismiss the **Connect To Your Instance** dialog box\. 
   + If you opened the \.rdp file, you'll see the **Remote Desktop Connection** dialog box\.
   + If you saved the \.rdp file, navigate to your downloads directory, and open the \.rdp file to display the dialog box\.

1. You may get a warning that the publisher of the remote connection is unknown\. You can continue to connect to your instance\.

1. When prompted, log in to the instance, using the administrator account for the operating system and the password that you recorded or copied previously\. If your **Remote Desktop Connection** already has an administrator account set up, you might have to choose the **Use another account** option and type the user name and password manually\.
**Note**  
Sometimes copying and pasting content can corrupt data\. If you encounter a "Password Failed" error when you log in, try typing in the password manually\.

1. Due to the nature of self\-signed certificates, you may get a warning that the security certificate could not be authenticated\. Use the following steps to verify the identity of the remote computer, or simply choose **Yes** or **Continue** to continue if you trust the certificate\.

   1. If you are using **Remote Desktop Connection** from a Windows PC, choose **View certificate**\. If you are using **Microsoft Remote Desktop** on a Mac, choose **Show Certificate**\.

   1. Choose the **Details** tab, and scroll down to the **Thumbprint** entry on a Windows PC, or the **SHA1 Fingerprints** entry on a Mac\. This is the unique identifier for the remote computer's security certificate\.

   1. In the Amazon EC2 console, select the instance, choose **Actions**, and then choose **Get System Log**\.

   1. In the system log output, look for an entry labeled `RDPCERTIFICATE-THUMBPRINT`\. If this value matches the thumbprint or fingerprint of the certificate, you have verified the identity of the remote computer\.

   1. If you are using **Remote Desktop Connection** from a Windows PC, return to the **Certificate** dialog box and choose **OK**\. If you are using **Microsoft Remote Desktop** on a Mac, return to the **Verify Certificate** and choose **Continue**\.

   1. \[Windows\] Choose **Yes** in the **Remote Desktop Connection** window to connect to your instance\.

      \[Mac OS\] Log in as prompted, using the default administrator account and the default administrator password that you recorded or copied previously\. Note that you might need to switch spaces to see the login screen\. For more information about spaces, see [support\.apple\.com/en\-us/HT204100](https://support.apple.com/en-us/HT204100)\.

   1. If you receive an error while attempting to connect to your instance, see [Remote Desktop can't connect to the remote computer](troubleshoot-connect-windows-instance.md#rdp-issues)\.

------

## Connect to a Windows instance using its IPv6 address<a name="connecting-to-windows-ipv6"></a>

If you've [enabled your VPC for IPv6](https://docs.aws.amazon.com/vpc/latest/userguide/get-started-ipv6.html) and [assigned an IPv6 address to your Windows instance](using-instance-addressing.md#assign-ipv6-address), you can use an RDP client to connect to your instance using its IPv6 address \(for example, `2001:db8:1234:1a00:9691:9503:25ad:1761`\) instead of using its public IPv4 address or public DNS hostname\.

**To connect to your Windows instance using its IPv6 address**

1. Get the initial administrator password for your instance, as described in [Connect to your Windows instance using RDP](#connect-rdp)\. This password is required to connect to your instance\.

1. \[Windows\] Open the RDP client on your Windows computer, choose **Show Options**, and do the following:  
![\[Remote Desktop client.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-ipv6-rdp.png)
   + For **Computer**, enter the IPv6 address of your Windows instance\.
   + For **User name**, enter **Administrator**\.
   + Choose **Connect**\.
   + When prompted, enter the password that you saved previously\.

   \[Mac OS X\] Open the RDP client on your computer and do the following:
   + Choose **New**\.
   + For **PC Name**, enter the IPv6 address of your Windows instance\.
   + For **User name**, enter **Administrator**\.
   + Close the dialog box\. Under **My Desktops**, select the connection, and choose **Start**\.
   + When prompted, enter the password that you saved previously\.

1. Due to the nature of self\-signed certificates, you may get a warning that the security certificate could not be authenticated\. If you trust the certificate, you can choose **Yes** or **Continue**\. Otherwise, you can verify the identity of the remote computer, as described in [Connect to your Windows instance using RDP](#connect-rdp)\.

## Connect to a Windows instance using Session Manager<a name="session-manager"></a>

Session Manager is a fully\-managed AWS Systems Manager capability for managing your Amazon EC2 instances through an interactive, one\-click, browser\-based shell, or through the AWS CLI\. You can use Session Manager to start a session with an instance in your account\. After the session is started, you can run PowerShell commands as you would for any other connection type\. For more information about Session Manager, see [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) in the *AWS Systems Manager User Guide*\. 

Before attempting to connect to an instance using Session Manager, ensure that the necessary setup steps have been completed\. For more information, see [Getting Started with Session Manager\.](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html)

**To connect to a Windows instance using Session Manager on the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Connect**\.

1. For **Connection method**, choose **Session Manager**\.

1. Choose **Connect**\.  
![\[Session Manager connection.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-session-manager.png)
**Tip**  
If you receive an error that you’re not authorized to perform one or more Systems Manager actions \(`ssm:command-name`\), then you must update your policies to allow you to start sessions from the Amazon EC2 console\. For more information and instructions, see [ Quickstart Default IAM Policies for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-restrict-access-quickstart.html) in the *AWS Systems Manager User Guide*\.

## Configure your accounts<a name="configure-admin-accounts"></a>

After you connect, we recommend that you perform the following:
+ Change the administrator password from the default value\. You [can change the password while you are logged on to the instance itself](https://support.microsoft.com/en-us/windows/change-or-reset-your-windows-password-8271d17c-9f9e-443f-835a-8318c8f68b9c), just as you would on any computer running Windows Server\.
+ Create another user account with administrator privileges on the instance\. This is a safeguard in case you forget the administrator password or have a problem with the administrator account\. The new user account must have permission to access the instance remotely\. Open **System Properties** by right\-clicking on the **This PC** icon on your Windows desktop or File Explorer and selecting **Properties**\. Choose **Remote settings**, and choose **Select Users** to add the user to the **Remote Desktop Users** group\.  
![\[System Properties window.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-properties-rdp.png)

## Transfer files to Windows instances<a name="AccessingInstancesWindowsFileTransfer"></a>

You can work with your Windows instance the same way that you would work with any Windows server\. For example, you can transfer files between a Windows instance and your local computer using the local file sharing feature of the Microsoft Remote Desktop Connection software\. If you enable this option, you can access your local files from your Windows instances\. You can access local files on hard disk drives, DVD drives, portable media drives, and mapped network drives\. 

To make local devices and resources available to a remote session on Windows, map the remote session drive to your local drive\.

**To map the remote session drive to your local drive**

1. Open the Remote Desktop Connection client\.

1. Choose **Show Options**\.

1. Choose the **Local Resources** tab\.

1. Under **Local Devices and resources**, choose **More\.\.\.**  
![\[RDP Local Resources window.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-rdp-local-resources.png)

1. Open **Drives** and select the local drive to map to your Windows instance\.

1. Choose **OK**\.  
![\[RDP Local devices and resources window.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-rdp-drives.png)

1. Choose **Connect** to connect to your Windows instance\.

For more information on making local devices available to a remote session on a Mac computer, see [Get Started with Remote Desktop on Mac](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac)\.