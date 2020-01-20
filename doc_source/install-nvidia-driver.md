# Installing NVIDIA Drivers on Windows Instances<a name="install-nvidia-driver"></a>

An instance with an attached GPU, such as a P3 or G4 instance, must have the appropriate NVIDIA driver installed\. Depending on the instance type, you can either download a public NVIDIA driver, download a driver from Amazon S3 that is available only to AWS customers, or use an AMI with the driver pre\-installed\.

**Contents**
+ [Types of NVIDIA Drivers](#nvidia-driver-types)
+ [Available Drivers by Instance Type](#nvidia-driver-instance-type)
+ [Installation Options](#nvidia-installation-options)
  + [Option 1: AMIs with the NVIDIA Drivers Installed](#preinstalled-nvidia-driver)
  + [Option 2: Public NVIDIA Tesla Drivers](#public-nvidia-driver)
  + [Option 3: GRID Drivers \(G3 and G4 Instances\)](#nvidia-GRID-driver)
  + [Option 4: NVIDIA Gaming Drivers \(G4 Instances\)](#nvidia-gaming-driver)

## Types of NVIDIA Drivers<a name="nvidia-driver-types"></a>

The following are the main types of NVIDIA drivers that can be used with GPU\-based instances\.

Tesla drivers  
These drivers are intended primarily for compute workloads, which use GPUs for computational tasks such as parallelized floating\-point calculations for machine learning and fast Fourier transforms for high performance computing applications\.

GRID drivers  
These drivers are certified to provide optimal performance for professional visualization applications that render content such as 3D models or high\-resolution videos\. You can configure GRID drivers to support two modes\. Quadro Virtual Workstations provide access to four 4K displays per GPU\. GRID vApps provide RDSH App hosting capabilities\.

Gaming drivers  
These drivers contain optimizations for gaming and are updated frequently to provide performance enhancements\. They support a single 4K display per GPU\.

**Configured Mode**  
On Windows, the Tesla drivers are configured to run in Tesla Compute Cluster \(TCC\) mode\. The GRID and gaming drivers are configured to run in Windows Display Driver Model \(WDDM\) mode\. In TCC mode, the card is dedicated to compute workloads\. In WDDM mode, the card supports both compute and graphics workloads\.

**Supported APIs for Tesla Drivers**
+ OpenCL
+ NVIDIA CUDA and related libraries \(for example, cuDNN, TensorRT, nvJPEG, and cuBLAS\)
+ NVENC for video encoding and NVDEC for video decoding

**Supported APIs for GRID and Gaming Drivers**
+ DirectX, Direct2D, DirectX Video Acceleration, DirectX Raytracing
+ OpenCL, OpenGL, and Vulkan
+ NVIDIA CUDA and related libraries \(for example, cuDNN, TensorRT, nvJPEG, and cuBLAS\)
+ NVENC for video encoding and NVDEC for video decoding

## Available Drivers by Instance Type<a name="nvidia-driver-instance-type"></a>

The following table summarizes the supported NVIDIA drivers for each GPU instance type\.


| Instance Type | Tesla Driver | GRID Driver | Gaming Driver | 
| --- | --- | --- | --- | 
| G2 | Yes | No | No | 
| G3 | Yes | Yes | No | 
| G4 | Yes | Yes | Yes | 
| P2 | Yes | No | No | 
| P3 | Yes | Yes † | No | 

† Using Marketplace AMIs only

## Installation Options<a name="nvidia-installation-options"></a>

Use one of the following options to get the NVIDIA drivers required for your GPU instance\.

**Topics**
+ [Option 1: AMIs with the NVIDIA Drivers Installed](#preinstalled-nvidia-driver)
+ [Option 2: Public NVIDIA Tesla Drivers](#public-nvidia-driver)
+ [Option 3: GRID Drivers \(G3 and G4 Instances\)](#nvidia-GRID-driver)
+ [Option 4: NVIDIA Gaming Drivers \(G4 Instances\)](#nvidia-gaming-driver)

### Option 1: AMIs with the NVIDIA Drivers Installed<a name="preinstalled-nvidia-driver"></a>

AWS and NVIDIA offer different Amazon Machine Images \(AMI\) that come with the NVIDIA drivers installed\.
+ [Marketplace offerings with the Tesla driver](http://aws.amazon.com/marketplace/search/results?page=1&filters=VendorId&VendorId=e6a5002c-6dd0-4d1e-8196-0a1d1857229b%2Cc568fe05-e33b-411c-b0ab-047218431da9&searchTerms=tesla+driver)
+ [Marketplace offerings with the GRID driver](http://aws.amazon.com/marketplace/search/results?&searchTerms=NVIDIA+quadro)
+ [Marketplace offerings with the Gaming driver](http://aws.amazon.com/marketplace/search/results?searchTerms=NVIDIA+gaming)

### Option 2: Public NVIDIA Tesla Drivers<a name="public-nvidia-driver"></a>

**To download the NVIDIA driver**  
Log on to your Windows instance and download the 64\-bit NVIDIA driver appropriate for the instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\.


| Instance | Product Series | Product | 
| --- | --- | --- | 
| G2 | K\-Series | K520 | 
| G3 | M\-Class | M60 | 
| G4 † | T\-Series | T4 | 
| P2 | K\-Series | K80 | 
| P3 | V\-Series | V100 | 

† G4 instances require driver version 426\.00 or later\.

**To install the NVIDIA driver on Windows**

1. Open the folder where you downloaded the driver and launch the installation file\. Follow the instructions to install the driver and reboot your instance as required\.

1. Disable the built\-in display adapter using Device Manager\. Install these Windows features: **Media Foundation** and **Quality Windows Audio Video Experience**\.

1. Check Device Manager to verify that the GPU is working correctly\.

1. To achieve the best performance from your GPU, complete the optimization steps in [Optimizing GPU Settings](optimize_gpu.md)\.

### Option 3: GRID Drivers \(G3 and G4 Instances\)<a name="nvidia-GRID-driver"></a>

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA Tesla T4 or NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**To install the NVIDIA GRID driver on your Windows instance**

1. Connect to your Windows instance and open a PowerShell window\.

1. Download the drivers and the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX) to your desktop using the following PowerShell commands\.

   For G3 instances:

   ```
   $KeyPrefix = "latest"
   ```

   For G4 instances:

   ```
   $KeyPrefix = "g4/latest"
   ```

   For G3 and G4 instances:

   ```
   $Bucket = "ec2-windows-nvidia-drivers"
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

1. \(Optional\) Disable the licensing page in the control panel to prevent users from accidentally changing the product type \(NVIDIA GRID Virtual Workstation is enabled by default\)\. For more information, see the [GRID Licensing User Guide](http://docs.nvidia.com/grid/4.6/grid-licensing-user-guide/index.html)\.

   1. Run regedit\.exe to open the registry editor\.

   1. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\NVIDIA Corporation\Global\GridLicensing`\.

   1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

   1. For **Name**, enter **NvCplDisableManageLicensePage** and type `Enter`\.

   1. Open the context \(right\-click\) menu on **NvCplDisableManageLicensePage** and choose **Modify**\.

   1. For **Value data**, type `1` and choose **OK**\.

1. \(Optional\) To enable NVIDIA GRID Virtual Applications, complete the GRID activation steps in [Activate NVIDIA GRID Virtual Applications](activate_grid.md)\.

1. \(Optional\) To help take advantage of the four displays of up to 4K resolution, set up the high\-performance display protocol, [NICE DCV](https://docs.aws.amazon.com/dcv/)\.

1. To achieve the best performance from your GPU, complete the optimization steps in [Optimizing GPU Settings](optimize_gpu.md)\.

### Option 4: NVIDIA Gaming Drivers \(G4 Instances\)<a name="nvidia-gaming-driver"></a>

These drivers are available to AWS customers only\. By downloading them, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA Tesla T4 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**To install the NVIDIA gaming driver on your Windows instance**

1. Connect to your Windows instance and open a PowerShell window\.

1. Download and install the gaming driver using the following PowerShell commands\.

   ```
   $Bucket = "nvidia-gaming"
   $KeyPrefix = "windows/latest"
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

   Multiple versions of the NVIDIA GRID driver are stored in this S3 bucket\. You can download all of the available versions in the bucket by removing the `-KeyPrefix $KeyPrefix` option\.

1. Use the following steps to create the required configuration\.

   1. Open the registry editor\.

   1. In the left pane, navigate to **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\NVIDIA Corporation\\Global**\.

   1. In the right pane, right\-click and choose **New**, **DWORD \(32\-bit\) value**\.

   1. Set the name to **vGamingMarketplace** and the value to 2\.

1. Download the [certification file](https://s3.amazonaws.com/nvidia-gaming/GridSwCert-Windows.cert), rename the file `GridSwCert.txt`, and move the file to the Public Documents folder on your system drive\. Typically, the folder path is C:\\Users\\Public\\Public Documents \(Windows Explorer\) or C:\\Users\\Public\\Documents \(Command Prompt window\)\.

1. Reboot your instance\.

1. Verify the NVIDIA Gaming license using the following command\.

   ```
   "C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe" -q
   ```

   The output should be similar to the following\.

   ```
   GRID Licensed Product
       Product Name              : GRID vGaming
       License Status            : Licensed
   ```