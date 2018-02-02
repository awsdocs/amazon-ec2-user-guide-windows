# Resetting a Lost or Expired Windows Administrator Password<a name="ResettingAdminPassword"></a>

If you've lost the Windows administrator password for your Amazon EC2 instance, or if the password has expired, and you are no longer able to access your Windows Amazon EC2 instance you can reset it\.

There is an AWS Systems Manager Automation document that automatically applies the manual steps necessary to reset the local Administrator password\. For more information, see [Reset the Local Administrator Password on Amazon EC2 Windows Instances](http://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.

The manual methods to reset the Administrator password use either EC2Config or EC2Launch\.

+ For Windows AMIs before Windows Server 2016, use the EC2Config service\.

+ For Windows Server 2016 AMIs, use the EC2Launch service\.

These procedures also describe how to connect to an instance if you've lost the key pair that was used to create the instance\. Amazon EC2 uses a public key to encrypt a piece of data, such as a password, and a private key to decrypt the data\. The public and private keys are known as a *key pair*\. With Windows instances, you use a key pair to obtain the administrator password and then log in using RDP\.

**Note**  
If you have disabled the local Administrator account on the instance and your instance is configured for SSM, you can also re\-enable and reset your local Administrator password by using EC2Rescue and Run Command\. For more information, see [Using EC2Rescue for Windows Server with Systems Manager Run Command](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2rw-ssm.html)\.


+ [Resetting the Windows Administrator Password Using EC2Config](ResettingAdminPassword_EC2Config.md)
+ [Resetting the Windows Administrator Password Using EC2Launch](ResettingAdminPassword_EC2Launch.md)