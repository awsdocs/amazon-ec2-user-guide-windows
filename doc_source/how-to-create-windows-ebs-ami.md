# Create a Windows AMI from a running instance<a name="how-to-create-windows-ebs-ami"></a>

You can create an AMI using the AWS Management Console or the command line\. The following diagram summarizes the process for creating an AMI from a running EC2 instance\. Start with an existing AMI, launch an instance, customize it, create a new AMI from it, and finally launch an instance of your new AMI\. The steps in the following diagram match the steps in the procedure below\. If you already have a running Windows instance, you can go directly to step 4\.

![\[Workflow for creating an AMI from an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/running-instance.png)

**To create an AMI from an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Images**, **AMIs**\.

1. Use the **Filter** options to scope the list of AMIs to the Windows AMIs that meet your needs\. For example, to view the Windows AMIs provided by AWS, choose **Public images** from the drop\-down list\. Choose the Search bar\. Choose **Owner** from the menu and choose **Amazon images**\. Choose **Source** from the menu and type one of the following, depending on the version of Windows Server that you need:
   + **amazon/Windows\_Server\-2019**
   + **amazon/Windows\_Server\-2016**
   + **amazon/Windows\_Server\-2012**
   + **amazon/Windows\_Server\-2008**

   Add any other filters that you need\. When you have chosen an AMI, select its checkbox\.

1. Choose **Launch**\. Accept the default values as you step through the wizard\. For more information, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\. When the instance is ready, connect to it\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. You can perform any of the following actions on your instance to customize it for your needs:
   + Install software and applications
   + Copy data
   + Reduce start time by deleting temporary files, defragmenting your hard drive, and zeroing out free space
   + Attach additional EBS volumes
   + Create a new user account and add it to the Administrators group

     If you are sharing your AMI, these credentials can be supplied for RDP access without disclosing your default administrator password\.
   + \[Windows Server 2016 and later\] Configure settings using EC2Launch\. To generate a random password at launch time, use the `adminPasswordType` setting\. For more information, see [Configuring EC2Launch](ec2launch.md#ec2launch-config)\.
   + \[Windows Server 2012 R2 and earlier\] Configure settings using EC2Config\. To generate a random password at launch time, enable the `Ec2SetPassword` plugin; otherwise, the current administrator password is used\. For more information, see [EC2Config Settings Files](ec2config-service.md#UsingConfigXML_WinAMI)\.
   + \[Windows Server 2008 R2\] If the instance uses RedHat drivers to access Xen virtualized hardware, upgrade to Citrix drivers before you create an AMI\. For more information, see [Upgrade Windows Server 2008 and 2008 R2 Instances \(Redhat to Citrix PV Upgrade\)](Upgrading_PV_drivers.md#win2008-citrix-upgrade)\.

1. In the navigation pane, choose **Instances** and select your instance\. Choose **Actions**, **Image**, and **Create Image**\.
**Tip**  
If this option is disabled, your instance isn't an Amazon EBS\-backed instance\.

1. Specify a unique name for the image and an optional description \(up to 255 characters\)\.

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

1. Launch an instance from your new AMI\. For more information, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\. The new running instance contains all of the customizations you applied in previous steps, and any additional customization you add when launching the instance, such as user data \(scripts that run when the instance starts\)\.

## To create an AMI from an instance using the command line<a name="create-windows-ami-cli"></a>

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\)
+ [New\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) \(AWS Tools for Windows PowerShell\)