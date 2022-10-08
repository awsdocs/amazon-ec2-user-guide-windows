# Configure the instance metadata options<a name="configuring-instance-metadata-options"></a>

Instance metadata options allow you to configure new or existing instances to do the following:
+ Require the use of IMDSv2 when requesting instance metadata
+ Specify the `PUT` response hop limit
+ Turn off access to instance metadata

You can also use IAM condition keys in an IAM policy or SCP to do the following:
+ Allow an instance to launch only if it's configured to require the use of IMDSv2
+ Restrict the number of allowed hops
+ Turn off access to instance metadata

**Note**  
If your PowerShell version is earlier than 4\.0, you must [update to Windows Management Framework 4\.0](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/) to require the use of IMDSv2\.

**Note**  
You should proceed cautiously and conduct careful testing before making any changes\. Take note of the following:  
If you enforce the use of IMDSv2, applications or agents that use IMDSv1 for instance metadata access will break\.
If you turn off all access to instance metadata, applications or agents that rely on instance metadata access to function will break\.
For IMDSv2, you must use `/latest/api/token` when retrieving the token\.

**Topics**
+ [Configure instance metadata options for new instances](configuring-IMDS-new-instances.md)
+ [Modify instance metadata options for existing instances](configuring-IMDS-existing-instances.md)