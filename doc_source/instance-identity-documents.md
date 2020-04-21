# Instance identity documents<a name="instance-identity-documents"></a>

An instance identity document is a JSON file that describes an instance\. The instance identity document is accompanied by a signature and a PKCS7 signature, which can be used to verify the accuracy, origin, and authenticity of the information provided in the document\. 

The instance identity document is generated when the instance is launched, and exposed to the instance through [instance metadata](ec2-instance-metadata.md)\. It validates the attributes of the instances, such as the instance size, instance type, operating system, and AMI\. 

**Important**  
Due to the dynamic nature of instance identity documents and signatures, we recommend retrieving the instance identity document and signature regularly\.

## Obtaining the instance identity document and signatures<a name="instance-identity-signatures"></a>

To retrieve the instance identity document, use the following command from your running instance\.

```
PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/dynamic/instance-identity/document
```

The following is example output\.

```
privateIp          : 10.0.2.174
availabilityZone   : us-west-2a
devpayProductCodes :
version            : 2010-08-31
instanceId         : i-1234567890abcdef0
billingProducts    : {bp-6ba54002}
instanceType       : m3.medium
architecture       : x86_64
accountId          : 123456789012
kernelId           :
ramdiskId          :
imageId            : ami-1562d075
pendingTime        : 2017-03-13T17:13:27Z
region             : us-west-2
```

To retrieve the instance identity signature, use the following command from your running instance\.

```
PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/dynamic/instance-identity/signature
```

The following is example output\.

```
dExamplesjNQhhJan7pORLpLSr7lJEF4V2DhKGlyoYVBoUYrY9njyBCmhEayaGrhtS/AWY+LPx
lVSQURF5n0gwPNCuO6ICT0fNrm5IH7w9ydyaexamplejJw8XvWPxbuRkcN0TAA1p4RtCAqm4ms
x2oALjWSCBExample=
```

To retrieve the PKCS7 signature, use the following command from your running instance\.

```
PS C:\> Invoke-RestMethod -uri http://169.254.169.254/latest/dynamic/instance-identity/pkcs7
```

The following is example output\.

```
MIICiTCCAfICCQD6m7oRw0uXOjANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMC
VVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6
b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAd
BgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5jb20wHhcNMTEwNDI1MjA0NTIxWhcN
MTIwNDI0MjA0NTIxWjCBiDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYD
VQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6b24xFDASBgNVBAsTC0lBTSBDb25z
b2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAdBgkqhkiG9w0BCQEWEG5vb25lQGFt
YXpvbi5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMaK0dn+a4GmWIWJ
21uUSfwfEvySWtC2XADZ4nB+BLYgVIk60CpiwsZ3G93vUEIO3IyNoH/f0wYK8m9T
rDHudUZg3qX4waLG5M43q7Wgc/MbQITxOUSQv7c7ugFFDzQGBzZswY6786m86gpE
Ibb3OhjZnzcvQAaRHhdlQWIMm2nrAgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAtCu4
nUhVVxYUntneD9+h8Mg9q6q+auNKyExzyLwaxlAoo7TJHidbtS4J5iNmZgXL0Fkb
FFBjvSfpJIlJ00zbhNYS5f6GuoEDmFJl0ZxBHjJnyp378OD8uTs7fLvjx79LjSTb
NYiytVbZPQUQ5Yaxu2jXnimvw3rrszlaEXAMPLE
```