# Troubleshooting connecting to your Windows instance<a name="troubleshoot-connect-windows-instance"></a>

The following are possible problems you may have and error messages you may see while trying to connect to your Windows instance\.

**Topics**
+ [Remote Desktop can't connect to the remote computer](#rdp-issues)
+ [Error using the macOS RDP client](#troubleshoot-instance-connect-mac-rdp)
+ [RDP displays a black screen instead of the desktop](#rdp-black-screen)
+ [Unable to remotely log on to an instance with a user account that is not an administrator](#remote-failure)
+ [Troubleshooting Remote Desktop issues using AWS Systems Manager](#Troubleshooting-Remote-Desktop-Connection-issues-using-AWS-Systems-Manager)
+ [Enable Remote Desktop on an EC2 Instance With Remote Registry](#troubleshooting-windows-rdp-remote-registry)

## Remote Desktop can't connect to the remote computer<a name="rdp-issues"></a>

Try the following to resolve issues related to connecting to your instance:
+ Verify that you're using the correct public DNS hostname\. \(In the Amazon EC2 console, select the instance and check **Public DNS \(IPv4\)** in the details pane\.\) If your instance is in a VPC and you do not see a public DNS name, you must enable DNS hostnames\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\.
+ Verify that your instance has a public IPv4 address\. If not, you can associate an Elastic IP address with your instance\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 
+ To connect to your instance using an IPv6 address, check that your local computer has an IPv6 address and is configured to use IPv6\. If you launched an instance from a Windows Server 2008 SP2 AMI or earlier, your instance is not automatically configured to recognize an IPv6 address assigned to the instance\. For more information, see [Configure IPv6 on Your Instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.
+ Verify that your security group has a rule that allows RDP access\. For more information, see [Create a security group](get-set-up-for-amazon-ec2.md#create-a-base-security-group)\.
+ If you copied the password but get the error `Your credentials did not work`, try typing them manually when prompted\. It's possible that you missed a character or got an extra white space character when you copied the password\.
+ Verify that the instance has passed status checks\. For more information, see [Status checks for your instances](monitoring-system-instance-status-check.md) and [Troubleshooting Instances with Failed Status Checks](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) \(*Amazon EC2 User Guide for Linux Instances*\)\.
+ Verify that the route table for the subnet has a route that sends all traffic destined outside the VPC to the internet gateway for the VPC\. For more information, see [Creating a Custom Route Table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing) \(Internet Gateways\) in the *Amazon VPC User Guide*\.
+ Verify that Windows Firewall, or other firewall software, is not blocking RDP traffic to the instance\. We recommend that you disable Windows Firewall and control access to your instance using security group rules\. You can use [AWSSupport\-TroubleshootRDP](#AWSSupport-TroubleshootRDP) to [disable the Windows Firewall profiles using SSM Agent](#disable-firewall) \. To disable Windows Firewall on a Windows instance that is not configured for AWS Systems Manager, use [AWSSupport\-ExecuteEC2Rescue](#AWSSupport-ExecuteEC2Rescue), or use the following manual steps: 

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
+ Verify that the password has not expired\. If the password has expired, you can reset it\. For more information, see [Resetting a lost or expired Windows administrator password](ResettingAdminPassword.md)\.
+ If you attempt to connect using a user account that you created on the instance and receive the error `The user cannot connect to the server due to insufficient access privileges`, verify that you granted the user the right to log on locally\. For more information, see [Grant a Member the Right to Log On Locally](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/ee957044(v%3dws.10))\.
+ If you attempt more than the maximum allowed concurrent RDP sessions, your session is terminated with the message `Your Remote Desktop Services session has ended. Another user connected to the remote computer, so your connection was lost.` By default, you are allowed two concurrent RDP sessions to your instance\.

## Error using the macOS RDP client<a name="troubleshoot-instance-connect-mac-rdp"></a>

If you are connecting to a Windows Server 2012 R2 instance using the Remote Desktop Connection client from the Microsoft website, you may get the following error:

```
Remote Desktop Connection cannot verify the identity of the computer that you want to connect to.
```

Download the Microsoft Remote Desktop app from the Mac App Store and use the app to connect to your instance\.

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
+ If the server is over\-utilized, it might have stopped responding\. To monitor the instance using the Amazon EC2 console, select the instance and then select the **Monitoring** tab\. If you need to change the instance type to a larger size, see [Changing the instance type](ec2-instance-resize.md)\.

## Unable to remotely log on to an instance with a user account that is not an administrator<a name="remote-failure"></a>

If you are not able to remotely log on to a Windows instance with a user account that is not an administrator account, ensure that you have granted the user the right to log on locally\. See [Grant a user or group the right to log on locally to the domain controllers in the domain](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee957044(v=ws.10)#grant-a-user-or-group-the-right-to-log-on-locally-to-the-domain-controllers-in-the-domain)\. 

## Troubleshooting Remote Desktop issues using AWS Systems Manager<a name="Troubleshooting-Remote-Desktop-Connection-issues-using-AWS-Systems-Manager"></a>

You can use AWS Systems Manager to troubleshoot issues connecting to your Windows instance using RDP\.

### AWSSupport\-TroubleshootRDP<a name="AWSSupport-TroubleshootRDP"></a>

The AWSSupport\-TroubleshootRDP automation document allows the user to check or modify common settings on the target instance that can impact Remote Desktop Protocol \(RDP\) connections, such as the **RDP Port**, **Network Layer Authentication \(NLA\)**, and **Windows Firewall** profiles\. By default, the document reads and outputs the values of these settings\.

The AWSSupport\-TroubleshootRDP automation document can be used with EC2 instances, on\-premises instances, and virtual machines \(VMs\) that are enabled for use with AWS Systems Manager \(managed instances\)\. In addition, it can also be used with EC2 instances for Windows Server that are *not* enabled for use with Systems Manager\. For information about enabling instances for use with AWS Systems Manager, see [AWS Systems Manager Managed Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/managed_instances.html) in the *AWS Systems Manager User Guide*\.

**To troubleshoot using the AWSSupport\-TroubleshootRDP document**

1. Log in to the [Systems Manager Console](https://console.aws.amazon.com/systems-manager/)\.

1.  Verify that you are in the same Region as the impaired instance\.

1. Open the [AWSSupport\-TroubleshootRDP](https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP) document\.

1. For **Execution Mode**, choose **Simple execution**\.

1. For **Input parameters**, **InstanceId**, enable **Show interactive instance picker**\.

1. Choose your Amazon EC2 instance\.

1. Review the [examples](#AWSSupport-TroubleshootRDP-Examples), then choose **Execute**\.

1. To monitor the execution progress, for **Execution status**, wait for the status to change from **Pending** to **Success**\. Expand **Outputs** to view the results\. To view the output of individual steps, in **Executed Steps**, choose an item from **Step ID\.**

#### AWSSupport\-TroubleshootRDP examples<a name="AWSSupport-TroubleshootRDP-Examples"></a>

The following examples show you how to accomplish common troubleshooting tasks using AWSSupport\-TroubleshootRDP\. You can use either the example AWS CLI [start\-automation\-execution](https://docs.aws.amazon.com/cli/latest/reference/ssm/start-automation-execution.html) command or the provided link to the AWS Management Console\.

**Example: Check the current RDP status**  <a name="check-rdp"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id" --region region_code
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region#documentVersion=$LATEST
```

**Example: Disable the Windows Firewall**  <a name="disable-firewall"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id,Firewall=Disable" --region region_code
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region_code#documentVersion=$LATEST&Firewall=Disable
```

**Example: Disable Network Level Authentication**  <a name="disable-nla"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id,NLASettingAction
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region_code#documentVersion
```

**Example: Set RDP Service Startup Type to Automatic and start the RDP service**  <a name="rdp-auto"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id,RDPServiceStartupType=Auto, RDPServiceAction=Start" --region region_code
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region_code#documentVersion=$LATEST&RDPServiceStartupType=Auto&RDPServiceAction=Start
```

**Example: Restore the default RDP Port \(3389\)**  <a name="restore-3389"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id,RDPPortAction=Modify" --region region_code
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region_code#documentVersion=$LATEST&RDPPortAction=Modify
```

**Example: Allow remote connections**  <a name="allow-rdp"></a>
AWS CLI:  

```
aws ssm start-automation-execution --document-name "AWSSupport-TroubleshootRDP" --parameters "InstanceId=instance_id,RemoteConnections=Enable" --region region_code
```
AWS Systems Manager console:  

```
https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-TroubleshootRDP?region=region_code#documentVersion=$LATEST&RemoteConnections=Enable
```

### AWSSupport\-ExecuteEC2Rescue<a name="AWSSupport-ExecuteEC2Rescue"></a>

The AWSSupport\-ExecuteEC2Rescue automation document uses [Using EC2Rescue for Windows Server](Windows-Server-EC2Rescue.md) to automatically troubleshoot and restore EC2 instance connectivity and RDP issues\. For more information, see [Run the EC2Rescue Tool on Unreachable Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2rescue.html)\.

The AWSSupport\-ExecuteEC2Rescue automation document requires a stop and restart of the instance\. Systems Manager Automation stops the instance and creates an Amazon Machine Image \(AMI\)\. Data stored in instance store volumes is lost\. The public IP address changes if you are not using an Elastic IP address\. For more information, see [Run the EC2Rescue Tool on Unreachable Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2rescue.html) in the *AWS Systems Manager User Guide*\.

**To troubleshoot using the AWSSupport\-ExecuteEC2Rescue document**

1. Open the [Systems Manager console](https://console.aws.amazon.com/systems-manager/)\.

1. Verify that you are in the same Region as the impaired Amazon EC2 instance\.

1.  Open the [AWSSupport\-ExecuteEC2Rescue](https://console.aws.amazon.com/systems-manager/automation/execute/AWSSupport-ExecuteEC2Rescue) document\.

1. In **Execution Mode**, choose **Simple execution**\.

1.  In the **Input parameters** section, for **UnreachableInstanceId**, enter the Amazon EC2 instance ID of the unreachable instance\.

1.  \(Optional\) For **LogDestination**, enter the Amazon Simple Storage Service \(Amazon S3\) bucket name if you want to collect operating system logs for troubleshooting your Amazon EC2 instance\. Logs are automatically uploaded to the specified bucket\.

1. Choose **Execute**\.

1.  To monitor the execution progress, in **Execution status**, wait for the status to change from **Pending** to **Success**\. Expand **Outputs** to view the results\. To view the output of individual steps, in **Executed Steps**, choose the **Step ID**\.

## Enable Remote Desktop on an EC2 Instance With Remote Registry<a name="troubleshooting-windows-rdp-remote-registry"></a>

If your unreachable instance is not managed by AWS Systems Manager Session Manager, then you can use remote registry to enable Remote Desktop\. 

1. From the EC2 console, stop the unreachable instance\.

1. Attach the root volume of the unreachable instance to another instance in the same Availability Zone\.

1. On the instance to which you attached the root volume, open Disk Management\. To open Disk Management, run

   ```
   diskmgmt.msc
   ```

1. Right click on the root volume of the affected instance and choose **Online**\.

1. Open the Windows Registry Editor by running the following command:

   ```
   regedit
   ```

1. In the Registry Editor console tree, choose **HKEY\_LOCAL\_MACHINE**, then select **File**>**Load Hive**\.

1. Select the drive of the attached volume, navigate to `\Windows\System32\config\`, select `SYSTEM`, and then choose **Open**\.

1. For **Key Name**, enter a unique name for the hive and choose **OK**\.

1. Back up the registry hive before making any changes to the registry\. 

   1. In the Registry Editor console tree, select the hive that you loaded: `HKEY_LOCAL_MACHINE\your key name`\.

   1. Choose **File**>**Export**\.

   1. In the Export Registry File dialog box, choose the location to which you want to save the backup copy, and then type a name for the backup file in the **File name** field\.

   1. Choose **Save**\.

1. In the Registry Editor console tree, navigate to `HKEY_LOCAL_MACHINE\your key name\ControlSet001\Control\Terminal Server`, and then, in the details pane, double\-click on **fDenyTSConnections**\.

1. In the **Edit DWORD** value box, enter `0` in the **Value data** field\. 

1. Choose **OK**\.
**Note**  
If the value in the **Value data** field is `1`, then the instance will deny remote desktop connections\. A value of `0` allows remote desktop connections\.

1. Close the Registry Editor and the Disk Management consoles\.

1. From the EC2 console, detach the root volume from the instance to which you attached it and reattach it to the unreachable instance\. When attaching the volume to the unreachable instance, enter `/dev/sda1` in the **device** field\.

1. Restart the unreachable instance\. 