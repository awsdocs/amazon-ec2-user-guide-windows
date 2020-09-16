# Using the RSA\-2048 signature to verify the instance identity document<a name="verify-rsa2048"></a>

This topic explains how to verify the instance identity document using the RSA\-2048 signature and the AWS RSA\-2048 public certificate\.

**Important**  
To validate the instance identity document using the RSA\-2048 signature, you must request the AWS RSA\-2048 public certificate from [AWS Support](https://console.aws.amazon.com/support/home#/)\. 

**Prerequisites**  
This procedure requires the `System.Security` Microsoft \.NET Core class\. To add the class to your PowerShell session, run the following command\.

```
PS C:\> Add-Type -AssemblyName System.Security
```

**Note**  
The command adds the class to the current PowerShell session only\. If you start a new session, you must run the command again\.

**To verify the instance identity document using the RSA\-2048 signature and the AWS RSA\-2048 public certificate**

1. Connect to the instance\.

1. Retrieve the RSA\-2048 signature from the instance metadata, convert it to a byte array, and add it to a variable named `$Signature`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   PS C:\> $Token = (Invoke-WebRequest -Method Put -Headers @{'X-aws-ec2-metadata-token-ttl-seconds' = '21600'} http://169.254.169.254/latest/api/token).Content
   ```

   ```
   PS C:\> $Signature = [Convert]::FromBase64String((Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token' = $Token} http://169.254.169.254/latest/dynamic/instance-identity/rsa2048).Content)
   ```

------
#### [ IMDSv1 ]

   ```
   PS C:\> $Signature = [Convert]::FromBase64String((Invoke-WebRequest http://169.254.169.254/latest/dynamic/instance-identity/rsa2048).Content)
   ```

------

1. Retrieve the plaintext instance identity document from the instance metadata, convert it to a byte array, and add it to a variable named `$Document`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   PS C:\> $Document = [Text.Encoding]::UTF8.GetBytes((Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token' = $Token} http://169.254.169.254/latest/dynamic/instance-identity/document).Content)
   ```

------
#### [ IMDSv1 ]

   ```
   PS C:\> $Document =  [Text.Encoding]::UTF8.GetBytes((Invoke-WebRequest http://169.254.169.254/latest/dynamic/instance-identity/document).Content)
   ```

------

1. Add the AWS RSA\-2048 public certificate that you received from AWS Support to a new file named `certificate.pem`\.

1. Extract the certificate from the certificate file and store it in a variable named `$Store`\.

   ```
   PS C:\> $Store = [Security.Cryptography.X509Certificates.X509Certificate2Collection]::new([Security.Cryptography.X509Certificates.X509Certificate2]::new((Resolve-Path certificate.pem)))
   ```

1. Verify the signature\.

   ```
   PS C:\> $SignatureDocument = [Security.Cryptography.Pkcs.SignedCms]::new()
   ```

   ```
   PS C:\> $SignatureDocument.Decode($Signature)
   ```

   ```
   PS C:\> $SignatureDocument.CheckSignature($Store, $true)
   ```

   If the signature is valid, the command returns no output\. If the signature cannot be verified, the command returns `Exception calling "CheckSignature" with "2" argument(s): "Cannot find the original signer`\. If your signature cannot be verified, contact AWS Support\.

1. Validate the content of the instance identity document\.

   ```
   PS C:\> [Linq.Enumerable]::SequenceEqual($SignatureDocument.ContentInfo.Content, $Document)
   ```

   If the content of the instance identity document is valid, the command returns `True`\. If instance identity document cannot be validated, contact AWS Support\.