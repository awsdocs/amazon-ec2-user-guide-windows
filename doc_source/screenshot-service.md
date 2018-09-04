# Troubleshoot an Unreachable Instance<a name="screenshot-service"></a>

If you are unable to reach your instance via SSH or RDP, you can capture a screenshot of your instance and view it as an image\. This provides visibility as to the status of the instance, and allows for quicker troubleshooting\.

There is no data transfer cost for this screenshot\. The image is generated in JPG format, no larger than 100kb\.
+ [How to Take a Screenshot of an Unreachable Instance](#how-to-ics)
+ [Common Screenshots](#ics-common)

## How to Take a Screenshot of an Unreachable Instance<a name="how-to-ics"></a>

**To access the instance console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance to capture\.

1. Choose **Actions**, **Instance Settings**\.

1. Choose **Get Instance Screenshot**\.

Right\-click on the image to download and save it\.

**To capture a screenshot using the command line**

You can use one of the following commands\. The returned output is base64\-encoded\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [get\-console\-screenshot](http://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-screenshot.html) \(AWS CLI\)
+ [GetConsoleScreenshot](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-GetConsoleScreenshot.html) \(Amazon EC2 Query API\)

For API calls, the returned content is base64\-encoded\. For command line tools, the decoding is performed for you\.

## Common Screenshots<a name="ics-common"></a>

You can use the following information to help you troubleshoot an unreachable instance based on screenshots returned by the service\.
+ [Log On Screen \(Ctrl\+Alt\+Delete\)](#logon-screen) 
+ [Recovery Console Screen](#recovery-console-screen) 
+ [Windows Boot Manager Screen](#boot-manager-screen) 
+ [Sysprep Screen](#sysprep-screen) 
+ [Getting Ready Screen](#getting-ready-screen) 
+ [Windows Update Screen](#windows-update-screen) 
+ [Chkdsk](#Chkdsk) 

### Log On Screen \(Ctrl\+Alt\+Delete\)<a name="logon-screen"></a>

Console Screenshot Service returned the following\.

![\[Log on screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-1.png)

If an instance becomes unreachable during log on, there could be a problem with your network configuration or Windows Remote Desktop Services\. An instance can also be unresponsive if a process is using large amounts of CPU\. 

#### Network Configuration<a name="network-config"></a>

Use the following information, to verify that your AWS, Microsoft Windows, and local \(or on\-premises\) network configurations aren't blocking access to the instance\.


**AWS Network Configuration**  

| Configuration | Verify | 
| --- | --- | 
| Security group configuration | Verify that port 3389 is open for your security group\. Verify you are connecting to the right public IP address\. If the instance was not associated with an Elastic IP, the public IP changes after the instance stops/starts\. For more information, see [Remote Desktop can't connect to the remote computer](troubleshooting-windows-instances.md#rdp-issues)\. | 
| VPC configuration \(Network ACLs\) | Verify that the access control list \(ACL\) for your Amazon VPC is not blocking access\. For information, see [Network ACLs](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html) in the Amazon VPC User Guide\. | 
| VPN configuration | If you are connecting to your VPC using a virtual private network \(VPN\), verify VPN tunnel connectivity\. For more information, see [How do I troubleshoot VPN tunnel connectivity to an Amazon VPC?](https://aws.amazon.com/premiumsupport/knowledge-center/vpn-tunnel-troubleshooting/) | 


**Windows Network Configuration**  

| Configuration | Verify | 
| --- | --- | 
| Windows Firewall | Verify that Windows Firewall isn't blocking connections to your instance\. Disable Windows Firewall as described in bullet 7 of the remote desktop troubleshooting section, [Remote Desktop can't connect to the remote computer](troubleshooting-windows-instances.md#rdp-issues)\.  | 
| Advanced TCP/IP configuration \(Use of static IP\) | The instance may be unresponsive because you configured a static IP address\. For a VPC, [Create a network interface](using-eni.md#create_eni) and [attach it to the instance](using-eni.md#attach_eni_running_stopped)\. For EC2 Classic, enable DHCP\. | 

**Local or On\-Premises Network Configuration**

Verify that a local network configuration isn't blocking access\. Try to connect to another instance in the same VPC as your unreachable instance\. If you can't access another instance, work with your local network administrator to determine whether a local policy is restricting access\.

#### Remote Desktop Services Issue<a name="rds-issue"></a>

If the instance can't be reached during log on, there could a problem with Remote Desktop Services \(RDS\) on the instance\.


**Remote Desktop Services Configuration**  

| Configuration | Verify | 
| --- | --- | 
| RDS is running | Verify that RDS is running on the instance\. Connect to the instance using the Microsoft Management Console \(MMC\) Services snap\-in \(services\.msc\)\. In the list of services, verify that Remote Desktop Services is Running\. If it isn't, start it and then set the startup type to Automatic\. If you can't connect to the instance by using the Services snap\-in, detach the root volume from the instance, take a snapshot of the volume or create an AMI from it, attach the original volume to another instance in the same availability zone as a secondary volume, and modify the [Start](https://technet.microsoft.com/en-us/library/cc959920.aspx) registry key\. When you are finished, reattach the root volume to the original instance\. For more information about detaching volumes, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\. | 
| RDS is enabled |  Even if the service is started, it might be disabled\. Detach the root volume from the instance, take a snapshot of the volume or create an AMI from it, attach the original volume to another instance in the same Availability Zone as a secondary volume, and enable the service by modifying the **Terminal Server** registry key as described in the following article: [Enable Remote Desktop Remotely By Using Remote Registry](http://silentcrash.com/2013/07/enable-remote-desktop-remotely-by-using-the-registry/)\. When you are finished, reattach the root volume to the original instance\. For more information, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.  | 

#### High CPU<a name="high-cpu"></a>

Check the **CPUUtilization \(Maximum\)** metric on your instance by using Amazon CloudWatch\. If **CPUUtilization \(Maximum\)** is a high number, wait for the CPU to go down and try connecting again\. High CPU usage can be caused by:
+ Windows Update
+ Security Software Scan
+ Custom Startup Script
+ Task Scheduler

For more information about the **CPUUtilization \(Maximum\)** metric, see [Get Statistics for a Specific EC2 Instance](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/US_SingleMetricPerInstance.html) in the *Amazon CloudWatch User Guide*\. For additional troubleshooting tips, see [High CPU usage shortly after Windows starts](troubleshooting-windows-instances.md#high-cpu-issue)\.

### Recovery Console Screen<a name="recovery-console-screen"></a>

Console Screenshot Service returned the following\.

![\[Recovery console screenshot\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-2.png)

The operating system may boot into the Recovery console and get stuck in this state if the `bootstatuspolicy` is not set to `ignoreallfailures`\. Use the following procedure to change the `bootstatuspolicy` configuration to `ignoreallfailures`\.

By default, the policy configuration for AWS\-provided public Windows AMIs is set to `ignoreallfailures`\. 

1. Stop the unreachable instance\.

1. Create a snapshot of the root volume\. The root volume is attached to the instance as `/dev/sda1`\. 

   Detach the root volume from the unreachable instance, take a snapshot of the volume or create an AMI from it, and attach it to another instance in the same Availability Zone as a secondary volume\. For more information, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.
**Warning**  
If your temporary instance is based on the same AMI that the original instance is based on, you must complete additional steps or you won't be able to boot the original instance after you restore its root volume because of a disk signature collision\. Alternatively, select a different AMI for the temporary instance\. For example, if the original instance uses an AMI for Windows Server 2008 R2, launch the temporary instance using an AMI for Windows Server 2012\. If you must create a temporary instance based on the same AMI, see Step 6 in [Remote Desktop can't connect to the remote computer](troubleshooting-windows-instances.md#rdp-issues) to avoid a disk signature collision\.

1. Log in to the instance and execute the following command from a command prompt to change the `bootstatuspolicy` configuration to `ignoreallfailures`:

   ```
   bcdedit /store Drive Letter:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
   ```

1. Reattach the volume to the unreachable instance and start the instance again\.

### Windows Boot Manager Screen<a name="boot-manager-screen"></a>

Console Screenshot Service returned the following\.

![\[Windows Boot Manager Screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-3.png)

The operating system experienced a fatal corruption in the system file and/or the registry\. When the instance is stuck in this state, you should recover the instance from a recent backup AMI or launch a replacement instance\. If you need to access data on the instance, detach any root volumes from the unreachable instance, take a snapshot of those volume or create an AMI from them, and attach them to another instance in the same Availability Zone as a secondary volume\. For more information, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.

### Sysprep Screen<a name="sysprep-screen"></a>

Console Screenshot Service returned the following\.

![\[Sysprep Screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-4.png)

You may see this screen if you did not use the EC2Config Service to call sysprep\.exe or if the operating system failed while running Sysprep\. To solve this problem, [Create a Standard Amazon Machine Image Using Sysprep](ami-create-standard.md)\.

### Getting Ready Screen<a name="getting-ready-screen"></a>

Console Screenshot Service returned the following\.

![\[Getting Ready Screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-5.png)

Refresh the Instance Console Screenshot Service repeatedly to verify that the progress ring is spinning\. If the ring is spinning, wait for the operating system to start up\. You can also check the **CPUUtilization \(Maximum\)** metric on your instance by using Amazon CloudWatch to see if the operating system is active\. If the progress ring is not spinning, the instance may be stuck at the boot process\. Reboot the instance\. If rebooting does not solve the problem, recover the instance from a recent backup AMI or launch a replacement instance\. If you need to access data on the instance, detach the root volume from the unreachable instance, take a snapshot of the volume or create an AMI from it\. Then attach it to another instance in the same Availability Zone as a secondary volume\. For more information about **CPUUtilization \(Maximum\)**, see [Get Statistics for a Specific EC2 Instance](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/US_SingleMetricPerInstance.html) in the *Amazon CloudWatch User Guide*\. 

### Windows Update Screen<a name="windows-update-screen"></a>

Console Screenshot Service returned the following\.

![\[Windows Update Screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-6.png)

The Windows Update process is updating the registry\. Wait for the update to finish\. Do not reboot or stop the instance as this may cause data corruption during the update\.

**Note**  
The Windows Update process can consume resources on the server during the update\. If you experience this problem often, consider using faster instance types and faster EBS volumes\. 

### Chkdsk<a name="Chkdsk"></a>

Console Screenshot Service returned the following\.

![\[Chkdsk Screen\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ts-cs-7.png)

Windows is running the chkdsk system tool on the drive to verify file system integrity and fix logical file system errors\. Wait for process to complete\.