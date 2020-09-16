# Using the base64\-encoded signature to verify the instance identity document<a name="verify-signature"></a>

This topic explains how to verify the instance identity document using the base64\-encoded signature and the AWS RSA public certificate\.

**Important**  
To validate the instance identity document using the base64\-encoded signature, you must request the AWS RSA public certificate from [AWS Support](https://console.aws.amazon.com/support/home#/)\. 

**To verify the instance identity document using the PKCS7 signature and the AWS DSA public certificate**

1. Connect to the instance\.

1. Retrieve the base64\-encoded signature from the instance metadata, convert it to a byte array, and add it to variable named `$Signature`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   PS C:\> $Token = (Invoke-WebRequest -Method Put -Headers @{'X-aws-ec2-metadata-token-ttl-seconds' = '21600'} http://169.254.169.254/latest/api/token).Content
   ```

   ```
   PS C:\> $Signature = [Convert]::FromBase64String((Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token' = $Token} http://169.254.169.254/latest/dynamic/instance-identity/signature).Content)
   ```

------
#### [ IMDSv1 ]

   ```
   PS C:\> $Signature = [Convert]::FromBase64String((Invoke-WebRequest http://169.254.169.254/latest/dynamic/instance-identity/signature).Content)
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

1. Add the AWS RSA public certificate that you received from AWS Support to a file named `certificate.pem`\.

1. Verify the instance identity document\.

   ```
   PS C:\> [Security.Cryptography.X509Certificates.X509Certificate2]::new((Resolve-Path certificate.pem)).PublicKey.Key.VerifyData($Document, 'SHA256', $Signature)
   ```

   If the signature is valid, the command returns `True`\. If the signature cannot be verified, contact AWS Support\.