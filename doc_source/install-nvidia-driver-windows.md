# Installing the NVIDIA Driver on Windows<a name="install-nvidia-driver-windows"></a>

A GPU\-based accelerated computing instance must have the appropriate NVIDIA driver\. Depending on the instance type, you can either download a public NVIDIA driver, use an NVIDIA Marketplace offering, or download a driver from Amazon S3 that is available only to AWS customers\.

**Topics**
+ [Public NVIDIA Drivers](#nvidia-drivers-public)
+ [NVIDIA GRID Drivers for G4 Instances](#nvidia-grid-g4)
+ [NVIDIA GRID Drivers for G3 Instances](#nvidia-grid-g3)

## Public NVIDIA Drivers<a name="nvidia-drivers-public"></a>

To install a public NVIDIA driver on your Windows instance, log on to your instance as the administrator using Remote Desktop and complete the following procedure\.

**To install a public NVIDIA driver**

1. Download the public NVIDIA driver that is appropriate for your instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/install-nvidia-driver-windows.html)

   † G4 instances require driver version 426\.00 or later\.

1. Open the folder where you downloaded the driver and launch the installation file\. Follow the instructions to install the driver and reboot your instance as required\.

1. Disable the built\-in display adapter using Device Manager\. Install the Windows features Media Foundation and Quality Windows Audio Video Experience\.

1. To verify that the GPU is working correctly, check Device Manager\.

1. Complete the optimization steps in [Optimizing GPU Settings](optimize_gpu.md) to achieve the best performance from your GPU\.

When you use P3 instances for graphics applications, you can use [AMIs published by NVIDIA](http://aws.amazon.com/marketplace/search/results?x=0&y=0&searchTerms=Quadro+NVIDIA) that enable NVIDIA Quadro Virtual Workstation\. For G4 instances, see [NVIDIA GRID Drivers for G4 Instances](#nvidia-grid-g4)\.

## NVIDIA GRID Drivers for G4 Instances<a name="nvidia-grid-g4"></a>

There are two ways that you can use NVIDIA GRID software for graphics applications on G4 instances\. You can use AMIs with GRID preinstalled or download the NVIDIA GRID vGaming driver from Amazon S3 and install it on your G4 instances\.

If you own GRID licenses, you should be able to use those licenses on your G4 instances\. For more information, see [NVIDIA GRID Software Quick Start Guide](https://docs.nvidia.com/grid/latest/grid-software-quick-start-guide/)\.

**Option 1: Use an AMI with GRID for your G4 instances**

There are two types of AMIs available\.
+ NVIDIA Marketplace offerings featuring NVIDIA Quadro Virtual Workstation software\. These AMIs support running up to four 4K displays per GPU on a G4 instance\. To find these AMIs, use this link: [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g4dn.xlarge&searchTerms=NVIDIA%20Quadro)\.
+ NVIDIA Marketplace offerings featuring NVIDIA Gaming drivers\. These AMIs support running a single 4K display per GPU on a G4 instance\. To find these AMIs, use this link: [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g4dn.xlarge&searchTerms=NVIDIA%20Gaming)\.

**Option 2: Use the NVIDIA GRID vGaming driver**

This driver is available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA Tesla T4 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

If you own GRID licenses, you should be able to use those licenses on your G4 instances\. For more information, see [NVIDIA GRID Software Quick Start Guide](https://docs.nvidia.com/grid/latest/grid-software-quick-start-guide/)\.

Use the following procedure to install this driver\.

1. Connect to your Windows instance\.

1. Download and install the NVIDIA GRID driver from Amazon S3 using this link: [NVIDIA Windows Gaming Driver for G4 Instances](https://s3.amazonaws.com/nvidia-gaming/GRID-436.30-vGaming-Windows-Guest-Drivers.zip)\.

1. Use the following steps to create the required configuration\.

   1. Open the registry editor\.

   1. In the left pane, navigate to **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\NVIDIA Corporation\\Global**\.

   1. In the right pane, right\-click and choose **New**, **DWORD \(32\-bit\) value**\.

   1. Set the name to **vGamingMarketplace** and the value to 2\.

1. Download the [certification file](https://s3.amazonaws.com/nvidia-gaming/GridSwCert-Windows.cert), rename the file `GridSwCert.txt`, and move the file to the Public Documents folder on your system drive\. Typically, the folder path is C:\\Users\\Public\\Public Documents \(Windows Explorer\) or C:\\Users\\Public\\Documents \(Command Prompt window\)\.

1. Reboot your instance\.

1. Verify the vGaming license using the following command\.

   ```
   "C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe" -q
   ```

   The output should be similar to the following\.

   ```
   GRID Licensed Product
       Product Name              : GRID vGaming
       License Status            : Licensed
   ```

## NVIDIA GRID Drivers for G3 Instances<a name="nvidia-grid-g3"></a>

AWS provides Windows AMIs for G3 instances that come with GRID drivers preinstalled\. To find these AMIs, open [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results?page=1&filters=VendorId&VendorId=e6a5002c-6dd0-4d1e-8196-0a1d1857229b&searchTerms=GRID+NVIDIA)\.

Alternatively, you can download the NVIDIA GRID driver from Amazon S3 using the AWS Tools for Windows PowerShell as described in the following procedure\.

**Important**  
This download is available to AWS customers only\. By downloading, you agree that you will use the downloaded software only to develop AMIs for use with the NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**
+ Confirm that the version of Windows you are launching is supported by NVIDIA GRID drivers\. Driver requirements are documented at [https://docs\.nvidia\.com/grid/index\.html]( https://docs.nvidia.com/grid/index.html)\. 
+ Associate an IAM role with your instance that has permissions to use the `s3:ListBucket` and `s3:GetObject` actions\. For more information, see [IAM Roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\. Alternatively, configure the Tools for Windows PowerShell to use your AWS credentials\. For more information, see [Using AWS Credentials](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)\.

**To install the NVIDIA GRID driver on a G3 instance**

1. Open a PowerShell window\.

1. Download the drivers and the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX) to your desktop with the following PowerShell commands \(you can copy and paste the entire block of commands at one time\)\.

   ```
   $Bucket = "ec2-windows-nvidia-drivers"
   $KeyPrefix = "latest"
   $LocalPath = "C:\Users\Administrator\Desktop\NVIDIA"
   $Objects = Get-S3Object -BucketName $Bucket -KeyPrefix $KeyPrefix -Region us-east-1
   foreach ($Object in $Objects) {
       $LocalFileName = $Object.Key
       if ($LocalFileName -ne '' -and $Object.Size -ne 0) {
           $LocalFilePath = Join-Path $LocalPath $LocalFileName
           Copy-S3Object -BucketName $Bucket -Key $Object.Key -LocalFile $LocalFilePath -Region us-east-1
       }
   }
   ```

   Multiple versions of the NVIDIA GRID driver are stored in this bucket\. You can download all of the available versions in the bucket by removing the `-KeyPrefix $KeyPrefix` option\.

1. Navigate to the desktop and double\-click the installation file to launch it \(choose the driver version that corresponds to your instance OS version\)\. Follow the instructions to install the driver and reboot your instance as required\. To verify that the GPU is working properly, check Device Manager\.

1. Disable the licensing page in the control panel to prevent users from accidentally changing the product type \(NVIDIA GRID Virtual Workstation is enabled by default\)\. For more information, see the [GRID Licensing User Guide](http://docs.nvidia.com/grid/4.6/grid-licensing-user-guide/index.html)\.

   1. Run regedit\.exe to open the registry editor\.

   1. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\NVIDIA Corporation\Global\GridLicensing`\.

   1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

   1. For **Name**, enter **NvCplDisableManageLicensePage** and type `Enter`\.

   1. Open the context \(right\-click\) menu on **NvCplDisableManageLicensePage** and choose **Modify**\.

   1. For **Value data**, type `1` and choose **OK**\.

1. \(Optional\) To enable NVIDIA GRID Virtual Applications, complete the GRID activation steps in [Activate NVIDIA GRID Virtual Applications on G3 Instances](activate_grid.md) \(NVIDIA GRID Virtual Workstation is enabled by default\)\.

1. Complete the optimization steps in [Optimizing GPU Settings](optimize_gpu.md) to achieve the best performance from your GPU\.