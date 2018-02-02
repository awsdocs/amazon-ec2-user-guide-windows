# Resetting the Windows Administrator Password Using EC2Config<a name="ResettingAdminPassword_EC2Config"></a>

If you have lost your Windows administrator password and are using a Windows AMI before Windows Server 2016, you can use the EC2Config service to generate a new password\.

If you are using a Windows Server 2016 AMI, see [Resetting the Windows Administrator Password Using EC2Launch](ResettingAdminPassword_EC2Launch.md)\.

**Note**  
If you have disabled the local Administrator account on the instance and your instance is configured for SSM, you can also re\-enable and reset your local Administrator password by using EC2Rescue and Run Command\. For more information, see [Using EC2Rescue for Windows Server with Systems Manager Run Command](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2rw-ssm.html)\.

**Note**  
There is an SSM Automation document that automatically applies the manual steps necessary to reset the local Administrator password\. For more information, see [Reset the Local Administrator Password on Amazon EC2 Windows Instances](http://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.

## Before You Begin<a name="byb-password"></a>

Before you attempt to reset the administrator password, use the following procedure to verify that the EC2Config service is installed and running\. You use the EC2Config service to reset the administrator password later in this section\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then choose the instance that needs a password reset\. \(This instance is referred to as the *original* instance in this procedure\.\)

1. Choose **Actions**, **Instance Settings**, **Get System Log**\.

1. Locate the EC2 Agent entry, for example, **EC2 Agent: Ec2Config service v3\.18\.1118**\. If you see this entry, the EC2Config service is running\.

   If the system log output is empty, or if the EC2Config service is not running, troubleshoot the instance using the Instance Console Screenshot service\. For more information, see [Troubleshoot an Unreachable Instance](screenshot-service.md)\.

## Resetting an Administrator Password<a name="resetting-password-win"></a>

To reset an administrator password for an EC2 instance, modify a configuration file on the instance boot volume\. However, you can't modify this file if the volume is attached to the instance as a root volume\. You must detach the volume and attach it to a temporary instance\. After you modify the configuration file on the temporary instance, you reattach it to your original instance as the root volume\.

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. Therefore, if you have any data on instance store volumes that you want to keep, be sure to back it up to persistent storage\.

**To reset the administrator password**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Instance State**, **Stop**\. When prompted for confirmation, choose **Yes, Stop**\. Wait until the instance state is `stopped` before going to the next step\.

1. \(Optional\) If you have the private key for the key pair you specified when you launched this instance, continue with the next step\. Otherwise, use the following steps to replace the instance with a new instance that you launch with a new key pair\.

   1. Create a new key pair using the Amazon EC2 console\. To give your new key pair the same name as the one for which you lost private key, you must delete the existing key pair\.

   1. Select the instance to replace\. Note the instance type, VPC, subnet, security group, and IAM role of the instance\.

   1. Choose **Actions**, **Image**, **Create Image**\. Type a name and a description for the image and choose **Create Image**\. Choose **View pending image**\.

   1. When the status of the new image is **available**, select the image and choose **Launch**\.

   1. Complete the wizard, selecting the same instance type, VPC, subnet, security group, and IAM role as the instance to replace\. Choose **Launch**\.

   1. When prompted, choose the key pair you created for the new instance, select the acknowledgement check box, and choose **Launch Instances**\.

   1. If the original instance has an associated Elastic IP address, transfer it to the new instance\. If the original instance has EBS volumes in addition to the root volume, transfer them to the new instance\.

   1. Terminate the stopped instance, as it is no longer needed\. For the remainder of this procedure, all references to the original instance apply to this instance that you just created\.

1. Create the temporary instance to which to attach the volume in order to modify the configuration file\. 

   1. Choose **Launch Instance** and then select an AMI\.
**Important**  
You must select an AMI for a different version of Windows to avoid disk signature collisions\. For example, if the original instance runs Windows Server 2012 R2, launch the temporary instance using the base AMI for Windows Server 2008 R2\.

   1. Leave the default instance type that the wizard selects for you and choose **Next: Configure Instance Details**\.

   1. On the **Configure Instance Details** page, for **Subnet**, choose the same Availability Zone as the original instance, and then choose **Review and Launch**\.
**Important**  
The temporary instance must be in the same Availability Zone as the original instance\. If the temporary instance is in a different Availability Zone, you won't be able to attach the original instance's root volume to it\.

   1. On the **Review Instance Launch** page, choose **Launch**\.

   1. When prompted, create a new key pair, download it to a safe location on your computer, and then choose **Launch Instances**\.

1. Detach the root volume from the original instance as follows:

   1. On the **Description** pane of the original instance, note the ID of the EBS volume listed as the **Root device**\.

   1. In the navigation pane, choose **Volumes**\.

   1. In the list of volumes, select the volume, and then choose **Actions**, **Detach Volume**\. After the volume's status changes to **available**, continue with the next step\.

1. Attach the volume to the temporary instance as a secondary volume as follows:

   1. Choose **Actions**, **Attach Volume**\.

   1. In the **Attach Volume** dialog box, start typing the name or ID of your temporary instance for **Instances**, and then select the instance from the list\.

   1. For **Device**, type **xvdf** \(if it isn't already there\), and then choose **Attach**\.

   1. Connect to the temporary instance, open the **Disk Management** utility, and bring the drive online using these instructions: [Making the Volume Available on Windows](ebs-using-volumes.md#using-ebs-volume-windows)\.

1. On the secondary volume, modify the configuration file as follows:

   1. From the temporary instance, navigate to the secondary volume, and open `\Program Files\Amazon\Ec2ConfigService\Settings\config.xml` using a text editor, such as Notepad\.

   1. At the top of the file, find the plugin with the name `Ec2SetPassword`, as shown here\. Change the state from `Disabled` to `Enabled` and save the file\.  
![\[The area of the Config.xml file to change\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/pwreset_config.png)

1. Detach the secondary volume from the temporary instance as follows:

   1. Using the **Disk Management** utility, bring the volume offline\.

   1. In the navigation pane, choose **Volumes**\.

   1. Select the volume and choose **Actions**, **Detach Volume**\. After the volume's status changes to **available**, continue with the next step\.

1. Reattach the volume to the original instance as its root volume as follows:

   1. Select the volume, and choose **Actions**, **Attach Volume**\.

   1. In the **Attach Volume** dialog box, start typing the name or ID of the original instance for **Instances** and then select the instance\.

   1. For **Device**, type `/dev/sda1`\.

   1. Choose **Attach**\. Wait until the state of the volume is `in-use` before continuing to the next step\. 

1. In the navigation pane, choose **Instances**\. Select the original instance and choose **Actions**, **Instance State**, **Start**\. When prompted for confirmation, choose **Yes, Start**\. Wait until the state of your instance is `running` before continuing to the next step\.

1. Retrieve your new Windows administrator password using the private key for the new key pair and connect to the instance\. For more information, see [Connecting to Your Windows Instance](connecting_to_windows_instance.md)\.
**Important**  
The instance gets a new public IP address after you stop and start it\. Be sure to connect to the instance using its current public DNS name\. For more information, see [Instance Lifecycle](ec2-instance-lifecycle.md)\.

1. \(Optional\) If you have no further use for the temporary instance, you can terminate it\. Select the temporary instance, and then choose **Actions**, **Instance State**, **Terminate**\.