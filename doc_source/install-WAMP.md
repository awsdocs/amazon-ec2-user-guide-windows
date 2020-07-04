# Tutorial: Installing a WAMP Server on an Amazon EC2 Instance Running Windows Server<a name="install-WAMP"></a>

This tutorial shows you how to install an Apache web server with PHP and MySQL on an EC2 instance running Windows Server\. This software configuration is sometimes called a WAMP server or WAMP stack \(Windows, Apache, MySQL, PHP\)\. For information about how to create a similar server on Linux, see [Tutorial: Installing a LAMP Web Server](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html) in the *Amazon EC2 User Guide for Linux Instances*\.

A WAMP stack is designed for easy installation to help developers get up and running quickly\. It is not designed for production environments for the following reasons:
+ The default configurations do not meet security requirements for most production environments\.
+ Upgrading and patching the different software components on a single production server would affect server availability\.
+ The WAMP one\-click installers do not place files in standard locations, which can make it difficult to locate important configuration files\.

You can, however, create a WAMP stack on an EC2 instance to prototype a web project in a controlled test environment\. For example, you can host a static website or deploy a dynamic PHP application that reads and writes information to a database\. 

There are many third\-party solutions that you can use to install a WAMP stack; this tutorial uses the Bitnami WAMP stack\. For more information, see [Review: WAMP stacks for Web developers](http://www.infoworld.com/d/application-development/review-wamp-stacks-web-developers-194346)\. 

**Prerequisites**
+ Provision a Windows Server 2008 R2 or 2012 R2 base instance\. You must configure the base instance with a public domain name system \(DNS\) name that is reachable from the Internet\. For more information, see [Tutorial: Getting started with Amazon EC2 Windows instances](EC2_GetStarted.md)\.
+ Verify that the security group for your instance has the following ports open:
  + Port 80 \(HTTP inbound and outbound\) \- Allows computers outside of the instance to connect by using HTTP\. 
  + Port 443 \(HTTPS inbound and outbound\) \- Allows computers outside of the instance to connect by using HTTPS\.
  + Port 3389 \(RDP inbound only\) \- Allows you to connect to the instance using Remote Desktop Protocol \(RDP\)\. As a security best practice, restrict RDP access to a range of IP addresses in your organization\.

**To install a WAMP server**

1. Connect to your instance using Microsoft Remote Desktop\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Disable Internet Explorer Enhanced Security Configuration so that you can download and install required software from the web\.

   1. From the instance, open Server Manager\.

   1. \[Windows Server 2008 R2\] Under **Server Summary**, **Security Information**, click **Configure IE ESC**\.

      \[Windows Server 2012 R2\] Click **Local Server** in the left pane\. In the **Properties** pane, locate **IE Enhanced Security Configuration**\. Click **On**\.

   1. Under **Administrators**, click **Off**, and then click **OK**\.

   1. Close Server Manager\.

   1. Make a note to re\-enable Internet Explorer Enhanced Security Configuration when you have finished installing software from the web\.

1. Install software updates to ensure that the instance has the latest security updates and bug fixes\.
   + **EC2Config** \- [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.
   + **Windows Update** \- Run Windows Update to ensure that the latest security and software updates are installed on the instance\. In Control Panel, click **System and Security**\. In the **Windows Update** section, click **Check for updates**\.

1. Download and install the WAMP stack\. For the purposes of this tutorial, we suggest that you download and install [this WAMP stack](https://bitnami.com/stack/wamp)\. You can, however, download and install other Bitnami WAMP stacks\. Regardless of which stack you install, the Bitnami site prompts you to either create a free Bitnami account or log in by using a social media account\. After you log in, run the Bitnami setup wizard\.

1. After setup completes, verify that the Apache web server is configured properly and running by browsing to a test page\. Open a web browser on a different computer and enter either the public DNS address of the WAMP server or the public IP address\. The public DNS address for your instance is listed on the Amazon EC2 console in the **Public DNS** column\. If this column is hidden, click the **Show/Hide** icon and select **Public DNS**\.
**Important**  
If you do not see the Bitnami test page, use Windows Firewall with Advanced Security to create a custom rule that allows the HTTP protocol through port 80 and the HTTPS protocol through port 443\. For more information, see [Network Security](https://technet.microsoft.com/en-us/library/cc725616.aspx) on Microsoft TechNet\. Also verify that the security group for your instances contains a rule to allow connections on HTTP \(port 80\)\. For more information, see [Adding rules to a security group](working-with-security-groups.md#adding-security-group-rule)\.

1. Test your WAMP server by viewing a PHP file from the web\. You must be logged onto the instance as an administrator to perform the following steps\. 

   1. Create a file named `phpinfo.php` containing the code below and place this file in the Apache root directory\. By default, the path is: `C:\Bitnami\wampstack-<version_number>\apache2\htdocs`\.

      ```
      <?php phpinfo(); ?>
      ```

   1. In a web browser, enter the URL of the file you just created\. This URL is the public DNS address of your instance followed by a forward slash and the file name\. For example: `http://my.public.dns.amazonaws.com/phpinfo.php`\.

   1. Verify that the PHP information page is displayed\. If the page does not display, verify that you entered the correct public DNS address\. Also verify that Windows folder options are configured to show known file extensions\. By default, folder options hide known file extensions\. If you created the file in Notepad and saved it in the root directory your phpinfo\.php file might incorrectly be saved as phpinfo\.php\.txt\.

   1. As a security best practice, delete the phpinfo\.php file when you finish testing the WAMP server\.

1. Enhance MySQL security by disabling default features and by setting a root password\. The `mysql_secure_installation` Perl script can perform these tasks for you\. To run the script, you must install Perl\.

   1.  Download and install Perl from the [Perl Programming Language](http://www.perl.org/) website\.

   1. In the `C:\Bitnami\wampstack-<version_number>\mysql\bin` directory, double\-click `mysql_secure_installation`\.

   1. When prompted, enter the MySQL root account password that you entered when you ran the Bitnami WAMP stack installer, and then press Enter\.

   1. Type **n** to skip changing the password\.

   1. Type **Y** to remove the anonymous user accounts\.

   1. Type **Y** to disable remote root login\.

   1. Type **Y** to remove the test database\.

   1. Type **Y** to reload the privilege tables and save your changes\.

If you successfully completed the steps in this tutorial, then your WAMP server is functioning properly\. To continue testing, you can add more content to the `C:\Bitnami\wampstack-<version_number>\apache2\htdocs` folder and view the content by using the public DNS address for your instance\.

**Important**  
As a best practice, stop the MySQL server if you do not plan to use it right away\. You can restart the server when you need it again\.