# Mapping disks to volumes on your Windows instance<a name="ec2-windows-volumes"></a>

Your Windows instance comes with an EBS volume that serves as the root volume\. If your Windows instance uses AWS PV or Citrix PV drivers, you can optionally add up to 25 volumes, making a total of 26 volumes\. For more information, see [Instance volume limits](volume_limits.md)

Depending on the instance type of your instance, you'll have from 0 to 24 possible instance store volumes available to the instance\. To use any of the instance store volumes that are available to your instance, you must specify them when you create your AMI or launch your instance\. You can also add EBS volumes when you create your AMI or launch your instance, or attach them while your instance is running\. For more information, see [Making an Amazon EBS volume available for use on Windows](ebs-using-volumes.md)\.

When you add a volume to your instance, you specify the device name that Amazon EC2 uses\. For more information, see [Device naming on Windows instances](device_naming.md)\. AWS Windows Amazon Machine Images \(AMIs\) contain a set of drivers that are used by Amazon EC2 to map instance store and EBS volumes to Windows disks and drive letters\. If you launch an instance from a Windows AMI that uses AWS PV or Citrix PV drivers, you can use the relationships described on this page to map your Windows disks to your instance store and EBS volumes\. If your Windows AMI uses Red Hat PV drivers, you can update your instance to use the Citrix drivers\. For more information, see [Upgrading PV Drivers on Your Windows Instances](Upgrading_PV_drivers.md)\.

**Topics**
+ [Listing the disks using Windows Disk Management](#windows-disks)
+ [Listing the disks using Windows PowerShell \(Windows Server 2012 and later\)](#windows-list-disks)
+ [Disk device to device name mapping](#windows-volume-mapping)

## Listing the disks using Windows Disk Management<a name="windows-disks"></a>

You can find the disks on your Windows instance using Windows Disk Management\.

**To find the disks on your Windows instance**

1. Log in to your Windows instance using Remote Desktop\. For more information, see, [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Start the Disk Management utility\.

   On Windows Server 2012 and later, on the taskbar, right\-click the Windows logo, and then choose **Disk Management**\. On Windows Server 2008, choose **Start**, **Administrative Tools**, **Computer Management**, **Disk Management**\.

1. Review the disks\. The root volume is an EBS volume mounted as `C:\`\. If there are no other disks shown, then you didn't specify additional volumes when you created the AMI or launched the instance\.

   The following is an example that shows the disks that are available if you launch an `m3.medium` instance with an instance store volume \(Disk 2\) and an additional EBS volume \(Disk 1\)\.  
![\[Disk Management with a root volume, one instance store volume, and one EBS volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/disk_management.png)

1. Right\-click the gray pane labeled Disk 1, and then select **Properties**\. Note the value of **Location** and look it up in the tables in [Disk device to device name mapping](#windows-volume-mapping)\. For example, the following disk has the location Bus Number 0, Target Id 9, LUN 0\. According to the table for EBS volumes, the device name for this location is `xvdj`\.  
![\[The location of an EBS volume.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/disk_1_location.png)

1. To map the device name of an EBS volume to its volume ID, open the Amazon EC2 console on your computer\. In the navigation pane, select **Instances**, and then select your instance\. Under **Block devices** on the **Description** tab, click the device name, and locate **EBS ID**\. For this example, the volume ID is `vol-0a07f3e37b14708b9`\.  
![\[Display the block device mapping in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/block_devices.png)

   Note that the Amazon EC2 console shows only the EBS volumes\.

   Another way to access volume IDs from the console is to select **Volumes** under **Elastic Block Store** in the navigation pane\. Volumes will be listed by instance name and volume size\. Review or select the **Attachment Information** to verify the instance to which the volume is attached\. 

## Listing the disks using Windows PowerShell \(Windows Server 2012 and later\)<a name="windows-list-disks"></a>

The following PowerShell script lists each disk and its corresponding device name and volume\. 

**Note**  
The following script will not return information for dynamic disks\.

```
# List the Windows disks

function Get-EC2InstanceMetadata {
  param([string]$Path)
  (Invoke-WebRequest -Uri "http://169.254.169.254/latest/$Path").Content 
}

function Convert-SCSITargetIdToDeviceName {
  param([int]$SCSITargetId)
  If ($SCSITargetId -eq 0) {
    return "sda1"
  }
  $deviceName = "xvd"
  If ($SCSITargetId -gt 25) {
    $deviceName += [char](0x60 + [int]($SCSITargetId / 26))
  }
  $deviceName += [char](0x61 + $SCSITargetId % 26)
  return $deviceName
}

Try {
  $InstanceId = Get-EC2InstanceMetadata "meta-data/instance-id"
  $AZ = Get-EC2InstanceMetadata "meta-data/placement/availability-zone"
  $Region = $AZ.Remove($AZ.Length - 1)
  $BlockDeviceMappings = (Get-EC2Instance -Region $Region -Instance $InstanceId).Instances.BlockDeviceMappings
  $VirtualDeviceMap = @{}
  (Get-EC2InstanceMetadata "meta-data/block-device-mapping").Split("`n") | ForEach-Object {
    $VirtualDevice = $_
    $BlockDeviceName = Get-EC2InstanceMetadata "meta-data/block-device-mapping/$VirtualDevice"
    $VirtualDeviceMap[$BlockDeviceName] = $VirtualDevice
    $VirtualDeviceMap[$VirtualDevice] = $BlockDeviceName
  }
}
Catch {
  Write-Host "Could not access the AWS API, therefore, VolumeId is not available. 
Verify that you provided your access keys." -ForegroundColor Yellow
}

Get-disk | ForEach-Object {
  $DriveLetter = $null
  $VolumeName = $null

  $DiskDrive = $_
  $Disk = $_.Number
  $Partitions = $_.NumberOfPartitions
  $EbsVolumeID = $_.SerialNumber -replace "_[^ ]*$" -replace "vol", "vol-"
  Get-Partition -DiskId $_.Path | ForEach-Object {
    if ($_.DriveLetter -ne "") {
      $DriveLetter = $_.DriveLetter
      $VolumeName = (Get-PSDrive | Where-Object {$_.Name -eq $DriveLetter}).Description
    }
  } 

  If ($DiskDrive.path -like "*PROD_PVDISK*") {
    $BlockDeviceName = Convert-SCSITargetIdToDeviceName((Get-WmiObject -Class Win32_Diskdrive | Where-Object {$_.DeviceID -eq ("\\.\PHYSICALDRIVE" + $DiskDrive.Number) }).SCSITargetId)
    $BlockDeviceName = "/dev/" + $BlockDeviceName
    $BlockDevice = $BlockDeviceMappings | Where-Object { $BlockDeviceName -like "*"+$_.DeviceName+"*" }
    $EbsVolumeID = $BlockDevice.Ebs.VolumeId 
    $VirtualDevice = If ($VirtualDeviceMap.ContainsKey($BlockDeviceName)) { $VirtualDeviceMap[$BlockDeviceName] } Else { $null }
  }
  ElseIf ($DiskDrive.path -like "*PROD_AMAZON_EC2_NVME*") {
    $BlockDeviceName = Get-EC2InstanceMetadata "meta-data/block-device-mapping/ephemeral$((Get-WmiObject -Class Win32_Diskdrive | Where-Object {$_.DeviceID -eq ("\\.\PHYSICALDRIVE"+$DiskDrive.Number) }).SCSIPort - 2)"
    $BlockDevice = $null
    $VirtualDevice = If ($VirtualDeviceMap.ContainsKey($BlockDeviceName)) { $VirtualDeviceMap[$BlockDeviceName] } Else { $null }
  }
  ElseIf ($DiskDrive.path -like "*PROD_AMAZON*") {
    $BlockDevice = ""
    $BlockDeviceName = ($BlockDeviceMappings | Where-Object {$_.ebs.VolumeId -eq $EbsVolumeID}).DeviceName
    $VirtualDevice = $null
  }
  Else {
    $BlockDeviceName = $null
    $BlockDevice = $null
    $VirtualDevice = $null
  }
  New-Object PSObject -Property @{
    Disk          = $Disk;
    Partitions    = $Partitions;
    DriveLetter   = If ($DriveLetter -eq $null) { "N/A" } Else { $DriveLetter };
    EbsVolumeId   = If ($EbsVolumeID -eq $null) { "N/A" } Else { $EbsVolumeID };
    Device        = If ($BlockDeviceName -eq $null) { "N/A" } Else { $BlockDeviceName };
    VirtualDevice = If ($VirtualDevice -eq $null) { "N/A" } Else { $VirtualDevice };
    VolumeName    = If ($VolumeName -eq $null) { "N/A" } Else { $VolumeName };
  }
} | Sort-Object Disk | Format-Table -AutoSize -Property Disk, Partitions, DriveLetter, EbsVolumeId, Device, VirtualDevice, VolumeName
```

**Note**  
This script requires a profile configured in the AWS Tools for PS, or an IAM role attached to the instance\.

Before you run this script, be sure to run the following command to enable PowerShell script execution\.

```
Set-ExecutionPolicy RemoteSigned
```

Copy the script and save it as a `.ps1` file on the Windows instance\. If you run the script without setting your access keys, you'll see output similar to the following\.

```
Disk Partitions DriveLetter EbsVolumeId           Device    VirtualDevice VolumeName
---- ---------- ----------- -----------           ------    ------------- ----------
   0          0 N/A         N/A                   xvdca     ephemeral0    N/A
   1          0 N/A         N/A                   xvdcb     ephemeral1    N/A
   2          1 C:          vol-0064aexamplec838a /dev/sda1 root          Windows
   3          0 N/A         vol-02256example8a4a3 xvdf      ebs2          N/A
```

If you specified an IAM role with a policy that allows access to Amazon EC2 when you launched the instance, or if you set up your credentials on the Windows instance as described in [Using AWS Credentials](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) in the *AWS Tools for Windows PowerShell User Guide*, you'll get the volume ID for the EBS volumes in the `VolumeId` column instead of `NA`\.

## Disk device to device name mapping<a name="windows-volume-mapping"></a>

The block device driver for the instance assigns the actual volume names when mounting volumes\.

**Topics**
+ [Instance store volumes](#instance-store-volume-map)
+ [EBS volumes](#ebs-volume-map)
+ [NVMe EBS volumes](#ebs-nvme-volume-map)

### Instance store volumes<a name="instance-store-volume-map"></a>

The following table describes how the Citrix PV and AWS PV drivers map non\-NVMe instance store volumes to Windows volumes\. The number of available instance store volumes is determined by the instance type\. For more information, see [Instance store volumes](InstanceStorage.md#instance-store-volumes)\.


| Location | Device name | 
| --- | --- | 
| Bus Number 0, Target ID 78, LUN 0 | xvdca | 
| Bus Number 0, Target ID 79, LUN 0 | xvdcb | 
| Bus Number 0, Target ID 80, LUN 0 | xvdcc | 
| Bus Number 0, Target ID 81, LUN 0 | xvdcd | 
| Bus Number 0, Target ID 82, LUN 0 | xvdce | 
| Bus Number 0, Target ID 83, LUN 0 | xvdcf | 
| Bus Number 0, Target ID 84, LUN 0 | xvdcg | 
| Bus Number 0, Target ID 85, LUN 0 | xvdch | 
| Bus Number 0, Target ID 86, LUN 0 | xvdci | 
| Bus Number 0, Target ID 87, LUN 0 | xvdcj | 
| Bus Number 0, Target ID 88, LUN 0 | xvdck | 
| Bus Number 0, Target ID 89, LUN 0 | xvdcl | 

### EBS volumes<a name="ebs-volume-map"></a>

The following table describes how the Citrix PV and AWS PV drivers map non\-NVME EBS volumes to Windows volumes\.


| Location | Device name | 
| --- | --- | 
| Bus Number 0, Target ID 0, LUN 0 | /dev/sda1 | 
| Bus Number 0, Target ID 1, LUN 0 | xvdb | 
| Bus Number 0, Target ID 2, LUN 0 | xvdc | 
| Bus Number 0, Target ID 3, LUN 0 | xvdd | 
| Bus Number 0, Target ID 4, LUN 0 | xvde | 
| Bus Number 0, Target ID 5, LUN 0 | xvdf | 
| Bus Number 0, Target ID 6, LUN 0 | xvdg | 
| Bus Number 0, Target ID 7, LUN 0 | xvdh | 
| Bus Number 0, Target ID 8, LUN 0 | xvdi | 
| Bus Number 0, Target ID 9, LUN 0 | xvdj | 
| Bus Number 0, Target ID 10, LUN 0 | xvdk | 
| Bus Number 0, Target ID 11, LUN 0 | xvdl | 
| Bus Number 0, Target ID 12, LUN 0 | xvdm | 
| Bus Number 0, Target ID 13, LUN 0 | xvdn | 
| Bus Number 0, Target ID 14, LUN 0 | xvdo | 
| Bus Number 0, Target ID 15, LUN 0 | xvdp | 
| Bus Number 0, Target ID 16, LUN 0 | xvdq | 
| Bus Number 0, Target ID 17, LUN 0 | xvdr | 
| Bus Number 0, Target ID 18, LUN 0 | xvds | 
| Bus Number 0, Target ID 19, LUN 0 | xvdt | 
| Bus Number 0, Target ID 20, LUN 0 | xvdu | 
| Bus Number 0, Target ID 21, LUN 0 | xvdv | 
| Bus Number 0, Target ID 22, LUN 0 | xvdw | 
| Bus Number 0, Target ID 23, LUN 0 | xvdx | 
| Bus Number 0, Target ID 24, LUN 0 | xvdy | 
| Bus Number 0, Target ID 25, LUN 0 | xvdz | 

### NVMe EBS volumes<a name="ebs-nvme-volume-map"></a>

With instances built on the [Nitro System](instance-types.md#ec2-nitro-instances), EBS volumes are exposed as NVMe devices\. You can use the [Get\-Disk](https://docs.microsoft.com/en-us/powershell/module/storage/get-disk) command to map Windows disk numbers to EBS volume IDs\. For more information, see [Identifying the EBS device](nvme-ebs-volumes.md#identify-nvme-ebs-device)\.