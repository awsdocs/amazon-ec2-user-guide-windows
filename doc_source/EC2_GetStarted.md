# Tutorial: Get started with Amazon EC2 Windows instances<a name="EC2_GetStarted"></a>

Use this tutorial to get started with Amazon Elastic Compute Cloud \(Amazon EC2\)\. You'll learn how to launch, connect to, and use a Windows instance\. An *instance* is a virtual server in the AWS Cloud\. With Amazon EC2, you can set up and configure the operating system and applications that run on your instance\.

When you sign up for AWS, you can get started with Amazon EC2 using the [AWS Free Tier](https://aws.amazon.com/free/)\. If you created your AWS account less than 12 months ago, and have not already exceeded the free tier benefits for Amazon EC2, it won't cost you anything to complete this tutorial because we help you select options that are within the free tier benefits\. Otherwise, you'll incur the standard Amazon EC2 usage fees from the time that you launch the instance until you terminate the instance \(which is the final task of this tutorial\), even if it remains idle\.

**Related tutorials**
+ If you'd prefer to launch a Linux instance, see this tutorial in the *Amazon EC2 User Guide for Linux Instances*: [Get started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)\.
+ If you'd prefer to use the command line, see this tutorial in the *AWS Command Line Interface User Guide*: [Using Amazon EC2 through the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-using-ec2.html)\.

**Topics**
+ [Overview](#ec2-get-started-overview)
+ [Prerequisites](#ec2-getstarted-prereqs)
+ [Step 1: Launch an instance](#ec2-launch-instance)
+ [Step 2: Connect to your instance](#ec2-connect-to-instance-windows)
+ [Step 3: Clean up your instance](#ec2-clean-up-your-instance)
+ [Next steps](#ec2-next-steps)

## Overview<a name="ec2-get-started-overview"></a>

The instance launched in this tutorial is an Amazon EBS\-backed instance \(meaning that the root volume is an EBS volume\)\. You can either specify the Availability Zone in which your instance runs, or let Amazon EC2 select an Availability Zone for you\. Availability Zones are multiple, isolated locations within each Region\. You can think of an Availability Zone as an isolated data center\.

When you launch your instance, you secure it by specifying a key pair \(to prove your identity\) and a security group \(which acts as a virtual firewall to control ingoing and outgoing traffic\)\. When you connect to your instance, you must provide the private key of the key pair that you specified when you launched your instance\.

![\[An Amazon EBS-backed instance with an additional Amazon EBS volume\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/overview_getting_started.png)

## Prerequisites<a name="ec2-getstarted-prereqs"></a>

Before you begin, be sure that you've completed the steps in [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)\.

## Step 1: Launch an instance<a name="ec2-launch-instance"></a>

You can launch a Windows instance using the AWS Management Console as described in the following procedure\. This tutorial is intended to help you quickly launch your first instance, so it doesn't cover all possible options\. For information about advanced options, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\. For information about other ways to launch your instance, see [Launch your instance](LaunchingAndUsingInstances.md)\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the EC2 console dashboard, in the **Launch instance** box, choose **Launch instance**, and then choose **Launch instance** from the options that appear\.

1. Under **Name and tags**, for **Name**, enter a descriptive name for your instance\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, do the following:

   1. Choose **Quick Start**, and then choose Windows\. This is the operating system \(OS\) for your instance\.

   1. From **Amazon Machine Image \(AMI\)**, select the AMI for Windows Server 2016 Base or later\.\. Notice that these AMIs are marked **Free tier eligible**\. An *Amazon Machine Image \(AMI\)* is a basic configuration that serves as a template for your instance\.

1. Under **Instance type**, from the **Instance type** list, you can select the hardware configuration for your instance\. Choose the `t2.micro` instance type, which is selected by default\. The `t2.micro` instance type is eligible for the free tier\. In Regions where `t2.micro` is unavailable, you can use a `t3.micro` instance under the free tier\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

1. Under **Key pair \(login\)**, for **Key pair name**, choose the key pair that you created when getting set up\. Note that you must select an **RSA** key\. **ED25519** keys are not supported for Windows instances\.
**Warning**  
Do not choose **Proceed without a key pair \(Not recommended\)**\. If you launch your instance without a key pair, then you can't connect to it\.

1. Next to **Network settings**, choose **Edit**\. For **Security group name**, you'll see that the wizard created and selected a security group for you\. You can use this security group, or alternatively you can select the security group that you created when getting set up using the following steps:

   1. Choose **Select existing security group**\.

   1. From **Common security groups**, choose your security group from the list of existing security groups\.

1. Keep the default selections for the other configuration settings for your instance\. 

1. Review a summary of your instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. A confirmation page lets you know that your instance is launching\. Choose **View all instances** to close the confirmation page and return to the console\.

1. On the **Instances** screen, you can view the status of the launch\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is `pending`\. After the instance starts, its state changes to `running` and it receives a public DNS name\. If the **Public IPv4 DNS** column is hidden, choose the settings icon \( ![\[Settings icon.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/settings-icon.png) \) in the top\-right corner, toggle on **Public IPv4 DNS**, and choose **Confirm**\.

1. It can take a few minutes for the instance to be ready for you to connect to it\. Check that your instance has passed its status checks; you can view this information in the **Status check** column\.

## Step 2: Connect to your instance<a name="ec2-connect-to-instance-windows"></a>

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
![\[Get password for RDP.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows-connect-rdp-get-password.png)

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

## Step 3: Clean up your instance<a name="ec2-clean-up-your-instance"></a>

After you've finished with the instance that you created for this tutorial, you should clean up by terminating the instance\. If you want to do more with this instance before you clean up, see [Next steps](#ec2-next-steps)\.

**Important**  
Terminating an instance effectively deletes it; you can't reconnect to an instance after you've terminated it\.

If you launched an instance that is not within the [AWS Free Tier](https://aws.amazon.com/free/), you'll stop incurring charges for that instance as soon as the instance status changes to `shutting down` or `terminated`\. To keep your instance for later, but not incur charges, you can stop the instance now and then start it again later\. For more information, see [Stop and start your instance](Stop_Start.md)\.

**To terminate your instance**

1. In the navigation pane, choose **Instances**\. In the list of instances, select the instance\.

1. Choose **Instance state**, **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

   Amazon EC2 shuts down and terminates your instance\. After your instance is terminated, it remains visible on the console for a short while, and then the entry is automatically deleted\. You cannot remove the terminated instance from the console display yourself\.

## Next steps<a name="ec2-next-steps"></a>

After you start your instance, you might want to try some of the following exercises:
+ Learn how to remotely manage your EC2 instance using Run Command\. For more information, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the *AWS Systems Manager User Guide*\.
+ Configure a CloudWatch alarm to notify you if your usage exceeds the Free Tier\. For more information, see [Tracking your AWS Free Tier usage](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html) in the *AWS Billing User Guide*\.
+ Add an EBS volume\. For more information, see [Create an Amazon EBS volume](ebs-creating-volume.md) and [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.