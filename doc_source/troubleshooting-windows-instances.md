# Troubleshooting Windows Instances<a name="troubleshooting-windows-instances"></a>

The following procedures and tips can help you troubleshoot problems with your Amazon EC2 Windows instances\.

**Topics**
+ [Troubleshooting Instance Launch Issues](troubleshooting-launch.md)
+ [Troubleshoot an Unreachable Instance](screenshot-service.md)
+ [Resetting a Lost or Expired Windows Administrator Password](ResettingAdminPassword.md)
+ [Common Issues](common-issues.md)
+ [High CPU usage shortly after Windows starts](#high-cpu-issue)
+ [No console output](#no-console-output)
+ [Remote Desktop can't connect to the remote computer](#rdp-issues)
+ [Error Using macOS RDP Client](#troubleshoot-instance-connect-mac-rdp)
+ [RDP displays a black screen instead of the desktop](#rdp-black-screen)
+ [Instance loses network connectivity or scheduled tasks don't run when expected](#instance-loses-network-connectivity)
+ [Windows Server 2012 R2 not available on the network](#server-2012-network-loss)
+ [Troubleshooting Remote Desktop Connection issues using AWS Systems Manager](#Troubleshooting-Remote-Desktop-Connection-issues-using-AWS-Systems-Manager)
+ [Common Messages](common-messages.md)

If you need additional help, you can post a question to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. Be sure to post the ID of your instance and any error messages, including error messages available through console output\.

To get additional information for troubleshooting problems with your instance, use [Using EC2Rescue for Windows Server](Windows-Server-EC2Rescue.md)\. For information about troubleshooting issues with PV drivers, see [Troubleshooting PV Drivers](pvdrivers-troubleshooting.md)\.

## High CPU usage shortly after Windows starts<a name="high-cpu-issue"></a>

If Windows Update is set to **Check for updates but let me choose whether to download and install them** \(the default instance setting\) this check can consume anywhere from 50 \- 99% of the CPU on the instance\. If this CPU consumption causes problems for your applications, you can manually change Windows Update settings in **Control Panel** or you can use the following script in the Amazon EC2 user data field:

```
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update" /v AUOptions /t REG_DWORD /d 3 /f net stop wuauserv net start wuauserv
```

When you execute this script specify a value for /d\. The default value is 3\. Possible values include the following: 
+ Never check for updates
+ Check for updates but let me choose whether to download and install them
+ Download updates but let me choose whether to install them
+ Install updates automatically

**To modify the user data for a Amazon EBS\-backed instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. Choose **Actions**, select **Instance State**, and then choose **Stop**\.

1. In the confirmation dialog box, select **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, select **Actions**, select **Instance Settings**, and then choose **View/Change User Data**\. Note that you can't change the user data if the instance is running, but you can view it\.

1. In the **View/Change User Data** dialog box, update the user data, and then choose **Save**\.

After you modify the user data for your instance, you can execute it\. For more information, see [User Data Execution](ec2-windows-user-data.md#user-data-execution)\.

## No console output<a name="no-console-output"></a>

For Windows instances, the instance console displays the output from the EC2Config service running on the instance\. The output logs the status of tasks performed during the Windows boot process\. If Windows boots successfully, the last message logged is `Windows is Ready to use`\. Note that you can also display event log messages in the console, but this feature is not enabled by default\. For more information, see [EC2 Service Properties](ec2config-service.md#UsingConfigInterface_WinAMI)\.

To get the console output for your instance using the Amazon EC2 console, select the instance, choose **Actions**, **Instance Settings**, and then **Get System Log**\. To get the console output using the command line, use one of the following commands: [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\) or [Get\-EC2ConsoleOutput](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ConsoleOutput.html) \(AWS Tools for Windows PowerShell\)\.

If the console output is empty, it could indicate an issue with the EC2Config service, such as a misconfigured configuration file, or that Windows failed to boot properly\. To fix the issue, download and install the latest version of EC2Config\. For more information, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\.

## Remote Desktop can't connect to the remote computer<a name="rdp-issues"></a>

Try the following to resolve issues related to connecting to your instance:
+ Verify that you're using the correct public DNS hostname\. \(In the Amazon EC2 console, select the instance and check **Public DNS \(IPv4\)** in the details pane\.\) If your instance is in a VPC and you do not see a public DNS name, you must enable DNS hostnames\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\.
+ Verify that your instance has a public IPv4 address\. If not, you can associate an Elastic IP address with your instance\. For more information, see [Elastic IP Addresses](elastic-ip-addresses-eip.md)\. 
+ To connect to your instance using an IPv6 address, check that your local computer has an IPv6 address and is configured to use IPv6\. If you launched an instance from a Windows Server 2008 SP2 AMI or earlier, your instance is not automatically configured to recognize an IPv6 address assigned to the instance\. For more information, see [Configure IPv6 on Your Instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.
+ Verify that your security group has a rule that allows RDP access\. For more information, see [Create a Security Group](get-set-up-for-amazon-ec2.md#create-a-base-security-group)\.
+ If you copied the password but get the error `Your credentials did not work`, try typing them manually when prompted\. It's possible that you missed a character or got an extra whitespace character when you copied the password\.
+ Verify that the instance has passed status checks\. For more information, see [Status Checks for Your Instances](monitoring-system-instance-status-check.md) and [Troubleshooting Instances with Failed Status Checks](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) \(*Amazon EC2 User Guide for Linux Instances*\)\.
+ \[EC2\-VPC\] Verify that the route table for the subnet has a route that sends all traffic destined outside the VPC to the Internet gateway for the VPC\. For more information, see [Creating a Custom Route Table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing) \(Internet Gateways\) in the *Amazon VPC User Guide*\.
+ Verify that Windows Firewall, or other firewall software, is not blocking RDP traffic to the instance\. We recommend that you disable Windows Firewall and control access to your instance using security group rules\. You can use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [disable the Windows Firewall profiles using the SSM agent](#disable-firewall) \. To disable Windows Firewall on a Windows instance that is not configured for SSM, use [AWSSupport\-ExecuteEC2Rescue](#AWSSupport-ExecuteEC2Rescue), or use the following manual steps: 

### <a name="manual-steps"></a>

#### Manual steps<a name="Manual-steps-disable-firewall"></a>

1. Stop the affected instance and detach its root volume\.

1. Launch a temporary instance in the same Availability Zone as the affected instance\.
**Warning**  
If your temporary instance is based on the same AMI that the original instance is based on, you must complete additional steps or you won't be able to boot the original instance after you restore its root volume because of a disk signature collision\. Alternatively, select a different AMI for the temporary instance\. For example, if the original instance uses the AWS Windows AMI for Windows Server 2008 R2, launch the temporary instance using the AWS Windows AMI for Windows Server 2012\.

1. Attach the root volume from the affected instance to this temporary instance\. Connect to the temporary instance, open the **Disk Management** utility, and bring the drive online\.

1. Open **Regedit** and select **HKEY\_LOCAL\_MACHINE**\. From the **File** menu, choose **Load Hive**\. Select the drive, open the file `Windows\System32\config\SYSTEM`, and specify a key name when prompted \(you can use any name\)\.

1. Select the key you just loaded and navigate to `ControlSet001\Services\SharedAccess\Parameters\FirewallPolicy`\. For each key with a name of the form *xxxx*Profile, select the key and change `EnableFirewall` from 1 to 0\. Select the key again, and from the **File** menu, choose **Unload Hive**\.

1. \(Optional\) If your temporary instance is based on the same AMI that the original instance is based on, you must complete the following steps or you won't be able to boot the original instance after you restore its root volume because of a disk signature collision\.
**Warning**  
The following procedure describes how to edit the Windows Registry using Registry Editor\. If you are not familiar with the Windows Registry or how to safely make changes using Registry Editor, see [Configure the Registry](https://technet.microsoft.com/en-us/library/cc725612.aspx)\.

   1. Open a command prompt, type regedit\.exe, and press Enter\.

   1. In the **Registry Editor**, choose **HKEY\_LOCAL\_MACHINE** from the context menu \(right\-click\), and then choose **Find**\.

   1. Type Windows Boot Manager and then choose **Find Next**\.

   1. Choose the key named `11000001`\. This key is a sibling of the key you found in the previous step\.

   1. In the right pane, choose `Element` and then choose **Modify** from the context menu \(right\-click\)\.

   1. Locate the four\-byte disk signature at offset 0x38 in the data\. Reverse the bytes to create the disk signature, and write it down\. For example, the disk signature represented by the following data is `E9EB3AA5`:

      ```
      ...
      0030  00 00 00 00 01 00 00 00
      0038  A5 3A EB E9 00 00 00 00
      0040  00 00 00 00 00 00 00 00
      ...
      ```

   1. In a Command Prompt window, run the following command to start Microsoft DiskPart\.

      ```
      diskpart
      ```

   1. Run the following DiskPart command to select the volume\. \(You can verify that the disk number is 1 using the **Disk Management** utility\.\)

      ```
      DISKPART> select disk 1
      
      Disk 1 is now the selected disk.
      ```

   1. Run the following DiskPart command to get the disk signature\.

      ```
      DISKPART>  uniqueid disk
      
      Disk ID: 0C764FA8
      ```

   1. If the disk signature shown in the previous step doesn't match the disk signature from BCD that you wrote down earlier, use the following DiskPart command to change the disk signature so that it matches:

      ```
      DISKPART> uniqueid disk id=E9EB3AA5
      ```

1. Using the **Disk Management** utility, bring the drive offline\.
**Note**  
The drive is automatically offline if the temporary instance is running the same operating system as the affected instance, so you won't need to bring it offline manually\.

1. Detach the volume from the temporary instance\. You can terminate the temporary instance if you have no further use for it\.

1. Restore the root volume of the affected instance by attaching it as `/dev/sda1`\.

1. Start the instance\.
+  Verify that Network Level Authentication is disabled on instances that are not part of an Active Directory domain \(use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [disable NLA](#disable-nla)\)\. 
+ Verify that the Remote Desktop Service \(TermService\) Startup Type is Automatic and the service is started \(use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [enable and start the RDP service](#rdp-auto)\)\. 
+ Verify that you are connecting to the correct Remote Desktop Protocol port, which by default is 3389 \(use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [read the current RDP port](#check-rdp) and [change it back to 3389](#restore-3389)\)\.
+  Verify that Remote Desktop connections are allowed on your instance \(use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [enable Remote Desktop connections](#allow-rdp)\)\.
+ Verify that the password has not expired\. If the password has expired, you can reset it\. For more information, see [Resetting a Lost or Expired Windows Administrator Password](ResettingAdminPassword.md)\.
+ If you attempt to connect using a user account that you created on the instance and receive the error `The user cannot connect to the server due to insufficient access privileges`, verify that you granted the user the right to log on locally\. For more information, see [Grant a Member the Right to Log On Locally](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/ee957044(v%3dws.10))\.
+ If you attempt more than the maximum allowed concurrent RDP sessions, your session is terminated with the message `Your Remote Desktop Services session has ended. Another user connected to the remote computer, so your connection was lost.` By default, you are allowed two concurrent RDP sessions to your instance\.

## Error Using macOS RDP Client<a name="troubleshoot-instance-connect-mac-rdp"></a>

If you are connecting to a Windows Server 2012 R2 instance using the Remote Desktop Connection client from the Microsoft website, you may get the following error:

```
Remote Desktop Connection cannot verify the identity of the computer that you want to connect to.
```

Download the Microsoft Remote Desktop app from the Apple iTunes store and use the app to connect to your instance\.

## RDP displays a black screen instead of the desktop<a name="rdp-black-screen"></a>

Try the following to resolve this issue:
+ Check the console output for additional information\. To get the console output for your instance using the Amazon EC2 console, select the instance, choose **Actions**, select **Instance Settings**, and then choose **Get System Log**\.
+ Verify that you are running the latest version of your RDP client\.
+ Try the default settings for the RDP client\. For more information, see [Remote Session Environment](http://technet.microsoft.com/en-us/library/cc772398.aspx)\.
+ If you are using Remote Desktop Connection, try starting it with the `/admin` option as follows\.

  ```
  mstsc /v:instance /admin
  ```
+ If the server is running a full\-screen application, it might have stopped responding\. Use Ctrl\+Shift\+Esc to start Windows Task Manager, and then close the application\.
+ If the server is over\-utilized, it might have stopped responding\. To monitor the instance using the Amazon EC2 console, select the instance and then select the **Monitoring** tab\. If you need to change the instance type to a larger size, see [Changing the Instance Type](ec2-instance-resize.md)\.

## Instance loses network connectivity or scheduled tasks don't run when expected<a name="instance-loses-network-connectivity"></a>

If you restart your instance and it loses network connectivity, it's possible that the instance has the wrong time\.

By default, Windows instances use Coordinated Universal Time \(UTC\)\. If you set the time for your instance to a different time zone and then restart it, the time becomes offset and the instance temporarily loses its IP address\. The instance regains network connectivity eventually, but this can take several hours\. The amount of time that it takes for the instance to regain network connectivity depends on the difference between UTC and the other time zone\.

This same time issue can also result in scheduled tasks not running when you expect them to\. In this case, the scheduled tasks do not run when expected because the instance has the incorrect time\.

To use a time zone other than UTC persistently, you must set the **RealTimeIsUniversal** registry key\. Without this key, an instance uses UTC after you restart it\.

**To resolve time issues that cause a loss of network connectivity**

1. Ensure that you are running the recommended PV drivers\. For more information, see [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)\.

1. Verify that the following registry key exists and is set to `1`: **HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\TimeZoneInformation\\RealTimeIsUniversal**

## Windows Server 2012 R2 not available on the network<a name="server-2012-network-loss"></a>

For information about troubleshooting a Windows Server 2012 R2 instance that is not available on the network, see [Windows Server 2012 R2 loses network and storage connectivity after an instance reboot](pvdrivers-troubleshooting.md#server2012R2-instance-unavailable)\.

## Troubleshooting Remote Desktop Connection issues using AWS Systems Manager<a name="Troubleshooting-Remote-Desktop-Connection-issues-using-AWS-Systems-Manager"></a>

### AWSSupport\-TroubleshootRDP<a name="AWSSupport-TroubleshootRDP"></a>

The AWSSupport\-TroubleshootRDP automation document allows the user to check or modify common settings on the target instance that can impact Remote Desktop Protocol \(RDP\) connections, such as the **RDP Port**, **Network Layer Authentication \(NLA\)**, and **Windows Firewall** profiles\. By default, the document reads and outputs the values of these settings\.

The AWSSupport\-TroubleshootRDP automation document can be used only with instances that are enabled for [AWS Systems Manager](https://aws.amazon.com/systems-manager/), also referred to as a [managed instance](https://docs.aws.amazon.com/systems-manager/latest/userguide/managed_instances.html)\. Your instance must have the SSM agent installed and have an IAM role attached with permissions to Systems Manager\. You must change the default parameter values to modify the common RDP settings on the target instance\.

1. Log in to the [Systems Manager Console](https://console.aws.amazon.com/systems-manager/)\.

1.  Verify that you are in the same region as the impaired instance\.

1. Open the [AWSSupport\-TroubleshootRDP](https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP) document\.

1. In **Execution Mode**, choose **Execute the entire automation at once**\.

1. In **Input parameters**, in the **InstanceId** field, enable **Show interactive instance picker**\.

1. Choose your Amazon EC2 instance\. **Note:** If you don't see your instance in the list, it's not enabled for Systems Manager\. To configure AWS Identity and Access Management \(IAM\) for SSM Agent, see [Create an Instance Profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-configuring-access-role.html)\.

1. Review the [examples](#AWSSupport-TroubleshootRDP-Examples), then choose **Execute automation**\.

1. To monitor the execution progress, in **Execution** status, wait for the status to change from **Pending** to **Success**\. Expand **Outputs** to view the results\. To view the output of individual steps, in **Executed Steps**, choose the **Step ID\.**

#### Examples<a name="AWSSupport-TroubleshootRDP-Examples"></a>

 The examples below show you how to accomplish common troubleshooting tasks with AWSSupport\-TroubleshootRDP\. Make sure to replace **INSTANCEID** and **REGION** with your values\.

**Example Check the current RDP status**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID" --region REGION  
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion=$LATEST
```

**Example Disable the Windows Firewall**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID,Firewall=Disable" --region REGION 
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion=$LATEST&Firewall=Disable 
```

**Example Disable Network Level Authentication**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID,NLASettingAction 
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion
```

**Example Set RDP Service Startup Type to Automatic and start the RDP service**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID,RDPServiceStartupType=Auto, RDPServiceAction=Start" --region REGION
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion=$LATEST&RDPServiceStartupType=Auto&RDPServiceAction=Start
```

**Example Restore the default RDP Port \(3389\)**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID,RDPPortAction=Modify" --region REGION
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion=$LATEST&RDPPortAction=Modify
```

**Example Allow remote connections**  
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=INSTANCEID,RemoteConnections=Enable" --region REGION
```

**Example**  
AWS Systems Manager Console Deeplink:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=REGION#documentVersion=$LATEST&RemoteConnections=Enable
```

### AWSSupport\-ExecuteEC2Rescue<a name="AWSSupport-ExecuteEC2Rescue"></a>

The AWSSupport\-ExecuteEC2Rescue automation document uses [Using EC2Rescue for Windows Server](Windows-Server-EC2Rescue.md) to automatically troubleshoot and restore EC2 instance connectivity and RDP issues\. For more information, see [Run the EC2Rescue Tool on Unreachable Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2rescue.html)\.

**Note**  
The AWSSupport\-ExecuteEC2Rescue automation document can be used on any Amazon EC2 instance, but it requires a stop and restart of the instance\. Systems Manager Automation stops the instance and creates an Amazon Machine Image \(AMI\)\. Data stored in instance store volumes are lost\. The public IP address changes if you are not using an Elastic IP\. For more information, see [Run the EC2Rescue Tool on Unreachable Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2rescue.html)\.

1. Open the [Systems Manager console](https://console.aws.amazon.com/systems-manager/)\.

1. Verify that you are in the same region as the impaired Amazon EC2 instance\.

1.  Open the [AWSSupport\-ExecuteEC2Rescue](https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-ExecuteEC2Rescue) document\.

1. In **Execution Mode**, choose **Execute the entire automation at once**\.

1.  In the **Input parameters** section, for **UnreachableInstanceId**, enter the Amazon EC2 instance ID of the unreachable instance\.

1.  \(Optional\) For **LogDestination**, enter the Amazon Simple Storage Service \(Amazon S3\) bucket name if you want to collect operating system logs for troubleshooting your Amazon EC2 instance\. Logs are automatically uploaded to the specified bucket\.

1. Choose **Execute automation**\.

1.  To monitor the execution progress, in **Execution status**, wait for the status to change from **Pending** to **Success**\. Expand **Outputs** to view the results\. To view the output of individual steps, in **Executed Steps**, choose the **Step ID**\.