# Tutorial: Deploying a WordPress blog on your Amazon EC2 instance running Windows Server<a name="EC2Win_CreateWordPressBlog"></a>

This tutorial will help you install and deploy a WordPress blog on an Amazon EC2 instance running Windows Server\.

If you'd prefer to host your WordPress blog on a Linux instance, see [Tutorial: Hosting a WordPress blog with Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/hosting-wordpress.html) in the *Amazon EC2 User Guide for Linux Instances*\. If you need a high\-availability solution with a decoupled database, see [Deploying a high\-availability WordPress website](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/php-hawordpress-tutorial.html) in the *AWS Elastic Beanstalk Developer Guide*\.

## Prerequisites<a name="EC2Win_PrerequisiteWordPressBlog"></a>

Before you get started, be sure that you do the following:
+ Launch an Amazon EC2 instance from a Windows Server AMI\. For information, see [Tutorial: Getting started with Amazon EC2 Windows instances](EC2_GetStarted.md)\.
+ Use the AWS free usage tier \(if eligible\) to launch and use the free Windows `t2.micro` instance for 12 months\. You can use the AWS free usage tier for launching new applications, testing existing applications, or simply gaining hands\-on experience with AWS\. For more information about eligibility and the highlights, see the [AWS Free Tier](https://aws.amazon.com/free/) product page\.
**Important**  
If you've launched a regular instance and use it to deploy the WordPress website, you will incur the standard Amazon EC2 usage fees for the instance until you terminate it\. For more information about Amazon EC2 usage rates, go to the [Amazon EC2 product page](https://aws.amazon.com/ec2)\.
+ Ensure that the security group in which you're launching your instance has ports 80 \(HTTP\), 443 \(HTTPS\), and 3389 \(RDP\) open for inbound traffic\. Ports 80 and 443 allow computers outside of the instance to connect with HTTP and HTTPS\. If these ports are not open, the WordPress site can't be accessed from outside the instance\. Port 3389 allows you to connect to the instance with Remote Desktop Protocol\.
+ Connect to your instance\. 

## Installing the Microsoft Web Platform Installer<a name="InstallWebDeployment"></a>

You can use the Microsoft Web Platform Installer to install and configure WordPress on your server\. This tool simplifies deployment of Web applications and Web sites to IIS servers\. For more information, see [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx)\.

**To install Microsoft Web Platform Installer**

1. Verify that you've met the conditions in [Prerequisites](#EC2Win_PrerequisiteWordPressBlog)\.

1. Connect to your instance\.

1. Disable Internet Explorer Enhanced Security Configuration so that you can download and install required software from the web\.

   1. Open Server Manager\.
      + On Windows Server 2008 R2, under **Server Summary**, in the **Security Information** section, click **Configure IE ESC**\.
      + On Windows Server 2012 R2, click **Local Server** in the left pane\. In the **Properties** pane, locate **IE Enhanced Security Configuration**\. Click **On**\. 

   1. Under **Administrators**, click **Off**, and then click **OK**\.

   1. Close Server Manager\.

   1. Make a note to re\-enable Internet Explorer Enhanced Security Configuration when you have finished installing software from the web\.

1. Download and install the latest version of the [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx)\.

## Installing WordPress<a name="InstallWordPress"></a>

Now you'll use the Web Platform Installer to deploy WordPress on your server\.

**To install WordPress**

1. [Download](https://www.microsoft.com/en-us/download/details.aspx?id=30679) and install Visual C\+\+ Redistributable for Visual Studio 2012 Update 4 or later\. 
**Important**  
Even if your operating system is a 64\-bit operating system, you must install the 32\-bit version as PHP will not run in 64 bit\. 

   Open the **Web Platform Installer** and click **Applications**\.

1. Select **WordPress**, click **Add**, and then click **Install**\.

1. On the **Prerequisites** page, select **MySQL** for the database to use\. Enter the desired administrator password for your MySQL database in the **Password** and **Re\-type Password** boxes, and then click **Continue**\.

   For more information about creating a secure password, see [https://identitysafe\.norton\.com/password\-generator/](https://identitysafe.norton.com/password-generator/)\. Do not reuse an existing password, and make sure to store this password in a safe place\.

1. Click **I Accept** for the list of third\-party application software, Microsoft products \(including the IIS web server\), and components\. After the Web Platform Installer finishes installing the software, you are prompted to configure your new site\.

1. On the **Configure** page, clear the default application name in the **'WordPress' application name:** box and leave it blank, then leave the default information in the other boxes and click **Continue**\.

1. Click **Yes** to accept that the contents of the folder will be overwritten\.

## Configuring security keys<a name="configure-WordPress-security"></a>

WordPress allows you to generate and enter unique authentication keys and salts for your site\. These key and salt values provide a layer of encryption to the browser cookies that WordPress users store on their local machines\. Basically, adding long, random values here makes your site more secure\.

For more information about security keys, see [http://codex\.wordpress\.org/Editing\_wp\-config\.php\#Security\_Keys](http://codex.wordpress.org/Editing_wp-config.php#Security_Keys)\.

**To configure security keys**

1. Visit [https://api\.wordpress\.org/secret\-key/1\.1/salt/](https://api.wordpress.org/secret-key/1.1/salt/) to randomly generate a set of key values that you can copy and paste into the installation wizard\. The following steps will show you how to modify these values in Notepad to work with a Windows installation\.

1. Copy all of the text in that page to your clipboard\. It should look similar to the example below\.
**Note**  
The values below are for example purposes only; do not use these values for your installation\.

   ```
   define('AUTH_KEY',         '3#U$$+[RXN8:b^-L 0(WU_+ c+WFkI~c]o]-bHw+)/Aj[wTwSiZ<Qb[mghEXcRh-');
   define('SECURE_AUTH_KEY',  'Zsz._P=l/|y.Lq)XjlkwS1y5NJ76E6EJ.AV0pCKZZB,*~*r ?6OP$eJT@;+(ndLg');
   define('LOGGED_IN_KEY',    'ju}qwre3V*+8f_zOWf?{LlGsQ]Ye@2Jh^,8x>)Y |;(^[Iw]Pi+LG#A4R?7N`YB3');
   define('NONCE_KEY',        'P(g62HeZxEes|LnI^i=H,[XwK9I&[2s|:?0N}VJM%?;v2v]v+;+^9eXUahg@::Cj');
   define('AUTH_SALT',        'C$DpB4Hj[JK:?{ql`sRVa:{:7yShy(9A@5wg+`JJVb1fk%_-Bx*M4(qc[Qg%JT!h');
   define('SECURE_AUTH_SALT', 'd!uRu#}+q#{f$Z?Z9uFPG.${+S{n~1M&%@~gL>U>NV<zpD-@2-Es7Q1O-bp28EKv');
   define('LOGGED_IN_SALT',   ';j{00P*owZf)kVD+FVLn-~ >.|Y%Ug4#I^*LVd9QeZ^&XmK|e(76miC+&W&+^0P/');
   define('NONCE_SALT',       '-97r*V/cgxLmp?Zy4zUU4r99QQ_rGs2LTd%P;|_e1tS)8_B/,.6[=UK<J_y9?JWG');
   ```

1. Open a Notepad window by clicking **Start**, **All Programs**, **Accessories**, and then **Notepad**\.

1. Paste the copied text into the Notepad window\.

1. Windows WordPress installations do not accept the dollar sign \($\) in key and salt values, so they need to be replaced with another character \(such as S\)\. In the Notepad window, click **Edit**, then click **Replace**\.

1. In the **Find what** box, type **$**\.

1. In the **Replace with** box, type **S**\.

1. Click **Replace All** to replace all of the dollar signs with **S** characters\.

1. Close the **Replace** window\.

1. Paste the modified key and salt values from the Notepad window into their corresponding boxes in the installation wizard\. For example, the `AUTH_KEY` value in the Notepad window should be pasted into the **Authentication Key** box in the wizard\.

   Do not include the single quotes or other text surrounding the values, just the actual value as in the example shown below\.

   The modified `AUTH_KEY` line from the Notepad window:

   ```
   define('AUTH_KEY',         '3#USS+[RXN8:b^-L 0(WU_+ c+WFkI~c]o]-bHw+)/Aj[wTwSiZ<Qb[mghEXcRh-');
   ```

   Paste this text into the **Authentication Key** box of the wizard:

   ```
   3#USS+[RXN8:b^-L 0(WU_+ c+WFkI~c]o]-bHw+)/Aj[wTwSiZ<Qb[mghEXcRh-
   ```

1. Click **Continue** and **Finish** to complete the Web Platform Installer wizard\.

## Configuring the site title and administrator<a name="configure-WordPress-site"></a>

When you complete the Web Platform Installer wizard, a browser window opens to your WordPress installation at `http://localhost/wp-admin/install.php`\. On this page, you configure the title for your site and an administrative user to moderate your blog\.

**To complete the installation**

1. On the WordPress **Welcome** page, enter the following information and click **Install WordPress**\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_CreateWordPressBlog.html)

1. Click **Log In**\.

1. On the **Log In** page, enter your user name for **Username** and the site password you entered previously for **Password**\.

## Making your WordPress site public<a name="publish-WordPress-site"></a>

Now that you can see your WordPress blog on your local host, you can publish this website as the default site on your instance so that other people can see it\. The next procedure walks you through the process of modifying your WordPress settings to point to the public DNS name of your instance instead of your local host\. 

**To configure the default settings for your WordPress site**

1. Open the WordPress dashboard by opening a browser on your instance and going to `http://localhost/wp-admin`\. If prompted for your credentials, enter your user name for the **Username** and your site password for **Password**\.

1. In the **Dashboard** pane, click **Settings**\. 

1. On the **General Settings** page, enter the following information and click **Save Changes**\.
   + **WordPress address \(URL\)**—The public DNS address of your instance\. For example, your URL may look something like `http://ec2-203-0-113-25.compute-1.amazonaws.com`\.

     You can get the public DNS for your instance using the Amazon EC2 console \(select the instance and check the **Public DNS** column; if this column is hidden, click the **Show/Hide** icon and select **Public DNS**\)\.
   + **Site address \(URL\)**—The same public DNS address of your instance that you set in **WordPress address \(URL\)**\.

1. To see your new site, open a browser on a computer other than the instance hosting WordPress and type the public DNS address of your instance in the web address field\. Your WordPress site appears\.

Congratulations\! You have just deployed a WordPress site on a Windows instance\.

## Next steps<a name="WordPress-next-steps"></a>

If you no longer need this instance, you can remove it to avoid incurring charges\. For more information, see [Clean up your instance](EC2_GetStarted.md#ec2-clean-up-your-instance)\.

If your WordPress blog becomes popular and you need more compute power or storage, consider the following steps:
+ Expand the storage space on your instance\. For more information, see [Amazon EBS Elastic Volumes](ebs-modify-volume.md)\.
+ Move your MySQL database to [Amazon RDS](https://aws.amazon.com/rds) to take advantage of the service's ability to scale automatically\.
+ Migrate to a larger instance type\. For more information, see [Changing the instance type](ec2-instance-resize.md)\.
+ Add additional instances\. For more information, see [Tutorial: Increase the availability of your application on Amazon EC2](ec2-increase-availability.md)\.

For information about WordPress, see the WordPress Codex help documentation at [http://codex\.wordpress\.org/](http://codex.wordpress.org/)\. For more information about troubleshooting your installation, see [https://wordpress\.org/support/article/how\-to\-install\-wordpress/\#common\-installation\-problems](https://wordpress.org/support/article/how-to-install-wordpress/#common-installation-problems)\. For information about making your WordPress blog more secure, see [https://wordpress\.org/support/article/hardening\-wordpress/](https://wordpress.org/support/article/hardening-wordpress/)\. For information about keeping your WordPress blog up\-to\-date, see [https://wordpress\.org/support/article/updating\-wordpress/](https://wordpress.org/support/article/updating-wordpress/)\.