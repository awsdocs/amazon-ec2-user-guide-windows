# Step 1: Installing the AWS Management Pack<a name="ConfiguringAWSmp"></a>

After you download the AWS Management Pack, you must configure it to monitor one or more AWS accounts\.

## System Center 2012<a name="install-2012"></a>

**To install the AWS Management Pack**

1. In the Operations console, on the **Go** menu, click **Administration**, and then click **Management Packs**\.

1. In the **Actions** pane, click **Import Management Packs**\.

1. On the **Select Management Packs** page, click **Add**, and then click **Add from disk**\.

1. In the **Select Management Packs to import** dialog box, select the `Amazon.AmazonWebServices.mpb` file from the location where you downloaded it, and then click **Open**\.

1. On the **Select Management Packs** page, under **Import list**, select the **Amazon Web Services** management pack, and then click **Install**\.
**Note**  
System Center Operations Manager doesn't import any management packs in the **Import** list that display an **Error** icon\.

1. The **Import Management Packs** page shows the progress for the import process\. If a problem occurs, select the management pack in the list to view the status details\. Click **Close**\.

## System Center 2007 R2<a name="install-2007"></a>

**To install the AWS Management Pack**

The management pack is distributed as a Microsoft System Installer file, `AWS-MP-Setup.msi`\. It contains the required DLLs for the watcher node, root management server, and Operations console, as well as the `Amazon.AmazonWebServices.mp` file\.

1. Run `AWS-MP-Setup.msi`\.
**Note**  
If your root management server, Operations console, and watcher node are on different computers, you must run the installer on each computer\.

1. On the **Welcome to the Amazon Web Services Management Pack Setup Wizard** screen, click **Next**\.

1. On the **End\-User License Agreement** screen, read the license agreement, and, if you accept the terms, select the **I accept the terms in the License Agreement** check box, and then click **Next**\.

1. On the **Custom Setup** screen, select the features you want to install, and then click **Next**\.  
**Operations Console**  
Installs `Amazon.AmazonWebServices.UI.Pages.dll` and registers it in the Global Assembly Cache \(GAC\), and then installs `Amazon.AmazonWebServices.mp`\.  
**Root Management Server**  
Installs `Amazon.AmazonWebServices.Modules.dll`, `Amazon.AmazonWebServices.SCOM.SDK.dll` and the AWS SDK for \.NET \(`AWSSDK.dll`\), and then registers them in the GAC\.  
**AWS Watcher Node**  
Installs `Amazon.AmazonWebServices.Modules.dll` and `Amazon.AmazonWebServices.SCOM.SDK.dll`, and then installs the AWS SDK for \.NET \(`AWSSDK.dll`\) and registers it in the GAC\.

1. On the **Ready to install Amazon Web Services Management Pack** screen, click **Install**\.

1. On the **Completed the Amazon Web Services Management Pack Setup Wizard** screen, click **Finish**\.
**Note**  
The required DLLs are copied and registered in the GAC, and the management pack file \(`*.mp`\) is copied to the `Program Files (x86)/Amazon Web Services Management Pack` folder on the computer running the Operations console\. Next, you must import the management pack into System Center\.

1. In the Operations console, on the **Go** menu, click **Administration**, and then click **Management Packs**\.

1. In the **Actions** pane, click **Import Management Packs**\.

1. On the **Select Management Packs** page, click **Add**, and then click **Add from disk**\.

1. In the **Select Management Packs to import** dialog box, change the directory to `C:\Program Files (x86)\Amazon Web Services Management Pack`, select the `Amazon.AmazonWebServices.mp` file, and then click **Open**\.

1. On the **Select Management Packs** page, under **Import list**, select the **Amazon Web Services** management pack, and then click **Install**\.
**Note**  
System Center Operations Manager doesn't import any management packs in the **Import** list that display an **Error** icon\.

1. The **Import Management Packs** page shows the progress for the import process\. If a problem occurs, select the management pack in the list to view the status details\. Click **Close**\.