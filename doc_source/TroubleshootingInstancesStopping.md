# Troubleshooting Stopping Your Instance<a name="TroubleshootingInstancesStopping"></a>

If you have stopped your Amazon EBS\-backed instance and it appears stuck in the `stopping` state, there may be an issue with the underlying host computer\.

There is no cost for any instance usage while an instance is not in the `running` state\.

Force the instance to stop using either the console or the AWS CLI\.
+ To force the instance to stop using the console, select the stuck instance, and choose **Actions**, **Instance State**, **Stop**, and **Yes, Forcefully Stop**\.
+ To force the instance to stop using the AWS CLI, use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command and the `--force` option as follows:

  ```
  aws ec2 stop-instances --instance-ids i-0123ab456c789d01e --force
  ```

If, after 10 minutes, the instance has not stopped, post a request for help in the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the instance ID, and describe the steps that you've already taken\. Alternatively, if you have a support plan, create a technical support case in the [Support Center](https://console.aws.amazon.com/support/home#/)\.

## Creating a Replacement Instance<a name="Creating_Replacement_Instance"></a>

To attempt to resolve the problem while you are waiting for assistance from the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30) or the [Support Center](https://console.aws.amazon.com/support/home#/), create a replacement instance\. Create an AMI of the stuck instance, and launch a new instance using the new AMI\. 

**To create a replacement instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the stuck instance\.

1. Choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** dialog box, fill in the following fields, and then choose **Create Image**:

   1. Specify a name and description for the AMI\.

   1. Choose **No reboot**\.

   For more information, see [ Creating a Windows AMI from a Running Instance You can create an AMI using the AWS Management Console or the command line\. The following diagram summarizes the process for creating an AMI from a running EC2 instance\. Start with an existing AMI, launch an instance, customize it, create a new AMI from it, and finally launch an instance of your new AMI\. The steps in the following diagram match the steps in the procedure below\. If you already have a running Windows instance, you can go directly to step 4\. 

![\[Workflow for creating an AMI from an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/running-instance.png) To create an AMI from an instance using the console  Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.   In the navigation pane, choose **Images**, **AMIs**\.   Use the **Filter** options to scope the list of AMIs to the Windows AMIs that meet your needs\. For example, to view the Windows AMIs provided by AWS, choose **Public images** from the drop\-down list\. Choose the Search bar\. Choose **Owner** from the menu and choose **Amazon images**\. Choose **Source** from the menu and type one of the following, depending on the version of Windows Server that you need:   **amazon/Windows\_Server\-2019**   **amazon/Windows\_Server\-2016**   **amazon/Windows\_Server\-2012**   **amazon/Windows\_Server\-2008**   Add any other filters that you need\. When you have chosen an AMI, select its checkbox\.   Choose **Launch**\. Accept the default values as you step through the wizard\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\. When the instance is ready, connect to it\. For more information, see [Connecting to Your Windows Instance](connecting_to_windows_instance.md)\.   You can perform any of the following actions on your instance to customize it for your needs:   Install software and applications   Copy data   Reduce start time by deleting temporary files, defragmenting your hard drive, and zeroing out free space   Attach additional EBS volumes   Create a new user account and add it to the Administrators group If you are sharing your AMI, these credentials can be supplied for RDP access without disclosing your default administrator password\.   \[Windows Server 2016 and later\] Configure settings using EC2Launch\. To generate a random password at launch time, use the `adminPasswordType` setting\. For more information, see [Configuring EC2Launch](ec2launch.md#ec2launch-config)\.   \[Windows Server 2012 R2 and earlier\] Configure settings using EC2Config\. To generate a random password at launch time, enable the `Ec2SetPassword` plugin; otherwise, the current administrator password is used\. For more information, see [EC2Config Settings Files](ec2config-service.md#UsingConfigXML_WinAMI)\.   \[Windows Server 2008 R2\] If the instance uses RedHat drivers to access Xen virtualized hardware, upgrade to Citrix drivers before you create an AMI\. For more information, see [Upgrade Windows Server 2008 and 2008 R2 Instances \(Redhat to Citrix PV Upgrade\)](Upgrading_PV_drivers.md#win2008-citrix-upgrade)\.     In the navigation pane, choose **Instances** and select your instance\. Choose **Actions**, **Image**, and **Create Image**\.  If this option is disabled, your instance isn't an Amazon EBS\-backed instance\.    Specify a unique name for the image and an optional description \(up to 255 characters\)\. By default, Amazon EC2 shuts down the instance, takes snapshots of any attached volumes, creates and registers the AMI, and then reboots the instance\. Choose **No reboot** if you don't want your instance to be shut down\.  If you choose **No reboot**, we can't guarantee the file system integrity of the created image\.  \(Optional\) Modify the root volume, Amazon EBS volumes, and instance store volumes as needed\. For example:   To change the size of the root volume, locate the **Root** volume in the **Type** column, and fill in the **Size** field\.   To suppress an Amazon EBS volume specified by the block device mapping of the AMI used to launch the instance, locate the EBS volume in the list and choose **Delete**\.   To add an Amazon EBS volume, choose **Add New Volume**, **Type**, and **EBS**, and fill in the fields\. When you then launch an instance from your new AMI, these additional volumes are automatically attached to the instance\. Empty volumes must be formatted and mounted\. Volumes based on a snapshot must be mounted\.   To suppress an instance store volume specified by the block device mapping of the AMI used to launch the instance, locate the volume in the list and choose **Delete**\.   To add an instance store volume, choose **Add New Volume**, **Type**, and **Instance Store**, and select a device name from the **Device** list\. When you launch an instance from your new AMI, these additional volumes are automatically initialized and mounted\. These volumes don't contain data from the instance store volumes of the running instance from which you based your AMI\.   When you are finished, choose **Create Image**\.   While your AMI is being created, you can choose **AMIs** in the navigation pane to view its status\. Initially, this is `pending`\. After a few minutes, the status should change to `available`\. \(Optional\) Choose **Snapshots** in the navigation pane to view the snapshot that was created for the new AMI\. When you launch an instance from this AMI, we use this snapshot to create its root device volume\.   Launch an instance from your new AMI\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\. The new running instance contains all of the customizations you applied in previous steps, and any additional customization you add when launching the instance, such as user data \(scripts that run when the instance starts\)\.    To create an AMI from an instance using the command line You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.   [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\)   [New\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) \(AWS Tools for Windows PowerShell\)    ](Creating_EBSbacked_WinAMI.md#how-to-create-windows-ebs-ami)\.

1. Launch a new instance from the AMI and verify that the new instance is working\.

1. Select the stuck instance, and choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.

**To create a replacement instance using the CLI**

1. Create an AMI from the stuck instance using the [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\) command and the `--no-reboot` option as follows:\.

   ```
   aws ec2 create-image --instance-id i-0123ab456c789d01e --name "AMI" --description "AMI for replacement instance" --no-reboot
   ```

1. Launch a new instance from the AMI using the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\) command as follows:

   ```
   aws ec2 run-instances --image-id ami-1a2b3c4d --count 1 --instance-type c3.large --key-name MyKeyPair --security-groups MySecurityGroup
   ```

1. Verify that the new instance is working\.

1. Terminate the stuck instance using the [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) \(AWS CLI\) command as follows:

   ```
   aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
   ```

If you are unable to create an AMI from the instance as described in the previous procedures, you can set up a replacement instance as follows:

**\(Alternate\) To create a replacement instance using the console**

1. Select the instance and choose **Description**, **Block devices**\. Select each volume and write down its volume ID\. Be sure to note which volume is the root volume\.

1. In the navigation pane, choose **Volumes**\. Select each volume for the instance, and choose **Actions**, **Create Snapshot**\.

1. In the navigation pane, choose **Snapshots**\. Select the snapshot that you just created, and choose **Actions**, **Create Volume**\.

1. Launch an instance with the same operating system as the stuck instance\. Note the volume ID and device name of its root volume\.

1. In the navigation pane, choose **Instances**, select the instance that you just launched, choose **Actions**, **Instance State**, and then choose **Stop**\.

1. In the navigation pane, choose **Volumes**, select the root volume of the stopped instance, and choose **Actions**, **Detach Volume**\.

1. Select the root volume that you created from the stuck instance, choose **Actions**, **Attach Volume**, and attach it to the new instance as its root volume \(using the device name that you wrote down\)\. Attach any additional non\-root volumes to the instance\.

1. In the navigation pane, choose **Instances** and select the replacement instance\. Choose **Actions**, **Instance State**, **Start**\. Verify that the instance is working\.

1. Select the stuck instance, choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.