# Adding Windows components Using installation media<a name="windows-optional-components"></a>

Windows Server operating systems include many optional components\. Including all optional components in each Amazon EC2 Windows Server AMI is not practical\. Instead, we provide you with installation media EBS snapshots that have the necessary files to configure or install components on your Windows instance\.

To access and install the optional components, you must find the correct EBS snapshot for your version of Windows Server, create a volume from the snapshot, and attach the volume to your instance\.

## Before you begin<a name="adding-windows-components-prereq"></a>

Use the AWS Management Console or a command line tool to get the instance ID and Availability Zone of your instance\. You must create your EBS volume in the same Availability Zone as your instance\.

## Adding Windows components using the console<a name="adding-windows-components-console"></a>

Use the following procedure to use the AWS Management Console to add Windows components to your instance\.

**To add Windows components to your instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. From the **Filter** bar, choose **Public Snapshots**\.

1. Add the **Owner** filter and choose **Amazon images**\.

1. Add the **Description** filter and type **Windows**\.

1. Press Enter

1. Select the snapshot that matches your system architecture and language preference\. For example, select **Windows 2019 English Installation Media** if your instance is running Windows Server 2019\.

1. Choose **Actions**, **Create Volume**\.

1. In the **Create Volume** dialog box, select the Availability Zone that matches your Windows instance, and then choose **Create**\.

1. In the **Volume Successfully Created** message, choose the volume that you just created\.

1. Choose **Actions**, **Attach Volume**\.

1. In the **Attach Volume** dialog box, type the instance ID and the name of the device for the attachment, and choose **Attach**\. If you need help with the device name, see [Device Naming](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/device_naming.html)\. 

1. Connect to your instance and make the volume available\. For more information, see [Making an Amazon EBS volume available for use on Windows](ebs-using-volumes.md)\.
**Important**  
Do not initialize the volume\.

1. Open **Control Panel**, **Programs and Features**\. Choose **Turn Windows features on or off**\. If you are prompted for installation media, specify the EBS volume with the installation media\.

## Adding Windows components using the Tools for Windows PowerShell<a name="adding-windows-components-powershell"></a>

Use the following procedure to use the Tools for Windows PowerShell to add Windows components to your instance\.

**To add Windows components to your instance using the Tools for Windows PowerShell**

1. Use the [Get\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Snapshot.html) cmdlet with the `Owner` and `description` filters to get a list of the available installation media snapshots\.

   ```
   PS C:\> Get-EC2Snapshot -Owner amazon -Filter @{ Name="description"; Values="Windows*" }
   ```

1. In the output, note the ID of the snapshot that matches your system architecture and language preference\. For example:

   ```
   ...
   DataEncryptionKeyId :
   Description         : Windows 2019 English Installation Media
   Encrypted           : False
   KmsKeyId            :
   OwnerAlias          : amazon
   OwnerId             : 123456789012
   Progress            : 100%
   SnapshotId          : snap-22da283e
   StartTime           : 10/25/2019 8:00:47 PM
   State               : completed
   StateMessage        :
   Tags                : {}
   VolumeId            : vol-be5eafcb
   VolumeSize          : 6
   ...
   ```

1. Use the [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) cmdlet to create a volume from the snapshot\. Specify the same Availability Zone as your instance\.

   ```
   PS C:\> New-EC2Volume -AvailabilityZone us-east-1a -VolumeType gp2 -SnapshotId snap-22da283e
   ```

1. In the output, note the volume ID\.

   ```
   Attachments      : {}
   AvailabilityZone : us-east-1a
   CreateTime       : 4/18/2017 10:50:25 AM
   Encrypted        : False
   Iops             : 100
   KmsKeyId         :
   Size             : 6
   SnapshotId       : snap-22da283e
   State            : creating
   Tags             : {}
   VolumeId         : vol-06aa9e1fbf8b82ed1
   VolumeType       : gp2
   ```

1. Use the [Add\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2Volume.html) cmdlet to attach the volume to your instance\.

   ```
   PS C:\> Add-EC2Volume -InstanceId i-087711ddaf98f9489 -VolumeId vol-06aa9e1fbf8b82ed1 -Device xvdh
   ```

1. Connect to your instance and make the volume available\. For more information, see [Making an Amazon EBS volume available for use on Windows](ebs-using-volumes.md)\.
**Important**  
Do not initialize the volume\.

1. Open **Control Panel**, **Programs and Features**\. Choose **Turn Windows features on or off**\. If you are prompted for installation media, specify the EBS volume with the installation media\.

## Adding Windows components using the AWS CLI<a name="adding-windows-components-cli"></a>

Use the following procedure to use the AWS CLI to add Windows components to your instance\.

**To add Windows components to your instance using the AWS CLI**

1. Use the [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) command with the `owner-ids` parameter and `description` filter to get a list of the available installation media snapshots\.

   ```
   aws ec2 describe-snapshots --owner-ids amazon --filters Name=description,Values=Windows*
   ```

1. In the output, note the ID of the snapshot that matches your system architecture and language preference\. For example:

   ```
   {
       "Snapshots": [
       ...
           {
               "OwnerAlias": "amazon", 
               "Description": "Windows 2019 English Installation Media", 
               "Encrypted": false, 
               "VolumeId": "vol-be5eafcb", 
               "State": "completed", 
               "VolumeSize": 6, 
               "Progress": "100%", 
               "StartTime": "2019-10-25T20:00:47.000Z", 
               "SnapshotId": "snap-22da283e", 
               "OwnerId": "123456789012"
           }, 
       ...
      ]
   }
   ```

1. Use the [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command to create a volume from the snapshot\. Specify the same Availability Zone as your instance\.

   ```
   aws ec2 create-volume --snapshot-id snap-22da283e --volume-type gp2 --availability-zone us-east-1a
   ```

1. In the output, note the volume ID\.

   ```
   {
       "AvailabilityZone": "us-east-1a", 
       "Encrypted": false, 
       "VolumeType": "gp2", 
       "VolumeId": "vol-0c98b37f30bcbc290", 
       "State": "creating", 
       "Iops": 100, 
       "SnapshotId": "snap-22da283e", 
       "CreateTime": "2017-04-18T10:33:10.940Z", 
       "Size": 6
   }
   ```

1. Use the [attach\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-volume.html) command to attach the volume to your instance\.

   ```
   aws ec2 attach-volume --volume-id vol-0c98b37f30bcbc290 --instance-id i-01474ef662b89480 --device xvdg 
   ```

1. Connect to your instance and make the volume available\. For more information, see [Making an Amazon EBS volume available for use on Windows](ebs-using-volumes.md)\.
**Important**  
Do not initialize the volume\.

1. Open **Control Panel**, **Programs and Features**\. Choose **Turn Windows features on or off**\. If you are prompted for installation media, specify the EBS volume with the installation media\.