# Install the latest version of EC2Launch v2<a name="ec2launch-v2-install"></a>

EC2Launch v2 is currently available by download, by installation from SSM Distributor, and on all supported Windows AMIs\.

**Download**  
To install the latest version of EC2Launch v2, download the service from the following locations:

**Note**  
AmazonEC2Launch\.msi does not uninstall previous versions of the EC2 launch services, such as EC2Launch \(v1\) or EC2Config\. To upgrade to EC2Launch v2 from an earlier launch service version, see [Migrate to EC2Launch v2Migrate](ec2launch-v2-migrate.md)\. 
+ **64Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2/windows/amd64/latest/AmazonEC2Launch\.msi](https://s3.amazonaws.com/amazon-ec2launch-v2/windows/amd64/latest/AmazonEC2Launch.msi)
+ **32Bit** — [https://s3\.amazonaws\.com/amazon\-ec2launch\-v2/windows/386/latest/AmazonEC2Launch\.msi](https://s3.amazonaws.com/amazon-ec2launch-v2/windows/386/latest/AmazonEC2Launch.msi)

**Install from AWS SSM Distributor**  
You can install the `AWSEC2Launch-Agent` package from AWS SSM Distributor\. For instructions on how to install a package from SSM Distributor, see [Install or update packages](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html) in the *AWS SSM User Guide*\.

**Use AMI with EC2Launch v2 preinstalled \(non\-production workloads\)**  
EC2Launch v2 is preinstalled on the following AMIs\. Do not use these AMIs for production workloads as they are intended only for you to verify if the EC2Launch v2 service works well with your existing processes and workloads\. You can find these AMIs from the Amazon EC2 console, or you can find them using the EC2 CLI and searching with the prefix `EC2LaunchV2_Preview-Windows_Server-`\.
+ EC2LaunchV2\_Preview\-Windows\_Server\-2004\-English\-Core\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2019\-English\-Full\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2019\-English\-Core\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2016\-English\-Full\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2016\-English\-Core\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2012\_R2\_RTM\-English\-Full\-Base
+ EC2LaunchV2\_Preview\-Windows\_Server\-2012\_R2\_RTM\-English\-Core
+ EC2LaunchV2\_Preview\-Windows\_Server\-2012\_RTM\-English\-Full\-Base
+ EC2LaunchV2\_Preview\-Windows\-Server\-2019\-English\-Full\-SQL\_2019\_Express
+ EC2LaunchV2\_Preview\-Windows\-Server\-2016\-English\-Full\-SQL\_2017\_Express