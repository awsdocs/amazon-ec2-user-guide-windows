# Amazon EC2 key pairs and Windows instances<a name="ec2-key-pairs"></a>

A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance\. Amazon EC2 stores the public key on your instance, and you store the private key\. For Windows instances, the private key is required to decrypt the administrator password\. You then use the decrypted password to connect to your instance\. Anyone who possesses your private key can connect to your instances, so it's important that you store your private key in a secure place\.

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using RDP, you must specify a key pair\. You can choose an existing key pair or create a new one\. With Windows instances, you use the private key to obtain the administrator password and then log in using RDP\. For more information about connecting to your instance, see [Connect to your Windows instance](connecting_to_windows_instance.md)\. For more information about key pairs and Linux instances, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Because Amazon EC2 doesn't keep a copy of your private key, there is no way to recover a private key if you lose it\. However, there can still be a way to connect to instances for which you've lost the private key\. For more information, see [Connect to your Windows instance if you lose your private key](#replacing-lost-key-pair-windows)\.

You can use Amazon EC2 to create your key pairs\. You can also use a third\-party tool to create your key pairs, and then import the public keys to Amazon EC2\.

Amazon EC2 supports 2048\-bit SSH\-2 RSA keys for Windows instances\.

You can have up to 5,000 key pairs per Region\.

**Topics**
+ [Create a key pair using Amazon EC2](#having-ec2-create-your-key-pair)
+ [Create a key pair using a third\-party tool and import the public key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)
+ [Tag a public key](#tag-key-pair)
+ [Retrieve the public key from the private key](#retrieving-the-public-key-windows)
+ [Retrieve the public key through instance metadata](#retrieving-the-public-key-instance)
+ [Identify the key pair that was specified at launch](#identify-key-pair-specified-at-launch)
+ [Verify your key pair's fingerprint](#verify-key-pair-fingerprints)
+ [Delete your key pair](#delete-key-pair)
+ [Connect to your Windows instance if you lose your private key](#replacing-lost-key-pair-windows)

## Create a key pair using Amazon EC2<a name="having-ec2-create-your-key-pair"></a>

You can create a key pair using one of the following methods\. 

------
#### [ Console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Network & Security**, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\. Amazon EC2 associates the public key with the name that you specify as the key name\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. For **Key pair type**, choose **RSA** \. Note that **ED25519** keys are not supported for Windows instances, EC2 Instance Connect, or EC2 Serial Console\.

1. For **Private key file format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSSH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

1. To add a tag to the public key, choose **Add tag**, and enter the key and value for the tag\. Repeat for each tag\. 

1. Choose **Create key pair**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name that you specified as the name of your key pair, and the file name extension is determined by the file format that you chose\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

------
#### [ AWS CLI ]

**To create your key pair**
+ Use the [create\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html) command as follows to generate the key pair and to save the private key to a `.pem` file\.

  For `--key-name`, specify a name for the public key\. The name can be up to 255 ASCII characters\.

  For `--key-type`, specify either `rsa` or `ed25519`\. If you do not include the `--key-type` parameter, an `rsa` key is created by default\. Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.

  `--query "KeyMaterial"` prints the private key material to the output\.

  `--output text > my-key-pair.pem` saves the private key material in a file with the `.pem` extension\. The private key can have a name that's different from the public key name, but for ease of use, use the same name\.

  ```
  aws ec2 create-key-pair \
      --key-name my-key-pair \
      --key-type rsa \
      --query "KeyMaterial" \
      --output text > my-key-pair.pem
  ```

------
#### [ PowerShell ]

**To create your key pair**  
Use the [New\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2KeyPair.html) AWS Tools for Windows PowerShell command as follows to generate the key and save it to a `.pem` file\.

For `-KeyName`, specify a name for the public key\. The name can be up to 255 ASCII characters\.

For `-KeyType`, specify either `rsa` or `ed25519`\. If you do not include the `-KeyType` parameter, an `rsa` key is created by default\. Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.

`KeyMaterial` prints the private key material to the output\.

`Out-File -Encoding ascii -FilePath C:\path\my-key-pair.pem` saves the private key material in a file with the `.pem` extension\. The private key can have a name that's different from the public key name, but for ease of use, use the same name\.

```
PS C:\> (New-EC2KeyPair -KeyName "my-key-pair" -KeyType "rsa").KeyMaterial | Out-File -Encoding ascii -FilePath C:\path\my-key-pair.pem
```

------

## Create a key pair using a third\-party tool and import the public key to Amazon EC2<a name="how-to-generate-your-own-key-and-import-it-to-aws"></a>

Instead of using Amazon EC2 to create your key pair, you can create an RSA key pair by using a third\-party tool, and then import the public key to Amazon EC2\.

**Requirements for key pairs**
+ Supported types: RSA\. Amazon EC2 does not accept DSA keys\.
  + Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.
+ Supported formats:
  + OpenSSH public key format 
  + SSH private key file format must be PEM
  + \(RSA only\) Base64 encoded DER format
  + \(RSA only\) SSH public key file format as specified in [RFC 4716](https://www.ietf.org/rfc/rfc4716.txt)
+ Supported lengths: 1024, 2048, and 4096\. 

**To create a key pair using a third\-party tool**

1. Generate a key pair with a third\-party tool of your choice\. For example, you can use ssh\-keygen \(a tool provided with the standard OpenSSH installation\)\. Alternatively, Java, Ruby, Python, and many other programming languages provide standard libraries that you can use to create an RSA key pair\.
**Important**  
The private key must be in the PEM format\. For example, use `ssh-keygen -m PEM` to generate the OpenSSH key in the PEM format\.

1. Save the public key to a local file\. For example, `C:\keys\my-key-pair.pub`\. The file name extension for this file is not important\.

1. Save the private key to a local file that has the `.pem` extension\. For example,  `C:\keys\my-key-pair.pem`\. The file name extension for this file is important because only `.pem` files can be selected when connecting to your Windows instance from the EC2 console\.
**Important**  
Save the private key file in a safe place\. You'll need to provide the name of your public key when you launch an instance, and the corresponding private key each time you connect to the instance\.

After you have created the key pair, use one of the following methods to import your public key to Amazon EC2\.

------
#### [ Console ]

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Import key pair**\.

1. For **Name**, enter a descriptive name for the public key\. The name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.
**Note**  
When you connect to your instance from the EC2 console, the console suggests this name for the name of your private key file\.

1. Either choose **Browse** to navigate to and select your public key, or paste the contents of your public key into the **Public key contents** field\.

1. Choose **Import key pair**\.

1. Verify that the public key that you imported appears in the list of key pairs\.

------
#### [ AWS CLI ]

**To import the public key**  
Use the [import\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-key-pair.html) AWS CLI command\.

**To verify that the key pair was imported successfully**  
Use the [describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) AWS CLI command\.

------
#### [ PowerShell ]

**To import the public key**  
Use the [Import\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Import-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

**To verify that the key pair was imported successfully**  
Use the [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

## Tag a public key<a name="tag-key-pair"></a>

To help categorize and manage the public keys that you've either created using Amazon EC2 or imported to Amazon EC2, you can tag them with custom metadata\. For more information about how tags work, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

You can view, add, and delete tags using one of the following methods\.

------
#### [ Console ]

**To view, add, or delete a tag for an existing public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select a public key, and then choose **Actions**, **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the public key\.
   + To add a tag, choose **Add tag**, and then enter the tag key and value\. You can add up to 50 tags per key\. For more information, see [Tag restrictions](Using_Tags.md#tag-restrictions)\.
   + To delete a tag, choose **Remove** next to the tag to delete\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To view public key tags**  
Use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) AWS CLI command\. In the following example, you describe the tags for all of your public keys\.

```
C:\> aws ec2 describe-tags --filters "Name=resource-type,Values=key-pair"
```

```
{
    "Tags": [
    {
        "Key": "Environment",
        "ResourceId": "key-0123456789EXAMPLE",
        "ResourceType": "key-pair",
        "Value": "Production"
    },
    {
        "Key": "Environment",
        "ResourceId": "key-9876543210EXAMPLE",
        "ResourceType": "key-pair",
        "Value": "Production"
    }]
}
```

**To describe the tags for a specific public key**  
Use the [ describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) AWS CLI command\.

```
C:\> aws ec2 describe-key-pairs --key-pair-ids key-0123456789EXAMPLE
```

```
{
    "KeyPairs": [
    {
        "KeyName": "MyKeyPair",
        "KeyFingerprint": "1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f",
        "KeyPairId": "key-0123456789EXAMPLE",
        "Tags": [
        {
            "Key": "Environment",
            "Value": "Production"
        }]			
    }]
}
```

**To tag an existing public key**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\. In the following example, the existing key is tagged with `Key=Cost-Center` and `Value=CC-123`\.

```
C:\> aws ec2 create-tags --resources key-0123456789EXAMPLE --tags Key=Cost-Center,Value=CC-123
```

**To delete a tag from a public key**  
Use the [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) AWS CLI command\. For examples, see [Examples](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html#examples) in the *AWS CLI Command Reference*\.

------
#### [ PowerShell ]

**To view public key tags**  
Use the [Get\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html) command\.

**To describe the tags for a specific public key**  
Use the [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) command\.

**To tag an existing public key**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) command\.

**To delete a tag from a public key**  
Use the [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) command\.

------

## Retrieve the public key from the private key<a name="retrieving-the-public-key-windows"></a>

On your local Windows computer, you can use PuTTYgen to get the public key for your key pair\.

Start PuTTYgen and choose **Load**\. Select the `.ppk` or `.pem` private key file\. PuTTYgen displays the public key under **Public key for pasting into OpenSSH authorized\_keys file**\. You can also view the public key by choosing **Save public key**, specifying a name for the file, saving the file, and then opening the file\.

## Retrieve the public key through instance metadata<a name="retrieving-the-public-key-instance"></a>

The public key that you specified when you launched an instance is also available through the instance metadata\. To view the public key that you specified when launching the instance, use the following command from your instance\.

```
PS C:\> Invoke-RestMethod -uri  http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

The following is an example output\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

If you change the key pair that you use to connect to the instance, we don't update the instance metadata to show the new public key\. Instead, the instance metadata continues to show the public key for the key pair that you specified when you launched the instance\. For more information, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

## Identify the key pair that was specified at launch<a name="identify-key-pair-specified-at-launch"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using RDP, you must specify a key pair\.

**To identify the key pair that was specified at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, under **Instance details**, the **Key pair name** field displays the name of the key pair that you specified when you launched the instance\. The value of the **Key pair name** does not change even if you change the public key on the instance, or add key pairs\.

## Verify your key pair's fingerprint<a name="verify-key-pair-fingerprints"></a>

On the **Key Pairs** page in the Amazon EC2 console, the **Fingerprint** column displays the fingerprints generated from your key pairs\. AWS calculates the fingerprint differently depending on whether the key pair was generated by AWS or a third\-party tool\. If you created the key pair using AWS, the fingerprint is calculated using an SHA\-1 hash function\. If you created the key pair with a third\-party tool and uploaded the public key to AWS, or if you generated a new public key from an existing AWS\-created private key and uploaded it to AWS, the fingerprint is calculated using an MD5 hash function\.

You can use the SSH2 fingerprint that's displayed on the **Key Pairs** page to verify that the private key you have on your local machine matches the public key stored in AWS\. From the computer where you downloaded the private key file, generate an SSH2 fingerprint from the private key file\. The output should match the fingerprint that's displayed in the console\.

If you're using a Windows local machine, you can run the following commands using the Windows Subsystem for Linux \(WSL\)\. Install the WSL and a Linux distribution using the instructions in the [Windows 10 Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10)\. The example in the instructions installs the Ubuntu distribution of Linux, but you can install any distribution\. You are prompted to restart your computer for the changes to take effect\.

If you created your key pair using AWS, you can use the OpenSSL tools to generate a fingerprint as shown in the following example\.

```
$ openssl pkcs8 -in path_to_private_key -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c
```

If you created a key pair using a third\-party tool and uploaded the public key to AWS, you can use the OpenSSL tools to generate the fingerprint as shown in the following example\.

```
$ openssl rsa -in path_to_private_key -pubout -outform DER | openssl md5 -c
```

If you created an OpenSSH key pair using OpenSSH 7\.8 or later and uploaded the public key to AWS, you can use ssh\-keygen to generate the fingerprint as shown in the following examples\.

For RSA key pairs:

```
$ ssh-keygen -ef path_to_private_key -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
```

```
$ ssh-keygen -l -f path_to_private_key.pem
```

## Delete your key pair<a name="delete-key-pair"></a>

When you delete a key pair using the following methods, you are only deleting the public key that you saved in Amazon EC2 when you [created](#having-ec2-create-your-key-pair) or [imported](#how-to-generate-your-own-key-and-import-it-to-aws) the key pair\. Deleting a key pair doesn't delete the public key from any instances that were previously launched using that key pair\. It also doesn't delete the private key on your local computer\. You can continue to connect to instances that you launched using a key pair that is subsequently deleted, as long as you still have the private key \(`.pem`\) file\.

If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the key pair you're deleting is not specified in an associated launch template or launch configuration\. If Amazon EC2 Auto Scaling detects an unhealthy instance, it launches a replacement instance\. However, the instance launch fails if the key pair cannot be found\. For more information, see [Launch templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) in the *Amazon EC2 Auto Scaling User Guide*\.

You can delete a key pair using one of the following methods\.

------
#### [ Console ]

**To delete your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select the key pair to delete and choose **Delete**\.

1. In the confirmation field, enter `Delete` and then choose **Delete**\.

------
#### [ AWS CLI ]

**To delete your key pair**  
Use the [delete\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html) AWS CLI command\.

------
#### [ PowerShell ]

**To delete your key pair**  
Use the [Remove\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

## Connect to your Windows instance if you lose your private key<a name="replacing-lost-key-pair-windows"></a>

When you connect to a newly launched Windows instance, you decrypt the password for the Administrator account using the private key for the key pair that you specified when you launched the instance\.

If you lose the Administrator password and you no longer have the private key, you must reset the password or create a new instance\. For more information, see [Reset a lost or expired Windows administrator password](ResettingAdminPassword.md)\. For steps to reset the password using an Systems Manager document, see [Walkthrough: Reset passwords and SSH keys on EC2 instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.