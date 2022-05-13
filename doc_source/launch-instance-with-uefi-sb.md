# Launch a Windows instance with UEFI Secure Boot support<a name="launch-instance-with-uefi-sb"></a>

When you [launch an instance](LaunchingAndUsingInstances.md) with the following prerequisites, the instance will automatically validate UEFI boot binaries against its UEFI Secure Boot database\. You can also configure UEFI Secure Boot on an instance after launch\.

**Note**  
UEFI Secure Boot protects your instance and its operating system against boot flow modifications\. Typically, UEFI Secure Boot is configured as part of the AMI\. If you create a new AMI with different parameters from the base AMI, such as changing the `UefiData` within the AMI, you can disable UEFI Secure Boot\.

**Prerequisites for Windows instances**
+ **AMI** – Requires an AMI with UEFI Secure Boot enabled\.

  The following Windows AMIs are preconfigured to enable UEFI Secure Boot with Microsoft keys:
  + TPM\-Windows\_Server\-2022\-English\-Full\-Base
  + TPM\-Windows\_Server\-2022\-English\-Core\-Base
  + TPM\-Windows\_Server\-2019\-English\-Full\-Base
  + TPM\-Windows\_Server\-2019\-English\-Core\-Base
  + TPM\-Windows\_Server\-2016\-English\-Full\-Base
  + TPM\-Windows\_Server\-2016\-English\-Core\-Base

  Currently, we do not support importing Windows with UEFI Secure Boot by using the [import\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) command\.
+ **Instance type** – All virtualized instance types that support UEFI also support UEFI Secure Boot\. Bare metal instance types do not support UEFI Secure Boot\. For the instance types that support UEFI Secure Boot, see [Considerations](launch-instance-boot-mode.md#boot-considerations)\.

For the prerequisites for Linux instances, see [Launch an instance with UEFI Secure Boot support](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/uefi-secure-boot.html#launch-instance-with-uefi-sb) in the ***Amazon EC2 User Guide for Linux Instances*\.