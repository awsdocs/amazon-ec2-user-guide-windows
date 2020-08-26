# Importing Your Virtual Machine Using AWS Systems Manager for Microsoft SCVMM<a name="scvmm-import-vm"></a>

You can launch an EC2 instance from a virtual machine that you import from SCVMM to Amazon EC2\.

**Important**  
You can't import Linux virtual machines from SCVMM to Amazon EC2\.

**Topics**
+ [Prerequisites](#scvmm-import-vm-prereqs)
+ [Importing Your Virtual Machine](#scvmm-importing)
+ [Checking the Import Task Status](#scvmm-import-status)
+ [Backing Up Your Imported Instance](#scvmm-backup)

## Prerequisites<a name="scvmm-import-vm-prereqs"></a>
+ Ensure that your VM is ready\. For more information, see [Prepare Your VM](https://docs.aws.amazon.com/vm-import/latest/userguide/prepare-vm-image.html) in the * VM Import/Export User Guide*\.
+ In AWS Systems Manager, click **Configuration**, select the **VM Import** tab, and review the following settings:
  + **S3 bucket prefix**: We create a bucket for disk images to be uploaded before they are imported\. The name of the bucket starts with the prefix listed here and includes the Region \(for example, `us-east-2`\)\. To delete the disk images after they are imported, select **Clean up S3 bucket after import**\.
  + **VM image export path**: A location for the disk images exported from the VM\. To delete the disk images after they are imported, select **Clean up export path after import**\.
  + **Alternate Hyper\-V PowerShell module path**: The location of the Hyper\-V PowerShell module, if it's not installed in the standard location\. For more information, see [Installing the Hyper\-V Management Tools](http://technet.microsoft.com/en-us/library/dn632582.aspx#BKMK_SERVER) in the Microsoft TechNet Library\.

## Importing Your Virtual Machine<a name="scvmm-importing"></a>

The permissions that you've been granted by your administrator determine whether you can import HyperV Windows virtual machines from SCVMM to AWS\.

**To import your virtual machine**

1. Open SCVMM\.

1. On the ribbon, click **VMs**\. Select your virtual machine from the list\.

1. On the ribbon, click **Import VM to Amazon EC2**\.

1. Complete the **Import Virtual Machine** dialog box as follows:

   1. Select a Region for the instance\. By default, we select the Region that you configured as your default Region\.

   1. Select an instance type for the instance\. First, select one of the latest instance families from **Family**, and then select an instance type from **Instance type**\. To include previous generation instance families in the list, select **Show previous generations\.** For more information, see [Amazon EC2 Instances](https://aws.amazon.com/ec2/instance-types/) and [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation/)\.

   1. Select a VPC from **Network \(VPC\)**\. Note that this list includes all VPCs for the region, including VPCs created using the Amazon VPC console and the default VPC \(if it exists\)\. If you have a default VPC in this region, we select it by default\. If the text is "There is no VPC available for launch or import operations in this region", then you must create a VPC in this region using the Amazon VPC console\.

   1. Select a subnet from **Subnet**\. Note that this list includes all subnets for the selected VPC, including any default subnets\. If this list is empty, you must add a subnet to the VPC using the Amazon VPC console, or select a different VPC\. Otherwise, we select a subnet for you\.  
![\[The Create Amazon EC2 Instance dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_systems_manager_import.png)

1. Click **Import**\. If you haven't specified the required information in the **VM Import** tab, you'll receive an error asking you to provide the required information\. Otherwise, you'll receive confirmation that the import task has started\. Click **Close**\.

## Checking the Import Task Status<a name="scvmm-import-status"></a>

The import task can take several hours to complete\. To view the current status, open AWS System Manager and click **Notifications**\.

You'll receive the following notifications as the import task progresses:
+ `Import VM: Created Import VM Task`
+ `Import VM: Export VM Disk Image Done`
+ `Import VM: Upload to S3`
+ `Import VM: Image Conversion Starting`
+ `Import VM: Image Conversion Done`
+ `Import VM: Import Complete`

Note that you'll receive the `Import VM: Upload to S3`, `Import VM: Image Conversion Starting`, and `Import VM: Image Conversion Done` notifications for each disk image converted\.

If the import task fails, you'll receive the notification `Import VM: Import Failed`\. For more information about troubleshooting issues with import tasks, see [Errors Importing a VM](scvmm-troubleshoot.md#scvmm-import-errors)\.

## Backing Up Your Imported Instance<a name="scvmm-backup"></a>

After the import operation completes, the instance runs until it is terminated\. If your instance is terminated, you can't connect to or recover the instance\. To ensure that you can start a new instance with the same software as an imported instance if needed, create an Amazon Machine Image \(AMI\) from the imported instance\. For more information, see [Create a custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.