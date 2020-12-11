# Install AMD drivers on Windows instances<a name="install-amd-driver"></a>

An instance with an attached AMD GPU, such as a G4ad instance, must have the appropriate AMD driver installed\. Depending on your requirements, you can either use an AMI with the driver preinstalled or download a driver from Amazon S3\.

To install NVIDIA drivers on an instance with an attached NVIDIA GPU, such as a G4dn instance, see [Installing NVIDIA drivers on Windows instances](install-nvidia-driver.md) instead\.

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

If you aren't using an AMI with the AMD driver installed, you can download the AMD driver and install it on your instance\.

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the AMD Radeon Pro V520 hardware\. Upon installation of the software, you are bound by the terms of the [AMD Software End User License Agreement](https://www.amd.com/en/support/eula)\.

**Prerequisites**
+ Configure default credentials for the AWS Tools for Windows PowerShell on your Windows instance\. For more information, see [Getting Started with the AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-started.html) in the *AWS Tools for Windows PowerShell User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.

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

1. Unzip the driver file that you downloaded\.

1. Open Device Manager and then open **Video Controller**\.

1. Choose **Update driver**, **Browse my computer for driver software**, **Browse**\.

1. Open the AMD folder\.

1. Verify that **Include subfolders** is selected and choose **Next**\.

1. Follow the instructions to install the driver and reboot your instance as required\.

1. To verify that the GPU is working properly, check Device Manager\. You should see "AMD Radeon Pro V520 MxGPU" listed as a display adapter\.

1. To help take advantage of the four displays of up to 4K resolution, set up the high\-performance display protocol, [NICE DCV](https://docs.aws.amazon.com/dcv/)\.