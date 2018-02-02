# Upgrading the AWS Management Pack<a name="upgrading-awsmp"></a>

The procedure that you'll use to update AWS Management Pack depends on the version of System Center\.

## System Center 2012<a name="upgrade-2012"></a>

**To upgrade the AWS Management Pack**

1. On the [AWS Add\-Ins for Microsoft System Center](https://aws.amazon.com/windows/system-center/) website, click **SCOM 2012**\. Download `AWS-SCOM-MP-2.0-2.5.zip` to your computer and unzip it\. The `.zip` file includes `Amazon.AmazonWebServices.mpb`\.

1. In the Operations console, on the **Go** menu, click **Administration**, and then click **Management Packs**\.

1. In the **Tasks** pane, click **Import Management Packs**\.

1. On the **Select Management Packs** page, click **Add**, and then click **Add from disk**\.

1. In the **Select Management Packs to import** dialog box, select the `Amazon.AmazonWebServices.mpb` file from the location where you downloaded it, and then click **Open**\.

1. On the **Select Management Packs** page, under **Import list**, select the **Amazon Web Services** management pack, and then click **Install**\.

   If the **Install** button is disabled, upgrading to the current version is not supported and you must uninstall the AWS Management Pack before you can install the current version\. For more information, see [Uninstalling the AWS Management Pack](uninstalling-awsmp.md)\.

## System Center 2007 R2<a name="upgrade-2007"></a>

**To upgrade the AWS Management Pack**

1. On the Management Server, go to the [AWS Add\-Ins for Microsoft System Center](https://aws.amazon.com/windows/system-center/) website and click **SCOM 2007**\. Save `AWS-MP-Setup-2.5.msi`, and then run it\.

1. Click **Next** and follow the directions to upgrade the components that you installed previously\.

1. If your root management server, Operations console, and watcher node are on different computers, you must download and run the setup program on each computer\.

1. On the watcher node, open a Command Prompt window as an administrator and run the following commands\.

   ```
   C:\> net stop HealthService
   The System Center Management service is stopping.
   The System Center Management service was stopped successfully.
   
   C:\> net start HealthService
   The System Center Management service is starting.
   The System Center Management service was started successfully.
   ```

1. In the Operations console, on the **Go** menu, click **Administration**, and then click **Management Packs**\.

1. In the **Actions** pane, click **Import Management Packs**\.

1. On the **Select Management Packs** page, click **Add**, and then click **Add from disk**\.

1. In the **Select Management Packs to import** dialog box, change the directory to `C:\Program Files (x86)\Amazon Web Services Management Pack`, select the `Amazon.AmazonWebServices.mp` file, and then click **Open**\.

1. On the **Select Management Packs** page, under **Import list**, select the **Amazon Web Services** management pack, and then click **Install**\.

   If the **Install** button is disabled, upgrading to the current version is not supported and you must uninstall AWS Management Pack first\. For more information, see [Uninstalling the AWS Management Pack](uninstalling-awsmp.md)\.