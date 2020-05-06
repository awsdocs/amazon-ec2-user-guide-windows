# Tutorial: Deploy Storage Spaces Direct \(S2D\) on Amazon EC2<a name="ec2-tutorial-s2d"></a>

Storage Spaces Direct \(S2D\) is a highly\-scalable, software\-defined storage architecture that enables users to cluster local storage with features in Windows Server 2016\. S2D is an alternative to traditional SAN or NAS arrays\. It uses built\-in Windows features and tools to configure highly\-available storage that crosses multiple nodes in a cluster\. For more information, see [Storage Spaces Direct](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview) in the Microsoft documentation\.

The following diagram shows the architecture of S2D on Amazon EC2 Windows\. 

![\[Storage Spaces Direct on Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/S2D_on_EC2_Windows.png)

**Skill Level**

A basic understanding of Windows Server computing as well as how to create and manage domain\-joined Amazon EC2 Windows instances in a VPC is required\. Knowledge of the AWS Tools for Windows PowerShell and Windows Failover Clustering is helpful, but not required\. 

**What you will accomplish in this tutorial**
+ Provision a highly\-available storage cluster using [Storage Spaces Direct \(S2D\)](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview)\.
+ Provision a fault\-tolerant, cluster\-shared volume \(CSV\) on your cluster\.<a name="s2d-requirements"></a>

**Before you begin**
+ If you haven't done so already, open [https://aws\.amazon\.com/](https://aws.amazon.com/) and create an AWS account\.
+ Create a virtual private cloud \(VPC\) with a public subnet and two private subnets for your instances\. A third, private, subnet should be configured for AWS Directory Service\.
+ Select one of the latest Amazon Machine Images \(AMIs\) for Windows Server 2016\. You can use this AMI as is, or use it as the basis for your own custom AMI\. AWS recommends using the latest public EC2 Windows Server 2016 AMI\. 
+ Create an AWS Directory Service directory\. This is no longer a requirement for enabling the Failover Clustering feature in Windows Server 2016\. However, this tutorial assumes that your instances will be joined to an Active Directory domain, either on EC2 or AWS Managed Active Directory\. For more information, see [Getting Started with AWS Directory Service](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/getting_started.html) in the *AWS Directory Service Administration Guide*\.
+ Install and configure the AWS Tools for Windows PowerShell on your computer\. For more information, see the [AWS Tools for Windows PowerShell User Guide](https://docs.aws.amazon.com/powershell/latest/userguide/)\.

**Important considerations**
+ Stopping instances with [instance store volumes](InstanceStorage.md) can cause data loss if the data is not backed up or replicated\. The data in an instance store persists only during the lifetime of its associated instance\. If an instance reboots \(intentionally or unintentionally\), data in the instance store persists\. However, data in the instance store is lost under the following circumstances: 
  + The underlying disk drive fails\.
  + The instance stops\.
  + The instance terminates\.
+ Stopping too many instances in a cluster can cause data loss if the data is not backed up or replicated\. When you use S2D on AWS, as with any cluster, losing more nodes than your fault tolerance allows will result in loss of data\. One of the biggest risks to any cluster is losing all nodes\. Cluster redundancy protects against failures on a single instance \(or more, if your fault tolerance supports it\)\. However, you can lose data if the number of instances with failed disk drives in a cluster exceeds the fault tolerance\. You can also lose data if the number of stopped or terminated instances exceeds the fault tolerance\. To reduce risk, limit the number of people or systems that can stop or terminate instances in the cluster\. To mitigate the risk of terminating cluster node instances, [enable termination protection](terminating-instances.md#Using_ChangingDisableAPITermination) on these instances\. You can also configure [IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_tag-owner.html) to allow users to only restart nodes from the AWS console but not stop them\.
+ S2D does not protect against networking or data center failures that affect the entire cluster\. To reduce risk, consider using Dedicated Hosts to ensure that instances are not placed in the same rack\.

**Topics**
+ [Step 1: Launch and Domain Join Instances](#ec2-s2d-launch-instances)
+ [Step 2: Install and Configure Instance Prerequisites](#ec2-s2d-requirements)
+ [Step 3: Create Failover Cluster](#ec2-s2d-failover-cluster)
+ [Step 4: Enable S2D](#ec2-enable-s2d)
+ [Step 5: Provision Storage](#ec2-s2d-provision-storage)
+ [Step 6: Review the S2D Resources](#ec2-s2d-resources)
+ [Step 7: Clean Up](#ec2-s2d-clean-up)
+ [Additional Resources](#s2d-additional-resources)

The following diagram shows the architecture of a two node EC2 Windows S2D Cluster using a file share witness hosted on an existing bastion machine on AWS\.

![\[Two Node EC2 S2D Cluster\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/2_Nodes_EC2_Windows_S2D_Cluster.jpg)

## Step 1: Launch and Domain Join Instances<a name="ec2-s2d-launch-instances"></a>

All Nitro instances support Storage Spaces Direct using EBS and/or NVMe\. All current generation Xen\-based instances support Storage Spaces Direct with installation of AWS PV driver 8\.2\.3 and later\. The best performance for storage can be achieved using I3 instances because they provide local instance store with NVMe and high network performance\. Configuring S2D on Amazon EC2 requires a cluster of at least two, but no more than 16 instances\. These instances must each have at least two NVMe devices with high performance network connections between nodes, and run Windows Server 2016\. For more information, see [Storage Spaces Direct hardware requirements](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements) in the Microsoft documentation\.

We recommend the I3 instance size because it satisfies the [S2D hardware requirements](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements) and includes the largest and fastest instance store devices available\. It also includes enhanced networking, which maximizes the available resources for S2D per instance\. You can use M5D and R5D instance types, which have at least 2 NVMe disks, but local instance store disks will be used as cache disks for the storage spaces direct cluster and at least 2 EBS volumes will have to be added to each instance to provide capacity storage\. 

We recommend that you launch three instances to take advantage of three\-way mirroring [S2D fault tolerance](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance), which enables you to conduct maintenance on a single node while maintaining fault tolerance in your cluster if a witness such as a file share witness is configured\. You can also use two\-way mirroring with two instances as a less expensive solution, but a witness will be necessary and high availability will not be maintained during maintenance on a cluster node\. 

We will deploy a two node cluster architecture using a file share witness hosted on an existing bastion machine that acts as our administration workstation\. Each cluster node must be deployed in a different subnet\. This architecture will be deployed into a single availability zone because Microsoft does not currently support stretch cluster with Storage Spaces Direct\. However, the performance of a single availability zone and multi\-availability zones are exactly the same as a result of our very low\-latency and high\-bandwidth design for availability zones\.

**To launch instances for your cluster**

1. Using the Amazon EC2 console or the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) cmdlet, launch two `i3.8xlarge` instances to create the cluster and a `t2.medium` instance as an administration workstation and to host the file share witness\. Use a different subnet for each instance\. If you wish to follow a logic for IP assignment, then define the primary private IP address at creation time\. In this case, you will need to define a secondary private IP address for each cluster node because the secondary IP will be assigned to the cluster VIP later\. 

   To create each instance with PowerShell, use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/New-EC2Instance.html) command\.

   ```
   New-EC2Instance -ImageId ami-c49c0dac -MinCount 1 -MaxCount 1 -KeyName myPSKeyPair -SecurityGroupId mySGID -InstanceType i3.8xlarge -SubnetId mysubnetID
   ```

   To create an AWS AD directory with PowerShell, use the [New\-DSMicrosoftAD](https://docs.aws.amazon.com/powershell/latest/reference/New-DSMicrosoftAD.html) command \(or, refer to [Create Your AWS Managed Microsoft AD Directory in AWS ](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/microsoftadbasestep2.html)\)\.

   ```
   New-DSMicrosoftAD -Name corp.example.com –ShortName corp –Password P@ssw0rd –Description “AWS DS Managed” - VpcSettings_VpcId vpc-xxxxxxxx -VpcSettings_SubnetId subnet-xxxxxxxx, subnet-xxxxxxxx
   ```

   We use the following S2D\-node1 network interface configuration:  
![\[S2D-node1 Network Interface Configuration\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/s2d_node1_nic.png)
**Note**  
Each role deployed on this cluster, such as a SQL Failover Cluster instance or file server, will require additional secondary IP addresses on each node\. The exception is the Scale\-Out File Server role, which does not require an access point\. 

   We use the following configuration:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-tutorial-s2d.html)

1. You can use seamless domain join at creation time to join instances to the domain\. If you want to join them to the domain after they are launched, use the [Add\-Computer](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/add-computer?view=powershell-5.1) command\. We recommend using AWS Systems Manager and [AWS Directory Service](https://aws.amazon.com/directoryservice/) to [seamlessly join EC2 instances to a domain](https://aws.amazon.com/blogs/aws/seamlessly-join-ec2-instances-to-a-domain/)\. 

   The steps in the remainder of this tutorial require execution with a domain account with local administrative privileges on each instance\. Rename the instances as you want them before moving to the configuration\. Ensure that your security groups and Windows firewalls are properly configured to allow remote PowerShell connection and cluster communications on these nodes\. 

## Step 2: Install and Configure Instance Prerequisites<a name="ec2-s2d-requirements"></a>

S2D requires File Services and Failover Clustering Window features, and at least one ten Gbps network interface\. We recommend that you configure SMB to use [SMB Multichannel](https://technet.microsoft.com/en-us/library/dn610980.aspx), with RSS client connection counts that match the RSS queue count of the enhanced network adapter\.

The following steps will be accomplished from the bastion instance ADM01\.

**To install required Windows features**
+ Install the File Services and Failover\-Clustering Windows features with the management tools on cluster nodes\. Install only failover management tools on ADM01\. 
**Note**  
Change "S2D\-Node1" and "S2D\-Node2" to reflect the computer names that you set for the two instances; otherwise, the values will not change\. 

  ```
  $nodes = "S2D-Node1", "S2D-Node2"
  foreach ($node in $nodes) {
      Install-WindowsFeature -ComputerName $node -Name File-Services, Failover-Clustering -IncludeManagementTools
  }
  Install-WindowsFeature -Name RSAT-Clustering
  ```

**To configure networking**

1. Enable multichannel and set the RSS Connection Count\.

   ```
   foreach ($node in $nodes) {
       Invoke-Command -ComputerName $node -ScriptBlock {    
           [int]$RssQCount = (Get-NetAdapterAdvancedProperty | Where DisplayName -like "Maximum Number of RSS Queues").RegistryValue | Select -First 1
           $Params = @{
               EnableMultiChannel                    = $true;
               ConnectionCountPerRssNetworkInterface = $RssQCount;
               Confirm                               = $false;
           }
           Set-SmbClientConfiguration @Params
       }
   }
   ```

1. Configure RSS\.

   ```
   foreach ($node in $nodes) {
       Invoke-Command -ComputerName $node -ScriptBlock { 
           Get-WmiObject –class Win32_processor | ft systemname, Name, DeviceID, NumberOfCores, NumberOfLogicalProcessors
           $maxvcpu = (Get-WmiObject –class Win32_processor).NumberOfLogicalProcessors
           Get-NetAdapter | Set-NetAdapterRss -BaseProcessorNumber 2 -MaxProcessors $maxvcpu
       }
   }
   ```
**Note**  
You will see a disconnection message when executing this command because the network adapter restarts after setting the RSS configuration\.

   Receive Side Scaling \(RSS\) is a very important technology in networking on Windows\. RSS ensures that incoming network traffic is spread among the available processors in the server for processing\. If RSS is not used, network processing is bound to one processor, which is limited to approximately 4GBps\. Currently, every NIC, by default, enables RSS, but the configuration is not optimized\. Every NIC is configured, by default, with “Base Processor” 0, which means it will start processing on processor 0 together with the others NICs \. To optimally configure RSS, start at processor 1 so we don't interfere with processes landing default on processor 0\.

1. Increase storage space I/O timeout value to 30 seconds \(recommended when configured into a guest cluster\)\.

   ```
   foreach ($node in $nodes) {
       Invoke-Command -ComputerName $node -ScriptBlock { 
           Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\spaceport\Parameters -Name HwTimeout -Value 0x00007530 -Verbose
       }
   }
   ```

1. Reboot all nodes to apply all of the changes\.

   ```
   Restart-Computer -ComputerName $nodes -Wait -For Wmi -Force
   ```

## Step 3: Create Failover Cluster<a name="ec2-s2d-failover-cluster"></a>

S2D is a feature that is enabled on an existing failover cluster\. After you enable S2D on a failover cluster, it takes control of the local storage of each node in the cluster\. For this reason, we recommend that you install a cluster with no storage at creation time, and then enable S2D\.

When you create a cluster on AWS, you must assign static IP addresses from each subnet from which a node is deployed\. From the console, they must be set as secondary private IP addresses on each node\. For this tutorial, we configured 172\.16\.1\.200 and 172\.16\.3\.200 upon deployment of each node\.

You can verify and review the cluster configuration with the built\-in [Test\-Cluster](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/test-cluster) command\. 

**Test and verify your cluster configuration**

1. Run the [Test\-Cluster](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/test-cluster) command with the `Storage Spaces Direct`, `Inventory`, `Network`,and `System Configuration` tests\.

   ```
   $report = Test-Cluster -Node $nodes -Include 'Storage Spaces Direct', 'Inventory', 'Network', 'System Configuration'
   ```

1. Review the test results\.

   ```
   $reportFilePath = $report.FullName
   Start-Process $reportFilePath
   ```

1. Create the cluster using [New\-Cluster](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/new-cluster)\. Virtual IPs must be assigned a secondary private IP address from the AWS Console to each respective node\. 

   ```
   $vips = "172.16.1.200", "172.16.3.200"
   New-Cluster -Name S2D -Node $nodes -StaticAddress $vips -NoStorage
   ```

1. Configure a file share witness\.

   ```
   New-Item -ItemType Directory -Path c:\Share\Witness
   [string]$DomainName = (Get-WmiObject win32_computersystem).domain
   New-SmbShare -Name fsw -Path c:\Share\Witness -FullAccess ($DomainName + "\Domain Computers")
   Set-ClusterQuorum -Cluster S2D -FileShareWitness \\$env:COMPUTERNAME\fsw
   ```

## Step 4: Enable S2D<a name="ec2-enable-s2d"></a>

When the cluster is ready, enable S2D on one of the nodes using [Enable\-ClusterS2D](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/enable-clusterstoragespacesdirect) as follows\. Because we have only one type of disk in our setup \(local NVMe\), we won't use any disks as a cache disk\. 

1. Enable S2D on i3 instance types using the [Enable\-ClusterS2D](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/enable-clusterstoragespacesdirect) command\.

   ```
   Enable-ClusterS2D -PoolFriendlyName S2DPool -Confirm:$false -SkipEligibilityChecks:$true -CimSession $nodes[0]
   ```

1. If you are using m5d or r5d instance types with NVMe and EBS, use NVMe disks as cache disks\. The command would look like this:

   ```
   Enable-ClusterS2D -PoolFriendlyName S2DPool -CacheDeviceModel "Amazon EC2 NVMe" -Confirm:$false -SkipEligibilityChecks:$true -CimSession $nodes[0]
   ```

## Step 5: Provision Storage<a name="ec2-s2d-provision-storage"></a>

To provision storage, create a storage pool and then create volumes in that pool\. To keep thing simple, by default, the [Enable\-ClusterS2D](https://technet.microsoft.com/en-us/itpro/powershell/windows/failoverclusters/enable-clusterstoragespacesdirect) command creates a pool using all of the disks available in the cluster\. With this command we configured the storage pool name as "S2D Pool\." 

After volumes are created, they become accessible to every node in the cluster\. The volumes can then be assigned to a specific role in the cluster, such as a file server role; or, they can be assigned as [cluster shared volumes](https://docs.microsoft.com/en-us/windows-server/failover-clustering/failover-cluster-csvs) \(CSV\)\. A CSV is accessible to the entire cluster, which means that every node in this cluster can write\-read to this volume\.

To improve performance, we recommend you use fixed provisioning and a ReFS file system for CSV\. Sector size depends on what type of workloads will be deployed on the cluster\. For more information on sector size, see [Cluster Size Recommendations for ReFS and NTFS](https://blogs.technet.microsoft.com/filecab/2017/01/13/cluster-size-recommendations-for-refs-and-ntfs/)\. For improved local read performance, we recommend that you align the CSV with the node hosting your application or workload\. You can have multiple CSV and multiple applications spread across nodes\. 

**Create a cluster shared volume \(CSV\)**
+ Use the [New\-Volume](https://docs.microsoft.com/en-us/powershell/module/storage/new-volume?view=win10-ps) command to create a new 1TB CSV\.

  ```
  $Params = @{
      FriendlyName            = 'CSV1';
      FileSystem              = 'CSVFS_ReFS';
      StoragePoolFriendlyName = 'S2DPool';
      Size                    = 1TB;
      AllocationUnitSize      = 65536;
      ProvisioningType        = 'Fixed';
      CimSession              = $nodes[0];
  }
  New-Volume @Params
  ```

## Step 6: Review the S2D Resources<a name="ec2-s2d-resources"></a>

The S2D resources that you configured are displayed in the Failover Cluster Manager\.

**To view your CSV**

1. Open Server Manager\.

1. Choose **Tools**, **Failover Cluster Manager**\.

1. Expand the name of the cluster, expand **Storage**, and choose **Disks**\.

   The friendly name, capacity, node hosting the CSV, and other data will be listed\. For more information on managing CSVs, see [Use Cluster Shared Volumes in a Failover Cluster](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612868(v=ws.11))\.

**To synthesize a load on your CSV**

Use a tool such as [Diskspd Utility](https://gallery.technet.microsoft.com/DiskSpd-a-robust-storage-6cd2f223)\. Connect to one of the cluster nodes with RDP and run the following with the Diskspd tool\.

```
$mycsv = (gci C:\ClusterStorage\ | select -First 1).Fullname
.\diskspd.exe -d60 -b4k -o1024 -t32 -L -Sh -r -w50 -W60 -c100G $mycsv\test.dat
```

**To view the S2D storage performance of the cluster**

Use the [Get\-StorageHealthReport](https://technet.microsoft.com/en-us/itpro/powershell/windows/storage/get-storagehealthreport) command to view the cluster performance on one of the cluster nodes\. 

1. Open a new PowerShell windows and start your synthesized workload\.

1. In your original PowerShell windows, run [Get\-StorageSubSystem](https://docs.microsoft.com/en-us/powershell/module/storage/Get-StorageSubSystem?view=win10-ps) \*cluster\* \| [Get\-StorageHealthReport](https://docs.microsoft.com/en-us/powershell/module/storage/Get-StorageHealthReport?view=win10-ps) to see the performance results of the storage subsystem while the workload is running\. 

```
PS C:\> Get-StorageSubSystem *cluster* | Get-StorageHealthReport
 
CPUUsageAverage                 :   60.44 %
 
CapacityPhysicalPooledAvailable :    9.82 GB
 
CapacityPhysicalPooledTotal     :    13.82 TB
 
CapacityPhysicalTotal           :    13.82 TB
 
CapacityPhysicalUnpooled        :        0 B
 
CapacityVolumesAvailable        :     1.89 TB
 
CapacityVolumesTotal            :        2 TB
 
IOLatencyAverage                :   257.56 ms
 
IOLatencyRead                   :   255.87 ms
 
IOLatencyWrite                  :   259.25 ms
 
IOPSRead                        : 64327.37 /S
 
IOPSTotal                       :128582.85 /S
 
IOPSWrite                       : 64255.49 /S
 
IOThroughputRead                :   251.28 MB/S
 
IOThroughputTotal               :   502.28 MB/S
 
IOThroughputWrite               :      251 MB/S
 
MemoryAvailable                 :   477.77 GB
 
MemoryTotal                     :      488 GB
```

## Step 7: Clean Up<a name="ec2-s2d-clean-up"></a>

If you followed the tutorial to create a highly available storage cluster using S2D in EC2 Windows, you created a Storage Spaces Direct cluster of two instances from a bastion server, which also serves as a file share witness to the cluster\. You are charged for each hour or partial hour that you keep your instances running\. When you no longer need your cluster, use the EC2 Console or the [AWS Tools for Windows](https://aws.amazon.com/powershell/) to delete the resources you created for this project\. Do this by deleting the cluster from the failover cluster management mmc, terminating the instances, and deleting the computer objects for the cluster and its respective nodes from your Active Directory\. 

## Additional Resources<a name="s2d-additional-resources"></a>

[Storage Spaces Direct Calculator \(Preview\)](https://s2dcalc.blob.core.windows.net/www/index.html)

[Planning Storage Spaces Direct](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)

[Storage Spaces Direct Overview](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview)

[Fault Tolerance and Storage Efficiency in Storage Spaces Direct](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance)