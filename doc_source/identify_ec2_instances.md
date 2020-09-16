# Identify EC2 Windows instances<a name="identify_ec2_instances"></a>

Your application might need to determine whether it is running on an EC2 instance\.

For information about identifying Linux instances, see [Identify EC2 Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/identify_ec2_instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Inspecting the instance identity document<a name="inspect-document"></a>

For a definitive and cryptographically verified method of identifying an EC2 instance, check the instance identity document, including its signature\. These documents are available on every EC2 instance at the local, non\-routable address `http://169.254.169.254/latest/dynamic/instance-identity/`\. For more information, see [Instance identity documents](instance-identity-documents.md)\.

## Inspecting the system UUID<a name="inspect-uuid"></a>

You can get the system UUID and look for the presence of the characters "EC2" in the beginning octet of the UUID\. This method to determine whether a system is an EC2 instance is quick but potentially inaccurate because there is a small chance that a system that is not an EC2 instance could have a UUID that starts with these characters\. Furthermore, EC2 instances using SMBIOS 2\.4 might represent the UUID in little\-endian format, therefore the "EC2" characters do not appear at the beginning of the UUID\.

**Example : Get the UUID using WMI or Windows PowerShell**  
Use the Windows Management Instrumentation command line \(WMIC\) as follows:  

```
wmic path win32_computersystemproduct get uuid
```
Alternatively, if you're using Windows PowerShell, use the Get\-WmiObject cmdlet as follows:  

```
PS C:\> Get-WmiObject -query "select uuid from Win32_ComputerSystemProduct" | Select UUID
```
In the following example output, the UUID starts with "EC2", which indicates that the system is probably an EC2 instance\.  

```
EC2AE145-D1DC-13B2-94ED-01234ABCDEF
```
For instances using SMBIOS 2\.4, the UUID might be represented in little\-endian format; for example:  

```
45E12AEC-DCD1-B213-94ED-01234ABCDEF
```