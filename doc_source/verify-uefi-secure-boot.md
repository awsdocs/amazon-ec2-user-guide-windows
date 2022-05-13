# Verify whether a Windows instance is enabled for UEFI Secure Boot<a name="verify-uefi-secure-boot"></a>

**To verify whether a Windows instance is enabled for UEFI Secure Boot**

1. Open the msinfo32 tool\.

1. Check the **Secure Boot State** field\. **Supported** indicates that UEFI Secure Boot is enabled\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/secure-boot-state-win.png)

You can also use the Windows PowerShell Cmdlet `Confirm-SecureBootUEFI` to check the the Secure Boot status\.

To verify whether a Linux instance is enabled, see [Verify whether a Linux instance is supported for UEFI Secure Boot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/uefi-secure-boot.html#verify-uefi-secure-boot) in the ***Amazon EC2 User Guide for Linux Instances*\.