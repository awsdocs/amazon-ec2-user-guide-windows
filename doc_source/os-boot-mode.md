# Determine the boot mode of the operating system<a name="os-boot-mode"></a>

The boot mode of the AMI guides Amazon EC2 on which boot mode to use to boot an instance\. To view whether the operating system of your instance is configured for UEFI, you need to connect to your instance using RDP\.

**To determine the boot mode of the instance’s operating system**

1. [Connect to your Windows instance using RDP](connecting_to_windows_instance.md)\.

1. Go to **System Information** and check the **BIOS Mode** row\.  
![\[System Information window showing the BIOS Mode row selected. The value for BIOS Mode is Legacy.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/BIOS-mode-win.png)