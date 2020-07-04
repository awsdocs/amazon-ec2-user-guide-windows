# Best Practices and Recommendations for SQL Server Clustering in EC2<a name="aws-sql-clustering"></a>

SQL Always On clustering offers high availability without the requirement for shared storage\. The list of practices in this topic, in addition to the prerequisites listed at [Prerequisites, Restrictions, and Recommendations for Always On availability groups](https://msdn.microsoft.com/en-us/library/ff878487.aspx), can help you get the best results when operating a SQL Server Always On cluster on AWS\. The practices listed in this topic also offer a method to gather logs\.

**Note**  
When nodes are deployed in different Availability Zones, or in different subnets within the same zone, they should be treated as a multi\-subnet cluster\. Keep this in mind as you apply best practices and when you address possible failure scenarios\.

**Topics**
+ [Assigning IP Addresses](#sql-ip-assignment)
+ [Cluster Properties](#sql-clustering-cluster-properties)
+ [Cluster Quorum Votes and 50/50 Splits in a Multi\-Site Cluster](#sql-clustering-quorum)
+ [DNS Registration](#sql-dns-registration)
+ [Elastic Network Adapters \(ENAs\)](#sql-clustering-ena)
+ [Multi\-Site Clusters and EC2 Instance Placement](#sql-multi-site-clusters)
+ [Instance Type Selection](#sql-clustering-instance-type)
+ [Assigning Elastic Network Interfaces and IPs to the Instance](#sql-clustering-assigning-ENI-IP)
+ [Heartbeat Network](#sql-clustering-heartbeat)
+ [Configuring the Network Adapter in the OS](#sql-clustering-network-adapter)
+ [IPv6](#sql-clustering-ipv6)
+ [Host Record TTL for SQL Availability Group Listeners](#sql-clustering-ttl)
+ [Logging](#sql-clustering-logging)
+ [NetBIOS over TCP](#sql-clustering-2012r2-netbios)
+ [NetFT Virtual Adapter](#sql-clustering-2012r2-netft)
+ [Setting Possible Owners](#sql-owners)
+ [Tuning the Failover Thresholds](#sql-failover-thresholds)
+ [Witness Importance and Dynamic Quorum Architecture](#sql-clustering-file-share-witness)
+ [Troubleshooting](#sql-troubleshooting)

## Assigning IP Addresses<a name="sql-ip-assignment"></a>

Each cluster node should have one elastic network interface assigned that includes three private IP addresses on the subnet: a primary IP address, a cluster IP address, and an Availability Group IP address\. The operating system \(OS\) should have the NIC configured for DHCP\. It should not be set for a static IP address because the IP addresses for the cluster IP and Availability Group will be handled virtually in the Failover Cluster Manager\. The NIC can be configured for a static IP as long as it is configured to only use the primary IP of **eth0**\. If the other IPs are assigned to the NIC, it can cause network drops for the instance during failover events\.

When the network drops because the IPs are incorrectly assigned, or when there is a failover event or network failure, it is not uncommon to see the following event log entries at the time of failure\.

```
Isatap interface isatap.{9468661C-0AEB-41BD-BB8C-1F85981D5482} is no longer active.
```

```
Isatap interface isatap.{9468661C-0AEB-41BD-BB8C-1F85981D5482} with address fe80::5efe:169.254.1.105 has been brought up.
```

Because these messages seem to describe network issues, it is easy to mistake the cause of the outage or failure as a network error\. However, these errors describe a symptom, rather than cause, of the failure\. ISATAP is a tunneling technology that uses IPv6 over IPv4\. When the IPv4 connection fails, the ISATAP adapter also fails\. When the network issues are resolved, these entries should no longer appear in the event logs\. Alternately, you can eliminate network errors by safely disabling ISATAP with the following command\.

```
netsh int ipv6 isatap set state disabled
```

When you run this command, the adapter is removed from Device Manager\. This command should be run on all nodes\. It does not impact the ability of the cluster to function\. Instead, when the command has been run, ISATAP is no longer used\. However, because this command might cause unknown impacts on other applications that leverage ISATAP, you should test it\. 

## Cluster Properties<a name="sql-clustering-cluster-properties"></a>

To see the complete cluster configuration, run the following PowerShell command\.

```
Get-Cluster | Format-List -Property *
```

## Cluster Quorum Votes and 50/50 Splits in a Multi\-Site Cluster<a name="sql-clustering-quorum"></a>

To learn how the cluster quorum works and what to expect if a failure occurs, see [Understanding Cluster and Pool Quorum](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/understand-quorum)\.

## DNS Registration<a name="sql-dns-registration"></a>

In Windows Server 2012, Failover Clustering, by default, attempts to register each DNS node under the cluster name\. This is acceptable for applications that are aware the SQL target is configured for multi\-site\. However, when the client is not configured this way, it can result in timeouts, delays, and application errors due to attempts to connect to each individual node and failing on the inactive ones\. To prevent these problems, the Cluster Resource parameter `RegisterAllProvidersIp` must be changed to **0**\. For more information, see [RegisterAllProvidersIP Setting](https://msdn.microsoft.com/en-us/library/hh213080.aspx#RegisterAllProvidersIP) and [Multi\-subnet Clustered SQL \+ RegisterAllProvidersIP \+ SharePoint 2013](https://blogs.msdn.microsoft.com/sambetts/2014/02/04/multi-subnet-clustered-sql-registerallprovidersip-sharepoint-2013/)\.

The `RegisterAllProvidersIp` can be modified with the following PowerShell script\. 

```
Import-Module FailoverClusters  
$cluster = (Get-ClusterResource | where {($_.ResourceType -eq "Network Name") -and ($_.OwnerGroup -ne "Cluster Group")}).Name
Get-ClusterResource $cluster | Set-ClusterParameter RegisterAllProvidersIP 0  
Get-ClusterResource $cluster |Set-ClusterParameter HostRecordTTL 300  
Stop-ClusterResource $cluster
Start-ClusterResource $cluster
```

In addition to setting the Cluster Resource parameter to **0**, you must ensure that the cluster has permissions to modify the DNS entry for your cluster name\. 

1. Log into the Domain Controller \(DC\) for the domain, or a server that hosts the forward lookup zone for the domain\.

1. Launch the DNS Management Console and locate the `A` record for the cluster\.

1. Right\-click the `A` record and choose **Properties**\.

1. Choose **Security**\.

1. Choose **Add**\.

1. Choose **Object Types\.\.\.**, select the box for **Computers**, and choose **OK**\.

1. Enter the name of the cluster resource object and choose **Check name** and **OK if resolve**\.

1. Select the check box for **Full Control**\.

1. Choose **OK**\.

## Elastic Network Adapters \(ENAs\)<a name="sql-clustering-ena"></a>

AWS has identified known issues with some clustering workloads running on ENA driver version 1\.2\.3\. We recommend upgrading to version 1\.5\.0 or later and adjusting settings on the NIC in the OS\. For the latest versions, see [Amazon ENA Driver Versions](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking-ena.html#ena-adapter-driver-versions)\. The first setting, which applies to all systems, increases Receive Buffers, which can be done with the following example PowerShell command\.

```
Set-NetAdapterAdvancedProperty -Name (Get-NetAdapter | Where-Object {$_.InterfaceDescription -like '*Elastic*'}).Name -DisplayName "Receive Buffers" -DisplayValue 8192
```

For instances with more than 16 vCPUs, we recommend preventing RSS from running on CPU 0\.

Run the following command\.

```
Set-NetAdapterRss -name (Get-NetAdapter | Where-Object {$_.InterfaceDescription -like '*Elastic*'}).Name -Baseprocessorgroup 0 -BaseProcessorNumber 1
```

## Multi\-Site Clusters and EC2 Instance Placement<a name="sql-multi-site-clusters"></a>

Each cluster is considered a [multi\-site cluster](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd197575(v=ws.10))\. The EC2 service does not share IP addresses virtually\. Each node must be in a unique [subnet](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\. Though not required, we recommend that each node also be in a unique [Availability Zone](EC2Win_Infrastructure.md#EC2Win_Regions)\.

## Instance Type Selection<a name="sql-clustering-instance-type"></a>

The type of instance recommended for Windows Server Failover Clustering depends on the workload\. For production workloads, we recommend instances that support [EBS Optimization](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) and [Enhanced Networking](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enhanced-networking.html)\.

## Assigning Elastic Network Interfaces and IPs to the Instance<a name="sql-clustering-assigning-ENI-IP"></a>

Each node in an EC2 cluster should have only one attached elastic network interface\. The network interface should have a minimum of two assigned private IP addresses\. However, for workloads that use Availability Groups, such as SQL Always On, you must include an additional IP address for each Availability Group\. The primary IP address is used for accessing and managing the server, the secondary IP address is used as the cluster IP address, and each additional IP address is assigned to Availability Groups, as needed\.

## Heartbeat Network<a name="sql-clustering-heartbeat"></a>

Some Microsoft documentation recommends using a dedicated [heartbeat network](https://support.microsoft.com/en-us/help/258750/recommended-private-heartbeat-configuration-on-a-cluster-server)\. However, this recommendation is not applicable to EC2\. With EC2, while you can assign and use a second elastic network interface for the heartbeat network, it uses the same infrastructure and shares bandwidth with the primary network interface\. Therefore, traffic within the infrastructure cannot be prioritized, and cannot benefit from a dedicated network interface\.

## Configuring the Network Adapter in the OS<a name="sql-clustering-network-adapter"></a>

The NIC in the OS can keep using DHCP as long as the DNS servers that are being retrieved from the DHCP Options Set allow for the nodes to resolve each other\. You can set the NIC to be configured statically\. When completed, you then manually configure only the primary IP address for the elastic network interface\. Failover Clustering manages and assigns additional IP addresses, as needed\.

For all instance types, you can increase the maximum transmission unit \(MTU\) on the network adapter to 9001 to support [Jumbo Frames](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/network_mtu.html)\. This configuration reduces fragmentation of packets wherever Jumbo Frames are supported\. The following example shows how to use PowerShell to configure Jumbo Frames for an Elastic Network Adapter\.

```
Get-NetAdapter | Set-NetAdapterAdvancedProperty -DisplayName "MTU" -DisplayValue 9001
```

## IPv6<a name="sql-clustering-ipv6"></a>

Microsoft does not recommend disabling IPv6 in a Windows Cluster\. While Failover Clustering works in an IPv4\-only environment, Microsoft tests clusters with IPv6 enabled\. See [Failover Clustering and IPv6 in Windows Server 2012 R2](https://techcommunity.microsoft.com/t5/Failover-Clustering/Failover-Clustering-and-IPv6-in-Windows-Server-2012-R2/ba-p/371912) for details\.

## Host Record TTL for SQL Availability Group Listeners<a name="sql-clustering-ttl"></a>

Set the host record TTL to **300** seconds instead of the default 20 minutes \(1200 seconds\)\. For legacy client comparability, set `RegisterAllProvidersIP` to **0** for SQL Availability Group Listeners\. This is not required in all environments\. These settings are important because some legacy client applications cannot use `MultiSubnetFailover` in their connection strings\. See [HostRecordTTL Setting](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server?view=sql-server-2017#HostRecordTTL) for more information\. When you change these settings, the Cluster Resource must be restarted\. The Cluster Group for the listener stops when the Cluster Resource is restarted, so it must be started\. If you do not start the Cluster Group, the Availability Group remains offline in a `RESOLVING` state\. The following are example PowerShell scripts for changing the TTL and `RegisterAllProvidersIP` settings\.

```
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0 
```

```
Get-ClusterResource yourListenerName|Set-ClusterParameter HostRecordTTL 300 
```

```
Stop-ClusterResource yourListenerName 
```

```
Start-ClusterResource yourListenerName 
```

```
Start-ClusterGroup yourListenerGroupName
```

## Logging<a name="sql-clustering-logging"></a>

The default logging level for the cluster log is **3**\. To increase the detail of log information, set the logging level to **5**\. See [Set\-ClusterLog](https://docs.microsoft.com/en-us/powershell/module/failoverclusters/set-clusterlog?view=win10-ps) for more information about the PowerShell cmdlet\.

```
Set-ClusterLog –Level 5
```

## NetBIOS over TCP<a name="sql-clustering-2012r2-netbios"></a>

On Windows Server 2012 R2, you can increase the speed of the failover process by disabling NetBIOS over TCP\. This feature was removed from Windows Server 2016\. You should test this procedure if you are using older operating systems in your environment\. For more information, see [Speeding Up Failover Tips\-n\-Tricks](https://techcommunity.microsoft.com/t5/Failover-Clustering/Speeding-Up-Failover-Tips-n-Tricks/ba-p/372086)\. The following is an example PowerShell command to disable NetBIOS over TCP\. 

```
Get-ClusterResource “Cluster IP Address” | Set-ClusterParameter EnableNetBIOS 0
```

## NetFT Virtual Adapter<a name="sql-clustering-2012r2-netft"></a>

For Windows Server versions earlier than 2016 and non\-Hyper\-V workloads, Microsoft recommends you enable the NetFT Virtual Adapter Performance Filter on the adapter in the OS\. When you enable the NetFT Virtual Adapter, internal cluster traffic is routed directly to the NetFT Virtual Adapter\. For more information, see [NetFT Virtual Adapter Performance Filter](https://techcommunity.microsoft.com/t5/Failover-Clustering/NetFT-Virtual-Adapter-Performance-Filter/ba-p/372090)\. You can enable NetFT Virtual Adapter by selecting the check box in the NIC properties, or by using the following PowerShell command\.

```
Get-NetAdapter | Set-NetAdapterBinding –ComponentID ms_netftflt –Enable $true
```

## Setting Possible Owners<a name="sql-owners"></a>

The Failover Cluster Manager can be configured so that each IP address specified on the Cluster Core Resources and Availability Group resources can be brought online only on the node to which the IP belongs\. When the Failover Cluster Manager is not configured for this and a failure occurs, there will be some delay in failover as the cluster attempts to bring up the IPs on nodes that do not recognize the address\. For more information, see [SQL Server Manages Preferred and Possible Owner Properties for AlwaysOn Availability Group/Role](https://blogs.msdn.microsoft.com/alwaysonpro/2014/02/28/sql-server-manages-preferred-and-possible-owner-properties-for-alwayson-availability-grouprole/)\.

Each resource in a cluster has a setting for Possible Owners\. This setting tells the cluster which nodes are permitted to “online” a resource\. Each node is running on a unique subnet in a VPC\. Because EC2 cannot share IPs between instances, the IP resources in the cluster can be brought online only by specific nodes\. By default, each IP address that is added to the cluster as a resource has every node listed as a Possible Owner\. This does not result in failures\. However, during expected and unexpected failures, you can see errors in the logs about conflicting IPs and failures to bring IPs online\. These errors can be ignored\. If you set the Possible Owner property, you can eliminate these errors entirely, and also prevent down time while the services are moved to another node\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/failover_cluster_manager_1.PNG)

## Tuning the Failover Thresholds<a name="sql-failover-thresholds"></a>

In Server 2012 R2, the network thresholds for the failover heartbeat network default to high values\. See [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) for details\. This potentially unreliable configuration \(for clusters with some distance between them\) was addressed in Server 2016 with an increase in the number of heartbeats\. It was discovered that clusters would fail over due to very brief transient network issues\. The heartbeat network is maintained with UDP 3343, which is traditionally far less reliable than TCP and more prone to incomplete conversations\. Although there are low\-latency connections between AWS Availability Zones, there are still geographic separations with a number of "hops" separating resources\. Within an Availability Zone, there may be some distance between clusters unless the customer is using Placement Groups or Dedicated Hosts\. As a result, there is a higher possibility for heartbeat failure with UDP than with TCP\-based heartbeats\.

The only time a cluster should fail over is when there is a legitimate outage, such as a service or node that experiences a hard failover, as opposed to a few UDP packets lost in transit\. To ensure legitimate outages, we recommend that you adjust the thresholds to match, or even exceed, the settings for Server 2016 listed in [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)\. You can change the settings with the following PowerShell commands\.

```
(get-cluster).SameSubnetThreshold = 10
```

```
(get-cluster).CrossSubnetThreshold = 20
```

When you set these values, unexpected failovers should be dramatically reduced\. You can fine\-tune these settings by increasing the delays between heartbeats\. However, we recommend that you send the heartbeats more frequently with greater thresholds\. Setting these thresholds even higher ensures that failovers occur only for hard failover scenarios, with longer delays before failing over\. You must decide how much down time is acceptable for your applications\.

After increasing the `SameSubnetThreshold` or `CrossSubnetThreshold`, we recommend that you increase the `RouteHistoryLength` to double the higher of the two values\. This ensures that there is sufficient logging for troubleshooting\. You can set the `RouteHistoryLength` with the following PowerShell command\.

```
(Get-Cluster).RouteHistoryLength = 20
```

## Witness Importance and Dynamic Quorum Architecture<a name="sql-clustering-file-share-witness"></a>

There is a difference between Disk Witness and File Share Witness\. Disk Witness keeps a backup of the cluster database while File Share Witness does not\. Both add a [vote to the cluster](#sql-clustering-quorum)\. You can use Disk Witness if you use iSCSI\-based storage\. For more about witness options, see [File Share witness vs Disk witness for local clusters](https://blogs.technet.microsoft.com/mspfe/2012/11/05/file-share-witness-vs-disk-witness-for-local-clusters/)\.

## Troubleshooting<a name="sql-troubleshooting"></a>

If you experience unexpected failovers, first make sure that you are not experiencing networking, service, or infrastructure issues\.

1. Check that your nodes are not experiencing network\-related issues\. 

1. Check driver updates\. If you are using outdated drivers on your instance, you should update them\. Updating your drivers might address bugs and stability issues that might be present in your currently installed version\.

1. Check for any possible resource bottlenecks that could cause an instance to become unresponsive, such as CPU and disk I/O\. If the node cannot service requests, it may appear to be down by the cluster service\.