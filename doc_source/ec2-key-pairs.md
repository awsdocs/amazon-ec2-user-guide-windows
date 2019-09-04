# Amazon EC2 Key Pairs and Windows Instances<a name="ec2-key-pairs"></a>

Amazon EC2 uses public–key cryptography to encrypt and decrypt login information\. Public–key cryptography uses a public key to encrypt a piece of data, and then the recipient uses the private key to decrypt the data\. The public and private keys are known as a *key pair*\. Public\-key cryptography enables you to securely access your instances using a private key instead of a password\.

When you launch an instance, you specify the key pair\. You can specify an existing key pair or a new key pair that you create at launch\. With Windows instances, you use the private key to obtain the administrator password and then log in using RDP\. For more information about key pairs and Linux instances, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Creating a Key Pair**  
You can use Amazon EC2 to create your key pair\. For more information, see [Creating a Key Pair Using Amazon EC2](#having-ec2-create-your-key-pair)\.

Alternatively, you could use a third\-party tool and then import the public key to Amazon EC2\. For more information, see [Importing Your Own Public Key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)\.

Each key pair requires a name\. Be sure to choose a name that is easy to remember\. Amazon EC2 associates the public key with the name that you specify as the key name\.

Amazon EC2 stores the public key only, and you store the private key\. Anyone who possesses your private key can decrypt your login information, so it's important that you store your private keys in a secure place\.

The keys that Amazon EC2 uses are 2048\-bit SSH\-2 RSA keys\. You can have up to five thousand key pairs per Region\.

**Launching and Connecting to Your Instance**  
When you launch an instance, you should specify the name of the key pair you plan to use to connect to the instance\. If you don't specify the name of an existing key pair when you launch an instance, you won't be able to connect to the instance\. When you connect to the instance, you must specify the private key that corresponds to the key pair you specified when you launched the instance\.

**Topics**
+ [Creating a Key Pair Using Amazon EC2](#having-ec2-create-your-key-pair)
+ [Importing Your Own Public Key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)
+ [Retrieving the Public Key for Your Key Pair on Linux](#retrieving-the-public-key)
+ [Retrieving the Public Key for Your Key Pair on Windows](#retrieving-the-public-key-windows)
+ [Retrieving the Public Key for Your Key Pair From Your Instance](#retrieving-the-public-key-instance)
+ [Verifying Your Key Pair's Fingerprint](#verify-key-pair-fingerprints)
+ [Deleting Your Key Pair](#delete-key-pair)
+ [Connecting to Your Windows Instance if You Lose Your Private Key](#replacing-lost-key-pair-windows)

## Creating a Key Pair Using Amazon EC2<a name="having-ec2-create-your-key-pair"></a>

You can create a key pair using the Amazon EC2 console or the command line\. After you create a key pair, you can specify it when you launch your instance\.

**To create your key pair using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.
**Note**  
The navigation pane is on the left side of the Amazon EC2 console\. If you do not see the pane, it might be minimized; choose the arrow to expand the pane\. 

1. Choose **Create Key Pair**\.

1. For **Key pair name**, enter a name for the new key pair, and then choose **Create**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

**To create your key pair using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html) \(AWS CLI\)
+ [New\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2KeyPair.html) \(AWS Tools for Windows PowerShell\)

## Importing Your Own Public Key to Amazon EC2<a name="how-to-generate-your-own-key-and-import-it-to-aws"></a>

Instead of using Amazon EC2 to create your key pair, you can create an RSA key pair using a third\-party tool and then import the public key to Amazon EC2\. For example, you can use ssh\-keygen \(a tool provided with the standard OpenSSH installation\) to create a key pair\. Alternatively, Java, Ruby, Python, and many other programming languages provide standard libraries that you can use to create an RSA key pair\.

**Requirements**
+ 

  The following formats are supported:
  + OpenSSH public key format 
  + Base64 encoded DER format
  + SSH public key file format as specified in [RFC4716](http://tools.ietf.org/html/rfc4716)
  + SSH private key file format must be PEM \(for example, use `ssh-keygen -m PEM` to convert the OpenSSH key into the PEM format\)
+ Create an RSA key\. Amazon EC2 does not accept DSA keys\.
+ The supported lengths are 1024, 2048, and 4096\. 

**To create a key pair using a third\-party tool**

1. Generate a key pair with a third\-party tool of your choice\.

1. Save the public key to a local file\. For example, `C:\keys\my-key-pair.pub`\. The file name extension for this file is not important\.

1. Save the private key to a different local file that has the `.pem` extension\. For example, `C:\keys\my-key-pair.pem`\. Save the private key file in a safe place\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

Use the following steps to import your key pair using the Amazon EC2 console\.

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Choose **Import Key Pair**\. 

1. In the **Import Key Pair** dialog box, choose **Browse**, and select the public key file that you saved previously\. Enter a name for the key pair in the **Key pair name** field, and choose **Import**\.

**To import the public key using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [import\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-key-pair.html) \(AWS CLI\)
+ [Import\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Import-EC2KeyPair.html) \(AWS Tools for Windows PowerShell\)

After the public key file is imported, you can verify that the key pair was imported successfully using the Amazon EC2 console as follows\. 

**To verify that your key pair was imported**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which you created the key pair\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Verify that the key pair that you imported is in the displayed list of key pairs\.

**To view your key pair using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) \(AWS CLI\)
+ [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) \(AWS Tools for Windows PowerShell\)

## Retrieving the Public Key for Your Key Pair on Linux<a name="retrieving-the-public-key"></a>

On your local Linux or Mac computer, you can use the ssh\-keygen command to retrieve the public key for your key pair\. Specify the path where you downloaded your private key \(the `.pem` file\)\.

```
ssh-keygen -y -f /path_to_key_pair/my-key-pair.pem
```

The command returns the public key\. For example:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
```

If the command fails, ensure that you've changed the permissions on your key pair file so that only you can view it by running the following command:

```
chmod 400 my-key-pair.pem
```

## Retrieving the Public Key for Your Key Pair on Windows<a name="retrieving-the-public-key-windows"></a>

On your local Windows computer, you can use PuTTYgen to get the public key for your key pair\.

Start PuTTYgen and choose **Load**\. Select the `.ppk` or `.pem` file\. PuTTYgen displays the public key under **Public key for pasting into OpenSSH authorized\_keys file**\. You can also view the public key by choosing **Save public key**, specifying a name for the file, saving the file, and then opening the file\.

## Retrieving the Public Key for Your Key Pair From Your Instance<a name="retrieving-the-public-key-instance"></a>

The public key that you specified when you launched an instance is also available to you through its instance metadata\. To view the public key that you specified when launching the instance, use the following command from your instance:

```
PS C:\> Invoke-RestMethod -uri  http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

If you change the key pair that you use to connect to the instance, we don't update the instance metadata to show the new public key; you'll continue to see the public key for the key pair you specified when you launched the instance in the instance metadata\.

For more information, see [Retrieving Instance Metadata](ec2-instance-metadata.md#instancedata-data-retrieval)\.

## Verifying Your Key Pair's Fingerprint<a name="verify-key-pair-fingerprints"></a>

On the **Key Pairs** page in the Amazon EC2 console, the **Fingerprint** column displays the fingerprints generated from your key pairs\. AWS calculates the fingerprint differently depending on whether the key pair was generated by AWS or a third\-party tool\. If you created the key pair using AWS, the fingerprint is calculated using an SHA\-1 hash function\. If you created the key pair with a third\-party tool and uploaded the public key to AWS, or if you generated a new public key from an existing AWS\-created private key and uploaded it to AWS, the fingerprint is calculated using an MD5 hash function\.

You can use the SSH2 fingerprint that's displayed on the **Key Pairs** page to verify that the private key you have on your local machine matches the public key stored in AWS\. From the computer where you downloaded the private key file, generate an SSH2 fingerprint from the private key file\. The output should match the fingerprint that's displayed in the console\.

If you created your key pair using AWS, you can use the OpenSSL tools to generate a fingerprint as follows:

```
C:\> openssl pkcs8 -in path_to_private_key -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c
```

If you created a key pair using a third\-party tool and uploaded the public key to AWS, you can use the OpenSSL tools to generate the fingerprint as follows:

```
C:\> openssl rsa -in path_to_private_key -pubout -outform DER | openssl md5 -c
```

If you created an OpenSSH key pair using OpenSSH 7\.8 or later and uploaded the public key to AWS, you can use ssh\-keygen to generate the fingerprint as follows:

```
C:\> ssh-keygen -ef path_to_private_key -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
```

## Deleting Your Key Pair<a name="delete-key-pair"></a>

When you delete a key pair, you are only deleting Amazon EC2's copy of the public key\. Deleting a key pair doesn't affect the private key on your computer or the public key on any instances already launched using that key pair\. You can't launch a new instance using a deleted key pair, but you can continue to connect to any instances that you launched using a deleted key pair, as long as you still have the private key \(`.pem`\) file\.

**Note**  
If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the key pair you're deleting is not specified in your launch configuration\. Amazon EC2 Auto Scaling launches a replacement instance if it detects an unhealthy instance; however, the instance launch fails if the key pair cannot be found\. 

You can delete a key pair using the Amazon EC2 console or the command line\.

**To delete your key pair using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Select the key pair and choose **Delete**\.

1. When prompted, choose **Yes**\.

**To delete your key pair using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html) \(AWS CLI\)
+ [Remove\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html) \(AWS Tools for Windows PowerShell\)

## Connecting to Your Windows Instance if You Lose Your Private Key<a name="replacing-lost-key-pair-windows"></a>

When you connect to a newly launched Windows instance, you decrypt the password for the Administrator account using the private key for the key pair that you specified when you launched the instance\.

If you lose the Administrator password and you no longer have the private key, you must reset the password or create a new instance\. For more information, see [Resetting a Lost or Expired Windows Administrator Password](ResettingAdminPassword.md)\. For steps to reset the password using an AWS Systems Manager document, see [Reset Passwords and SSH Keys on Amazon EC2 Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.