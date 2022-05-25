# Prerequisites<a name="enable-nitrotpm-prerequisites"></a>

**Prerequisites for Windows instances**
+ **AMI**

  Requires an AMI with NitroTPM enabled\.

  The following Windows AMIs are preconfigured to enable NitroTPM and UEFI Secure Boot with Microsoft keys:
  + TPM\-Windows\_Server\-2022\-English\-Full\-Base
  + TPM\-Windows\_Server\-2022\-English\-Core\-Base
  + TPM\-Windows\_Server\-2019\-English\-Full\-Base
  + TPM\-Windows\_Server\-2019\-English\-Core\-Base
  + TPM\-Windows\_Server\-2016\-English\-Full\-Base
  + TPM\-Windows\_Server\-2016\-English\-Core\-Base

  Currently, we do not support importing Windows with NitroTPM by using the [import\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) command\.
+ **Operating system**

  The AMI must include an operating system with a TPM 2\.0 CRB driver\. Most current operating systems, such as TPM\-Windows\_Server\-2022\-English\-Full\-Base, contain a TPM 2\.0 CRB driver\.
+ **Instance type**

  Supported virtualized instance types: `C5`, `C5a`, `C5ad`, `C5d`, `C5n`, `C6i`, `D3`, `D3en`, `G4dn`, `G5`, `Hpc6a`, `I3en`, `I4i`, `Inf1`, `M5`, `M5a`, `M5ad`, `M5d`, `M5dn`, `M5n`, `M5zn`, `M6a`, `M6i`, `R5`, `R5a`, `R5ad`, `R5b`, `R5d`, `R5dn`, `R5n`, `R6i`, `U-3tb1`, `U-6tb1`, `U-9tb1`, `U-12tb1`, `X2idn`, `X2iedn`, `X2iezn`, and `z1d`\.

  Support is coming soon on: `C6a`, `G4ad`, `P3dn`, `T3`, and `T3a`

  Not supported: Graviton1, Graviton2, Xen, Mac, and bare metal instances
+ **UEFI boot mode**

  NitroTPM requires that an instance runs in UEFI boot mode, which requires that the AMI must be configured for UEFI boot mode\. For more information, see [UEFI Secure Boot](uefi-secure-boot.md)\.