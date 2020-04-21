# Troubleshooting Issues with the EC2Config Service<a name="repair-ec2config"></a>

The following information can help you troubleshoot issues with the EC2Config service\.

## Update EC2Config on an Unreachable Instance<a name="repair-stopped-w2k3"></a>

Use the following procedure to update the EC2Config service on a Windows Server instance that is inaccessible using Remote Desktop\.

**To update EC2Config on an Amazon EBS\-backed Windows instance that you can't connect to**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Locate the affected instance\. Open the context \(right\-click\) menu for the instance, choose **Instance State**, and then choose **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. Choose **Launch Instance** and create a temporary `t2.micro` instance in the same Availability Zone as the affected instance\. Use a different AMI than the one that you used to launch the affected instance\.
**Important**  
If you do not create the instance in the same Availability Zone as the affected instance you will not be able to attach the root volume of the affected instance to the new instance\.

1. In the EC2 console, choose **Volumes**\.

1. Locate the root volume of the affected instance\. [Detach the volume](ebs-detaching-volume.md) and then [attach the volume](ebs-attaching-volume.md) to the temporary instance that you created earlier\. Attach it with the default device name \(xvdf\)\.

1. Use Remote Desktop to connect to the temporary instance, and then use the Disk Management utility to [make the volume available for use](ebs-using-volumes.md)\.

1. [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) the latest version of the EC2Config service\. Extract the files from the `.zip` file to the `Temp` directory on the drive you attached\.

1. On the temporary instance, open the Run dialog box, type **regedit**, and press Enter\.

1. Choose `HKEY_LOCAL_MACHINE`\. From the **File** menu, choose **Load Hive**\. Choose the drive and then navigate to and open the following file: `Windows\System32\config\SOFTWARE`\. When prompted, specify a key name\.

1. Select the key you just loaded and navigate to `Microsoft\Windows\CurrentVersion`\. Choose the `RunOnce` key\. If this key doesn't exist, choose `CurrentVersion` from the context \(right\-click\) menu, choose **New** and then choose **Key**\. Name the key `RunOnce`\. 

1. From the context \(right\-click\) menu choose the `RunOnce` key, choose **New** and then choose **String Value**\. Enter `Ec2Install` as the name and `C:\Temp\Ec2Install.exe /quiet` as the data\.

1. Choose the `HKEY_LOCAL_MACHINE\specified key name\Microsoft\Windows NT\CurrentVersion\Winlogon` key\. From the context \(right\-click\) menu choose **New**, and then choose **String Value**\. Enter **AutoAdminLogon** as the name and **1** as the value data\. 

1. Choose the `HKEY_LOCAL_MACHINE\specified key name\Microsoft\Windows NT\CurrentVersion\Winlogon>` key\. From the context \(right\-click\) menu choose **New**, and then choose **String Value**\. Enter **DefaultUserName** as the name and **Administrator** as the value data\.

1. Choose the `HKEY_LOCAL_MACHINE\specified key name\Microsoft\Windows NT\CurrentVersion\Winlogon` key\. From the context \(right\-click\) menu choose **New**, and then choose **String Value**\. Type **DefaultPassword** as the name and enter a password in the value data\. 

1. In the Registry Editor navigation pane, choose the temporary key that you created when you first opened Registry Editor\.

1. From the **File** menu, choose **Unload Hive**\.

1. In Disk Management Utility, choose the drive you attached earlier, open the context \(right\-click\) menu, and choose **Offline**\.

1. In the Amazon EC2 console, detach the affected volume from the temporary instance and reattach it to your instance with the device name `/dev/sda1`\. You must specify this device name to designate the volume as a root volume\.

1. [Stop and start your instance](Stop_Start.md) the instance\.

1. After the instance starts, check the system log and verify that you see the message Windows is ready to use\.

1. Open Registry Editor and choose `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`\. Delete the String Value keys you created earlier: **AutoAdminLogon**, **DefaultUserName**, and **DefaultPassword**\. 

1. Delete or stop the temporary instance you created in this procedure\.