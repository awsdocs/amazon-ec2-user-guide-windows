# Troubleshooting an upgrade<a name="os-upgrade-trbl"></a>

AWS provides upgrade support for issues or problems with the Upgrade Helper Service, an AWS utility that helps you perform in\-place upgrades involving Citrix PV drivers\.

After the upgrade, the instance might temporarily experience higher than average CPU utilization while the \.NET Runtime Optimization service optimizes the \.NET framework\. This is expected behavior\.

If the instance has not passed both status checks after several hours, check the following\.
+ If you upgraded to Windows Server 2008 and both status checks fail after several hours, the upgrade may have failed and be presenting a prompt to **Click OK** to confirm rolling back\. Because the console is not accessible at this state, there is no way to click the button\. To get around this, perform a reboot via the Amazon EC2 console or API\. The reboot takes ten minutes or more to initiate\. The instance might become available after 25 minutes\.
+ Remove applications or server roles from the server and try again\.

If the instance does not pass both status checks after removing applications or server roles from the server, do the following\.
+ Stop the instance and attach the root volume to another instance\. For more information, see the description of how to stop and attach the root volume to another instance in ["Waiting for the metadata service"](common-messages.md#metadata-unavailable)\.
+ Analyze Windows Setup log files and event logs for failures\. 

For other issues or problems with an operating system upgrade or migration, we recommend reviewing the articles listed in [Before you begin an in\-place upgrade](os-inplaceupgrade.md#os-upgrade-before)\.