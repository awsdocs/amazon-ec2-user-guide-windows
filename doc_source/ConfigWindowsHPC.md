# Tutorial: Setting Up a Windows HPC Cluster on Amazon EC2<a name="ConfigWindowsHPC"></a>

You can launch a scalable Windows High Performance Computing \(HPC\) cluster using Amazon EC2 instances\. A Windows HPC cluster requires an Active Directory domain controller, a DNS server, a head node, and one or more compute nodes\.

To set up a Windows HPC cluster on Amazon EC2, complete the following tasks:
+ [Step 1: Create Security Groups](#Set_ADSecurity_Groups)
+ [Step 2: Set Up Your Active Directory Domain Controller](#ADSetup)
+ [Step 3: Configure Your Head Node](#HeadNode)
+ [Step 4: Set Up the Compute Node](#ComputeNode)
+ [Step 5: Scale Your HPC Compute Nodes \(Optional\)](#scale_cluster)

For more information about high performance computing, see [High Performance Computing \(HPC\) on AWS](https://aws.amazon.com/hpc-applications/)\.

## Prerequisites<a name="prereqs"></a>

You must launch your instances in a VPC\. You can use the default VPC or create a nondefault VPC\. For more information, see [Getting Started](https://docs.aws.amazon.com/vpc/latest/userguide/GetStarted.html) in the *Amazon VPC User Guide*\.

## Step 1: Create Security Groups<a name="Set_ADSecurity_Groups"></a>

Use the Tools for Windows PowerShell to create security groups for the domain controller, domain members, and the HPC cluster\.

**To create the security groups**

1. Use the [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) cmdlet to create the security group for the domain controller\. Note the ID of the security group in the output\.

   ```
   PS C:\> New-EC2SecurityGroup -VpcId vpc-id -GroupName "SG - Domain Controller" -Description "Active Directory Domain Controller"
   ```

1. Use the [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) cmdlet to create the security group for the domain members\. Note the ID of the security group in the output\.

   ```
   PS C:\> New-EC2SecurityGroup -VpcId vpc-id -GroupName "SG - Domain Member" -Description "Active Directory Domain Member"
   ```

1. Use the [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) cmdlet to create the security group for the HPC cluster\. Note the ID of the security group in the output\.

   ```
   PS C:\> New-EC2SecurityGroup -VpcId vpc-id -GroupName "SG - Windows HPC Cluster" -Description "Windows HPC Cluster Nodes"
   ```

**To add rules to the security groups**

1. Create the following rules to add to the domain controller security group\. Replace the placeholder security group ID with the ID of the domain member security group and the placeholder CIDR block with the CIDR block of your network\.

   ```
   PS C:\> $sg_dm = New-Object Amazon.EC2.Model.UserIdGroupPair
   PS C:\> $sg_dm.GroupId = "sg-12345678
   PS C:\> $r1 = @{ IpProtocol="UDP"; FromPort="123"; ToPort="123"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r2 = @{ IpProtocol="TCP"; FromPort="135"; ToPort="135"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r3 = @{ IpProtocol="UDP"; FromPort="138"; ToPort="138"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r4 = @{ IpProtocol="TCP"; FromPort="49152"; ToPort="65535"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r5 = @{ IpProtocol="TCP"; FromPort="389"; ToPort="389"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r6 = @{ IpProtocol="UDP"; FromPort="389"; ToPort="389"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r7 = @{ IpProtocol="TCP"; FromPort="636"; ToPort="636"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r8 = @{ IpProtocol="TCP"; FromPort="3268"; ToPort="3269"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r9 = @{ IpProtocol="TCP"; FromPort="53"; ToPort="53"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r10 = @{ IpProtocol="UDP"; FromPort="53"; ToPort="53"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r11 = @{ IpProtocol="TCP"; FromPort="88"; ToPort="88"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r12 = @{ IpProtocol="UDP"; FromPort="88"; ToPort="88"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r13 = @{ IpProtocol="TCP"; FromPort="445"; ToPort="445"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r14 = @{ IpProtocol="UDP"; FromPort="445"; ToPort="445"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r15 = @{ IpProtocol="ICMP"; FromPort="-1"; ToPort="-1"; UserIdGroupPairs=$sg_dm }
   PS C:\> $r16 = @{ IpProtocol="UDP"; FromPort="53"; ToPort="53"; IpRanges="203.0.113.25/32" }
   PS C:\> $r17 = @{ IpProtocol="TCP"; FromPort="3389"; ToPort="3389"; IpRanges="203.0.113.25/32" }
   ```

1. Use the [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) cmdlet to add the rules to the domain controller security group\.

   ```
   PS C:\> Grant-EC2SecurityGroupIngress -GroupId sg-1a2b3c4d -IpPermission @( $r1, $r2, $r3, $r4, $r5, $r6, $r7, $r8, $r9, $r10, $r11, $r12, $r13, $r14, $r15, $r16, $r17 )
   ```

   For more information about these security group rules, see the following Microsoft article: [How to configure a firewall for domains and trusts](https://support.microsoft.com/en-us/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)\.

1. Create the following rules to add to the domain member security group\. Replace the placeholder security group ID with the ID of the domain controller security group\.

   ```
   PS C:\> $sg_dc = New-Object Amazon.EC2.Model.UserIdGroupPair
   PS C:\> $sg_dc.GroupId = "sg-1a2b3c4d
   PS C:\> $r1 = @{ IpProtocol="TCP"; FromPort="49152"; ToPort="65535"; UserIdGroupPairs=$sg_dc }
   PS C:\> $r2 = @{ IpProtocol="UDP"; FromPort="49152"; ToPort="65535"; UserIdGroupPairs=$sg_dc }
   PS C:\> $r3 = @{ IpProtocol="TCP"; FromPort="53"; ToPort="53"; UserIdGroupPairs=$sg_dc }
   PS C:\> $r4 = @{ IpProtocol="UDP"; FromPort="53"; ToPort="53"; UserIdGroupPairs=$sg_dc }
   ```

1. Use the [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) cmdlet to add the rules to the domain member security group\.

   ```
   PS C:\> Grant-EC2SecurityGroupIngress -GroupId sg-12345678 -IpPermission @( $r1, $r2, $r3, $r4 )
   ```

1. Create the following rules to add to the HPC cluster security group\. Replace the placeholder security group ID with the ID of the HPC cluster security group and the placeholder CIDR block with the CIDR block of your network\.

   ```
   $sg_hpc = New-Object Amazon.EC2.Model.UserIdGroupPair
   PS C:\> $sg_hpc.GroupId = "sg-87654321
   PS C:\> $r1 = @{ IpProtocol="TCP"; FromPort="80"; ToPort="80"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r2 = @{ IpProtocol="TCP"; FromPort="443"; ToPort="443"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r3 = @{ IpProtocol="TCP"; FromPort="1856"; ToPort="1856"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r4 = @{ IpProtocol="TCP"; FromPort="5800"; ToPort="5800"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r5 = @{ IpProtocol="TCP"; FromPort="5801"; ToPort="5801"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r6 = @{ IpProtocol="TCP"; FromPort="5969"; ToPort="5969"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r7 = @{ IpProtocol="TCP"; FromPort="5970"; ToPort="5970"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r8 = @{ IpProtocol="TCP"; FromPort="5974"; ToPort="5974"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r9 = @{ IpProtocol="TCP"; FromPort="5999"; ToPort="5999"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r10 = @{ IpProtocol="TCP"; FromPort="6729"; ToPort="6730"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r11 = @{ IpProtocol="TCP"; FromPort="7997"; ToPort="7997"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r12 = @{ IpProtocol="TCP"; FromPort="8677"; ToPort="8677"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r13 = @{ IpProtocol="TCP"; FromPort="9087"; ToPort="9087"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r14 = @{ IpProtocol="TCP"; FromPort="9090"; ToPort="9092"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r15 = @{ IpProtocol="TCP"; FromPort="9100"; ToPort="9163"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r16 = @{ IpProtocol="TCP"; FromPort="9200"; ToPort="9263"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r17 = @{ IpProtocol="TCP"; FromPort="9794"; ToPort="9794"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r18 = @{ IpProtocol="TCP"; FromPort="9892"; ToPort="9893"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r19 = @{ IpProtocol="UDP"; FromPort="9893"; ToPort="9893"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r20 = @{ IpProtocol="TCP"; FromPort="6498"; ToPort="6498"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r21 = @{ IpProtocol="TCP"; FromPort="7998"; ToPort="7998"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r22 = @{ IpProtocol="TCP"; FromPort="8050"; ToPort="8050"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r23 = @{ IpProtocol="TCP"; FromPort="5051"; ToPort="5051"; UserIdGroupPairs=$sg_hpc }
   PS C:\> $r24 = @{ IpProtocol="TCP"; FromPort="3389"; ToPort="3389"; IpRanges="203.0.113.25/32" }
   ```

1. Use the [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) cmdlet to add the rules to the HPC cluster security group\.

   ```
   PS C:\> Grant-EC2SecurityGroupIngress -GroupId sg-87654321 -IpPermission @( $r1, $r2, $r3, $r4, $r5, $r6, $r7, $r8, $r9, $r10, $r11, $r12, $r13, $r14, $r15, $r16, $r17, $r18, $r19, $r20, $r21, $r22, $r23, $r24 )
   ```

   For more information about these security group rules, see the following Microsoft article: [HPC Cluster Networking: Windows Firewall configuration](http://technet.microsoft.com/en-us/library/ff919486.aspx#Windows Firewall configuration)\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. Verify that the all three security groups appear in the list and have the required rules\.

## Step 2: Set Up Your Active Directory Domain Controller<a name="ADSetup"></a>

The Active Directory domain controller provides authentication and centralized resource management of the HPC environment and is required for the installation\. To set up your Active Directory, launch an instance to serve as the domain controller for your HPC cluster and configure it\.

**To launch a domain controller for your HPC cluster**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the console dashboard, choose **Launch Instance**\.

1. On the **Choose an AMI** page, select an AMI for Windows Server, and choose **Select**\.

1. On the next page of the wizard, select an instance type, then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select your VPC from **Network** and a subnet from **Subnet**\. On the next page of the wizard, you can specify additional storage for your instance\.

1. On the **Add Tags** page, enter `Domain Controller` as the value for the `Name` tag for the instance, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, choose **Select an existing security group**, choose the `SG - Domain Controller` security group, and then choose **Review and Launch**\.

1. Choose **Launch**\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Choose **Allocate new address**\. Choose **Allocate**\. Choose **Close**\.

1. Select the Elastic IP address you created, and choose **Actions**, **Associate address**\. For **Instance**, choose the domain controller instance\. Choose **Associate**\.

Connect to the instance you created, and configure the server as a domain controller for the HPC cluster\.

**To configure your instance as a domain controller**

1. Connect to your `Domain Controller` instance\. For more information, see [Connecting to your Windows instance](connecting_to_windows_instance.md)\.

1. Open **Server Manager**, and add the **Active Directory Domain Services** role\. 

1. Promote the server to a domain controller using Server Manager or by running DCPromo\.exe\. 

1. Create a new domain in a new forest\. 

1. Type **hpc\.local** as the fully qualified domain name \(FQDN\)\. 

1. Select **Forest Functional Level** as **Windows Server 2008 R2**\.

1. Ensure that the **DNS Server** option is selected, and then choose **Next**\.

1. Select **Yes, the computer will use an IP address automatically assigned by a DHCP server \(not recommended\)**\.

1. When prompted, choose **Yes** to continue\.

1. Complete the wizard and then select **Reboot on Completion**\.

1. Connect to the instance as **hpc\.local\\administrator**\.

1. Create a domain user **hpc\.local\\hpcuser**\.

## Step 3: Configure Your Head Node<a name="HeadNode"></a>

An HPC client connects to the head node\. The head node facilitates the scheduled jobs\. You configure your head node by launching an instance, installing the HPC Pack, and configuring the cluster\.

Launch an instance and then configure it as a member of the `hpc.local` domain and with the necessary user accounts\.

**To configure an instance as your head node**

1. Launch an instance and name it **HPC\-Head**\. When you launch the instance, select both of these security groups: SG \- Windows HPC Cluster and SG \- Domain Member\.

1. Connect to the instance and get the existing DNS server address using the following command:

   ```
   IPConfig /all
   ```

1. Update the TCP/IPv4 properties of the `HPC-Head` NIC to include the Elastic IP address for the `Domain Controller` instance as the primary DNS, and then add the additional DNS IP address from the previous step\.

1. Join the machine to the `hpc.local` domain using the credentials for `hpc.local\administrator` \(the domain administrator account\)\.

1. Add `hpc.local\hpcuser` as the local administrator\. When prompted for credentials, use `hpc.local\administrator`, and then restart the instance\. 

1. Connect to **HPC\-Head** as `hpc.local\hpcuser`\.

**To install the HPC Pack**

1. Connect to your **HPC\-Head** instance using the `hpc.local\hpcuser` account\.

1. Using **Server Manager**, turn off Internet Explorer Enhanced Security Configuration \(IE ESC\) for Administrators\.

   1. In **Server Manager**, under **Security Information**, choose **Configure IE ESC**\.

   1. Turn off IE ESC for administrators\.

1. Install the HPC Pack on **HPC\-Head**\.

   1. Download the HPC Pack to `HPC-Head` from the [Microsoft Download Center](https://www.microsoft.com/en-us/download/default.aspx)\. Choose the HPC Pack for the version of Windows Server on `HPC-Head`\.

   1. Extract the files to a folder, open the folder, and double\-click **setup\.exe**\.

   1. On the Installation page, select **Create a new HPC cluster by creating a head node**, and then choose **Next**\.

   1. Accept the default settings to install all the databases on the Head Node, and then choose **Next**\.

   1. Complete the wizard\.

**To configure your HPC cluster on the head node**

1. Start **HPC Cluster Manager**\.

1. In the **Deployment To\-Do List**, select **Configure your network**\.

   1. In the wizard, select the default option \(5\), and then choose **Next**\.

   1. Complete the wizard accepting default values on all screens, and choose how you want to update the server and participate in customer feedback\.

   1. Choose **Configure**\.

1. Select **Provide Network Credentials**, then provide the `hpc.local\hpcuser` credentials\.

1. Select **Configure the naming of new nodes**, and then choose **OK**\.

1. Select **Create a node template**\.

   1. Select the **Compute node template**, and then choose **Next**\.

   1. Select **Without operating system**, and then continue with the defaults\. 

   1. Choose **Create**\.

## Step 4: Set Up the Compute Node<a name="ComputeNode"></a>

You set up the compute node by launching an instance, installing the HPC Pack, and adding the node to your cluster\.

First, launch an instance, and then configure it as a member of the `hpc.local` domain with the necessary user accounts\.

**To configure an instance for your compute node**

1. Launch an instance and name it `HPC-Compute`\. When you launch the instance, select the following security groups: **SG \- Windows HPC Cluster** and **SG \- Domain Member**\.

1. Log in to the instance and get the existing DNS server address from **HPC\-Compute** using the following command:

   ```
   IPConfig /all
   ```

1. Update the TCP/IPv4 properties of the `HPC-Compute` NIC to include the Elastic IP address of the `Domain Controller` instance as the primary DNS\. Then add the additional DNS IP address from the previous step\.

1. Join the machine to the `hpc.local` domain using the credentials for `hpc.local\administrator` \(the domain administrator account\)\.

1. Add `hpc.local\hpcuser` as the local administrator\. When prompted for credentials, use `hpc.local\administrator`, and then restart\.

1. Connect to `HPC-Compute` as `hpc.local\hpcuser`\.

**To install the HPC Pack on the compute node**

1. Connect to your `HPC-Compute` instance using the `hpc.local\hpcuser` account\.

1. Using **Server Manager**, turn off Internet Explorer Enhanced Security Configuration \(IE ESC\) for Administrators\. 

   1. In **Server Manager**, under **Security Information**, choose **Configure IE ESC**\. 

   1. Turn off IE ESC for administrators\. 

1. Install the HPC Pack on `HPC-Compute`\. 

   1. Download the HPC Pack to `HPC-Compute` from the [Microsoft Download Center](https://www.microsoft.com/en-us/download/default.aspx)\. Choose the HPC Pack for the version of Windows Server on `HPC-Compute`\.

   1. Extract the files to a folder, open the folder, and double\-click **setup\.exe**\.

   1. On the **Installation** page, select **Join an existing HPC cluster by creating a new compute node**, and then choose **Next**\.

   1. Specify the fully\-qualified name of the `HPC-Head` instance, and then choose the defaults\.

   1. Complete the wizard\.

To complete your cluster configuration, from the head node, add the compute node to your cluster\.

**To add the compute node to your cluster**

1. Connect to the `HPC-Head` instance as `hpc.local\hpcuser`\.

1. Open **HPC Cluster Manager**\.

1. Select **Node Management**\.

1. If the compute node displays in the **Unapproved** bucket, right\-click the node that is listed and select **Add Node**\.

   1. Select **Add compute nodes or broker nodes that have already been configured**\.

   1. Select the check box next to the node and choose **Add**\.

1. Right\-click the node and choose **Bring Online**\.

## Step 5: Scale Your HPC Compute Nodes \(Optional\)<a name="scale_cluster"></a>

**To scale your compute nodes**

1. Connect to the `HPC-Compute` instance as `hpc.local\hpcuser`\.

1. Delete any files you downloaded locally from the HP Pack installation package\. \(You have already run setup and created these files on your image so they do not need to be cloned for an AMI\.\) 

1. From `C:\Program Files\Amazon\Ec2ConfigService` open the file `sysprep2008.xml`\. 

1. At the bottom of `<settings pass="specialize">`, add the following section\. Make sure to replace *hpc\.local*, *password*, and *hpcuser* to match your environment\.

   ```
   <component name="Microsoft-Windows-UnattendedJoin" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35"
   language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" 
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
       <Identification>
           <UnsecureJoin>false</UnsecureJoin>
           <Credentials>
               <Domain>hpc.local</Domain>
               <Password>password</Password>
               <Username>hpcuser</Username>
           </Credentials>
           <JoinDomain>hpc.local</JoinDomain>
       </Identification>
   </component>
   ```

1. Save `sysprep2008.xml`\.

1. Choose **Start**, **All Programs**, **EC2ConfigService Settings**\. 

   1. Choose the **General** tab, and clear the **Set Computer Name** check box\. 

   1. Choose the **Bundle** tab, and then choose **Run Sysprep and Shutdown Now**\. 

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **Instances**\.

1. Wait for the instance status to show **stopped**\.

1. Select the instance, choose **Actions**, **Image**, **Create Image**\.

1.  Specify an image name and image description, and then choose **Create Image** to create an AMI from the instance\. 

1. Start the original `HPC-Compute` instance that was shut down\.

1. Connect to the head node using the `hpc.local\hpcuser` account\.

1. From **HPC Cluster Manager**, delete the old node that now appears in an error state\.

1. In the Amazon EC2 console, in the navigation pane, choose **AMIs**\.

1. Use the AMI you created to add additional nodes to the cluster\.

You can launch additional compute nodes from the AMI that you created\. These nodes are automatically joined to the domain, but you must add them to the cluster as already configured nodes in **HPC Cluster Manager** using the head node and then bring them online\.