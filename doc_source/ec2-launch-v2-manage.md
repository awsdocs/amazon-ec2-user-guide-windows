# Stop, restart, delete, or uninstall EC2Launch v2<a name="ec2-launch-v2-manage"></a>

You can manage the EC2Launch v2 service just as you would any other Windows service\.

EC2Launch v2 runs once on boot and executes all of the configured tasks\. After executing tasks, the service enters a stopped state\. When you restart the service, the service will run all of the configured tasks again and return to a stopped state\.

To apply updated settings to your instance, you can stop and restart the service\. If you are manually installing EC2Launch v2, you must first stop the service first\.

**To stop the EC2Launch v2 service**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, choose **Administrative Tools**, and then open **Services**\.

1. In the list of services, right\-click **Amazon EC2Launch**, and select **Stop**\.

**To restart the EC2Launch v2 service**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, choose **Administrative Tools**, and then open **Services**\.

1. In the list of services, right\-click **Amazon EC2Launch**, and select **Restart**\.

If you don’t need to update the configuration settings, create your own AMI, or use AWS Systems Manager, you can delete and uninstall the service\. Deleting a service removes its registry subkey\. Uninstalling a service removes the files, the registry subkeys, and any shortcuts to the service\.

**To delete the EC2Launch v2 service**

1. Start a command prompt window\.

1. Run the following command:

   ```
   sc delete EC2Launch
   ```

**To uninstall EC2Launch v2**

1. Launch and connect to your Windows instance\.

1. On the **Start** menu, select **Control Panel**\.

1. Open **Programs and Features**\.

1. In the list of programs, select **Amazon EC2Launch v2**, and select **Uninstall**\.