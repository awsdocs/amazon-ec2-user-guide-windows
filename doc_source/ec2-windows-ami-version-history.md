# AWS Windows AMI Version History<a name="ec2-windows-ami-version-history"></a>

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
+ [EC2Config version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2config-version-details.html)
+ [EC2Launch version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)
+ [Systems Manager SSM Agent Release Notes](https://github.com/aws/amazon-ssm-agent/blob/master/RELEASENOTES.md)
+ [Amazon ENA driver versions](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html#ena-adapter-driver-versions)
+ [AWS PV drivers](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/xen-drivers-overview.html#xen-driver-awspv)

## Monthly AMI updates for 2020 \(to date\)<a name="amis-2020"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2020](https://support.microsoft.com/en-us/help/894199/description-of-software-update-services-and-windows-server-update-serv-2020)\.


| Release | Changes | 
| --- | --- | 
| 2020\.11\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.9\.25 |  A new version of Amazon Machine Images with SQL Server 2019 dated 2020\.09\.25 has been released\. This release includes the same software components as the previous release dated 2020\.09\.09 but does not include CU7 for SQL 2019, which has recently been removed from public availability by Microsoft due to a known issue with reliability of the database snapshot feature\. For more information, please see the following Microsoft blog post: [https://techcommunity\.microsoft\.com/t5/sql\-server/cumulative\-update\-7\-for\-sql\-server\-2019\-rtm\-removed/ba\-p/1629317](https://techcommunity.microsoft.com/t5/sql-server/cumulative-update-7-for-sql-server-2019-rtm-removed/ba-p/1629317)\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.9\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.8\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.7\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.7\.01 |  A new version of Amazon Machine Images has been released\. These images include EC2Launch v2 and serve as a functional preview of the new launch agent in advance of it being included by default on all Windows AMIs currently provided by AWS later this year\. Note that some SSM documents and dependent services, such as EC2 Image Builder, may require updates to support EC2 Launch v2\. These updates will follow in the coming weeks\. These images are not recommended for use in production environments\. You can read more about EC2Launch v2 at [https://aws\.amazon\.com/about\-aws/whats\-new/2020/07/introducing\-ec2\-launch\-v2\-simplify\-customizing\-windows\-instances/](http://aws.amazon.com/about-aws/whats-new/2020/07/introducing-ec2-launch-v2-simplify-customizing-windows-instances/) and [Configuring a Windows instance using EC2Launch v2](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-v2.html)\. All current Windows Server AMIs will continue to be provided without changes to the current launch agent, either EC2Config \(Server 2012 RTM or 2012 R2\) or EC2Launch v1 \(Server 2016 or later\), for the next several months\. In the near future, all Windows Server AMIs currently provided by AWS will be migrated to use EC2Launch v2 by default as part of the monthly release\. EC2LaunchV2\_Preview AMIs will be updated monthly and remain available until this migration occurs\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.6\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.5\.27 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.5\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.4\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.3\.18 |  **Windows Server 2019 AMIs** Resolves an intermittent issue discovered in the 2020\.3\.11 release in which the Background Intelligent Transfer Service \(BITS\) may not start within the expected time after initial OS boot, potentially resulting in timeouts, BITS errors in the event log, or failures of cmdlets involving BITS invoked quickly after the initial boot\. Other Windows Server AMIs are not affected by this issue, and their latest version remains 2020\.03\.11\.  | 
| 2020\.3\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2020\.2\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Windows Server 2008 SP2 and Window Server 2008 R2 reached End of Support \(EOS\) on 01/14/20 and will no longer receive regular security updates from Microsoft\. AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account are not impacted, and you can continue to use them after the EOS date\.  For more information about Microsoft End of Service on AWS, including upgrade and import options, as well as a full list of AMIs that are no longer published as of 01/14/2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 
| 2020\.1\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Windows Server 2008 SP2 and Window Server 2008 R2 reached End of Support \(EOS\) on 01/14/20 and will no longer receive regular security updates from Microsoft\. AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account are not impacted, and you can continue to use them after the EOS date\.  For more information about Microsoft End of Service on AWS, including upgrade and import options, as well as a full list of AMIs that are no longer published as of 01/14/2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 

## Monthly AMI updates for 2019<a name="amis-2019"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2019](https://support.microsoft.com/en-us/help/4538374/software-update-services-and-windows-server-update-services-2019)\.


| Release | Changes | 
| --- | --- | 
| 2019\.12\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2008 SP2 and Windows Server 2008 R2 ** Microsoft will end mainstream support for Windows Server 2008 SP2 and Windows Server 2008 R2 on January 14, 2020\. On this date, AWS will no longer publish or distribute Windows Server 2008 SP2 or Windows Server 2008 R2 AMIs\. Existing 2008 SP2/R2 instances and custom AMIs in your account will not be impacted and you can continue to use them after the end\-of\-service \(EOS\) date\.  For more information about Microsoft EOS on AWS, including upgrade and import options, along with a full list of AMIS that will no longer be published or distributed on January 14, 2020, see [End of Support \(EOS\) for Microsoft Products](https://aws.amazon.com/windows/faq/#eos-microsoft-products)\.  | 
| 2019\.11\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) Previous versions of AMIs have been marked private\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.11\.05 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.10\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.09\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) \.NET Core 2\.2, Mono 5\.18, and PowerShell 6\.2 pre\-installed to run your \.NET applications on Amazon Linux 2 with Long Term Support \(LTS\)  | 
| 2019\.09\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) Previous versions of AMIs have been marked private\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  NLA is now enabled on all 2012 RTM, 2012 R2, and 2016 AMIs to increase default RDP security posture\. NLA remains enabled on 2019 AMIs\.  | 
| 2019\.08\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.07\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.07\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.06\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) Previous versions of AMIs have been marked private\.  | 
| 2019\.05\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.05\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.04\.26 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.04\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.04\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.03\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.02\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) SQL 2014 with Service Pack 2 and SQL 2016 with Service Pack 1 will no longer be updated after this release\.   | 
| 2019\.02\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2019\.01\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2018<a name="amis-2018"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2018](https://support.microsoft.com/en-us/help/4486929/software-update-services-and-windows-server-update-services-2018)\.


| Release | Changes | 
| --- | --- | 
| 2018\.12\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.11\.28 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.11\.20 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.11\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2016 Datacenter and Standard Editions for Nano Server ** Microsoft ended mainstream support for Windows Server 2016 Datacenter and Standard Editions for Nano Server installation options as of April 10, 2018\.  | 
| 2018\.09\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.08\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) **Microsoft Windows Server 2016 Base Nano** Access to all public versions of Windows\_Server\-2016\-English\-Nano\-Base will be removed in September 2018\. Additional information about Nano Server lifecycle, including details on launching Nano Server as a Container, can be found here: [https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel](https://docs.microsoft.com/en-us/windows-server/get-started/nano-in-semi-annual-channel)\.  | 
| 2018\.07\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.06\.22 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.06\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.05\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.04\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.03\.24 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.03\.16 |  AWS has removed all Windows AMIs dated 2018\.03\.16 due to an issue with an unquoted path in the configuration for the Amazon EC2 Hibernate Agent\. For more information, see [Issue with the Hibernate Agent \(2018\.03\.16 AMIs\)](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#ec2hibernateagent-2018-03-16)\.  | 
| 2018\.03\.06 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.02\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.02\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.01\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2018\.01\.05 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2017<a name="amis-2017"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2017](https://support.microsoft.com/en-us/help/4073007/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2017\.12\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.11\.29 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.11\.19 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.11\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.10\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.10\.04 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) Microsoft SQL Server 2017 supports the following features: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.09\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.08\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.07\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.06\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.05\.30 |  The Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2008\-SP2\-English\-32Bit\-Base\-2017\.05\.30 AMI to resolve an issue with password generation\.  | 
| 2017\.05\.22 |  The Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.10 AMI was updated to the Windows\_Server\-2016\-English\-Full\-Base\-2017\.05\.22 AMI after some log cleaning\.  | 
| 2017\.05\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.04\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.03\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2017\.02\.21 | Microsoft recently [announced](https://blogs.technet.microsoft.com/msrc/2017/02/14/february-2017-security-update-release/) that they will not release monthly patches or security updates for the month of February\. All February patches and security updates will be included in the March update\.Amazon Web Services did not release updated Windows Server AMIs in February\.   | 
| 2017\.01\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2016<a name="amis-2016"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2016](https://support.microsoft.com/en-us/help/3215781/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2016\.12\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) SQL Server 2016 SP1 is a major release\. The following features, which were previously available in Enterprise edition only, are now enabled in Standard, Web, and Express editions with SQL Server 2016 SP1: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.11\.23 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.11\.09 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.10\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.9\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.8\.26 | All Windows Server 2008 R2 AMIs dated 2016\.08\.11 were updated to fix a known issue\. New AMIs are dated 2016\.08\.25\. | 
| 2016\.8\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.8\.2 |  All Windows Server 2008 R2 AMIs for July were removed and rolled back to AMIs dated 2016\.06\.15, because of an issue discovered in the AWS PV driver\. The AWS PV driver issue has been fixed\. The August AMI release will include Windows Server 2008 R2 AMIs with the fixed AWS PV driver and July/August Windows updates\.  | 
| 2016\.7\.26 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.7\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.6\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.5\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.4\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.3\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.2\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.1\.25 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2016\.1\.5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2015<a name="amis-2015"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2015](https://support.microsoft.com/en-us/help/3132806/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
| 2015\.12\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.11\.11 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.10\.26 |  Corrected boot volume sizes of base AMIs to be 30GB instead of 35GB  | 
| 2015\.10\.14 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.9\.9 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.8\.18 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.7\.21 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.6\.10 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.5\.13 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
| 2015\.04\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2015\.03\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2015\.02\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2015\.01\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2014<a name="amis-2014"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2014](https://support.microsoft.com/en-us/help/3028013/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2014\.12\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.11\.19  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.10\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.09\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.08\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.05\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.04\.09  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.03\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2014\.02\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2013<a name="amis-2013"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2013](https://support.microsoft.com/en-us/help/2921911/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2013\.11\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.07\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.05\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.04\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.03\.14  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2013\.02\.22  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html) [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2012<a name="amis-2012"></a>

For more information about Microsoft updates, see [Description of Software Update Services and Windows Server Update Services changes in content for 2012](https://support.microsoft.com/en-us/help/2800436/description-of-software-update-services-and-windows-server-update-serv)\.


| Release | Changes | 
| --- | --- | 
|  2012\.12\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.11\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.10\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.08\.15  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.07\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.06\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.05\.10  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.04\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.03\.13  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.02\.24  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  2012\.01\.12  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 

## Monthly AMI updates for 2011 and earlier<a name="amis-2011"></a>


| Release | Changes | 
| --- | --- | 
| 2011\.09\.11  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  1\.04  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  1\.02  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  1\.01  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 
|  1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)  | 