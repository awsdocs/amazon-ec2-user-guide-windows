# Connecting to your Windows instance<a name="connecting_to_windows_instance"></a>

Amazon EC2 instances created from most Windows Amazon Machine Images \(AMIs\) enable you to connect using Remote Desktop\. Remote Desktop uses the Remote Desktop Protocol \(RDP\) and enables you to connect to and use your instance in the same way you use a computer sitting in front of you\. It is available on most editions of Windows and available for Mac OS\.

For information about connecting to a Linux instance, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Prerequisites](#rdp-prereqs)
+ [Connect to your Windows instance](#connect-rdp)
+ [Connect to a Windows instance using its IPv6 address](#connecting-to-windows-ipv6)
+ [Connect to a Windows instance using Session Manager](#session-manager)
+ [Transfer files to Windows instances](#AccessingInstancesWindowsFileTransfer)

## Prerequisites<a name="rdp-prereqs"></a>
+ **Install an RDP client**
  + \[Windows\] Windows includes an RDP client by default\. To verify, type mstsc at a Command Prompt window\. If your computer doesn't recognize this command, see the [Windows home page](https://windows.microsoft.com) and search for the download for the Microsoft Remote Desktop app\.
  + \[Mac OS X\] Download the Microsoft Remote Desktop app from the Mac App Store\.
  + \[Linux\] Use [Remmina](https://remmina.org/)\.
+ **Get the ID of the instance\.**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance\.**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the **Show/Hide Columns** icon in the top\-right corner of the screen and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance\.**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Locate the private key**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. For more information about how you created your key pair, see [Creating a Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\.
+ **Enable inbound RDP traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming RDP traffic \(port 3389\) from your IP address\. The default security group does not allow incoming RDP traffic by default\. For more information, see [Authorizing inbound traffic for your Windows instances](authorizing-access-to-an-instance.md)\.
+ For the best experience using Internet Explorer, run the latest version\.

## Connect to your Windows instance<a name="connect-rdp"></a>

To connect to a Windows instance, you must retrieve the initial administrator password \(see step 2 below\) and then specify this password when you connect to your instance using Remote Desktop\.

The name of the administrator account depends on the language of the operating system\. For example, for English, it's Administrator, for French it's Administrateur, and for Portuguese it's Administrador\. For more information, see [Localized Names for Administrator Account in Windows](http://social.technet.microsoft.com/wiki/contents/articles/13813.localized-names-for-administrator-account-in-windows.aspx) in the Microsoft TechNet Wiki\.

If you've joined your instance to a domain, you can connect to your instance using domain credentials you've defined in AWS Directory Service\. On the Remote Desktop login screen, instead of using the local computer name and the generated password, use the fully\-qualified user name for the administrator \(for example, **corp\.example\.com\\Admin**\) and the password for this account\.

The license for the Windows Server operating system \(OS\) allows two simultaneous remote connections for administrative purposes\. The license for Windows Server is included in the price of your Windows instance\. If you need more than two simultaneous remote connections, you must purchase a Remote Desktop Services \(RDS\) license\. If you attempt a third connection, an error occurs\. For more information, see [Configure the Number of Simultaneous Remote Connections Allowed for a Connection](http://technet.microsoft.com/en-us/library/cc753380.aspx)\.

**To connect to your Windows instance using an RDP client**

1. In the Amazon EC2 console, select the instance, and then choose **Connect**\.

1. In the **Connect To Your Instance** dialog box, choose **Get Password** \(it will take a few minutes after the instance is launched before the password is available\)\.

1. Choose **Browse** and navigate to the private key file you created when you launched the instance\. Select the file and choose **Open** to copy the entire contents of the file into the **Contents** field\.

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

After you connect, we recommend that you do the following:
+ Change the administrator password from the default value\. You change the password while logged on to the instance itself, just as you would on any other Windows Server\.
+ Create another user account with administrator privileges on the instance\. Another account with administrator privileges is a safeguard if you forget the administrator password or have a problem with the administrator account\. The user account must have permission to access the instance remotely\. Open **System Properties** by right\-clicking on the **This PC** icon on your Windows desktop or File Explorer and selecting **Properties**\. Choose **Remote settings**, and choose **Select Users** to add the user to the **Remote Desktop Users** group\.

## Connect to a Windows instance using its IPv6 address<a name="connecting-to-windows-ipv6"></a>

If you've enabled your VPC for IPv6 and assigned an IPv6 address to your Windows instance, you can use an RDP client to connect to your instance using its IPv6 address instead of a public IPv4 address or public DNS hostname\. For more information, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\. 

**To connect to your Windows instance using its IPv6 address**

1. In the Amazon EC2 console, select the instance, and then choose **Connect**\.

1. In the **Connect To Your Instance** dialog box, choose **Get Password** \(it will take a few minutes after the instance is launched before the password is available\)\.

1. Choose **Browse** and navigate to the private key file you created when you launched the instance\. Select the file and choose **Open** to copy the entire contents of the file into the **Contents** field\.

1. Choose **Decrypt Password**\.

1. Copy the default administrator password\. You need this password to connect to the instance\.

1. Open the RDP client on your computer\. 

1. \[Windows\] For the RDP client on a Windows computer, choose **Show Options** and do the following:
   + For **Computer**, type the IPv6 address of your Windows instance, for example, `2001:db8:1234:1a00:9691:9503:25ad:1761`\.
   + For **User name**, enter **Administrator**\.
   + Choose **Connect**\.

   \[Mac OS X\] For the Microsoft Remote Desktop app, choose **New** and do the following:
   + For **PC Name**, enter the IPv6 address of your Windows instance; for example, `2001:db8:1234:1a00:9691:9503:25ad:1761`\.
   + For **User name**, enter **Administrator**\.
   + Close the dialog box\. Under **My Desktops**, select the connection and choose **Start**\.

1. Due to the nature of self\-signed certificates, you may get a warning that the security certificate could not be authenticated\. Use the following steps to verify the identity of the remote computer, or simply choose **Yes** or **Continue** to continue if you trust the certificate\.

1. When prompted, enter the password that you recorded or copied previously\.

## Connect to a Windows instance using Session Manager<a name="session-manager"></a>

Session Manager is a fully managed AWS Systems Manager capability that lets you manage your Amazon EC2 instances through an interactive one\-click browser\-based shell or through the AWS CLI\. You can use Session Manager to start a session with an instance in your account\. After the session is started, you can run Powershell commands as you would through any other connection type\. For more information about Session Manager, see [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) in the *AWS Systems Manager User Guide*\. 

Before attempting to connect to an instance using Session Manager, ensure that the necessary setup steps have been completed\. For more information, see [Getting Started with Session Manager\.](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html)

**To connect to a Windows instance using Session Manager using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Connect**\.

1. For **Connection method**, choose **Session Manager**\.

1. Choose **Connect**\.
**Note**  
If you receive an error that you’re not authorized to perform one or more Systems Manager actions \(`ssm:command-name`\), then you must update your policies to allow you to start sessions from the Amazon EC2 console\. For more information and instructions, see [ Quickstart Default IAM Policies for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/getting-started-restrict-access-quickstart.html) in the *AWS Systems Manager User Guide*\.

## Transfer files to Windows instances<a name="AccessingInstancesWindowsFileTransfer"></a>

You can work with your Windows instance the same way that you would work with any Windows server\. For example, you can transfer files between a Windows instance and your local computer using the local file sharing feature of the Microsoft Remote Desktop Connection software\. If you enable this option, you can access your local files from your Windows instances\. You can access local files on hard disk drives, DVD drives, portable media drives, and mapped network drives\. 

To make local devices and resources available to a remote session on Windows, map the remote session drive to your local drive\.

**To map the remote session drive to your local drive**

1. Open the Remote Desktop Connection client\.

1. Choose **Show Options**\.

1. Choose the **Local Resources** tab\.

1. Under **Local Devices and resources**, choose **More\.\.\.**

1. Open **Drives** and select the local drive to map to your Windows instance\.

1. Choose **OK**\.

1. Choose **Connect** to connect to your Windows instance\.

For more information on making local devices available to a remote session on a Mac computer, see [Get Started with Remote Desktop on Mac](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac)\.