# Managed AWS Windows AMIs<a name="windows-ami-version-history"></a>

AWS provides managed Amazon Machine Images \(AMIs\) that include various versions and configurations of Windows Server\. In general, the AWS Windows AMIs are configured with the default settings used by the Microsoft installation media\. However, there are customizations\. For example, the AWS Windows AMIs come with the following software and drivers:
+ EC2Config service \(through Windows Server 2012 R2\)
+ EC2Launch \(Windows Server 2016 and later\)
+ AWS Systems Manager
+ AWS CloudFormation
+ AWS Tools for Windows PowerShell
+ Network drivers \(SRIOV, ENA, Citrix PV\)
+ Storage drivers \(NVMe, AWS PV, Citrix PV\)
+ Graphics drivers \(NVidia GPU, Elastic GPU\)
+ Spot Instance hibernation

For information about other customizations, see [Configuration changes for AWS Windows AMIs](#windows-ami-configuration)\.

**Contents**
+ [Updating your Windows instance](#update-windows-instance)
+ [Upgrading or migrating to a newer version of Windows Server](#WinAMI_Upgrading)
+ [Subscribing to Windows AMI notifications](#subscribe-notifications)
+ [Configuration changes for AWS Windows AMIs](#windows-ami-configuration)
+ [Details about AWS Windows AMI versions](windows-ami-versions.md)
  + [What to expect in an official AWS Windows AMI](windows-ami-versions.md#windows-ami-creation-standards)
  + [How AWS decides which Windows AMIs to offer](windows-ami-versions.md#windows-ami-creation-standards-AMI-type)
  + [Patches, security updates, and AMI IDs](windows-ami-versions.md#ami-patches-security-ID)
  + [Semiannual channel releases](windows-ami-versions.md#channel-releases)
  + [AWS Windows AMI Version History](windows-ami-versions.md#ec2-windows-ami-version-history)
    + [Monthly AMI updates for 2020 \(to date\)](windows-ami-versions.md#amis-2020)
    + [Monthly AMI updates for 2019](windows-ami-versions.md#amis-2019)
    + [Monthly AMI updates for 2018](windows-ami-versions.md#amis-2018)
    + [Monthly AMI updates for 2017](windows-ami-versions.md#amis-2017)
    + [Monthly AMI updates for 2016](windows-ami-versions.md#amis-2016)
    + [Monthly AMI updates for 2015](windows-ami-versions.md#amis-2015)
    + [Monthly AMI updates for 2014](windows-ami-versions.md#amis-2014)
    + [Monthly AMI updates for 2013](windows-ami-versions.md#amis-2013)
    + [Monthly AMI updates for 2012](windows-ami-versions.md#amis-2012)
    + [Monthly AMI updates for 2011 and earlier](windows-ami-versions.md#amis-2011)
+ [Changes in Windows Server 2016 and later AMIs](#win2k16-amis)
+ [Docker container conflict on Windows Server 2016 instances](#ec2launch-docker)
+ [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](#ec2hibernateagent-2018-03-16)
+ [AMIs for STIG compliance](ami-windows-stig.md)
  + [Core and base operating system](ami-windows-stig.md#base-os-stig)
  + [Internet Explorer \(IE\) 11 STIG V1 Release 18](ami-windows-stig.md#ie-os-stig)
  + [Microsoft \.NET Framework 4\.0 STIG V1 Release 9: STIGS not applied](ami-windows-stig.md#dotnet-os-stig)
  + [Windows Firewall STIG V1 Release 7](ami-windows-stig.md#windows-firewall-stig)
  + [Version history](ami-windows-stig.md#stig-version-history)

## Updating your Windows instance<a name="update-windows-instance"></a>

After you launch a Windows instance, you are responsible for installing updates on it\. You can manually install only the updates that interest you, or you can start from a current AWS Windows AMI and build a new Windows instance\. For information about finding the current AWS Windows AMIs, see [Finding a Windows AMI](finding-an-ami.md)\.

**Note**  
Instances should be stateless when updating\. For more information, see [Managing Your AWS Infrastructure at Scale](https://d1.awsstatic.com/whitepapers/managing-your-aws-infrastructure-at-scale.pdf)\.

For Windows instances, you can install updates to the following services or applications:
+  [Microsoft Windows Server](http://windows.microsoft.com/en-us/windows/windows-update) 
+  [Microsoft SQL Server](http://technet.microsoft.com/en-us/library/ff803383.aspx) 
+  [Windows PowerShell](http://technet.microsoft.com/en-us/scriptcenter/dd772288) 
+ [EC2Launch](ec2launch-download.md)
+ [EC2Config service](UsingConfig_Install.md)
+ [AWS Systems Manager SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
+ [ENA](enhanced-networking-ena.md#enable-enhanced-networking-ena-WIN)
+ [NVMe drivers](aws-nvme-drivers.md#install-nvme-drivers)
+ [PV drivers](Upgrading_PV_drivers.md)
+ [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell)
+ [AWS CloudFormation helper scripts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-helper-scripts-reference.html)

You can reboot a Windows instance after installing updates\. For more information, see [Reboot your instance](ec2-instance-reboot.md)\.

## Upgrading or migrating to a newer version of Windows Server<a name="WinAMI_Upgrading"></a>

For information about how to upgrade or migrate a Windows instance to a newer version of Windows Server, see [Upgrading an Amazon EC2 Windows instance to a newer version of Windows Server](serverupgrade.md)\.

## Subscribing to Windows AMI notifications<a name="subscribe-notifications"></a>

To be notified when new AMIs are released or when previously released AMIs are made private, subscribe for notifications using Amazon SNS\.

**To subscribe to Windows AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must use this Region because the SNS notifications that you are subscribing to were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. For the **Create subscription** dialog box, do the following:

   1. For **Topic ARN**, copy and paste one of the following Amazon Resource Names \(ARNs\):
      + **arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-ami\-update**
      + **arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-ami\-private**

      For **AWS GovCloud \(US\)**:

      **arn:aws\-us\-gov:sns:us\-gov\-west\-1:077303321853:ec2\-windows\-ami\-update** 

   1. For **Protocol**, choose **Email**\.

   1. For **Endpoint**, type an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive a confirmation email with the subject line `AWS Notification - Subscription Confirmation`\. Open the email and choose **Confirm subscription** to complete your subscription\.

Whenever Windows AMIs are released, we send notifications to the subscribers of the `ec2-windows-ami-update` topic\. Whenever released Windows AMIs are made private, we send notifications to the subscribers of the `ec2-windows-ami-private` topic\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from Windows AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must use this Region because the SNS notifications were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the subscriptions and then choose **Actions**, **Delete subscriptions** When prompted for confirmation, choose **Delete**\.

## Configuration changes for AWS Windows AMIs<a name="windows-ami-configuration"></a>

The following changes are applied to each AWS Windows AMI\.


**Clean and prepare**  

| Change | Applies to | 
| --- | --- | 
| Check for pending file renames or reboots, and reboot as needed | All AMIs | 
| Delete `.dmp` files | All AMIs | 
| Delete logs \(event logs, Systems Manager, EC2Config\) | All AMIs | 
| Delete temporary folders and files for sysprep | All AMIs | 
| Clear recent history \(Start menu, Windows Explorer, and so on\) | Windows Server 2012 R2 and earlier | 
| Perform virus scan | All AMIs | 
| Pre\-compile queued \.NET assemblies \(before sysprep\) | All AMIs | 
| Run Windows maintenance tools | Windows Server 2012 R2 and later | 
| Restore default values for Internet Explorer | All AMIs | 
| Restore default values for EC2Config | Windows Server 2012 R2 and earlier | 
| Set EC2Launch to run at the next launch | Windows Server 2016 and later | 
| Reset the Windows wallpaper | All AMIs | 
| Run sysprep | All AMIs | 


**Install and configure**  

| Change | Applies to | 
| --- | --- | 
| Add links to the Amazon EC2 Windows Guide | All AMIs | 
| Attach instance storage volumes to extended mount points | All AMIs | 
| Install the current AWS Tools for Windows PowerShell | All AMIs | 
| Install the current AWS CloudFormation helper scripts | All AMIs | 
| Install the current EC2Config and SSM Agent | Windows Server 2012 R2 and earlier | 
| Install the current EC2Launch and SSM Agent | Windows Server 2016 and later | 
| Install the current AWS PV, ENA, and NVMe drivers | Windows Server 2008 R2 and later | 
| Install the current SRIOV drivers | Windows Server 2012 R2 and later | 
| Install the current Citrix PV driver | Windows Server 2008 SP2 and earlier | 
| Install the current EC2WinUtil driver | Windows Server 2008 R2 and later | 
| Install PowerShell 2\.0 and 3\.0 | Windows Server 2008 SP2 and R2 | 
|  If Microsoft SQL Server is installed: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | All AMIs | 
|  Apply the following hotfixes: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | Windows Server 2008 SP2 and R2 | 
| Allow ICMP traffic through the firewall | Windows Server 2012 R2 and earlier | 
| Enable file and printer sharing | Windows Server 2012 R2 and earlier | 
| Disable RunOnce for Internet Explorer | All AMIs | 
| Enable remote PowerShell | All AMIs | 
|  Configure a paging file on the system volume as follows: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | All AMIs | 
|  Configure an additional system managed paging file on `Z:`, if available  | Windows Server 2012 R2 and earlier | 
| Disable hibernation and delete the hibernation file | All AMIs | 
| Set the performance options for best performance | All AMIs | 
| Set the power setting to high performance | All AMIs | 
| Disable the screen saver password | All AMIs | 
| Set the RealTimeIsUniversal registry key | All AMIs | 
| Set the timezone to UTC | All AMIs | 
| Disable Windows updates and notifications | All AMIs | 
| Run Windows Update and reboot until there are no pending updates | All AMIs | 
| Set the display in all power schemes to never turn off | All AMIs | 
| Set the PowerShell execution policy to "Unrestricted" | All AMIs | 

## Changes in Windows Server 2016 and later AMIs<a name="win2k16-amis"></a>

AWS provides AMIs for Windows Server 2016 and later\. These AMIs include the following high\-level changes from earlier Windows AMIs:
+ To accommodate the change from \.NET Framework to \.NET Core, the EC2Config service has been deprecated on Windows Server 2016 AMIs and replaced by EC2Launch\. EC2Launch is a bundle of Windows PowerShell scripts that perform many of the tasks performed by the EC2Config service\. For more information, see [Configuring a Windows instance using EC2Launch](ec2launch.md)\. 
+ On earlier versions of Windows Server AMIs, you can use the EC2Config service to join an EC2 instance to a domain and configure integration with Amazon CloudWatch\. On Windows Server 2016 and later AMIs, you can use the CloudWatch agent to configure integration with Amazon CloudWatch\. For more information about configuring instances to send log data to CloudWatch, see [Collect Metrics and Logs from Amazon EC2 Instances and On\-Premises Servers with the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)\. For information about joining an EC2 instance to a domain, see [ Join an Instance to a Domain Using the AWS\-JoinDirectoryServiceDomain JSON Document](https://docs.aws.amazon.com/systems-manager/latest/userguide/walkthrough-powershell.html#walkthrough-powershell-domain-join) in the *AWS Systems Manager User Guide*\.

**Other Differences**

Note these additional important differences for instances created from Windows Server 2016 and later AMIs\.
+ By default, EC2Launch does not initialize secondary EBS volumes\. You can configure EC2Launch to initialize disks automatically by either scheduling the script to run or by calling EC2Launch in user data\. For the procedure to initialize disks using EC2Launch, see "Initialize Drives and Drive Letter Mappings" in [Configuring EC2Launch](ec2launch.md#ec2launch-config)\.
+ If you previously enabled CloudWatch integration on your instances by using a local configuration file \(AWS\.EC2\.Windows\.CloudWatch\.json\), you can configure the file to work with the SSM Agent on instances created from Windows Server 2016 and later AMIs\.

For more information, see [Windows Server 2019](https://www.microsoft.com/en-us/cloud-platform/windows-server) on Microsoft\.com\.

## Docker container conflict on Windows Server 2016 instances<a name="ec2launch-docker"></a>

If you run the Docker service on Windows Server 2016 AMIs, the service is configured to use a different CIDR value than the default internal IP address prefix value\. The default value is 172\.16\.0\.0/12\. Windows Server 2016 AMIs use 172\.17\.0\.0/16 to avoid a conflict with the default Amazon EC2 VPC/subnet\. If you don't change VPC/subnet settings for your EC2 instances, then you don't need to do anything\. The conflict is essentially avoided because of the different CIDR values\. If you do change VPC/subnet settings, be aware of these internal IP address prefix values and avoid creating a conflict\. For more information, read the following section\.

**Important**  
If you plan to run Docker on a Windows Server 2016 instance, you must create the instance from the following Amazon Machine Image \(AMI\) or an AMI based on an image with `Windows_Server-2016-English-Full-Containers` in the name\. Otherwise, if you use a different Windows Server 2016 AMI, instances fail to boot correctly after installing Docker and then running Sysprep\.

## Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)<a name="ec2hibernateagent-2018-03-16"></a>

After the release of the 2018\.03\.16 Windows AMIs, we discovered an unquoted path in the configuration of the Amazon EC2 Hibernate Agent\. The agent was included in the AMIs for Windows Server 2008 through Windows Server 2016\. This issue does not impact the AMIs for Windows Server 2003\.

AWS has removed the Windows AMIs dated 2018\.03\.16\. To be notified when new Windows AMIs are available, see [Subscribing to Windows AMI notifications](#subscribe-notifications)\.

To mitigate the issue, you can use one of the following procedures to add the missing quotation marks\. If the agent is running, you must also restart the agent\. Alternatively, you can terminate any instances that you launched from a 2018\.03\.16 Windows AMI and replace them with instances launched using a different AMI\.

**Windows PowerShell**

1. On your Windows instance, open Windows Powershell\.

1. Use the following command to update the configuration, adding the missing quotation marks:

   ```
   cmd /c 'sc config EC2HibernateAgent binPath="\"%ProgramFiles%\Amazon\Hibernate\EC2HibernateAgent.exe\""'
   ```

1. Use the following command to view the updated configuration:

   ```
   (Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EC2HibernateAgent\).ImagePath
   ```

   Verify that the response is enclosed in quotation marks, as shown in the following example:

   ```
   "C:\Program Files\Amazon\Hibernate\EC2HibernateAgent.exe"
   ```

1. Use the following command to check whether `Status` is `Running`:

   ```
   Get-Service EC2HibernateAgent
   ```

   If the agent is running, you must restart it using the following command so that the change takes effect:

   ```
   Restart-Service EC2HibernateAgent
   ```

**Command Prompt**

1. On your Windows instance, open a Command Prompt window\.

1. Use the following command to update the configuration, adding the missing quotation marks:

   ```
   sc config EC2HibernateAgent binPath="\"%ProgramFiles%\Amazon\Hibernate\EC2HibernateAgent.exe\""
   ```

1. Use the following command to view the updated configuration:

   ```
   sc qc EC2HibernateAgent
   ```

   Verify that the path in `BINARY_PATH_NAME` is enclosed in quotation marks, as shown in the following example:

   ```
   "C:\Program Files\Amazon\Hibernate\EC2HibernateAgent.exe"
   ```

1. Use the following command to check whether `STATE` is `RUNNING`:

   ```
   sc query EC2HibernateAgent
   ```

   If the agent is running, you must restart it using the following command so that the change takes effect:

   ```
   sc stop EC2HibernateAgent && sc start EC2HibernateAgent
   ```