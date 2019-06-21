# Before You Begin<a name="application-consistent-snapshots-prereqs"></a>

Before you create VSS\-enabled EBS snapshots by using Run Command, review the following requirements and limitations, and complete the required tasks\. 

**Amazon EC2 Windows instance requirements**  
VSS\-enabled EBS snapshots are supported for instances running Windows Server 2008 R2 or later\. \(Windows Server 2008 R2 Core is currently not supported\.\) Verify that your instances meet all requirements for Amazon EC2 Windows\. For more information, see [Setting Up AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

**SSM Agent version**  
Update your instances to use SSM Agent version 2\.2\.58\.0 or later\. If you are using an older version of SSM Agent, you can update it by using Run Command\. For more information, see [Update SSM Agent by using Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/rc-console.html#rc-console-agentexample) in the *AWS Systems Manager User Guide*\.

**AWS Tools for Windows PowerShell version**  
Ensure that your instance is running version 3\.3\.48\.0 or later of the AWS Tools for Windows PowerShell\. To check your version number, run the following command on the instance:

```
Get-AWSPowerShellVersion
```

If you need to update the version of Tools for Windows PowerShell on your instance, see [Setting up the AWS Tools for Windows PowerShell on a Windows\-based Computer](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up-windows.html) in the *AWS Tools for Windows PowerShell User Guide*\.