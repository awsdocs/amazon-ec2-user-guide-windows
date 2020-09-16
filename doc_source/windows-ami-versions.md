# Details about AWS Windows AMI versions<a name="windows-ami-versions"></a>

## What to expect in an official AWS Windows AMI<a name="windows-ami-creation-standards"></a>

AWS provides AMIs with a variety of configurations for all supported Windows Operating System versions\. For each of these images, AWS:
+ Installs all Microsoft recommended Windows security patches\. We release images shortly after the monthly Microsoft patches are made available\.
+ Installs the latest drivers for AWS hardware, including network and disk drivers, EC2WinUtil for troubleshooting, as well as GPU drivers in selected AMIs\.
+ Includes AWS helper software, like [EC2 Config](ec2config-service.md) for Server 2012 R2 and earlier, or [EC2 Launch](ec2launch.md) for Server 2016 and later\.
+ Configures Windows Time to use the [AWS Time Service](windows-set-time.md#default-ntp-settings)\.
+ Makes changes in all power schemes to set the display to never turn off\.
+ Performs minor bug fixes – generally one\-line registry changes to enable or disable features that we have found to improve performance on AWS\.

Other than the adjustments listed above, we keep our AMIs as close as possible to the default install\. This means we default to the “stock” PowerShell or \.NET framework versions, don’t install Windows Features, and generally don’t change the AMI\.

## How AWS decides which Windows AMIs to offer<a name="windows-ami-creation-standards-AMI-type"></a>

Each AMI is extensively tested prior to release to the general public\. We periodically streamline our AMI offerings to simplify customer choice and to reduce costs\.
+ New AMI offerings are created for new OS releases\. You can count on AWS releasing “Base,” “Core/Container,” and “SQL Express/Standard/Web/Enterprise” offerings in English and other widely used languages\. The primary difference between Base and Core offerings is that Base offerings have a desktop/GUI whereas Core offerings are PowerShell command line only\. For more information about Windows Server Core, see [ https://docs\.microsoft\.com/en\-us/windows\-server/administration/server\-core/what\-is\-server\-core](https://docs.microsoft.com/en-us/windows-server/administration/server-core/what-is-server-core)\.
+ New AMI offerings are created to support new platforms – for example, the Deep Learning and “NVidia” AMIs were created to support customers using our GPU\-based instance types \(P2 and P3, G2 and G3, etc\.\)\.
+ Less popular AMIs are sometimes removed\. If we see a particular AMI is launched only a few times in its entire lifespan, we will remove it in favor of more widely used options\.

If there is an AMI variant that you would like to see, let us know by filing a ticket with Cloud Support, or by providing feedback through [one of our established channels](https://aws.amazon.com/premiumsupport/knowledge-center/send-feedback-aws/)\.

## Patches, security updates, and AMI IDs<a name="ami-patches-security-ID"></a>

AWS provides updated, fully\-patched Windows AMIs within five business days of Microsoft's patch Tuesday \(the second Tuesday of each month\)\. The new AMIs are available immediately through the **Images** page in the Amazon EC2 console\. The new AMIs are available in the AWS Marketplace and the **Quick Start** tab of the launch instance wizard within a few days of their release\.

**Note**  
Instances launched from the latest Windows Server 2019 AMIs may show a Windows Update dialog message stating "Some settings are managed by your organization\." This message appears as a result of changes in Windows Server 2019 and does not impact the behavior of Windows Update or your ability to manage update settings\.  
To remove this warning, see ["Some settings are managed by your organization"](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/common-messages.html#some-settings-managed-by-org)\.

To ensure that customers have the latest security updates by default, AWS keeps Windows AMIs available for three months\. After releasing new Windows AMIs, AWS makes the Windows AMIs that are older than three months private within 10 days\. After an AMI has been made private, if you look at an instance launched from that AMI in the console, the **AMI ID** field states, "Cannot load detail for ami\-xxxxx\. You may not be permitted to view it\." You can still retrieve the AMI ID using the AWS CLI or an AWS SDK\.

The Windows AMIs in each release have new AMI IDs\. Therefore, we recommend that you write scripts that locate the latest AWS Windows AMIs by their names, rather than by their IDs\. For more information, see the following examples:
+ [Get\-EC2ImageByName](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html#pstools-ec2-get-ec2imagebyname) \(AWS Tools for Windows PowerShell\)
+ [Query for the Latest Windows AMI Using Systems Manager Parameter Store](https://aws.amazon.com/blogs/mt/query-for-the-latest-windows-ami-using-systems-manager-parameter-store/)
+ [Walkthrough: Looking Up Amazon Machine Image IDs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-custom-resources-lambda-lookup-amiids.html) \(AWS Lambda, AWS CloudFormation\)

## Semiannual channel releases<a name="channel-releases"></a>

AWS provides Windows Server semiannual channel releases that combine the scale, performance, and elasticity of AWS with the new capabilities in the [Semiannual channel release versions of Windows Server](https://docs.microsoft.com/en-us/windows-server/get-started/semi-annual-channel-overview)\. 

## AWS Windows AMI Version History<a name="ec2-windows-ami-version-history"></a>

The following tables summarize the changes to each release of the AWS Windows AMIs\. Note that some changes apply to all AWS Windows AMIs while others apply to only a subset of these AMIs\.

**Topics**
+ [Monthly AMI updates for 2020 \(to date\)](#amis-2020)
+ [Monthly AMI updates for 2019](#amis-2019)
+ [Monthly AMI updates for 2018](#amis-2018)
+ [Monthly AMI updates for 2017](#amis-2017)
+ [Monthly AMI updates for 2016](#amis-2016)
+ [Monthly AMI updates for 2015](#amis-2015)
+ [Monthly AMI updates for 2014](#amis-2014)
+ [Monthly AMI updates for 2013](#amis-2013)
+ [Monthly AMI updates for 2012](#amis-2012)
+ [Monthly AMI updates for 2011 and earlier](#amis-2011)

For more information about components included in these AMIs, see the following:
+ [EC2Config Version History](ec2config-version-details.md)
+ [EC2Launch version history](ec2launch-version-details.md)
+ [Systems Manager SSM Agent Release Notes](https://github.com/aws/amazon-ssm-agent/blob/master/RELEASENOTES.md)
+ [Amazon ENA driver versions](enhanced-networking-ena.md#ena-adapter-driver-versions)
+ [AWS PV Driver Package History](xen-drivers-overview.md#pv-driver-history)

### Monthly AMI updates for 2020 \(to date\)<a name="amis-2020"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2020](https://support.microsoft.com/en-us/help/894199/description-of-software-update-services-and-windows-server-update-serv-2020)\.


| Release | Changes | 
| --- | --- | 
| 2020\.9\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.8\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.7\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.7\.01 |  A new version of Amazon Machine Images has been released\. These images include EC2Launch v2 and serve as a functional preview of the new launch agent in advance of it being included by default on all Windows AMIs currently provided by AWS later this year\. Note that some SSM documents and dependent services, such as EC2 Image Builder, may require updates to support EC2 Launch v2\. These updates will follow in the coming weeks\. These images are not recommended for use in production environments\. You can read more about EC2Launch v2 at [https://aws\.amazon\.com/about\-aws/whats\-new/2020/07/introducing\-ec2\-launch\-v2\-simplify\-customizing\-windows\-instances/](http://aws.amazon.com/about-aws/whats-new/2020/07/introducing-ec2-launch-v2-simplify-customizing-windows-instances/) and [https://docs\.aws\.amazon\.com/AWSEC2/latest/WindowsGuide/ec2launch\-v2\.html](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-v2.html)\. All current Windows Server AMIs will continue to be provided without changes to the current launch agent, either EC2Config \(Server 2012 RTM or 2012 R2\) or EC2Launch v1 \(Server 2016 or later\), for the next several months\. In the near future, all Windows Server AMIs currently provided by AWS will be migrated to use EC2Launch v2 by default as part of the monthly release\. EC2LaunchV2\_Preview AMIs will be updated monthly and remain available until this migration occurs\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.6\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.5\.27 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.5\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.4\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.3\.18 |  **Windows Server 2019 AMIs** Resolves an intermittent issue discovered in the 2020\.3\.11 release in which the Background Intelligent Transfer Service \(BITS\) may not start within the expected time after initial OS boot, potentially resulting in timeouts, BITS errors in the event log, or failures of cmdlets involving BITS invoked quickly after the initial boot\. Other Windows Server AMIs are not affected by this issue, and their latest version remains 2020\.03\.11\.  | 
| 2020\.3\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2020\.2\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Windows Server 2008 SP2 and Window Server 2008 R2 reached End of Support \(EOS\) on 01/14/20 and will no longer receive regular security updates from Microsoft\. AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account are not impacted, and you can continue to use them after the EOS date\.  For more information about Microsoft End of Service on AWS, including upgrade and import options, as well as a full list of AMIs that are no longer published as of 01/14/2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 
| 2020\.1\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Windows Server 2008 SP2 and Window Server 2008 R2 reached End of Support \(EOS\) on 01/14/20 and will no longer receive regular security updates from Microsoft\. AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account are not impacted, and you can continue to use them after the EOS date\.  For more information about Microsoft End of Service on AWS, including upgrade and import options, as well as a full list of AMIs that are no longer published as of 01/14/2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 

### Monthly AMI updates for 2019<a name="amis-2019"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2019](https://support.microsoft.com/en-us/help/4538374/software-update-services-and-windows-server-update-services-2019)\.


| Release | Changes | 
| --- | --- | 
| 2019\.12\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Microsoft will end mainstream support for Windows Server 2008 SP2 and Windows Server 2008 R2 on January 14, 2020\. On this date, AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account will not be impacted and you can continue to use them after the end\-of\-service \(EOS\) date\.  For more information about Microsoft EOS on AWS, including upgrade and import options, along with a full list of AMIS that will no longer be published or distributed on January 14, 2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 
| 2019\.11\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) Previous versions of AMIs have been marked private\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.11\.05 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.10\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.09\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) \.NET Core 2\.2, Mono 5\.18, and PowerShell 6\.2 pre\-installed to run your \.NET applications on Amazon Linux 2 with Long Term Support \(LTS\)  | 
| 2019\.09\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) Previous versions of AMIs have been marked private\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  NLA is now enabled on all 2012 RTM, 2012 R2, and 2016 AMIs to increase default RDP security posture\. NLA remains enabled on 2019 AMIs\.  | 
| 2019\.08\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.07\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.07\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.06\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) Previous versions of AMIs have been marked private\.  | 
| 2019\.05\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.05\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.04\.26 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.04\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.04\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.03\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.02\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) SQL 2014 with Service Pack 2 and SQL 2016 with Service Pack 1 will no longer be updated after this release\.   | 
| 2019\.02\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2019\.01\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2018<a name="amis-2018"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2018](https://support.microsoft.com/en-us/help/4486929/software-update-services-and-windows-server-update-services-2018)\.


| Release | Changes | 
| --- | --- | 
| 2018\.12\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.11\.28 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.11\.20 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.11\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2016 Datacenter and Standard Editions for Nano Server ** Microsoft ended mainstream support for Windows Server 2016 Datacenter and Standard Editions for Nano Server installation options as of April 10, 2018\.  | 
| 2018\.09\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.08\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.07\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.06\.22 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.06\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.05\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.04\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.03\.24 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.03\.16 |  AWS has removed all Windows AMIs dated 2018\.03\.16 due to an issue with an unquoted path in the configuration for the Amazon EC2 Hibernate Agent\. For more information, see [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](windows-ami-version-history.md#ec2hibernateagent-2018-03-16)\.  | 
| 2018\.03\.06 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.02\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.02\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.01\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2018\.01\.05 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2017<a name="amis-2017"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2017](https://support.microsoft.com/en-us/help/4073007/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2017\.12\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.11\.29 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.11\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.11\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.10\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.10\.04 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) Microsoft SQL Server 2017 supports the following features: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.09\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.08\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.07\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.06\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.05\.30 |  The Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.30 AMI to resolve an issue with password generation\.  | 
| 2017\.05\.22 |  The Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.22 AMI after some log cleaning\.  | 
| 2017\.05\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.04\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.03\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2017\.02\.21 | Microsoft recently [announced](https://blogs.technet.microsoft.com/msrc/2017/02/14/february-2017-security-update-release/) that they will not release monthly patches or security updates for the month of February\. All February patches and security updates will be included in the March update\.Amazon Web Services did not release updated Windows Server AMIs in February\.   | 
| 2017\.01\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2016<a name="amis-2016"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2016](https://support.microsoft.com/en-us/help/3215781/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2016\.12\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) SQL Server 2016 SP1 is a major release\. The following features, which were previously available in Enterprise edition only, are now enabled in Standard, Web, and Express editions with SQL Server 2016 SP1: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.11\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.11\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.10\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.9\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.8\.26 | All Windows Server 2008 R2 AMIs dated 2016\.08\.11 were updated to fix a known issue\. New AMIs are dated 2016\.08\.25\. | 
| 2016\.8\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.8\.2 |  All Windows Server 2008 R2 AMIs for July were removed and rolled back to AMIs dated 2016\.06\.15, because of an issue discovered in the AWS PV driver\. The AWS PV driver issue has been fixed\. The August AMI release will include Windows Server 2008 R2 AMIs with the fixed AWS PV driver and July/August Windows updates\.  | 
| 2016\.7\.26 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.7\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.6\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.5\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.4\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.3\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.2\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.1\.25 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2016\.1\.5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2015<a name="amis-2015"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2015](https://support.microsoft.com/en-us/help/3132806/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2015\.12\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.11\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.10\.26 |  Corrected boot volume sizes of base AMIs to be 30GB instead of 35GB  | 
| 2015\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.9\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.8\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.7\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.6\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.5\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
| 2015\.04\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2015\.03\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2015\.02\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2015\.01\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2014<a name="amis-2014"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2014](https://support.microsoft.com/en-us/help/3028013/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2014\.12\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.11\.19  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.10\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.09\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.08\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.05\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.04\.09  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.03\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2014\.02\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2013<a name="amis-2013"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2013](https://support.microsoft.com/en-us/help/2921911/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2013\.11\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.05\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.04\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.03\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2013\.02\.22  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2012<a name="amis-2012"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2012](https://support.microsoft.com/en-us/help/2800436/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2012\.12\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.11\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.10\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.08\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.07\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.05\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.04\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.03\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.02\.24  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  2012\.01\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 

### Monthly AMI updates for 2011 and earlier<a name="amis-2011"></a>


| Release | Changes | 
| --- | --- | 
| 2011\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  1\.04  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  1\.02  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  1\.01  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 
|  1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-versions.html)  | 