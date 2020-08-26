# Troubleshooting Sysprep<a name="sysprep-troubleshoot"></a>

If you experience problems or receive error messages during image preparations, review the following logs: 
+ %WINDIR%\\Panther\\Unattendgc
+ %WINDIR%\\System32\\Sysprep\\Panther
+ "C:\\Program Files\\Amazon\\Ec2ConfigService\\Logs\\Ec2ConfigLog\.txt"

If you receive an error message during image preparation with Sysprep, the OS might not be reachable\. To review the log files, you must stop the instance, attach its root volume to another healthy instance as a secondary volume, and then review the logs mentioned earlier on the secondary volume\. For more information about the purpose of the log files by name, see [Windows Setup\-Related Log Files](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/deployment-troubleshooting-and-log-files#windows-setup-related-log-files) in the Microsoft documentation\.

If you locate errors in the Unattendgc log file, use the [Microsoft Error Lookup Tool](https://www.microsoft.com/en-us/download/details.aspx?id=100432) to get more details about the error\. The following issue reported in the Unattendgc log file is typically the result of one or more corrupted user profiles on the instance:

```
Error [Shell Unattend] _FindLatestProfile failed (0x80070003) [gle=0x00000003]
		Error [Shell Unattend] CopyProfile failed (0x80070003) [gle=0x00000003]
```

There are two options for resolving this issue:

**Option 1:** Use Regedit on the instance to search for the following key\. Verify that there are no profile registry keys for a deleted user:

\[HKEY\_LOCAL\_MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\ProfileList\\

**Option 2:** Edit the EC2Config answer file \(`C:\Program Files\Amazon\Ec2ConfigService\sysprep2008.xml`\) and change <CopyProfile>true</CopyProfile> to <CopyProfile>false</CopyProfile>\. Run Sysprep again\. Note that this configuration change will delete the built\-in administrator user profile after Sysprep completes\.