# Troubleshoot hibernation<a name="troubleshoot-instance-hibernate"></a>

Use this information to help diagnose and fix issues that you might encounter when hibernating an instance\.

## Can't hibernate immediately after launch<a name="hibernate-troubleshooting-1"></a>

If you try to hibernate an instance too quickly after you've launched it, you get an error\.

You must wait for about five minutes after launch before hibernating\.

## Takes too long to transition from `stopping` to `stopped`, and memory state not restored after start<a name="hibernate-troubleshooting-2"></a>

If it takes a long time for your hibernating instance to transition from the `stopping` state to `stopped`, and if the memory state is not restored after you start, this could indicate that hibernation was not properly configured\.







**Windows Server 2016 and later**  
Check the EC2 launch log and look for messages that are related to hibernation\. To access the EC2 launch log, [connect](connecting_to_windows_instance.md) to the instance and open the `C:\ProgramData\Amazon\EC2-Windows\Launch\Log\Ec2Launch.log` file in a text editor\.

**Note**  
By default, Windows hides files and folders under `C:\ProgramData`\. To view EC2 Launch directories and files, enter the path in Windows Explorer or change the folder properties to show hidden files and folders\.

Find the log lines for hibernation\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that hibernation failed to configure: `Message: Failed to enable hibernation.`

If the log line contains `HibernationEnabled: true`, hibernation was successfully configured\.

**Windows Server 2012 R2 and earlier**  
Check the EC2 config log and look for messages that are related to hibernation\. To access the EC2 config log, [connect](connecting_to_windows_instance.md) to the instance and open the `C:\Program Files\Amazon\Ec2ConfigService\Logs\Ec2ConfigLog.txt` file in a text editor\. Find the log lines for `SetHibernateOnSleep`\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that the instance root volume is not large enough: `SetHibernateOnSleep: Failed to enable hibernation: Hibernation failed with the following error: There is not enough space on the disk.`

If the log line is `SetHibernateOnSleep: HibernationEnabled: true`, hibernation was successfully configured\.

If you do not see any logs from these processes, your AMI might not support hibernation\. For information about supported AMIs, see [Hibernation prerequisites](hibernating-prerequisites.md)\. 

**Instance size**  
If you’re using a T3 or T3a instance with less than 1 GB of RAM, try increasing the size of the instance to one that has at least 1 GB of RAM\.

## Instance "stuck" in the stopping state<a name="hibernate-troubleshooting-3"></a>

If you hibernated your instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshoot stopping your instance](TroubleshootingInstancesStopping.md)\.