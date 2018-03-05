# Step 4: Run the Add Monitoring Wizard<a name="RunAddMonitoringWizard"></a>

You can configure the AWS Management Pack to monitor a particular AWS account by using the Add Monitoring Wizard, which is available in the **Authoring** workspace of the Operations console\. This wizard creates a management pack that contains the settings for the AWS account to monitor\. You must run this wizard to monitor each AWS account\. For example, if you want to monitor two AWS accounts, you must run the wizard twice\.

## System Center 2012<a name="add-monitoring-2012"></a>

**To run the Add Monitoring Wizard on System Center 2012 — Operations Manager**

1. In the Operations console, on the **Go** menu, click **Authoring**\.

1. In the **Authoring** workspace, expand the **Management Pack Templates** node, right\-click **Amazon Web Services**, and then click **Add Monitoring Wizard**\.

1. In the **Add Monitoring Wizard**, in the **Select the monitoring type** list, select **Amazon Web Services**, and then click **Next**\.

1. On the **General Properties** page, in the **Name** box, enter a name \(for example, "My AWS Resources"\)\. In the **Description** box, enter a description\. 

1. In the **Select destination management pack** list, select an existing management pack \(or click **New** to create one\) where you want to save the settings\. Click **Next**\.  
![\[Configuring the AWS Management Pack for monitoring an AWS account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AddMonitoringWizard-Step2.png)

   By default, when you create a management pack object, disable a rule or monitor, or create an override, Operations Manager saves the setting to the default management pack\. As a best practice, you should create a separate management pack for each sealed management pack that you want to customize, instead of saving your customized settings to the default management pack\.

1. The AWS Management Pack automatically creates a resource pool and adds the management servers to it\. To control server membership, make the following changes:

   1. Click **Administration** on the **Go** menu\.

   1. Click the **Resource Pools** node\.

   1. Right\-click the **AWS Resource Pool** in the **Resource Pools** pane and select **Manual Membership**\.  
![\[Configuring the AWS Management Pack for monitoring an AWS account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ResourcePool-ManualMembership.png)

   1. Right\-click the **AWS Resource Pool** in the **Resource Pools** pane and select **Properties**\.

   1. On the **Pool Membership** page, remove the management servers that should not monitor AWS resources\.  
![\[Configuring the AWS Management Pack for monitoring an AWS account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ResourcePool-MemberServers.png)

1. After the AWS Management Pack is configured, it shows up as a sub\-folder of the `Amazon Web Services` folder in the **Monitoring** workspace of the Operations console\.  
![\[Configuring the AWS Management Pack for monitoring an AWS account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AddMonitoringWizard-CreatedNodeInMonitoring.png)

## System Center 2007 R2<a name="add-monitoring-2007"></a>

**To run the Add Monitoring Wizard on System Center Operations Manager 2007**

1. In the Operations console, on the **Go** menu, click **Authoring**\.

1. In the **Authoring** workspace, expand the **Management Pack Templates** node, right\-click **Amazon Web Services**, and then click **Add Monitoring Wizard**\.

1. In the **Add Monitoring Wizard**, in the **Select the monitoring type list**, select **Amazon Web Services**, and then click **Next**\.

1. On the **General Properties** page, in the **Name** box, enter a name \(for example, "My AWS Resources"\)\. In the **Description** box, enter a description\. 

1. In the **Select destination management pack** drop\-down list, select an existing management pack \(or click **New** to create a new one\) where you want to save the settings\. Click **Next**\.  
![\[Configuring the AWS Management Pack to monitor an AWS account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/monitor_aws_account.png)

   By default, when you create a management pack object, disable a rule or monitor, or create an override, Operations Manager saves the setting to the default management pack\. As a best practice, you should create a separate management pack for each sealed management pack that you want to customize, instead of saving your customized settings to the default management pack\.

1. On the **Watcher Node Configuration** page, in the **Watcher Node** list, select an agent\-managed computer to act as the watcher node\.

1. In the **Select AWS Run As account** drop\-down list, select the Run As account that you created earlier, and then click **Create**\.

1. After the AWS Management Pack is configured, it first discovers the watcher node\. To verify that the watcher node was discovered successfully, navigate to the **Monitoring** workspace in the Operations console\. You should see a new `Amazon Web Services` folder and an `Amazon Watcher Nodes` subfolder under it\. This subfolder displays the watcher nodes\. The AWS Management Pack automatically checks and monitors the watcher node connectivity to AWS\. When the watcher node is discovered, it shows up in this list\. When the watcher node is ready, its state changes to `Healthy`\.
**Note**  
To establish connectivity with AWS, the AWS Management Pack requires that you deploy the AWS SDK for \.NET, modules, and scripts to the watcher node\. This can take about ten minutes\. If the watcher node doesn't appear, or if you see the state as `Not Monitored`, verify your Internet connectivity and IAM permissions\. For more information, see [Troubleshooting the AWS Management Pack](TroubleshootingAWSmp.md)\.  
![\[Checking the watcher node discovery\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/check_watcher_node_discovery.png)

1. After the watcher node is discovered, dependent discoveries are triggered, and the AWS resources are added to the **Monitoring** workspace of the Operations console\.

   The discovery of AWS resources should finish within twenty minutes\. This process can take more time, based on your Operations Manager environment, your AWS environment, the load on the management server, and the load on the watcher node\. For more information, see [Troubleshooting the AWS Management Pack](TroubleshootingAWSmp.md)\.