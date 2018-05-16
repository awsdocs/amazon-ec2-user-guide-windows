# Installing the NVIDIA Driver on Windows<a name="install-nvidia-driver-windows"></a>

To install the NVIDIA driver on your Windows instance, log on to your instance as the administrator using Remote Desktop and download the appropriate driver\. The driver that you download and install depends on the instance type\.

## NVIDIA GRID Drivers for \(G3\)<a name="nvidia-drivers-grid"></a>

For G3 instances, you can download the NVIDIA GRID driver from Amazon S3 using the AWS Tools for Windows PowerShell\.

**Important**  
This download is available to AWS customers only\. By downloading, you agree that you will only use the downloaded software to develop AMIs for use with the NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**  
Associate an IAM role with your instance that has permissions to use the `s3:ListBucket` and `s3:GetObject` actions\. For more information, see [IAM Roles for Amazon EC2](iam-roles-for-amazon-ec2.md)\. Alternatively, configure the Tools for Windows PowerShell to use your AWS credentials\. For more information, see [Using AWS Credentials](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)\.

**To install the NVIDIA GRID driver \(G3 instances\)**

1. Open a PowerShell window\.

1. Download the drivers and the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX) to your desktop with the following PowerShell commands \(you can copy and paste the entire block of commands at one time\)\.

   ```
   $Bucket = "ec2-windows-nvidia-drivers"
   $LocalPath = "C:\Users\Administrator\Desktop\NVIDIA"
   $Objects = Get-S3Object -BucketName $Bucket -Region us-east-1
   foreach ($Object in $Objects) {
       $LocalFileName = $Object.Key
       if ($LocalFileName -ne '' -and $Object.Size -ne 0) {
           $LocalFilePath = Join-Path $LocalPath $LocalFileName
           Copy-S3Object -BucketName $Bucket -Key $Object.Key -LocalFile $LocalFilePath -Region us-east-1
       }
   }
   ```

1. Navigate to the desktop and double\-click the installation file to launch it \(choose the driver version that corresponds to your instance OS version\)\. Follow the instructions to install the driver and reboot your instance as required\. To verify that the GPU is working properly, check Device Manager\.

1. Complete the GRID activation steps in [Activate NVIDIA GRID Capabilities \(G3 Instances Only\)](activate_grid.md)\.

1. Complete the optimization steps in [Optimizing GPU Settings \(P2, P3, and G3 Instances\)](optimize_gpu.md) to achieve the best performance from your GPU\.

## Public NVIDIA Drivers \(G2, P2, P3\)<a name="nvidia-drivers-public"></a>

For instance types other than G3, or if you are not using NVIDIA GRID capabilities on a G3 instance, you can download a public NVIDIA driver\.

**To install a public NVIDIA driver**

1. Download the public NVIDIA driver that is appropriate for your instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/install-nvidia-driver-windows.html)

1. Open the folder where you downloaded the driver and launch the installation file\. Follow the instructions to install the driver and reboot your instance as required\.

1. Disable the built\-in display adapter using Device Manager\. Install Media Foundation and Quality Windows Audio Video Experience\.

1. To verify that the GPU is working correctly, check Device Manager\.

1. \[P2, P3, and G3\] Complete the optimization steps in [Optimizing GPU Settings \(P2, P3, and G3 Instances\)](optimize_gpu.md) to achieve the best performance from your GPU\.