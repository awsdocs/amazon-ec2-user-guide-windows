# Tutorial: Getting started with Amazon EC2 Windows instances<a name="EC2_GetStarted"></a>

Use this tutorial to get started with Amazon Elastic Compute Cloud \(Amazon EC2\)\. You'll learn how to launch, connect to, and use a Windows instance\. An *instance* is a virtual server in the AWS cloud\. With Amazon EC2, you can set up and configure the operating system and applications that run on your instance\.

To get started with a Linux instance, see [Getting started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)\.\)

When you sign up for AWS, you can get started with Amazon EC2 using the [AWS Free Tier](https://aws.amazon.com/free/)\. If you created your AWS account less than 12 months ago, and have not already exceeded the free tier benefits for Amazon EC2, it will not cost you anything to complete this tutorial, because we help you select options that are within the free tier benefits\. Otherwise, you'll incur the standard Amazon EC2 usage fees from the time that you launch the instance until you terminate the instance \(which is the final task of this tutorial\), even if it remains idle\.

For step\-by\-step tutorials on specific use cases for EC2 instances running Windows Server, see [Tutorials for Amazon EC2 instances running Windows Server](ec2-tutorials.md)\.

**Topics**
+ [Overview](#ec2-get-started-overview)
+ [Prerequisites](#ec2-getstarted-prereqs)
+ [Step 1: Launch an instance](#ec2-launch-instance)
+ [Step 2: Connect to your instance](#ec2-connect-to-instance-windows)
+ [Step 3: Clean up your instance](#ec2-clean-up-your-instance)
+ [Next steps](#ec2-next-steps)

## Overview<a name="ec2-get-started-overview"></a>

The instance is an Amazon EBS\-backed instance \(meaning that the root volume is an EBS volume\)\. You can either specify the Availability Zone in which your instance runs, or let Amazon EC2 select an Availability Zone for you\. When you launch your instance, you secure it by specifying a key pair and security group\. When you connect to your instance, you must specify the private key of the key pair that you specified when launching your instance\.

![\[An Amazon EBS-backed instance with an additional Amazon Elastic Block Store (EBS) volume\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/overview_getting_started.png)

**Tasks**

To complete this tutorial, perform the following tasks:

1. [Launch an instance](#ec2-launch-instance)

1. [Connect to your instance](#ec2-connect-to-instance-windows)

1. [Clean up your instance](#ec2-clean-up-your-instance)

**Related tutorials**
+ If you'd prefer to launch a Linux instance, see this tutorial in the *Amazon EC2 User Guide for Linux Instances*: [Getting started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)\.
+ If you'd prefer to use the command line, see this tutorial in the *AWS Command Line Interface User Guide*: [Using Amazon EC2 through the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-using-ec2.html)\.

## Prerequisites<a name="ec2-getstarted-prereqs"></a>

Before you begin, be sure that you've completed the steps in [Setting up with Amazon EC2](get-set-up-for-amazon-ec2.md)\.

## Step 1: Launch an instance<a name="ec2-launch-instance"></a>

You can launch a Windows instance using the AWS Management Console as described in the following procedure\. This tutorial is intended to help you launch your first instance quickly, so it doesn't cover all possible options\. For more information about the advanced options, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, choose **Launch Instance**\.

1. The **Choose an Amazon Machine Image \(AMI\)** page displays a list of basic configurations, called *Amazon Machine Images \(AMIs\)*, that serve as templates for your instance\. Select the AMI for Windows Server 2016 Base or later\. Notice that these AMIs are marked "Free tier eligible\."

1. On the **Choose an Instance Type** page, you can select the hardware configuration of your instance\. Select the `t2.micro` instance type, which is selected by default\. The `t2.micro` instance type is eligible for the free tier\. In Regions where `t2.micro` is unavailable, you can use a `t3.micro` instance under the free tier\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

1. Choose **Review and Launch** to let the wizard complete the other configuration settings for you\.

1. On the **Review Instance Launch** page, under **Security Groups**, you'll see that the wizard created and selected a security group for you\. You can use this security group, or alternatively you can select the security group that you created when getting set up using the following steps:

   1. Choose **Edit security groups**\.

   1. On the **Configure Security Group** page, ensure that **Select an existing security group** is selected\.

   1. Select your security group from the list of existing security groups, and then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted for a key pair, select **Choose an existing key pair**, then select the key pair that you created when getting set up\.
**Warning**  
Don't select **Proceed without a key pair**\. If you launch your instance without a key pair, then you can't connect to it\.

   When you are ready, select the acknowledgement check box, and then choose **Launch Instances**\.

1. A confirmation page lets you know that your instance is launching\. Choose **View Instances** to close the confirmation page and return to the console\.

1. On the **Instances** screen, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running` and it receives a public DNS name\. \(If the **Public DNS \(IPv4\)** column is hidden, choose **Show/Hide Columns** \(the gear\-shaped icon\) in the top right corner of the page and then select **Public DNS \(IPv4\)**\.\)

1. It can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks; you can view this information in the **Status Checks** column\.

## Step 2: Connect to your instance<a name="ec2-connect-to-instance-windows"></a>

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

## Step 3: Clean up your instance<a name="ec2-clean-up-your-instance"></a>

After you've finished with the instance that you created for this tutorial, you should clean up by terminating the instance\. If you want to do more with this instance before you clean up, see [Next steps](#ec2-next-steps)\.

**Important**  
Terminating an instance effectively deletes it; you can't reconnect to an instance after you've terminated it\.

If you launched an instance that is not within the [AWS Free Tier](https://aws.amazon.com/free/), you'll stop incurring charges for that instance as soon as the instance status changes to `shutting down` or `terminated`\. If you'd like to keep your instance for later, but not incur charges, you can stop the instance now and then start it again later\. For more information, see [Stopping Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)\.

**To terminate your instance**

1. In the navigation pane, choose **Instances**\. In the list of instances, select the instance\.

1. Choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

   Amazon EC2 shuts down and terminates your instance\. After your instance is terminated, it remains visible on the console for a short while, and then the entry is deleted\.

## Next steps<a name="ec2-next-steps"></a>

After you start your instance, you might want to try some of the following exercises:
+ Learn how to remotely manage your EC2 instance using Run Command\. For more information, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the *AWS Systems Manager User Guide*\.
+ Configure a CloudWatch alarm to notify you if your usage exceeds the Free Tier\. For more information, see [Create a Billing Alarm](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-alarms.html) in the *AWS Billing and Cost Management User Guide*\.
+ Add an EBS volume\. For more information, see [Creating an Amazon EBS volume](ebs-creating-volume.md) and [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.
+ Install the WAMP or WIMP stack\. For more information, see [Tutorial: Installing a WAMP Server on an Amazon EC2 Instance Running Windows Server](install-WAMP.md) and [Tutorial: Installing a WIMP server on an Amazon EC2 instance running Windows Server](install-WIMP.md)\.