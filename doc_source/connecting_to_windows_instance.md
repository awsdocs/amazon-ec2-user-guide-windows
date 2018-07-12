# Connecting to Your Windows Instance<a name="connecting_to_windows_instance"></a>

Amazon EC2 instances created from most Windows Amazon Machine Images \(AMIs\) enable you to connect using Remote Desktop\. Remote Desktop uses the Remote Desktop Protocol \(RDP\) and enables you to connect to and use your instance in the same way you use a computer sitting in front of you\. It is available on most editions of Windows and available for Mac OS\.

**Important**  
The Windows Server 2016 Nano installation option \(Nano Server\) does not support RDP\. For more information, see [Connect to a Windows Server 2016 Nano Server Instance](#connecting-nano)\.

For information about connecting to a Linux instance, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Prerequisites](#rdp-prereqs)
+ [Connect to Your Windows Instance](#connect-rdp)
+ [Connect to a Windows Instance Using Its IPv6 Address](#connecting-to-windows-ipv6)
+ [Connect to a Windows Server 2016 Nano Server Instance](#connecting-nano)
+ [Transfer Files to Windows Instances](#AccessingInstancesWindowsFileTransfer)

## Prerequisites<a name="rdp-prereqs"></a>
+ **Install an RDP client**
  + \[Windows\] Windows includes an RDP client by default\. To verify, type mstsc at a Command Prompt window\. If your computer doesn't recognize this command, see the [Windows home page](http://windows.microsoft.com) and search for the download for Remote Desktop Connection\.
  + \[Mac OS X\] Use the Microsoft Remote Desktop app from the Apple App Store\.
  + \[Linux\] Use [rdesktop](http://www.rdesktop.org/)\.
+ **Get the ID of the instance**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the **Show/Hide** icon and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Locate the private key**

  Get the fully qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\.
+ **Enable inbound RDP traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming RDP traffic from your IP address\. The default security group does not allow incoming RDP traffic by default\. For more information, see [Authorizing Inbound Traffic for Your Windows Instances](authorizing-access-to-an-instance.md)\.
+ For the best experience using Internet Explorer, run the latest version\.

## Connect to Your Windows Instance<a name="connect-rdp"></a>

To connect to a Windows instance, you must retrieve the initial administrator password and then specify this password when you connect to your instance using Remote Desktop\.

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

      \[Mac OS\] Log in as prompted, using the default administrator account and the default administrator password that you recorded or copied previously\. Note that you might need to switch spaces to see the login screen\. For more information about spaces, see [http://support\.apple\.com/kb/PH14155](http://support.apple.com/kb/PH14155)\.

   1. If you receive an error while attempting to connect to your instance, see [Remote Desktop can't connect to the remote computer](troubleshooting-windows-instances.md#rdp-issues)\.

After you connect, we recommend that you do the following:
+ Change the administrator password from the default value\. You change the password while logged on to the instance itself, just as you would on any other Windows Server\.
+ Create another user account with administrator privileges on the instance\. Another account with administrator privileges is a safeguard if you forget the administrator password or have a problem with the administrator account\. The user account must have permission to access the instance remotely\. Open **System Properties**, choose **Remote**, and add the user to the **Remote Desktop Users** group\.

## Connect to a Windows Instance Using Its IPv6 Address<a name="connecting-to-windows-ipv6"></a>

If you've enabled your VPC for IPv6 and assigned an IPv6 address to your Windows instance, you can use an RDP client to connect to your instance using its IPv6 address instead of a public IPv4 address or public DNS hostname\. For more information, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\. 

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

## Connect to a Windows Server 2016 Nano Server Instance<a name="connecting-nano"></a>

Windows Server 2016 Nano Server does not support Remote Desktop connections\. To connect to a Windows Server 2016 Nano Server instance, you must connect using Windows PowerShell, as described in the following procedure\.

**Prerequisites**
+ Ensure that the security group associated with the instance allows inbound TCP traffic from your IP address on port 5985 \(HTTP\)\.
+ Get the ID of the instance\.
+ Get the public IP address of the instance\. If you use the private IP address, you must connect to the instance from another instance in the same virtual private cloud \(VPC\)\.
+ Get the fully\-qualified path to the location of the `.pem` file for the key pair that you specified when you launched the instance\. You need this to retrieve the Administrator password for the instance\.

**To connect to a Nano Server instance**

1. Start a PowerShell session in administrator mode \(from **Start**, **Amazon Web Services**, right\-click **Windows PowerShell** and choose **Run as administrator**\)\.

1. Store the IP address of your instance in a variable as follows\.

   ```
   PS C:\> $ip = "198.51.100.1"
   ```

1. Add the IP address of your instance to the list of trusted hosts as follows\. When prompted for confirmation, press Enter\. Note that you must do this step only the first time you connect to this instance from a computer\.

   ```
   PS C:\> Set-Item WSMan:\localhost\Client\TrustedHosts $ip
   ```

1. Retrieve the administrator password for your instance using the [Get\-EC2PasswordData](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2PasswordData.html) command as follows\. Save the password, as you'll need it to connect to the instance\.

   ```
   PS C:\> Get-EC2PasswordData -InstanceId i-1234567890abcdef0 -PemFile C:\path\my-key-pair.pem
   ```

1. Start the session as follows\.

   ```
   PS C:\> Enter-PSSession -ComputerName $ip -Credential ~\Administrator
   ```

1. When prompted for the password, specify the password that you saved\. Upon success, the prompt is modified with the IP address of your instance as follows, indicating that any commands will be run on the instance\.

   ```
   [198.51.100.1]: PS C:\> 
   ```

1. After you are finished, you can end the session as follows\.

   ```
   [198.51.100.1]: PS C:\> Exit-PSSession
   ```

WS\-Management encrypts all transmitted Windows PowerShell data, even when you use HTTP\. If you prefer to connect to your Nano Server instance using HTTPS, you must connect using HTTP and enable HTTPS support\. Before you can connect using HTTPS, you must also add a rule to the security group associated with the instance that allows inbound TCP traffic from your IP address on port 5986 \(HTTPS\)\. For more information, see [Configuring WinRM over HTTPS to enable PowerShell remoting](https://blogs.technet.microsoft.com/uktechnet/2016/02/11/configuring-winrm-over-https-to-enable-powershell-remoting/) on the Microsoft TechNet Blog\.

## Transfer Files to Windows Instances<a name="AccessingInstancesWindowsFileTransfer"></a>

You can work with your Windows instance the same way that you would work with any Windows server\. For example, you can transfer files between a Windows instance and your local computer using the local file sharing feature of the Microsoft Remote Desktop Connection software\. If you enable this option, you can access your local files from your Windows instances\. You can access local files on hard disk drives, DVD drives, portable media drives, and mapped network drives\. For more information, see the following articles from Microsoft:
+ [Make Local Devices and Resources Available in a Remote Session](http://technet.microsoft.com/en-us/library/cc770631.aspx)
+ [Getting Started with Remote Desktop Client on Mac](http://technet.microsoft.com/en-us/library/dn473012.aspx)
+ [How to copy files to and from Nano Server](https://msdn.microsoft.com/en-us/library/mt708806.aspx)