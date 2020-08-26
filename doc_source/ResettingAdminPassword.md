# Resetting a lost or expired Windows administrator password<a name="ResettingAdminPassword"></a>

If you are no longer able to access your Windows Amazon EC2 instance because the Windows administrator password is lost or expired, you can reset the password\.

**Note**  
There is an AWS Systems Manager Automation document that automatically applies the manual steps necessary to reset the local administrator password\. For more information, see [Reset Passwords and SSH Keys on Amazon EC2 Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.

The manual methods to reset the administrator password use either EC2Config or EC2Launch\.
+ For Windows AMIs before Windows Server 2016, use the EC2Config service\.
+ For Windows Server 2016 and later AMIs, use the EC2Launch service\.

These procedures also describe how to connect to an instance if you lost the key pair that was used to create the instance\. Amazon EC2 uses a public key to encrypt a piece of data, such as a password, and a private key to decrypt the data\. The public and private keys are known as a *key pair*\. With Windows instances, you use a key pair to obtain the administrator password and then log in using RDP\.

**Note**  
If you have disabled the local administrator account on the instance and your instance is configured for Systems Manager, you can also re\-enable and reset your local administrator password by using EC2Rescue and Run Command\. For more information, see [Using EC2Rescue for Windows Server with Systems Manager Run Command](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2rw-ssm.html)\.

**Topics**
+ [Resetting the Windows administrator password using EC2Config](ResettingAdminPassword_EC2Config.md)
+ [Resetting the Windows administrator password using EC2Launch](ResettingAdminPassword_EC2Launch.md)