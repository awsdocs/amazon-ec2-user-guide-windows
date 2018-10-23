# Managed AWS Windows AMIs<a name="windows-ami-version-history"></a>

AWS provides managed Amazon Machine Images \(AMIs\) that include various versions and configurations of Windows Server\. In general, the AWS Windows AMIs are configured with the default settings used by the Microsoft installation media\. However, there are customizations\. For example, the AWS Windows AMIs come with the following software and drivers:
+ EC2Config service \(through Windows Server 2012 R2\)
+ EC2Launch \(Windows Server 2016 only\)
+ AWS Systems Manager
+ AWS CloudFormation
+ AWS Tools for Windows PowerShell
+ Network drivers \(SRIOV, ENA, Citrix PV\)
+ Storage drivers \(NVMe, AWS PV, Citrix PV\)
+ Graphics drivers \(NVidia GPU, Elastic GPU\)
+ Spot Instance hibernation

For information about other customizations, see [Configuration Changes for AWS Windows AMIs](#windows-ami-configuration)\.

**Topics**
+ [Updating Your Windows Instance](#update-windows-instance)
+ [Upgrading or Migrating to a Newer Version of Windows Server](#WinAMI_Upgrading)
+ [Subscribing to Windows AMI Notifications](#subscribe-notifications)
+ [Configuration Changes for AWS Windows AMIs](#windows-ami-configuration)
+ [Details About AWS Windows AMI Versions](#windows-ami-versions)
+ [Changes in Windows Server 2016 AMIs](#win2k16-amis)
+ [Docker Container Conflict on Windows Server 2016 Instances](#ec2launch-docker)
+ [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](#ec2hibernateagent-2018-03-16)

## Updating Your Windows Instance<a name="update-windows-instance"></a>

After you launch a Windows instance, you are responsible for installing updates on it\. You can manually install only the updates that interest you, or you can start from a current AWS Windows AMI and build a new Windows instance\. For information about finding the current AWS Windows AMIs, see [Finding a Windows AMI](finding-an-ami.md)\.

For Windows instances, you can install updates to the following services or applications:
+  [Microsoft Windows Server](http://windows.microsoft.com/en-us/windows/windows-update) 
+  [Microsoft SQL Server](http://technet.microsoft.com/en-us/library/ff803383.aspx) 
+  [Windows PowerShell](http://technet.microsoft.com/en-us/scriptcenter/dd772288) 
+ [EC2Launch](ec2launch-download.md)
+ [EC2Config service](UsingConfig_Install.md)
+ [SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
+ [PV Drivers](Upgrading_PV_drivers.md)
+ [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell)
+ [AWS CloudFormation helper scripts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-helper-scripts-reference.html)

You can reboot a Windows instance after installing updates\. For more information, see [Reboot Your Instance](ec2-instance-reboot.md)\.

## Upgrading or Migrating to a Newer Version of Windows Server<a name="WinAMI_Upgrading"></a>

For information about how to upgrade or migrate a Windows instance to a newer version of Windows Server, see [Upgrading an Amazon EC2 Windows Instance to a Newer Version of Windows Server](serverupgrade.md)\.

## Subscribing to Windows AMI Notifications<a name="subscribe-notifications"></a>

To be notified when new AMIs are released or when previously released AMIs are made private, subscribe for notifications using Amazon SNS\.

**To subscribe to Windows AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation bar, change the region to **US East \(N\. Virginia\)**, if necessary\. You must use this region because the SNS notifications that you are subscribing to were created in this region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. For the **Create subscription** dialog box, do the following:

   1. For **Topic ARN**, copy and paste one of the following Amazon Resource Names \(ARNs\):
      + **arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-ami\-update**
      + **arn:aws:sns:us\-east\-1:801119661308:ec2\-windows\-ami\-private**

   1. For **Protocol**, choose **Email**\.

   1. For **Endpoint**, type an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive a confirmation email with the subject line `AWS Notification - Subscription Confirmation`\. Open the email and choose **Confirm subscription** to complete your subscription\.

Whenever Windows AMIs are released, we send notifications to the subscribers of the `ec2-windows-ami-update` topic\. Whenever released Windows AMIs are made private, we send notifications to the subscribers of the `ec2-windows-ami-private` topic\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from Windows AMI notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home)\.

1. In the navigation bar, change the region to **US East \(N\. Virginia\)**, if necessary\. You must use this region because the SNS notifications were created in this region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the subscriptions and then choose **Actions**, **Delete subscriptions** When prompted for confirmation, choose **Delete**\.

## Configuration Changes for AWS Windows AMIs<a name="windows-ami-configuration"></a>

The following changes are applied to each AWS Windows AMI\.


**Clean and Prepare**  

| Change | Applies to | 
| --- | --- | 
| Check for pending file renames or reboots, and reboot as needed | All AMIs | 
| Delete `.dmp` files | All AMIs | 
| Delete logs \(event logs, SSM, EC2Config\) | All AMIs | 
| Delete temporary folders and files for sysprep | All AMIs | 
| Clear recent history \(Start menu, Windows Explorer, and so on\) | Windows Server 2012 R2 and earlier | 
| Perform virus scan | All AMIs | 
| Pre\-compile queued \.NET assemblies \(before sysprep\) | All AMIs | 
| Run Windows maintenance tools | Windows Server 2012 R2 and later | 
| Restore default values for Internet Explorer | All AMIs | 
| Restore default values for EC2Config | Windows Server 2012 R2 and earlier | 
| Set EC2Launch to run at the next launch | Windows Server 2016 | 
| Reset the Windows wallpaper | All AMIs | 
| Run sysprep | All AMIs | 


**Install and Configure**  

| Change | Applies to | 
| --- | --- | 
| Add links to the Amazon EC2 Windows Guide | All AMIs | 
| Attach instance storage volumes to extended mount points | All AMIs | 
| Install the current AWS Tools for Windows PowerShell | All AMIs | 
| Install the current AWS CloudFormation helper scripts | All AMIs | 
| Install the current EC2Config and SSM Agent | Windows Server 2012 R2 and earlier | 
| Install the current EC2Launch and SSM Agent | Windows Server 2016 | 
| Install the current AWS PV, ENA, and NVMe drivers | Windows Server 2008 R2 and later | 
| Install the current SRIOV drivers | Windows Server 2012 R2 and later | 
| Install the current Citrix PV driver | Windows Server 2008 SP2 and earlier | 
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

## Details About AWS Windows AMI Versions<a name="windows-ami-versions"></a>

### What to Expect in an Official AWS Windows AMI<a name="windows-ami-creation-standards"></a>

AWS provides AMIs with a variety of configurations for all supported Windows Operating System versions from 2003 R2 to 2016\. For each of these images, AWS:
+ installs all Microsoft recommended Windows security patches\. We release images shortly after each monthly Microsoft patches are made available\.
+ installs the latest drivers for AWS hardware, including network and disk drivers, as well as GPU drivers in selected AMIs\.
+ includes AWS helper software, like [EC2 Config](ec2config-service.md) for Server 2012 R2 and earlier, or [EC2 Launch](ec2launch.md) for Server 2016\.
+ configures Windows Time to use the [AWS Time Service](windows-set-time.md#default-ntp-settings)\.
+ performs minor bug fixes – generally one\-line registry changes to enable or disable features that we have found to improve performance on AWS\.

Other than the adjustments listed above, we keep our AMIs as close as possible to the default install\. This means we default to the “stock” PowerShell or \.NET framework versions, don’t install Windows Features, and generally don’t change the AMI\.

### How AWS Decides Which Windows AMIs to Offer<a name="windows-ami-creation-standards-AMI-type"></a>

Each AMI is extensively tested prior to release to the general public\. We periodically streamline our AMI offerings to simplify customer choice and to reduce costs\.
+ New AMI offerings are created for new OS releases\. You can count on AWS releasing “Base,” “Core/Container,” and “SQL Express/Standard/Web/Enterprise” offerings in English and other widely used languages\.
+ New AMI offerings are created to support new platforms – for example, the Deep Learning and “NVidia” AMIs were created to support customers using our GPU\-based instance types \(P2 and P3, G2 and G3, etc\.\)\.
+ Less popular AMIs are sometimes removed\. If we see a particular AMI is launched only a few times in its entire lifespan, we will remove it in favor of more widely used options\.

If there is an AMI variant that you would like to see, let us know by filing a ticket with Cloud Support, or by providing feedback through [one of our established channels](https://aws.amazon.com/premiumsupport/knowledge-center/send-feedback-aws/)\.

### Patches, Security Updates, and AMI IDs<a name="ami-patches-security-ID"></a>

AWS provides updated, fully\-patched Windows AMIs within five business days of Microsoft's patch Tuesday \(the second Tuesday of each month\)\. The new AMIs are available immediately through the **Images** page in the Amazon EC2 console\. The new AMIs are available in the AWS Marketplace and the **Quick Start** tab of the launch instance wizard within a few days of their release\.

To ensure that customers have the latest security updates by default, AWS keeps Windows AMIs available for three months\. After releasing new Windows AMIs, AWS makes the Windows AMIs that are older than three months private within 10 days\. After an AMI has been made private, if you look at an instance launched from that AMI in the console, the **AMI ID** field states, "Cannot load detail for ami\-xxxxx\. You may not be permitted to view it\." You can still retrieve the AMI ID using the AWS CLI or an AWS SDK\.

The Windows AMIs in each release have new AMI IDs\. Therefore, we recommend that you write scripts that locate the latest AWS Windows AMIs by their names, rather than by their IDs\. For more information, see the following examples:
+ [Get\-EC2ImageByName](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html#pstools-ec2-get-ec2imagebyname) \(AWS Tools for Windows PowerShell\)
+ [Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store/)
+ [Walkthrough: Looking Up Amazon Machine Image IDs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-custom-resources-lambda-lookup-amiids.html) \(AWS Lambda, AWS CloudFormation\)

The following tables summarize the changes to each release of the AWS Windows AMIs\. Note that some changes apply to all AWS Windows AMIs while others apply to only a subset of these AMIs\.

**Topics**
+ [What to Expect in an Official AWS Windows AMI](#windows-ami-creation-standards)
+ [How AWS Decides Which Windows AMIs to Offer](#windows-ami-creation-standards-AMI-type)
+ [Patches, Security Updates, and AMI IDs](#ami-patches-security-ID)
+ [Monthly AMI Updates for 2018 \(to date\)](#amis-2018)
+ [Monthly AMI Updates for 2017](#amis-2017)
+ [Monthly AMI Updates for 2016](#amis-2016)
+ [Monthly AMI Updates for 2015](#amis-2015)
+ [Monthly AMI Updates for 2014](#amis-2014)
+ [Monthly AMI Updates for 2013](#amis-2013)
+ [Monthly AMI Updates for 2012](#amis-2012)
+ [Monthly AMI Updates for 2011 and earlier](#amis-2011)

For more information about components included in these AMIs, see the following:
+ [EC2Config Version History](ec2config-version-details.md)
+ [EC2Launch Version History](ec2launch-version-details.md)
+ [Amazon SSM Agent Release Notes](https://github.com/aws/amazon-ssm-agent/blob/master/RELEASENOTES.md)
+ [Amazon ENA Driver Versions](enhanced-networking-ena.md#ena-adapter-driver-versions)
+ [AWS PV Driver Version History](xen-drivers-overview.md#pv-driver-history)

### Monthly AMI Updates for 2018 \(to date\)<a name="amis-2018"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2018](https://support.microsoft.com/en-us/help/894199/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2018\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) **Microsoft Windows Server 2016 Datacenter and Standard Editions for Nano Server ** Microsoft ended mainstream support for Windows Server 2016 Datacenter and Standard Editions for Nano Server installation options as of April 10, 2018 \(see the [Microsoft Support Lifecycle page](https://support.microsoft.com/en-us/lifecycle/search?alpha=nano) for more details\)\.  Additional information about Nano Server lifecycle, including details on launching Nano Server as a container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.09\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.08\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.07\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.06\.22 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.06\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.05\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.04\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.03\.24 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.03\.16 |  AWS has removed all Windows AMIs dated 2018\.03\.16 due to an issue with an unquoted path in the configuration for the Amazon EC2 Hibernate Agent\. For more information, see [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](#ec2hibernateagent-2018-03-16)\.  | 
| 2018\.03\.06 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.02\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.02\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.01\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2018\.01\.05 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2017<a name="amis-2017"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2017](https://support.microsoft.com/en-us/help/4073007/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2017\.12\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.11\.29 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.11\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.11\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.10\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.10\.04 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) Microsoft SQL Server 2017 supports the following features: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.09\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.08\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.07\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.06\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.05\.30 |  The Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.30 AMI to resolve an issue with password generation\.  | 
| 2017\.05\.22 |  The Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.22 AMI after some log cleaning\.  | 
| 2017\.05\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.04\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.03\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2017\.02\.21 | Microsoft recently [announced](https://blogs.technet.microsoft.com/msrc/2017/02/14/february-2017-security-update-release/) that they will not release monthly patches or security updates for the month of February\. All February patches and security updates will be included in the March update\.Amazon Web Services did not release updated Windows Server AMIs in February\.   | 
| 2017\.01\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2016<a name="amis-2016"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2016](https://support.microsoft.com/en-us/help/3215781/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2016\.12\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) SQL Server 2016 SP1 is a major release\. The following features, which were previously available in Enterprise edition only, are now enabled in Standard, Web, and Express editions with SQL Server 2016 SP1: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.11\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.11\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.10\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.9\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.8\.26 | All Windows Server 2008 R2 AMIs dated 2016\.08\.11 were updated to fix a known issue\. New AMIs are dated 2016\.08\.25\. | 
| 2016\.8\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.8\.2 |  All Windows Server 2008 R2 AMIs for July were removed and rolled back to AMIs dated 2016\.06\.15, because of an issue discovered in the AWS PV driver\. The AWS PV driver issue has been fixed\. The August AMI release will include Windows Server 2008 R2 AMIs with the fixed AWS PV driver and July/August Windows updates\.  | 
| 2016\.7\.26 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.7\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.6\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.5\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.4\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.3\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.2\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.1\.25 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2016\.1\.5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2015<a name="amis-2015"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2015](https://support.microsoft.com/en-us/help/3132806/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2015\.12\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.11\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.10\.26 |  Corrected boot volume sizes of base AMIs to be 30GB instead of 35GB  | 
| 2015\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.9\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.8\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.7\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.6\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.5\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
| 2015\.04\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2015\.03\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2015\.02\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2015\.01\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2014<a name="amis-2014"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2014](https://support.microsoft.com/en-us/help/3028013/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2014\.12\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.11\.19  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.10\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.09\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.08\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.05\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.04\.09  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.03\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2014\.02\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2013<a name="amis-2013"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2013](https://support.microsoft.com/en-us/help/2921911/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2013\.11\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.05\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.04\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.03\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2013\.02\.22  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2012<a name="amis-2012"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2012](https://support.microsoft.com/en-us/help/2800436/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2012\.12\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.11\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.10\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.08\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.07\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.05\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.04\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.03\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.02\.24  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  2012\.01\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

### Monthly AMI Updates for 2011 and earlier<a name="amis-2011"></a>


| Release | Changes | 
| --- | --- | 
| 2011\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  1\.04  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  1\.02  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  1\.01  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 
|  1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html)  | 

## Changes in Windows Server 2016 AMIs<a name="win2k16-amis"></a>

AWS provides AMIs for Windows Server 2016\. These AMIs include the following high\-level changes from earlier Windows AMIs:
+ To accommodate the change from \.NET Framework to \.NET Core, the EC2Config service has been deprecated on Windows Server 2016 AMIs and replaced by EC2Launch\. EC2Launch is a bundle of Windows PowerShell scripts that perform many of the tasks performed by the EC2Config service\. For more information, see [Configuring a Windows Instance Using EC2Launch](ec2launch.md)\. 
+ On earlier versions of Windows Server AMIs, you can use the EC2Config service to join an EC2 instance to a domain and configure integration with Amazon CloudWatch\. On Windows Server 2016 AMIs, the Amazon EC2 Systems Manager \(SSM\) agent performs these tasks\. This means that you must use either Amazon EC2 Run Command or SSM Config to join an EC2 instance to a domain or configure integration with Amazon CloudWatch on Windows Server 2016 instances\. For more information about configuring instances to send log data to CloudWatch, see [Sending Logs, Events, and Performance Counters to Amazon CloudWatch](send_logs_to_cwl.md) For information about joining an EC2 instance to a domain, see [Joining a Windows Instance to an AWS Directory Service Domain](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-state-domain-join.html)\.

**Other Differences**

Note these additional important differences for instances created from Windows Server 2016 AMIs\.
+ By default, EC2Launch does not initialize secondary EBS volumes\. You can configure EC2Launch to initialize disks automatically by either scheduling the script to run or by calling EC2Launch in user data\. For the procedure to initialize disks using EC2Launch, see "Initialize Drives and Drive Letter Mappings" in [Configuring EC2Launch](ec2launch.md#ec2launch-config)\.
+ If you previously enabled CloudWatch integration on your instances by using a local configuration file \(AWS\.EC2\.Windows\.CloudWatch\.json\), you can configure the file to work with the SSM Agent on instances created from Windows Server 2016 AMIs\. For more information, see [Use SSM Agent to Configure CloudWatch](send_logs_to_cwl_instances.md#configure-ssm-agent)\.

For more information, see [Windows Server 2016](https://www.microsoft.com/en-us/cloud-platform/windows-server) on Microsoft\.com\.

## Docker Container Conflict on Windows Server 2016 Instances<a name="ec2launch-docker"></a>

If you run the Docker service on Windows Server 2016 AMIs, the service is configured to use a different CIDR value than the default internal IP address prefix value\. The default value is 172\.16\.0\.0/12\. Windows Server 2016 AMIs use 172\.17\.0\.0/16 to avoid a conflict with the default Amazon EC2 VPC/subnet\. If you don't change VPC/subnet settings for your EC2 instances, then you don't need to do anything\. The conflict is essentially avoided because of the different CIDR values\. If you do change VPC/subnet settings, be aware of these internal IP address prefix values and avoid creating a conflict\. For more information, read the following section\.

**Important**  
If you plan to run Docker on a Windows Server 2016 instance, you must create the instance from the following Amazon Machine Image \(AMI\) or an AMI based on an image with `Windows_Server-2016-English-Full-Containers` in the name\. Otherwise, if you use a different Windows Server 2016 AMI, instances fail to boot correctly after installing Docker and then running Sysprep\.

## Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)<a name="ec2hibernateagent-2018-03-16"></a>

After the release of the 2018\.03\.16 Windows AMIs, we discovered an unquoted path in the configuration of the Amazon EC2 Hibernate Agent\. The agent was included in the AMIs for Windows Server 2008 through Windows Server 2016\. This issue does not impact the AMIs for Windows Server 2003\.

AWS has removed the Windows AMIs dated 2018\.03\.16\. To be notified when new Windows AMIs are available, see [Subscribing to Windows AMI Notifications](#subscribe-notifications)\.

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