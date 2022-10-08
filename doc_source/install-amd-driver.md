# Install AMD drivers on Windows instances<a name="install-amd-driver"></a>

An instance with an attached AMD GPU, such as a G4ad instance, must have the appropriate AMD driver installed\. Depending on your requirements, you can either use an AMI with the driver preinstalled or download a driver from Amazon S3\.

To install NVIDIA drivers on an instance with an attached NVIDIA GPU, such as a G4dn instance, see [Install NVIDIA drivers](install-nvidia-driver.md) instead\.  To install AMD drivers on a Linux instance, see [Install AMD drivers on a Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-amd-driver.html)\.

**Contents**
+ [AMD Radeon Pro Software for Enterprise Driver](#amd-radeon-pro-software-for-enterprise-driver)
+ [AMIs with the AMD driver installed](#preinstalled-amd-driver)
+ [AMD driver download](#download-amd-driver)

## AMD Radeon Pro Software for Enterprise Driver<a name="amd-radeon-pro-software-for-enterprise-driver"></a>

The AMD Radeon Pro Software for Enterprise Driver is built to deliver support for professional\-grade graphics use cases\. Using the driver, you can configure your instances with two 4K displays per GPU\.

**Supported APIs**
+ OpenGL, OpenCL
+ Vulkan
+ DirectX 9 and later
+ AMD Advanced Media Framework
+ Microsoft Hardware Media Foundation Transform

## AMIs with the AMD driver installed<a name="preinstalled-amd-driver"></a>

AWS offers different Amazon Machine Images \(AMI\) that come with the AMD drivers installed\. Open [Marketplace offerings with the AMD driver](http://aws.amazon.com/marketplace/search/results?page=1&filters=VendorId&VendorId=e6a5002c-6dd0-4d1e-8196-0a1d1857229b&searchTerms=AMD+Radeon+Pro+Driver)\.

## AMD driver download<a name="download-amd-driver"></a>

If you aren't using an AMI with the AMD driver installed, you can download the AMD driver and install it on your instance\. The AMD driver is only supported for Windows Server 2016 and Windows Server 2019 operating systems\.

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the AMD Radeon Pro V520 hardware\. Upon installation of the software, you are bound by the terms of the [AMD Software End User License Agreement](https://www.amd.com/en/support/eula)\.

**Prerequisites**
+ Configure default credentials for the AWS Tools for Windows PowerShell on your Windows instance\. For more information, see [Getting Started with the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-started.html) in the *AWS Tools for Windows PowerShell User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\. For more information, see [AWS managed policy: AmazonS3ReadOnlyAccess](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-iam-awsmanpol.html#security-iam-awsmanpol-amazons3readonlyaccess) in the *Amazon Simple Storage Service User Guide*\.

**To install the AMD driver on your Windows instance**

1. Connect to your Windows instance and open a PowerShell window\.

1. Download the drivers from Amazon S3 to your desktop using the following PowerShell commands\.

   ```
   $Bucket = "ec2-amd-windows-drivers"
   $KeyPrefix = "latest"
   $LocalPath = "$home\Desktop\AMD"
   $Objects = Get-S3Object -BucketName $Bucket -KeyPrefix $KeyPrefix -Region us-east-1
   foreach ($Object in $Objects) {
       $LocalFileName = $Object.Key
       if ($LocalFileName -ne '' -and $Object.Size -ne 0) {
           $LocalFilePath = Join-Path $LocalPath $LocalFileName
           Copy-S3Object -BucketName $Bucket -Key $Object.Key -LocalFile $LocalFilePath -Region us-east-1
       }
   }
   ```

1. Unzip the downloaded driver file and run the installer using the following PowerShell commands\.

   ```
   Expand-Archive $LocalFilePath -DestinationPath "$home\Desktop\AMD\latest" -Verbose
   $Driverdir = Get-ChildItem "$home\Desktop\" -Directory -Filter "*WHQL*"
   Write-Host $Driverdir
   ```

   Now, check the name of the new directory\. The directory name can be retrieved using the `Get-ChildItem` PowerShell command\.

   ```
   Get-ChildItem "$home\Desktop\AMD\latest\"
   ```

   The output should be similar to the following:

   ```
   Directory: C:\Users\Administrator\Desktop\AMD\latest
   						
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----       10/13/2021  12:52 AM                210414a-365562C-Retail_End_User.2
   ```

   Note the correct file path and install the drivers:

   ```
   pnputil /add-driver $home\Desktop\AMD\latest\210414a-365562C-Retail_End_User.2\packages\Drivers\Display\WT6A_INF/*.inf /install
   ```

1. Follow the instructions to install the driver and reboot your instance as required\.

1. To verify that the GPU is working properly, check Device Manager\. You should see "AMD Radeon Pro V520 MxGPU" listed as a display adapter\.

1. To help take advantage of the four displays of up to 4K resolution, set up the high\-performance display protocol, [NICE DCV](https://docs.aws.amazon.com/dcv/)\.