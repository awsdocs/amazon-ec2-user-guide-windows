# Resetting the Windows Administrator Password Using EC2Launch<a name="ResettingAdminPassword_EC2Launch"></a>

If you have lost your Windows administrator password and are using a Windows Server 2016 AMI, you can use the EC2Rescue tool which uses the EC2Launch service to generate a new password\.

If you are using a Windows Server AMI earlier than Windows Server 2016, see [Resetting the Windows Administrator Password Using EC2Config](ResettingAdminPassword_EC2Config.md)\.

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. Therefore, if you have any data on instance store volumes that you want to keep, be sure to back it up to persistent storage\.

**Note**  
If you have disabled the local Administrator account on the instance and your instance is configured for SSM, you can also re\-enable and reset your local Administrator password by using EC2Rescue and Run Command\. For more information, see [Using EC2Rescue for Windows Server with Systems Manager Run Command](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2rw-ssm.html)\.

**Note**  
There is an SSM Automation document that automatically applies the manual steps necessary to reset the local Administrator password\. For more information, see [Reset the Local Administrator Password on Amazon EC2 Windows Instances](http://docs.aws.amazon.com/systems-manager/latest/userguide/automation-ec2reset.html) in the *AWS Systems Manager User Guide*\.

**Resetting a Windows administrator password using EC2Rescue**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance that needs a password reset and choose **Actions**, **Instance State**, **Stop**\. Wait until the instance state is `stopped` state before continuing to the next step\.

1. \(Optional\) If you have the private key that you specified when you launched this instance, continue with the next step\. Otherwise, use the following steps to replace the instance with a new instance that you launch with a new key pair\.

   1. Create a new key pair using the Amazon EC2 console\. To give your new key pair the same name as the one for which you lost private key, you must delete the existing key pair\.

   1. Select the instance to replace\. Note the instance type, VPC, subnet, security group, and IAM role of the instance\.

   1. Choose **Actions**, **Image**, **Create Image**\. Type a name and a description for the image and choose **Create Image**\. Choose **View pending image**\.

   1. When the status of the new image is **available**, select the image and choose **Launch**\.

   1. Complete the wizard, selecting the same instance type, VPC, subnet, security group, and IAM role as the instance to replace\. Choose **Launch**\.

   1. When prompted, choose the key pair you created for the new instance, select the acknowledgement check box, and choose **Launch Instances**\.

   1. If the original instance has an associated Elastic IP address, transfer it to the new instance\. If the original instance has EBS volumes in addition to the root volume, transfer them to the new instance\.

   1. Terminate the stopped instance, as it is no longer needed\. For the remainder of this procedure, all references to the original instance apply to this instance that you just created\.

1. Create the temporary instance to use to download and run the EC2Rescue for Windows Server tool\.

   1. Choose **Launch Instance** and then select an AMI\.
**Important**  
You must select an AMI for a different version of Windows in order to avoid disk signature collisions\. For example, if the original instance runs Windows Server 2012 R2, launch the temporary instance using the base AMI for Windows Server 2008 R2\.

   1. Leave the default instance type that the wizard selects for you and choose **Next: Configure Instance Details**\.

   1. On the **Configure Instance Details** page, for **Subnet**, choose the same Availability Zone as the original instance, and then choose **Review and Launch**\.
**Important**  
The temporary instance must be in the same Availability Zone as the original instance\. If your temporary instance is in a different Availability Zone, you won't be able to attach the original instance's root volume to it\.

   1. On the **Review Instance Launch** page, choose **Launch**\.

   1. When prompted, create a new key pair, download it to a safe location on your computer, and then choose **Launch Instances**\.

1. From the temporary instance, download the [EC2Rescue for Windows Server](https://s3.amazonaws.com/ec2rescue/windows/EC2Rescue_latest.zip) tool and extract the files\.

1. Detach the root volume from the original instance as follows:

   1. On the **Description** pane of the original instance, note the ID of the EBS volume listed as the **Root device**\.

   1. In the navigation pane, choose **Volumes**\.

   1. In the list of volumes, select the volume, and then choose **Actions**, **Detach Volume**\. After the volume's status changes to **available**, continue with the next step\.

1. Attach the volume to the temporary instance as a secondary volume as follows:

   1. Choose **Actions**, **Attach Volume**\.

   1. In the **Attach Volume** dialog box, start typing the name or ID of your temporary instance for **Instances**, and then select the instance from the list\.

   1. For **Device**, type **xvdf** \(if it isn't already there\), and then choose **Attach**\.

1. Connect to the temporary instance and use the EC2Rescue for Windows Server tool on the instance to reset the administrator password as follows:

   1. On the EC2Rescue for Windows Server tool, choose **Offline instance**\.

   1. Select the disk of the newly mounted volume and choose **Next**\.

   1. Confirm the disk selection and choose **Yes**\.

   1. Choose **Diagnose and Rescue**\.

   1. On the **Summary** dialog box, review the information and choose **Next**\.

   1. On the **Detected possible issues** dialog box, select **Reset Administrator Password** and choose **Next**\.

   1. Choose **Rescue**, confirm the selection, and then choose **Next**\.

   1. Choose **Finish**\.

1. Detach the root volume from the temporary instance using the Amazon EC2 console as follows:

   1. On the **Description** pane of the original instance, note the ID of the EB volume listed as the **Root device**\.

   1. In the navigation pane, choose **Volumes**\.

   1. Select the volume and choose **Actions**, **Detach Volume**\. After the volume's status changes to **available**, continue with the next step\.

1. Reattach the volume to the original instance as follows:

   1. Select the volume and choose **Actions**, **Attach Volume**\.

   1. In the **Attach Volume** dialog box, start typing the name or ID of your original instance for **Instances** and then select the instance\.

   1. For **Device**, type **/dev/sda1**\.

   1. Choose **Attach**\. Wait until the state of the volume is `in-use` before continuing to the next step\.

1. In the navigation pane, choose **Instances**\. Select the original instance and choose **Actions**, **Instance State**, **Start**\. When prompted for confirmation, choose **Yes, Start**\. Wait until the state of your instance is `running` before continuing to the next step\.

1. Retrieve your new Windows administrator password using the private key for the new key pair and connect to the instance\. For more information, see [Connecting to Your Windows Instance](connecting_to_windows_instance.md)\.

1. \(Optional\) If you have no further use for it, you can terminate the temporary instance\. Select the temporary instance, and then choose **Actions**, **Instance State**, **Terminate**\.