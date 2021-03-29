# Use EC2Rescue for Windows Server<a name="Windows-Server-EC2Rescue"></a>

EC2Rescue for Windows Server is an easy\-to\-use tool that you run on an Amazon EC2 Windows Server instance to diagnose and troubleshoot possible problems\. It is valuable for collecting log files and troubleshooting issues and also proactively searching for possible areas of concern\. It can even examine Amazon EBS root volumes from other instances and collect relevant logs for troubleshooting Windows Server instances using that volume\.

EC2Rescue for Windows Server has two different modules: a data collector module that collects data from all different sources, and an analyzer module that parses the data collected against a series of predefined rules to identify issues and provide suggestions\.

The EC2Rescue for Windows Server tool only runs on Amazon EC2 instances running Windows Server 2008 R2 and later\. When the tool starts, it checks whether it is running on an Amazon EC2 instance\.

The `AWSSupport-ExecuteEC2Rescue` runbook uses the EC2Rescue tool to troubleshoot and, where possible, fix common connectivity issues with the specified EC2 instance\. For more information, and to run this automation, see [AWSSupport\-ExecuteEC2Rescue](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awssupport-executeec2rescue.html)\.

**Note**  
If you are using a Linux instance, see [EC2Rescue for Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Linux-Server-EC2Rescue.html)\.

**Contents**
+ [Use EC2Rescue for Windows Server GUI](ec2rw-gui.md)
+ [Use EC2Rescue for Windows Server with the command line](ec2rw-cli.md)
+ [Use EC2Rescue for Windows Server with Systems Manager Run Command](ec2rw-ssm.md)