# Configuring a secondary private IPv4 address for your Windows instance<a name="config-windows-multiple-ip"></a>

You can specify multiple private IPv4 addresses for your instances\. After you assign a secondary private IPv4 address to an instance, you must configure the operating system on the instance to recognize the secondary private IPv4 address\.

Configuring the operating system on a Windows instance to recognize a secondary private IPv4 address requires the following:
+ [Step 1: Configure static IP addressing on your instance](#step1)
+ [Step 2: Configure a secondary private IP address for your instance](#step2)
+ [Step 3: Configure applications to Use the secondary private IP address](#step3)

**Note**  
These instructions are based on Windows Server 2008 R2\. The implementation of these steps may vary based on the operating system of the Windows instance\.

## Prerequisites<a name="prerequisites"></a>

Before you begin, make sure you meet the following requirements:
+ As a best practice, launch your Windows instances using the latest AMIs\. If you are using an older Windows AMI, ensure that it has the Microsoft hot fix referenced in [http://support\.microsoft\.com/kb/2582281](http://support.microsoft.com/kb/2582281)\.
+ After you launch your instance in your VPC, add a secondary private IP address\. For more information, see [Assigning a secondary private IPv4 address](MultipleIP.md#ManageMultipleIP)\.
+ To allow Internet requests to your website after you complete the tasks in these steps, you must configure an Elastic IP address and associate it with the secondary private IP address\. For more information, see [Associating an Elastic IP address with the secondary private IPv4 address](MultipleIP.md#StepThreeEIP)\.

## Step 1: Configure static IP addressing on your instance<a name="step1"></a>

To enable your Windows instance to use multiple IP addresses, you must configure your instance to use static IP addressing rather than a DHCP server\.

**Important**  
When you configure static IP addressing on your instance, the IP address must match exactly what is shown in the console, CLI, or API\. If you enter these IP addresses incorrectly, the instance could become unreachable\. 

**To configure static IP addressing on a Windows instance**

1. Connect to your instance\.

1. Find the IP address, subnet mask, and default gateway addresses for the instance by performing the following steps: 

   1. At a Command Prompt window, run the following command:

     ```
     ipconfig /all
     ```

     Review the following section in your output, and note the **IPv4 Address**, **Subnet Mask**, **Default Gateway**, and **DNS Servers** values for the network interface\.

     ```
     Ethernet adapter Local Area Connection:
     
        Connection-specific DNS Suffix  . :
        Description . . . . . . . . . . . :
        Physical Address  . . . . . . . . :
        DHCP Enabled. . . . . . . . . . . : 
        Autoconfiguration Enabled . . . . :
        IPv4 Address. . . . . . . . . . . : 10.0.0.131
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        Default Gateway . . . . . . . . . : 10.0.0.1
        DNS Servers . . . . . . . . . . . : 10.1.1.10
                                            10.1.1.20
     ```

1. Open the **Network and Sharing Center** by running the following command:

   ```
   %SystemRoot%\system32\control.exe ncpa.cpl
   ```

1. Open the context \(right\-click\) menu for the network interface \(Local Area Connection\) and choose **Properties**\.

1. Choose **Internet Protocol Version 4 \(TCP/IPv4\)**, **Properties**\.

1. In the **Internet Protocol Version 4 \(TCP/IPv4\) Properties** dialog box, choose **Use the following IP address**, enter the following values, and then choose **OK**\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/config-windows-multiple-ip.html)
**Important**  
If you set the IP address to any value other than the current IP address, you will lose connectivity to the instance\.  
![\[IP Addresses\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/WinIPV4_filled.png)

You will lose RDP connectivity to the Windows instance for a few seconds while the instance converts from using DHCP to static addressing\. The instance retains the same IP address information as before, but now this information is static and not managed by DHCP\.

## Step 2: Configure a secondary private IP address for your instance<a name="step2"></a>

After you have set up static IP addressing on your Windows instance, you are ready to prepare a second private IP address\.

**To configure a secondary IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. On the **Description** tab, note the secondary IP address\.

1. Connect to your instance\.

1. On your Windows instance, choose **Start**, **Control Panel**\.

1. Choose **Network and Internet**, **Network and Sharing Center**\.

1. Select the network interface \(Local Area Connection\) and choose **Properties**\.

1. On the **Local Area Connection Properties** page, choose **Internet Protocol Version 4 \(TCP/IPv4\)**, **Properties**, **Advanced**\.

1. Choose **Add**\.

1. In the **TCP/IP Address** dialog box, type the secondary private IP address for **IP address**\. For **Subnet mask**, type the same subnet mask that you entered for the primary private IP address in [Step 1: Configure static IP addressing on your instance](#step1), and then choose **Add**\.   
![\[TCP/IP Address dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/WinOSAddSPIP.png)

1. Verify the IP address settings and choose **OK**\.  
![\[IP Settings tab\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/WinOSPIP_added.png)

1. Choose **OK**, **Close**\.

1. To confirm that the secondary IP address has been added to the operating system, at a command prompt, run the command ipconfig /all\.

## Step 3: Configure applications to Use the secondary private IP address<a name="step3"></a>

You can configure any applications to use the secondary private IP address\. For example, if your instance is running a website on IIS, you can configure IIS to use the secondary private IP address\.

**To configure IIS to use the secondary private IP address**

1. Connect to your instance\.

1. Open Internet Information Services \(IIS\) Manager\.

1. In the **Connections** pane, expand **Sites**\.

1. Open the context \(right\-click\) menu for your website and choose **Edit Bindings**\.

1. In the **Site Bindings** dialog box, for **Type**, choose **http**, **Edit**\.

1. In the **Edit Site Binding** dialog box, for **IP address**, select the secondary private IP address\. \(By default, each website accepts HTTP requests from all IP addresses\.\)  
![\[IP Addresses\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/IIS.png)

1. Choose **OK**, **Close**\.

## Configure a secondary network interface<a name="config-windows-multiple-eni"></a>

You can attach a second elastic network interface to the instance\.

**To configure a second network interface**

1. Configure the static IP addressing for the primary elastic network interface as per the procedures above in [Step 1: Configure static IP addressing on your instance](#step1)\.

1. Configure the static IP addressing for the secondary elastic network interface as per the same procedures\.