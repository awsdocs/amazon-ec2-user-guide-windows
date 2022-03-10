# Verify the EC2Launch v2 version<a name="ec2launch-v2-verify-version"></a>

Use one of the following procedures to verify the version of EC2Launch v2 that is installed on your instances\.



**To verify the installed version of EC2Launch v2 with a command**

1. Launch an instance from your AMI and connect to it\.

1. Run the following command in PowerShell to verify the installed version of EC2Launch v2:

   ```
   & "C:\Program Files\Amazon\EC2Launch\EC2Launch.exe" version
   ```

**To verify the installed version of EC2Launch v2 in the Control Panel**

In the list of installed programs, look for `Amazon EC2Launch`\. Its version number appears in the **Version** column\.

1. Launch an instance from your AMI and connect to it\.

1. In the Control Panel, select **Programs and Features**\.

1. In the list of installed programs, look for `Amazon EC2Launch`\. Its version number appears in the **Version** column\.

For information about the EC2Launch v2 versions included in the Windows AMIs, see [AWS Windows AMIs](windows-ami-version-history.md)\.

For the latest version of EC2Launch v2, see [EC2Launch v2 version history](ec2launchv2-versions.md#ec2launchv2-version-history)\.

For the latest version of the EC2Launch v2 migration tool, see [EC2Launch v2 migration tool version history](ec2launchv2-versions.md#ec2launchv2-migration-tool-version-history)\.

You can receive notifications when new versions of the EC2Launch v2 service are released\. For more information, see [Subscribe to EC2Launch v2 service notifications](ec2launch-v2-sns.md)\.