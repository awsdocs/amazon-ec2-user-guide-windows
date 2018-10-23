# Operating System Optimizations<a name="enhanced-networking-os"></a>

To achieve the maximum network performance on instances with enhanced networking, you may need to modify the default operating system configuration\. We recommend the following configuration changes for applications that require high network performance\.Other optimizations \(such as turning on checksum offloading and enabling RSS, for example\) are already in place on official Windows AMIs\. 

**Note**  
TCP chimney offloading should be disabled in most use cases, and has been deprecated as of Windows Server 2016\.

In addition to these operating system optimizations, you should also consider the maximum transmission unit \(MTU\) of your network traffic, and adjust according to your workload and network architecture\. For more information, see [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](network_mtu.md)\.

## Configure RSS CPU Affinity<a name="windows-rss-cpu-affinity"></a>

Receive side scaling \(RSS\) is used to distribute network traffic CPU load across multiple processors\. By default, Amazon official Windows AMIs are configured with RSS enabled and allow up to eight RSS queues\. By defining CPU affinity for RSS queues as well as other processes that demand CPU, it is possible to spread the CPU load on multi\-core systems and allow more network traffic to be processed\. To avoid contention when using multiple elastic network interfaces, use the Set\-NetAdapterRSS command to manually assign different sets of processors to handle RSS queues for different interfaces\.

For example, in a 16\-core instance with two elastic network interfaces, the following two commands spread the load evenly:

```
Set-NetAdapterRss -Name NIC1 -NumaNode 0 -BaseProcessorNumber 1 -MaxProcessorNumber 8
Set-NetAdapterRss -Name NIC2 -NumaNode 0 -BaseProcessorNumber 9 -MaxProcessorNumber 16
```

For more information, see [Set\-NetAdapterRss](https://docs.microsoft.com/en-us/powershell/module/netadapter/set-netadapterrss?view=win10-ps)\.

## Set Device Processor Affinity<a name="windows-processor-affinity"></a>

 Hardware devices need access to the CPU to raise interrupts and communicate with the host OS\. In the event of heavy network traffic becoming CPU bound, it may be possible to smooth out the load by allocating separate cores to network devices, other hardware devices, and applications \(such as SQL\) running on the OS\.

Configure interrupt affinity on the ENA devices to ensure that interrupt requests are all handled by the same NUMA node \(setting `DevicePolicy` to `0x01` or `0x04`\)\. For more information, see [Interrupt Affinity](https://technet.microsoft.com/en-us/ff547969(v=vs.96)) in the Microsoft documentation\. When using multiple elastic network interfaces, we recommend that you configure them to use different NUMA groups, to further spread out the IRQ load\.

To minimize performance bottlenecks that are caused by processor interrupts, it is important to consider configuration changes for other sources of load on the machine\. For example, [SQL Server may be configured to bind affinity to certain cores](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/affinity-mask-server-configuration-option?view=sql-server-2017)\. When spreading load, keep in mind that inter\-node NUMA communication is much more expensive than intra\-node\. In a hypothetical server with 64 processors distributed into two NUMA nodes, two elastic network interfaces, a web server, and a SQL database, it's optimal to divide CPUs up logically, rather than allowing the operating system to handle the distribution\. 