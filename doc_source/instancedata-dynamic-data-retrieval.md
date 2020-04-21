# Retrieving dynamic data<a name="instancedata-dynamic-data-retrieval"></a>

To retrieve dynamic data from within a running instance, use the following URI\. 

```
http://169.254.169.254/latest/dynamic/
```

This example shows how to retrieve the high\-level instance identity categories\.

------
#### [ IMDSv2 ]

```
PS C:\> $token = Invoke-RestMethod -Headers @{"X-aws-ec2-metadata-token-ttl-seconds" = "21600"} -Method PUT –Uri http://169.254.169.254/latest/api/token
```

```
PS C:\> Invoke-RestMethod -Headers @{"X-aws-ec2-metadata-token" = $token} -Method GET -Uri http://169.254.169.254/latest/dynamic/instance-identity/
document
rsa2048
pkcs7
signature
```

------
#### [ IMDSv1 ]

```
PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/dynamic/instance-identity/
document
rsa2048
pkcs7
signature
```

------

For more information about dynamic data and examples of how to retrieve it, see [Instance identity documents](instance-identity-documents.md)\.