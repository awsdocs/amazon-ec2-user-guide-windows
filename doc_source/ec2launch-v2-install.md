# Install the latest version of EC2Launch v2<a name="ec2launch-v2-install"></a>

EC2Launch v2 is currently available by download, by installation from SSM Distributor, by installation as an EC2 Image Builder component, and on all supported Windows AMIs\.

**Download**  
To install the latest version of EC2Launch v2, download the installer from the following locations:

**Warning**  
AmazonEC2Launch\.msi uninstalls previous versions of the EC2 launch services, such as EC2Launch \(v1\) and EC2Config\.

**Note**  
The 32\-bit installation link will be deprecated\. We recommend that you use the 64\-bit installation link to install EC2Launch v2\. If you require a 32\-bit launch agent, use [EC2Config](ec2config-service.md)\.
+ **64Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2/windows/amd64/latest/AmazonEC2Launch\.msi](https://s3.amazonaws.com/amazon-ec2launch-v2/windows/amd64/latest/AmazonEC2Launch.msi)
+ **32Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2/windows/386/latest/AmazonEC2Launch\.msi](https://s3.amazonaws.com/amazon-ec2launch-v2/windows/386/latest/AmazonEC2Launch.msi)

**Install from AWS SSM Distributor**  
You can install the `AWSEC2Launch-Agent` package from AWS SSM Distributor\. For instructions on how to install a package from SSM Distributor, see [Install or update packages](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html) in the *AWS SSM User Guide*\.

**Install as an EC2 Image Builder component**  
You can install the `ec2launch-v2-windows` component when you build a custom image with EC2 Image Builder\. For instructions on how to build a custom image with EC2 Image Builder, see [Create an image pipeline using the EC2 Image Builder console wizard](https://docs.aws.amazon.com/imagebuilder/latest/userguide/start-build-image-pipeline.html) in the *EC2 Image Builder User Guide*\.

**To download and install the latest version of EC2Launch v2 using PowerShell**  
AmazonEC2Launch\.msi uninstalls previous versions of the EC2 launch services, such as EC2Launch \(v1\) and EC2Config\.

To install the latest version of EC2Launch v2 using PowerShell, run the following commands from a PowerShell window:

```
mkdir $env:USERPROFILE\Desktop\EC2Launchv2
```

**64Bit**

```
$Url = "https://s3.amazonaws.com/amazon-ec2launch-v2/windows/amd64/latest/AmazonEC2Launch.msi" 
```

**32Bit**

**Note**  
The 32\-bit installation link will be deprecated\. We recommend that you use the 64\-bit installation link to install EC2Launch v2\. If you require a 32\-bit launch agent, use [EC2Config](ec2config-service.md)\.

```
$Url = "https://s3.amazonaws.com/amazon-ec2launch-v2/windows/386/latest/AmazonEC2Launch.msi" 
```

```
$DownloadFile = "$env:USERPROFILE\Desktop\EC2Launchv2\" + $(Split-Path -Path $Url -Leaf)
Invoke-WebRequest -Uri $Url -OutFile $DownloadFile
msiexec /i "$DownloadFile"
```

The installation wizard will open\. Verify the installation by checking `C:\ProgramData\Amazon\EC2Launch`\.

**Use AMI with EC2Launch v2 preinstalled**  
EC2Launch v2 is preinstalled by default on Windows Server 2022 AMIs and UEFI AMIs:
+ Windows\_Server\-2022\-English\-Full\-Base
+ Windows\_Server\-2022\-English\-Core\-Base
+ Windows\_Server\-2022\-English\-Full\-Containers
+ Windows Server 2022 AMIs with all other languages
+ Windows Server 2022 AMIs with SQL installed

EC2Launch is also preinstalled on the following Windows Server AMIs\. You can find these AMIs from the Amazon EC2 console, or by using the AWS Command Line Interface and using the following search prefix: `EC2LaunchV2-`
+ EC2LaunchV2\-Windows\_Server\-2019\-English\-Full\-Base
+ EC2LaunchV2\-Windows\_Server\-2016\-English\-Full\-Base
+ EC2LaunchV2\-Windows\_Server\-2012\_R2\_RTM\-English\-Full\-Base
+ EC2LaunchV2\-Windows\_Server\-2019\-English\-Core\-Base
+ EC2LaunchV2\-Windows\_Server\-2016\-English\-Core\-Base
+ EC2LaunchV2\-Windows\_Server\-2019\-English\-Full\-ContainersLatest
+ EC2LaunchV2\-Windows\_Server\-2012\_RTM\-English\-Full\-Base

EC2Launch Preview AMIs will be deprecated in December 2021\.

## Installation options<a name="ec2launch-v2-install-options"></a>

When you install or upgrade EC2Launch v2, your existing configuration, located at `%ProgramData%/Amazon/EC2Launch/config/agent-config.yml`, is not replaced\. Perform a clean installation to overwrite an existing configuration to use the latest version\.

You can perform a clean installation using the EC2Launch v2 interface or the command line\.

**Perform a clean installation using the EC2Launch v2 user interface**  
When you install EC2Launch v2, choose the **Default Configuration** option under **Basic Install**\.

![\[EC2Launch v2 clean install.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2launchv2-clean-default-config.png)

**Perform a clean installation using the command line**  
To perform a clean installation of EC2Launch v2 using the command line, run the following Windows command:

```
msiexec /i "C:\Users\Administrator\Desktop\EC2Launchv2\AmazonEC2Launch.msi" ADDLOCAL="Basic,Clean" /q
```