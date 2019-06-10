# Storage Optimized Instances<a name="storage-optimized-instances"></a>

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage\. They are optimized to deliver tens of thousands of low\-latency, random I/O operations per second \(IOPS\) to applications\.<a name="d2-instances"></a>

**D2 Instances**

D2 instances are well suited for the following applications:
+ Massive parallel processing \(MPP\) data warehouse
+ MapReduce and Hadoop distributed computing
+ Log or data processing applications<a name="h1-instances"></a>

**H1 Instances**

H1 instances are well suited for the following applications:
+ Data\-intensive workloads such as MapReduce and distributed file systems
+ Applications requiring sequential access to large amounts of data on direct\-attached instance storage
+ Applications that require high\-throughput access to large quantities of data<a name="i3-instances"></a>

**I3 and I3en Instances**

These instances are well suited for the following applications:
+ High frequency online transaction processing \(OLTP\) systems
+ Relational databases
+ NoSQL databases
+ Cache for in\-memory databases \(for example, Redis\)
+ Data warehousing applications
+ Distributed file systems

`i3.metal` instances provide your applications with direct access to physical resources of the host server, such as processors and memory\. These instances are well suited for the following:
+ Workloads that require access to low\-level hardware features \(for example, Intel VT\) that are not available or fully supported in virtualized environments
+ Applications that require a non\-virtualized environment for licensing or support

For more information, see [Amazon EC2 I3 Instances](https://aws.amazon.com/ec2/instance-types/i3)\.

**Topics**
+ [Hardware Specifications](#storage-instances-hardware)
+ [Instance Performance](#storage-performance)
+ [Network Performance](#storage-network-performance)
+ [SSD I/O Performance](#i2-instances-diskperf)
+ [Instance Features](#storage-instances-features)
+ [Release Notes](#storage-instance-release-notes)

## Hardware Specifications<a name="storage-instances-hardware"></a>

The primary data storage for D2 instances is HDD instance store volumes\. The primary data storage for I3 instances is non\-volatile memory express \(NVMe\) SSD instance store volumes\.

Instance store volumes persist only for the life of the instance\. When you stop or terminate an instance, the applications and data in its instance store volumes are erased\. We recommend that you regularly back up or replicate important data in your instance store volumes\. For more information, see [Amazon EC2 Instance Store](InstanceStorage.md) and [SSD Instance Store Volumes](ssd-instance-store.md)\.

The following is a summary of the hardware specifications for storage optimized instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| d2\.xlarge | 4 | 30\.5 | 
| d2\.2xlarge | 8 | 61 | 
| d2\.4xlarge | 16 | 122 | 
| d2\.8xlarge | 36 | 244 | 
| h1\.2xlarge | 8 | 32 | 
| h1\.4xlarge | 16 | 64 | 
| h1\.8xlarge | 32 | 128 | 
| h1\.16xlarge | 64 | 256 | 
| i3\.large | 2 | 15\.25 | 
| i3\.xlarge | 4 | 30\.5 | 
| i3\.2xlarge | 8 | 61 | 
| i3\.4xlarge | 16 | 122 | 
| i3\.8xlarge | 32 | 244 | 
| i3\.16xlarge | 64 | 488 | 
| i3\.metal | 72 | 512 | 
| i3en\.large | 2 | 16 | 
| i3en\.xlarge | 4 | 32 | 
| i3en\.2xlarge | 8 | 64 | 
| i3en\.3xlarge | 12 | 96 | 
| i3en\.6xlarge | 24 | 192 | 
| i3en\.12xlarge | 48 | 384 | 
| i3en\.24xlarge | 96 | 768 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="storage-performance"></a>

For instances with NVMe instance store volumes, be sure to use the AWS NVMe driver\. For more information, see [AWS NVMe Drivers for Windows Instances](aws-nvme-drivers.md)\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some storage optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

## Network Performance<a name="storage-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Windows](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for storage optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `i3.4xlarge` and smaller  |  Up to 10 Gbps, use network I/O credit mechanism  | [ENA](enhanced-networking-ena.md) | 
|  `i3.8xlarge` \| `h1.8xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `i3en.3xlarge` and smaller  |  Up to 25 Gbps, use network I/O credit mechanism  | [ENA](enhanced-networking-ena.md) | 
|  `i3.16xlarge` \| `i3.metal` \| `i3en.6xlarge \| ``h1.16xlarge`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `i3en.12xlarge`  |  50 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `i3en.24xlarge`  |  100 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `d2.xlarge`  |  Moderate  | [Intel 82599 VF](sriov-networking.md) | 
| d2\.2xlarge \| d2\.4xlarge |  High  | [Intel 82599 VF](sriov-networking.md) | 
| d2\.8xlarge |  10 Gbps  | [Intel 82599 VF](sriov-networking.md) | 

## SSD I/O Performance<a name="i2-instances-diskperf"></a>

If you use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
|  `i3.large` \*  |  100,125  |  35,000  | 
|  `i3.xlarge` \*  |  206,250  |  70,000  | 
|  `i3.2xlarge`  |  412,500  |  180,000  | 
|  `i3.4xlarge`  |  825,000  |  360,000  | 
|  `i3.8xlarge`  |  1\.65 million  |  720,000  | 
|  `i3.16xlarge`  |  3\.3 million  |  1\.4 million  | 
| `i3en.large` \* | 42,500 | 32,500 | 
| `i3en.xlarge` \* | 85,000 | 65,000 | 
| `i3en.2xlarge` \* | 170,000 | 130,000 | 
| `i3en.3xlarge` | 250,000 | 200,000 | 
| `i3en.6xlarge` | 500,000 | 400,000 | 
| `i3en.12xlarge` | 1 million | 800,000 | 
| `i3en.24xlarge` | 2 million | 1\.6 million | 

\* For these instances, you can get up to the specified performance\.

As you fill your SSD\-based instance store volumes, the I/O performance that you get decreases\. This is due to the extra work that the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance Features<a name="storage-instances-features"></a>

The following is a summary of features for storage optimized instances:


|  | EBS only | Instance store | Placement group | 
| --- | --- | --- | --- | 
| D2 | No | HDD  | Yes | 
| H1 | No | HDD \* | Yes | 
| I3 | No | NVMe \* | Yes | 
| I3en | No | NVMe \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Windows Instances](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Release Notes<a name="storage-instance-release-notes"></a>
+ You must launch storage optimized instances using an HVM AMI\. 
+ The following are requirements for I3en and `i3.metal` instances:
  + NVMe drivers must be installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.
  + Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers must be installed\.

  The following AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 or later
  + Red Hat Enterprise Linux 7\.4 or later
  + SUSE Linux Enterprise Server 12 SP2 or later
  + CentOS 7 or later
  + FreeBSD 11\.1 or later
+ Launching an `i3.metal` instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\.
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.