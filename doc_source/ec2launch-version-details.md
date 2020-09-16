# EC2Launch version history<a name="ec2launch-version-details"></a>

Windows AMIs starting with Windows Server 2016 include a set of Windows Powershell scripts called EC2Launch\. EC2Launch performs tasks during the initial instance boot\. For information about the EC2Launch versions included in the Windows AMIs, see see [Managed AWS Windows AMIs](windows-ami-version-history.md)\.

To download and install the latest version of EC2Launch, see [Installing the latest version of EC2Launch](ec2launch-download.md)\.

The following table describes the released versions of EC2Launch\. Note that the version format changed after version 1\.3\.610\.


| Version | Details | Release date | 
| --- | --- | --- | 
| 1\.3\.2003155 | Updated instance type information\. | 25 August 2020 | 
| 1\.3\.2003150 | Added OsCurrentBuild and OsReleaseId to console output \. | 22 April 2020 | 
| 1\.3\.2003040 | Fixed IMDS version 1 fallback logic\. | 7 April 2020 | 
|  1\.3\.2002730  | Added support for IMDS V2\. | 3 March 2020 | 
|  1\.3\.2002240  | Fixed minor issues\.  | 31 October 2019 | 
|  1\.3\.2001660  | Fixed automatic login issue for users without password after first time executing Sysprep\.  | 2 July 2019 | 
|  1\.3\.2001360  | Fixed minor issues\.  | 27 March 2019 | 
|  1\.3\.2001220  | All PowerShell scripts signed\.  | 28 February 2019 | 
|  1\.3\.2001200  | Fixed issue with InitializeDisks\.ps1 where running the script on a node in a Microsoft Windows Server Failover Cluster would format drives on remote nodes whose drive letter matched the local drive letter\.  | 27 February 2019 | 
|  1\.3\.2001160  | Fixed missing wallpaper in Windows 2019\. | 22 February 2019 | 
|  1\.3\.2001040  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  | 21 January 2019 | 
|  1\.3\.2000930  | Fix for adding routes to metadata on ipv6\-enabled ENIs\.  | 2 January 2019 | 
|  1\.3\.2000760  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  | 5 December 2018 | 
|  1\.3\.2000630  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  | 9 November 2018 | 
|  1\.3\.2000430\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  | 19 September 2018 | 
|  1\.3\.200039\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  | 15 August 2018 | 
|  1\.3\.2000080  | Fixed minor issues\. |  | 
|  1\.3\.610  |  Fixed issue with redirecting output and errors to files from user data\.  |  | 
|  1\.3\.590  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  |  | 
|  1\.3\.580  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  |  | 
|  1\.3\.550  |  Added a `-NoShutdown` option to enable Sysprep with no shutdown\.  |  | 
|  1\.3\.540  |  Fixed minor issues\.  |  | 
|  1\.3\.530  |  Fixed minor issues\.  |  | 
|  1\.3\.521  |  Fixed minor issues\.  |  | 
|  1\.3\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  |  | 
|  1\.2\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  |  | 
|  1\.1\.2  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2launch-version-details.html)  |  | 
|  1\.1\.1  |  Initial release\.  |  | 