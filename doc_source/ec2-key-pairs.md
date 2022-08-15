# Amazon EC2 key pairs and Windows instances<a name="ec2-key-pairs"></a>

A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance\. Amazon EC2 stores the public key on your instance, and you store the private key\. For Windows instances, the private key is required to decrypt the administrator password\. You then use the decrypted password to connect to your instance\. As an alternative to key pairs, you can use [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) to connect to your instance with an interactive one\-click browser\-based shell or the AWS Command Line Interface \(AWS CLI\)\.

Anyone who possesses your private key can connect to your instances, so it's important that you store your private key in a secure place\.

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using RDP, you must specify a key pair\. You can choose an existing key pair or create a new one\. With Windows instances, you use the private key to obtain the administrator password and then log in using RDP\. For more information about connecting to your instance, see [Connect to your Windows instance](connecting_to_windows_instance.md)\. For more information about key pairs and Linux instances, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Because Amazon EC2 doesn't keep a copy of your private key, there is no way to recover a private key if you lose it\. However, there can still be a way to connect to instances for which you've lost the private key\. For more information, see [I've lost my private key\. How can I connect to my Windows instance?](troubleshoot-connect-windows-instance.md#replacing-lost-key-pair-windows)

You can use Amazon EC2 to create your key pairs\. You can also use a third\-party tool to create your key pairs, and then import the public keys to Amazon EC2\.

Amazon EC2 supports 2048\-bit SSH\-2 RSA keys for Windows instances\. ED25519 keys are not supported for Windows instances\.

You can have up to 5,000 key pairs per Region\.

**Topics**
+ [Create key pairs](create-key-pairs.md)
+ [Tag a public key](tag-key-pair.md)
+ [Describe public keys](describe-keys.md)
+ [Delete your public key on Amazon EC2](delete-key-pair.md)
+ [Verify keys](verify-keys.md)