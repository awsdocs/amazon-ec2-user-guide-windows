# Tutorial: Installing a WIMP server on an Amazon EC2 instance running Windows Server<a name="install-WIMP"></a>

This tutorial shows you how to install a Microsoft Internet Information Services \(IIS\) web server with PHP and MySQL on an EC2 instance running Windows Server\. This software configuration is sometimes called a WIMP server or WIMP stack \(Windows, IIS, MySQL, PHP\)\.

A WIMP stack is designed for easy installation to help developers get up and running quickly\. It is *not* designed for production environments for the following reasons:
+ The default configurations do not meet security requirements for most production environments\.
+ Upgrading and patching the different software components on a single production server would affect server availability\.
+ The WAMP one\-click installers do not place files in standard locations, which can make it difficult to locate important configuration files\.

You can, however, create a WIMP stack on an EC2 instance to prototype a web project in a controlled test environment\. For example, you can host a static website or deploy a dynamic PHP application that reads and writes information to a database\.

## Prerequisites<a name="wimp-prerequisites"></a>
+ Provision a Windows Server 2008 R2 or 2012 R2 base instance\. You must configure the base instance with a public domain name system \(DNS\) name that is reachable from the Internet\. For more information, see [Tutorial: Getting started with Amazon EC2 Windows instances](EC2_GetStarted.md)\.
+ Verify that the security group for your instance has the following ports open:
  + Port 80 \(HTTP inbound and outbound\) \- Allows computers outside of the instance to connect by using HTTP\. 
  + Port 443 \(HTTPS inbound and outbound\) \- Allows computers outside of the instance to connect by using HTTPS\.
  + Port 3389 \(RDP inbound only\) \- Allows you to connect to the instance using Remote Desktop Protocol \(RDP\)\. As a security best practice, restrict RDP access to a range of IP addresses in your organization\.
+ Read the best practices for installing PHP on the [Microsoft web platform](http://www.iis.net/learn/application-frameworks/running-php-applications-on-iis/best-practices-for-php-on-the-microsoft-web-platform)\.

## Prepare your instance<a name="wimp-prepare-instance"></a>

**To prepare your instance**

1. Connect to your instance using Microsoft Remote Desktop\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Disable Internet Explorer Enhanced Security Configuration so that you can download and install required software from the web\.

   1. From the instance, open Server Manager\.

   1. \[Windows Server 2008 R2\] Under **Server Summary**, **Security Information**, choose **Configure IE ESC**\.

      \[Windows Server 2012 R2\] Choose **Local Server** in the left pane\. In the **Properties** pane, locate **IE Enhanced Security Configuration**\. Choose **On**\.

   1. Under **Administrators**, choose **Off**, and then choose **OK**\.

   1. Close Server Manager\.
**Note**  
Make a note to re\-enable Internet Explorer Enhanced Security Configuration when you have finished installing software from the web\.

1. Install software updates to ensure that the instance has the latest security updates and bug fixes\.

   1.  **EC2Config** \- [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information about how to install this service, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

   1.  **Windows Update** \- Run Windows Update to ensure that the latest security and software updates are installed on the instance\. In Control Panel, choose **System and Security**\. In the **Windows Update** section, choose **Check for updates**\.

## Install the IIS web server<a name="wimp-iis"></a>

IIS is a feature of Windows Server and is installed by using Server Manager\. The procedure you'll use depends on the version of Windows Server your instance is running\.

**Install IIS on Windows Server 2012**

1. On your Windows instance, choose **Start**, **Server Manager**, and then choose **Add roles and features**\.

1. On the **Before you begin** page, choose **Next**\.

1. On the **Select installation type** page, choose **Role\-based or feature\-based installation**, and then choose **Next**\.

1. On the **Select destination server** page, select your instance from the server pool, and then choose **Next**\.

1. On the **Select server roles** page, select **Web Server \(IIS\)**, choose **Add Features**, and then choose **Next**\.

1. On the **Select features** page, retain the default features and expand **\.NET Framework 4\.5 Features**, choose **ASP\.NET 4\.5**, and then choose **Next**\.

1. On the **Web Server Role \(IIS\)** page, choose **Next**\.

1. On the **Select role services** page, retain the default services and select **Application Development**\.

1. Expand **Application Development**, and then select the following features\. When selecting these features, if prompted, choose **Add Features**:

   1. \.NET Extensibility 3\.5

   1. \.NET Extensibility 4\.5

   1. Application Initialization

   1. ASP\.NET 3\.5

   1. ASP\.NET 4\.5

   1. CGI

1. Choose **Next**\.

1. On the **Confirm installation selections** page, select **Restart the destination server automatically if required**\. When prompted for confirmation, choose **Yes**\. 

1. Choose **Install**, and then after the installation is complete, choose **Close**\.

1. Run Windows update again\.

**Install IIS on Windows Server 2008**

1. On your Windows instance, choose **Start**, **Server Manager**, and then choose **Roles**\.

1. Choose **Add Roles**\.

1. On the **Before You Begin** page, choose **Next**\.

1. On the **Select Server Roles** page, choose **Web Server \(IIS\)**\.

1. On the **Select Role Services** page under **Application Development**, choose **ASP\.NET**\. 

   1. When prompted, choose **Add Required Role Services**\.

   1. Choose **CGI**\.

   1. Choose **Next**\.

1. On the **Confirm Installation Selections**, choose **Install**\.

1. Run Windows update again\.

**To verify that the web server is running**  
After setup completes, verify that the IIS web server is configured properly and running by going to the IIS welcome page\. Open a web browser on a different computer and enter either the public DNS address of the WIMP server or the public IP address\. The public DNS address for your instance is listed on the Amazon EC2 console in the **Public DNS** column\. If this column is hidden, choose the **Show/Hide** icon and choose **Public DNS**\.

**Important**  
If you do not see the Bitnami test page, use Windows Firewall with Advanced Security to create a custom rule that allows the HTTP protocol through port 80 and the HTTPS protocol through port 443\. For more information, see [Network Security](https://technet.microsoft.com/en-us/library/cc725616.aspx) on Microsoft TechNet\. Also verify that the security group for your instances contains a rule to allow connections on HTTP \(port 80\)\. For more information, see [Adding rules to a security group](working-with-security-groups.md#adding-security-group-rule)\.

## Install MySQL and PHP<a name="wimp-mysql-php"></a>

You can download and install MySQL and PHP using the Microsoft Web Platform Installer\.

**To install MySQL and PHP**

1. In your Windows Server instance, download and install the latest version of the [Microsoft Web Platform Installer 5\.0](http://www.iis.net/learn/install/web-platform-installer/web-platform-installer-direct-downloads)\.

1. In the Microsoft Web Platform Installer, choose the **Products** tab\.

1. Choose **MySQL Windows 5\.5** and then choose **Add**\.

1. Choose **PHP 5\.6\.0** and then choose **Add**\.

1. Choose **Install**\.

1. On the **Prerequisites** page, enter a password for the MySQL default database administrator account, and then choose **Continue**\.

1. When the installation is complete, choose **Finish**, and then choose **Exit** to close the Web Platform Installer\.

## Test your server<a name="wimp-test-server"></a>

Test your server by viewing a PHP file from the web\. You must be logged onto the instance as an administrator to perform the following steps\. 

**To test your WIMP server**

1. Download and install the [Visual C\+\+ Redistributable for Visual Studio 2012 Update 4 x86 package](http://www.microsoft.com/en-us/download/details.aspx?id=30679)\. Even if your server is a 64\-bit server, you must install the x86 package\.

1. Create a file named `phpinfo.php` that contains the following code and place this file in the IIS root directory\. By default, the path is: `C:\inetpub\wwwroot`\.

   ```
   <?php phpinfo(); ?>
   ```

1. In a web browser, enter the URL of the file you just created\. This URL is the public DNS address of your instance followed by a forward slash and the file name, as in the following example: `http://my.public.dns.amazonaws.com/phpinfo.php`\.

1. Verify that the PHP information page is displayed\. If the page does not display, verify that you entered the correct public DNS address\. Also verify that Windows folder options are configured to show known file extensions\. By default, folder options hide known file extensions\. If you created the file in Notepad and saved it in the root directory your phpinfo\.php file might incorrectly be saved as phpinfo\.php\.txt\.

1. As a security best practice, delete the phpinfo\.php file when you finish testing the WAMP server\.

1. Enhance MySQL security by disabling default features and by setting a root password\. The `mysql_secure_installation` Perl script can perform these tasks for you\. To run the script, you must install Perl\.

   1. Download and install Perl from the [Perl Programming Language](http://www.perl.org/) website\.

   1. In the `C:\Program Files\MySQL\MySQL Server 5.5\bin` directory, double\-click `mysql_secure_installation`\.

   1. When prompted, enter the current root password and press Enter\.

   1. Type **n** to skip changing the password\.

   1. Type **Y** to remove the anonymous user accounts\.

   1. Type **Y** to disable remote root login\.

   1. Type **Y** to remove the test database\.

   1. Type **Y** to reload the privilege tables and save your changes\.

You should now have a fully functional WIMP web server\. If you add content to the IIS document root at `C:\inetpub\wwwroot`, you can view that content at the public DNS address for your instance\.

**Important**  
As a best practice, stop the MySQL server if you do not plan to use it right away\. You can restart the server when you need it again\.