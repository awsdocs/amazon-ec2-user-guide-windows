# Managed AWS Windows AMIs<a name="aws-windows-ami"></a>

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

For information about other customizations, see [Configuration changes for AWS Windows AMIs](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#windows-ami-configuration)\.

**Contents**
+ [Details about AWS Windows AMI versions](#windows-ami-versions)
  + [What to expect in an official AWS Windows AMI](#windows-ami-creation-standards)
  + [How AWS decides which Windows AMIs to offer](#windows-ami-creation-standards-AMI-type)
  + [Patches, security updates, and AMI IDs](#ami-patches-security-ID)
  + [Semiannual channel releases](#channel-releases)
+ [Configuration changes for AWS Windows AMIs](#windows-ami-configuration)
+ [Update your Windows instance](#update-windows-instance)
+ [Upgrade or migrate to a newer version of Windows Server](#WinAMI_Upgrading)
+ [Subscribe to Windows AMI notifications](#subscribe-notifications)
+ [Changes in Windows Server 2016 and later AMIs](#win2k16-amis)
+ [Docker container conflict on Windows Server 2016 instances](#ec2launch-docker)
+ [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](#ec2hibernateagent-2018-03-16)

## Details about AWS Windows AMI versions<a name="windows-ami-versions"></a>

### What to expect in an official AWS Windows AMI<a name="windows-ami-creation-standards"></a>

AWS provides AMIs with a variety of configurations for all supported Windows Operating System versions\. For each of these images, AWS:
+ Installs all Microsoft recommended Windows security patches\. We release images shortly after the monthly Microsoft patches are made available\.
+ Installs the latest drivers for AWS hardware, including network and disk drivers, EC2WinUtil for troubleshooting, as well as GPU drivers in selected AMIs\.
+ Includes AWS helper software, like [EC2 Config](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2config-service.html) for Server 2012 R2 and earlier, or [EC2 Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch.html) for Server 2016 and later\.
+ Configures Windows Time to use the [AWS Time Service](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html#default-ntp-settings)\.
+ Makes changes in all power schemes to set the display to never turn off\.
+ Performs minor bug fixes – generally one\-line registry changes to enable or disable features that we have found to improve performance on AWS\.

Other than the adjustments listed above, we keep our AMIs as close as possible to the default install\. This means we default to the “stock” PowerShell or \.NET framework versions, don’t install Windows Features, and generally don’t change the AMI\.

### How AWS decides which Windows AMIs to offer<a name="windows-ami-creation-standards-AMI-type"></a>

Each AMI is extensively tested prior to release to the general public\. We periodically streamline our AMI offerings to simplify customer choice and to reduce costs\.
+ New AMI offerings are created for new OS releases\. You can count on AWS releasing “Base,” “Core/Container,” and “SQL Express/Standard/Web/Enterprise” offerings in English and other widely used languages\. The primary difference between Base and Core offerings is that Base offerings have a desktop/GUI whereas Core offerings are PowerShell command line only\. For more information about Windows Server Core, see [ https://docs\.microsoft\.com/en\-us/windows\-server/administration/server\-core/what\-is\-server\-core](https://docs.microsoft.com/en-us/windows-server/administration/server-core/what-is-server-core)\.
+ New AMI offerings are created to support new platforms – for example, the Deep Learning and “NVidia” AMIs were created to support customers using our GPU\-based instance types \(P2 and P3, G2 and G3, etc\.\)\.
+ Less popular AMIs are sometimes removed\. If we see a particular AMI is launched only a few times in its entire lifespan, we will remove it in favor of more widely used options\.

If there is an AMI variant that you would like to see, let us know by filing a ticket with Cloud Support, or by providing feedback through [one of our established channels](https://aws.amazon.com/premiumsupport/knowledge-center/send-feedback-aws/)\.

### Patches, security updates, and AMI IDs<a name="ami-patches-security-ID"></a>

AWS provides updated, fully\-patched Windows AMIs within five business days of Microsoft's patch Tuesday \(the second Tuesday of each month\)\. The new AMIs are available immediately through the **Images** page in the Amazon EC2 console\. The new AMIs are available in the AWS Marketplace and the **Quick Start** tab of the launch instance wizard within a few days of their release\.

**Note**  
Instances launched from the latest Windows Server 2019 AMIs may show a Windows Update dialog message stating "Some settings are managed by your organization\." This message appears as a result of changes in Windows Server 2019 and does not impact the behavior of Windows Update or your ability to manage update settings\.  
To remove this warning, see ["Some settings are managed by your organization"](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/common-messages.html#some-settings-managed-by-org)\.

To ensure that customers have the latest security updates by default, AWS keeps Windows AMIs available for three months\. After releasing new Windows AMIs, AWS makes the Windows AMIs that are older than three months private within 10 days\. After an AMI has been made private, if you look at an instance launched from that AMI in the console, the **AMI ID** field states, "Cannot load detail for ami\-xxxxx\. You may not be permitted to view it\." You can still retrieve the AMI ID using the AWS CLI or an AWS SDK\.

The Windows AMIs in each release have new AMI IDs\. Therefore, we recommend that you write scripts that locate the latest AWS Windows AMIs by their names, rather than by their IDs\. For more information, see the following examples:
+ [Get\-EC2ImageByName](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html#pstools-ec2-get-ec2imagebyname) \(AWS Tools for Windows PowerShell\)
+ [Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store/)
+ [Walkthrough: Looking Up Amazon Machine Image IDs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-custom-resources-lambda-lookup-amiids.html) \(AWS Lambda, AWS CloudFormation\)

### Semiannual channel releases<a name="channel-releases"></a>

AWS provides Windows Server semiannual channel releases that combine the scale, performance, and elasticity of AWS with the new capabilities in the [Semiannual channel release versions of Windows Server](https://docs.microsoft.com/en-us/windows-server/get-started/semi-annual-channel-overview)\. 

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
|  If Microsoft SQL Server is installed: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-windows-ami.html)  | All AMIs | 
|  Apply the following hotfixes: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-windows-ami.html)  | Windows Server 2008 SP2 and R2 | 
| Allow ICMP traffic through the firewall | Windows Server 2012 R2 and earlier | 
| Enable file and printer sharing | Windows Server 2012 R2 and earlier | 
| Disable RunOnce for Internet Explorer | All AMIs | 
| Enable remote PowerShell | All AMIs | 
|  Configure a paging file on the system volume as follows: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-windows-ami.html)  | All AMIs | 
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

## Update your Windows instance<a name="update-windows-instance"></a>

After you launch a Windows instance, you are responsible for installing updates on it\. You can manually install only the updates that interest you, or you can start from a current AWS Windows AMI and build a new Windows instance\. For information about finding the current AWS Windows AMIs, see [Finding a Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/finding-an-ami.html)\.

**Note**  
Instances should be stateless when updating\. For more information, see [Managing Your AWS Infrastructure at Scale](https://d1.awsstatic.com/whitepapers/managing-your-aws-infrastructure-at-scale.pdf)\.

For Windows instances, you can install updates to the following services or applications:
+  [Microsoft Windows Server](http://windows.microsoft.com/en-us/windows/windows-update) 
+  [Microsoft SQL Server](http://technet.microsoft.com/en-us/library/ff803383.aspx) 
+  [Windows PowerShell](http://technet.microsoft.com/en-us/scriptcenter/dd772288) 
+ [EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-download.html)
+ [EC2Config service](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_Install.html)
+ [AWS Systems Manager SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
+ [ENA](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html#enable-enhanced-networking-ena-WIN)
+ [NVMe drivers](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/aws-nvme-drivers.html#install-nvme-drivers)
+ [PV drivers](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Upgrading_PV_drivers.html)
+ [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell)
+ [AWS CloudFormation helper scripts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-helper-scripts-reference.html)

You can reboot a Windows instance after installing updates\. For more information, see [Reboot your instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-instance-reboot.html)\.

## Upgrade or migrate to a newer version of Windows Server<a name="WinAMI_Upgrading"></a>

For information about how to upgrade or migrate a Windows instance to a newer version of Windows Server, see [Upgrading an Amazon EC2 Windows instance to a newer version of Windows Server](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/serverupgrade.html)\.

## Subscribe to Windows AMI notifications<a name="subscribe-notifications"></a>

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

## Changes in Windows Server 2016 and later AMIs<a name="win2k16-amis"></a>

AWS provides AMIs for Windows Server 2016 and later\. These AMIs include the following high\-level changes from earlier Windows AMIs:
+ To accommodate the change from \.NET Framework to \.NET Core, the EC2Config service has been deprecated on Windows Server 2016 AMIs and replaced by EC2Launch\. EC2Launch is a bundle of Windows PowerShell scripts that perform many of the tasks performed by the EC2Config service\. For more information, see [Configuring a Windows instance using EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch.html)\. 
+ On earlier versions of Windows Server AMIs, you can use the EC2Config service to join an EC2 instance to a domain and configure integration with Amazon CloudWatch\. On Windows Server 2016 and later AMIs, you can use the CloudWatch agent to configure integration with Amazon CloudWatch\. For more information about configuring instances to send log data to CloudWatch, see [Collect Metrics and Logs from Amazon EC2 Instances and On\-Premises Servers with the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)\. For information about joining an EC2 instance to a domain, see [ Join an Instance to a Domain Using the AWS\-JoinDirectoryServiceDomain JSON Document](https://docs.aws.amazon.com/systems-manager/latest/userguide/walkthrough-powershell.html#walkthrough-powershell-domain-join) in the *AWS Systems Manager User Guide*\.

**Other Differences**

Note these additional important differences for instances created from Windows Server 2016 and later AMIs\.
+ By default, EC2Launch does not initialize secondary EBS volumes\. You can configure EC2Launch to initialize disks automatically by either scheduling the script to run or by calling EC2Launch in user data\. For the procedure to initialize disks using EC2Launch, see "Initialize Drives and Drive Letter Mappings" in [Configure EC2Launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch.html#ec2launch-config)\.
+ If you previously enabled CloudWatch integration on your instances by using a local configuration file \(AWS\.EC2\.Windows\.CloudWatch\.json\), you can configure the file to work with the SSM Agent on instances created from Windows Server 2016 and later AMIs\.

For more information, see [Windows Server 2019](https://www.microsoft.com/en-us/cloud-platform/windows-server) on Microsoft\.com\.

## Docker container conflict on Windows Server 2016 instances<a name="ec2launch-docker"></a>

If you run the Docker service on Windows Server 2016 AMIs, the service is configured to use a different CIDR value than the default internal IP address prefix value\. The default value is 172\.16\.0\.0/12\. Windows Server 2016 AMIs use 172\.17\.0\.0/16 to avoid a conflict with the default Amazon EC2 VPC/subnet\. If you don't change VPC/subnet settings for your EC2 instances, then you don't need to do anything\. The conflict is essentially avoided because of the different CIDR values\. If you do change VPC/subnet settings, be aware of these internal IP address prefix values and avoid creating a conflict\. For more information, read the following section\.

**Important**  
If you plan to run Docker on a Windows Server 2016 instance, you must create the instance from the following Amazon Machine Image \(AMI\) or an AMI based on an image with `Windows_Server-2016-English-Full-Containers` in the name\. Otherwise, if you use a different Windows Server 2016 AMI, instances fail to boot correctly after installing Docker and then running Sysprep\.

## Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)<a name="ec2hibernateagent-2018-03-16"></a>

After the release of the 2018\.03\.16 Windows AMIs, we discovered an unquoted path in the configuration of the Amazon EC2 Hibernate Agent\. The agent was included in the AMIs for Windows Server 2008 through Windows Server 2016\. This issue does not impact the AMIs for Windows Server 2003\.

AWS has removed the Windows AMIs dated 2018\.03\.16\. To be notified when new Windows AMIs are available, see [Subscribing to Windows AMI notifications](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#subscribe-notifications)\.

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