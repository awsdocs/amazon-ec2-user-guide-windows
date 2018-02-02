# Stopping, Restarting, Deleting, or Uninstalling EC2Config<a name="UsingConfig_StopDelete"></a>

You can manage the EC2Config service just as you would any other service\.

To apply updated settings to your instance, you can stop and restart the service\. If you're manually installing EC2Config, you must stop the service first\.

**To stop the EC2Config service**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, point to **Administrative Tools**, and then click **Services**\.

1. In the list of services, right\-click **EC2Config**, and select **Stop**\.

**To restart the EC2Config service**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, point to **Administrative Tools**, and then click **Services**\.

1. In the list of services, right\-click **EC2Config**, and select **Restart**\.

If you don't need to update the configuration settings, create your own AMI, or use Amazon EC2 Systems Manager \(SSM\), you can delete and uninstall the service\. Deleting a service removes its registry subkey\. Uninstalling a service removes the files, the registry subkey, and any shortcuts to the service\.

**To delete the EC2Config service**

1. Start a command prompt window\.

1. Run the following command:

   ```
   sc delete ec2config
   ```

**To uninstall EC2Config**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, click **Control Panel**\.

1. Double\-click **Programs and Features**\.

1. On the list of programs, select **EC2ConfigService**, and click **Uninstall** \.