# Retrieve dynamic data<a name="instancedata-dynamic-data-retrieval"></a>

To retrieve dynamic data from within a running instance, use the following URI\. 

```
http://169.254.169.254/latest/dynamic/
```

**Note**  
The examples in this section use the IPv4 address of the instance metadata service: `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the instance metadata service is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

This example shows how to retrieve the high\-level instance identity categories\.

------
#### [ IMDSv2 ]

```
PS C:\> [string]$token = Invoke-RestMethod -Headers @{"X-aws-ec2-metadata-token-ttl-seconds" = "21600"} -Method PUT –Uri http://169.254.169.254/latest/api/token
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