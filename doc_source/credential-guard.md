# Credential Guard<a name="credential-guard"></a>

The AWS Nitro System supports Credential Guard for Amazon Elastic Compute Cloud \(Amazon EC2\) Windows instances\. Credential Guard is a Windows virtualization\-based security \(VBS\) feature that enables the creation of isolated environments to protect security assets, such as Windows user credentials and code integrity enforcement, beyond Windows kernel protections\. When you run EC2 Windows instances, Credential Guard uses the AWS Nitro System to protect Windows login credentials from being extracted from the OS memory\.

**Topics**
+ [Prerequisites](#credential-guard-prerequisites)
+ [Launching a supported instance](#credential-guard-launch-instance)
+ [Turning on Credential Guard](#turn-on-credential-guard)
+ [Verifying Credential Guard is running](#verify-credential-guard)
+ [Turning off Credential Guard](#turn-off-credential-guard)

## Prerequisites<a name="credential-guard-prerequisites"></a>

Your Windows instance must meet the following prerequisites to utilize Credential Guard:

**Amazon Machine Images \(AMIs\)**  
The AMI must be preconfigured to enable NitroTPM and UEFI Secure Boot\. For more information on supported AMIs, see [Prerequisites for launching a Windows instance with NitroTPM enabled](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enable-nitrotpm-prerequisites.html)\.

**Instance types**  
The following instance types and sizes support Credential Guard:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/credential-guard.html)
Though NitroTPM has some required instance types in common, the instance type must be one of the above to support Credential Guard\. 

## Launching a supported instance<a name="credential-guard-launch-instance"></a>

You can use the Amazon EC2 console or AWS Command Line Interface \(AWS CLI\) to launch an instance which can support Credential Guard\. You will need a compatible AMI ID for launching your instance which is unique for each AWS Region\.

**Tip**  
You can use the following link to discover and launch instances with compatible Amazon provided AMIs in the Amazon EC2 console:  
[https://console.aws.amazon.com/ec2/v2/home?#Images:visibility=public-images;v=3;search=:TPM-Windows_Server;ownerAlias=amazon](https://console.aws.amazon.com/ec2/v2/home?#Images:visibility=public-images;v=3;search=:TPM-Windows_Server;ownerAlias=amazon)

------
#### [ Amazon EC2 console ]

**To launch an instance using the Amazon EC2 console**  
Follow the steps to [Launch an instance using the new launch instance wizard](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-launch-instance-wizard.html) while specifying a supported instance type and preconfigured Windows AMI\.

------
#### [ AWS CLI ]

**To launch an instance using the AWS CLI**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch an instance using a supported instance type and preconfigured Windows AMI\.

```
aws ec2 run-instances \
--image-id ami-abcdef01234567890 \
--region us-east-1 \
--instance-type m5.2xlarge \
--subnet-id subnet-abcdef01234567890
```

------

## Turning on Credential Guard<a name="turn-on-credential-guard"></a>

After you have launched a Windows instance with a supported instance type and compatible AMI, you can turn on Credential Guard\.

**Important**  
Administrator privileges are required to perform the following steps to turn on Credential Guard\.

**To turn on Credential Guard**

1. Connect to your instance as a user account with administrator privileges using the Remote Desktop Protocol \(RDP\)\. For more information, see [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#connect-rdp)\.

1. Open the Start menu and search for **cmd** to start a command prompt\.

1. Run the following command to open the Local Group Policy Editor: `gpedit.msc`

1. In the Local Group Policy Editor, choose **Computer Configuration**, **Administrative Templates**, **System**, **Device Guard**\.

1. Select **Turn On Virtualization Based Security**, then select **Edit policy setting**\.

1. Choose **Enabled** within the **Turn On Virtualization Based Security** menu\.

1. For **Select Platform Security Level**, choose **Secure Boot and DMA Protection**\.

1. For **Credential Guard Configuration**, choose **Enabled without lock**\.
**Note**  
The remaining policy settings are not required to enable Credential Guard and can be left as **Not Configured**\.

   The following image displays the VBS settings configured as described previously:  
![\[\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/vbs-credential-guard-gpo-enabled.png)

1. Reboot the instance to apply the settings\.

## Verifying Credential Guard is running<a name="verify-credential-guard"></a>

You can use the Microsoft System Information \(`Msinfo32.exe`\) tool to confirm that Credential Guard is running\.

**Important**  
You must first reboot the instance to finish applying the policy settings required to enable Credential Guard\.

**To verify Credential Guard is running**

1. Connect to your instance using the Remote Desktop Protocol \(RDP\)\. For more information, see [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#connect-rdp)\.

1. Within the RDP session to your instance, open the Start menu and search for **cmd** to start a command prompt\.

1. Open System Information by running the following command: `msinfo32.exe`

1. The Microsoft System Information tool lists the details for VBS configuration\. Next to Virtualization\-based security Services, confirm that **Credential Guard** appears as **Running**\.

   The following image displays VBS is running as described previously:  
![\[\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/vbs-credential-guard-msinfo32-enabled.png)

## Turning off Credential Guard<a name="turn-off-credential-guard"></a>

You can turn off Credential Guard if it has been enabled on your EC2 instance\.

**Important**  
Administrator privileges are required to perform the following steps to turn off Credential Guard\.

**To turn off Credential Guard**

1. Connect to your instance as a user account with administrator privileges using the Remote Desktop Protocol \(RDP\)\. For more information, see [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#connect-rdp)\.

1. Open the Start menu and search for **cmd** to start a command prompt\.

1. Run the following command to open the Local Group Policy Editor: `gpedit.msc`

1. In the Local Group Policy Editor, choose **Computer Configuration**, **Administrative Templates**, **System**, **Device Guard**\.

1. Select **Turn On Virtualization Based Security**, then select **Edit policy setting**\.

1. Choose **Disabled** within the **Turn On Virtualization Based Security** menu\.

1. The following image displays the VBS settings configured as described previously:  
![\[\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/vbs-credential-guard-gpo-disabled.png)

1. Reboot the instance to apply the settings\.