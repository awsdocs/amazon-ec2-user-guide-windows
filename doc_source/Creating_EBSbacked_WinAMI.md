# Creating a Custom Windows AMI<a name="Creating_EBSbacked_WinAMI"></a>

To create a Windows AMI, you launch an instance from an existing Windows AMI, customize the instance, and create a new AMI from the instance\.

To create a custom Linux AMI, use the procedure for the type of volume for the instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) or [Creating an Instance Store\-Backed Linux AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Overview of Creating an AMI<a name="process-creating-a-windows-ami-ebs"></a>

First, launch an instance from an AMI that's similar to the AMI that you'd like to create\. You can connect to your instance and customize it\. When the instance is set up the way you want it, ensure data integrity by stopping the instance before you create an AMI and then create the image\. We automatically register the AMI for you\.

During the AMI\-creation process, Amazon EC2 creates snapshots of your instance's root volume and any other EBS volumes attached to your instance\. If any volumes attached to the instance are encrypted, the new AMI only launches successfully on instance types that support Amazon EBS encryption\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\.

Depending on the size of the volumes, it can take several minutes for the AMI\-creation process to complete \(sometimes up to 24 hours\)\. You may find it more efficient to create snapshots of your volumes prior to creating your AMI\. This way, only small, incremental snapshots need to be created when the AMI is created, and the process completes more quickly \(the total time for snapshot creation remains the same\)\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

After the process completes, you have a new AMI and snapshot created from the root volume of the instance\. When you launch an instance using the new AMI, we create a new EBS volume for its root volume using the snapshot\. Both the AMI and the snapshot incur charges to your account until you delete them\. For more information, see [Deregistering Your Windows AMI](deregister-ami.md)\.

If you add instance\-store volumes or Amazon EBS volumes to your instance in addition to the root device volume, the block device mapping for the new AMI contains information for these volumes, and the block device mappings for instances that you launch from the new AMI automatically contain information for these volumes\. The instance\-store volumes specified in the block device mapping for the new instance are new and don't contain any data from the instance store volumes of the instance you used to create the AMI\. The data on EBS volumes persists\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

**Note**  
When you create a new instance from a custom AMI, you should initialize both its root volume and any additional EBS storage before putting it into production\. For more information, see [Initializing Amazon EBS Volumes](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-initialize.html)\.

## Creating a Windows AMI from a Running Instance<a name="how-to-create-windows-ebs-ami"></a>

You can create an AMI using the AWS Management Console or the command line\. The following diagram summarizes the process for creating an AMI from a running EC2 instance\. Start with an existing AMI, launch an instance, customize it, create a new AMI from it, and finally launch an instance of your new AMI\. The steps in the following diagram match the steps in the procedure below\. If you already have a running Windows instance, you can go directly to step 4\.

![\[Workflow for creating an AMI from an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/running-instance.png)

**To create an AMI from an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Images**, **AMIs**\.

1. Use the **Filter** options to scope the list of AMIs to the Windows AMIs that meet your needs\. For example, to view the Windows AMIs provided by AWS, choose **Public images** from the drop\-down list\. Choose the Search bar\. Choose **Owner** from the menu and choose **Amazon images** Choose **Source** from the menu and type one of the following, depending on the version of Windows Server that you need:

   + **amazon/Windows\_Server\-2016**

   + **amazon/Windows\_Server\-2012**

   + **amazon/Windows\_Server\-2008**

   Add any other filters that you need\. When you have chosen an AMI, select its checkbox\.

1. Choose **Launch**\. Accept the default values as you step through the wizard\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\. When the instance is ready, connect to it\. For more information, see [Connecting to Your Windows Instance](connecting_to_windows_instance.md)\.

1. You can perform any of the following actions on your instance to customize it for your needs:

   + Install software and applications

   + Copy data

   + Reduce start time by deleting temporary files, defragmenting your hard drive, and zeroing out free space

   + Attach additional EBS volumes

   + Create a new user account and add it to the Administrators group

     If you are sharing your AMI, these credentials can be supplied for RDP access without disclosing your default administrator password\.

   + \[Windows Server 2016\] Configure settings using EC2Launch\. To generate a random password at launch time, use the `adminPasswordType` setting\. For more information, see [Configuring EC2Launch](ec2launch.md#ec2launch-config)\.

   + \[Windows Server 2012 R2 and earlier\] Configure settings using EC2Config\. To generate a random password at launch time, enable the `Ec2SetPassword` plugin; otherwise, the current administrator password is used\. For more information, see [EC2Config Settings Files](UsingConfig_WinAMI.md#UsingConfigXML_WinAMI)\.

   + \[Windows Server 2008 R2\] If the instance uses RedHat drivers to access Xen virtualized hardware, upgrade to Citrix drivers before you create an AMI\. For more information, see [Upgrade Windows Server 2008 and 2008 R2 Instances \(Redhat to Citrix PV Upgrade\)](Upgrading_PV_drivers.md#win2008-citrix-upgrade)\.

1. In the navigation pane, choose **Instances** and select your instance\. Choose **Actions**, **Image**, and **Create Image**\.
**Tip**  
If this option is disabled, your instance isn't an Amazon EBS\-backed instance\.

1. Specify a unique name for the image and an optional description \(up to 255 characters\.\)

   By default, Amazon EC2 shuts down the instance, takes snapshots of any attached volumes, creates and registers the AMI, and then reboots the instance\. Choose **No reboot** if you don't want your instance to be shut down\.
**Warning**  
If you choose **No reboot**, we can't guarantee the file system integrity of the created image\.

   \(Optional\) Modify the root volume, Amazon EBS volumes, and instance store volumes as needed\. For example:

   + To change the size of the root volume, locate the **Root** volume in the **Type** column, and fill in the **Size** field\.

   + To suppress an Amazon EBS volume specified by the block device mapping of the AMI used to launch the instance, locate the EBS volume in the list and choose **Delete**\.

   + To add an Amazon EBS volume, choose **Add New Volume**, **Type**, and **EBS**, and fill in the fields\. When you then launch an instance from your new AMI, these additional volumes are automatically attached to the instance\. Empty volumes must be formatted and mounted\. Volumes based on a snapshot must be mounted\.

   + To suppress an instance store volume specified by the block device mapping of the AMI used to launch the instance, locate the volume in the list and choose **Delete**\.

   + To add an instance store volume, choose **Add New Volume**, **Type**, and **Instance Store**, and select a device name from the **Device** list\. When you launch an instance from your new AMI, these additional volumes are automatically initialized and mounted\. These volumes don't contain data from the instance store volumes of the running instance from which you based your AMI\.

   When you are finished, choose **Create Image**\.

1. While your AMI is being created, you can choose **AMIs** in the navigation pane to view its status\. Initially, this is `pending`\. After a few minutes, the status should change to `available`\.

   \(Optional\) Choose **Snapshots** in the navigation pane to view the snapshot that was created for the new AMI\. When you launch an instance from this AMI, we use this snapshot to create its root device volume\.

1. Launch an instance from your new AMI\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\. The new running instance contains all of the customizations you applied in previous steps, and any additional customization you add when launching the instance, such as user data \(scripts that run when the instance starts\)\.

### To create an AMI from an instance using the command line<a name="w3ab1c15c35b9b9"></a>

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [create\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\)

+ [New\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) \(AWS Tools for Windows PowerShell\)