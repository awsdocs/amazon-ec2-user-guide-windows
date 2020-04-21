# Guidelines for shared Windows AMIs<a name="windows-amis-guidelines"></a>

Use the following guidelines to reduce the attack surface and improve the reliability of the AMIs you create\. 
+ No list of security guidelines can be exhaustive\. Build your shared AMIs carefully and take time to consider where you might expose sensitive data\.
+ Develop a repeatable process for building, updating, and republishing AMIs\.
+ Build AMIs using the most up\-to\-date operating systems, packages, and software\.
+ [Download](https://s3.amazonaws.com/ec2-downloads-windows/EC2Config/EC2Install.zip) and install the latest version of the EC2Config service\. For more information about installing this service, see [Installing the Latest Version of EC2Config](UsingConfig_Install.md)\. 
+ Verify that Ec2SetPassword, Ec2WindowsActivate and Ec2HandleUserData are enabled\.
+ Verify that no guest accounts or Remote Desktop user accounts are present\.
+ Disable or remove unnecessary services and programs to reduce the attack surface of your AMI\.
+ Remove instance credentials, such as your key pair, from the AMI \(if you saved them on the AMI\)\. Store the credentials in a safe location\.
+ Ensure that the administrator password and passwords on any other accounts are set to an appropriate value for sharing\. These passwords are available for anyone who launches your shared AMI\.
+ Test your AMI before you share it\.