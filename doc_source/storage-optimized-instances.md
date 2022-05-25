# Storage optimized instances<a name="storage-optimized-instances"></a>

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage\. They are optimized to deliver tens of thousands of low\-latency, random I/O operations per second \(IOPS\) to applications\.<a name="d2-instances"></a>

**D2 instances**

These instances are well suited for the following:
+ Massive parallel processing \(MPP\) data warehouse
+ MapReduce and Hadoop distributed computing
+ Log or data processing applications<a name="d3-instances"></a>

**D3 and D3en instances**

These instances offer scale out of instance storage and are well suited for the following:
+ Distributed file systems for Hadoop workloads
+ File storage workloads such as GPFC and BeeFS
+ Large data lakes for HPC workloads<a name="h1-instances"></a>

**H1 instances**

These instances are well suited for the following:
+ Data\-intensive workloads such as MapReduce and distributed file systems
+ Applications requiring sequential access to large amounts of data on direct\-attached instance storage
+ Applications that require high\-throughput access to large quantities of data<a name="i3-instances"></a>

**I3 and I3en instances**

These instances are well suited for the following:
+ High frequency online transaction processing \(OLTP\) systems
+ Relational databases
+ NoSQL databases
+ Cache for in\-memory databases \(for example, Redis\)
+ Data warehousing applications
+ Distributed file systems

Bare metal instances provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 I3 Instances](https://aws.amazon.com/ec2/instance-types/i3)\.
<a name="i4i-instances"></a>
**I4i instances**  
These instances are well suited for I/O intensive workloads that require small to medium sized data sets on local storage, such as transactional databases and NoSQL databases\.

For more information, see [Amazon EC2 I4i Instances](https://aws.amazon.com/ec2/instance-types/i4i)\.

**Topics**
+ [Hardware specifications](#storage-instances-hardware)
+ [Instance performance](#storage-performance)
+ [Network performance](#storage-network-performance)
+ [SSD I/O performance](#storage-instances-diskperf)
+ [Instance features](#storage-instances-features)
+ [Release notes](#storage-instance-release-notes)

## Hardware specifications<a name="storage-instances-hardware"></a>

The following is a summary of the hardware specifications for storage optimized instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| d2\.xlarge | 4 | 30\.5 | 
| d2\.2xlarge | 8 | 61 | 
| d2\.4xlarge | 16 | 122 | 
| d2\.8xlarge | 36 | 244 | 
| d3\.xlarge | 4 | 32 | 
| d3\.2xlarge | 8 | 64 | 
| d3\.4xlarge | 16 | 128 | 
| d3\.8xlarge | 32 | 256 | 
| d3en\.large | 2 | 8 | 
| d3en\.xlarge | 4 | 16 | 
| d3en\.2xlarge | 8 | 32 | 
| d3en\.4xlarge | 16 | 64 | 
| d3en\.6xlarge | 24 | 96 | 
| d3en\.8xlarge | 32 | 128 | 
| d3en\.12xlarge | 48 | 192 | 
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
| i3en\.metal | 96 | 768 | 
| i4i\.large | 2 | 16 | 
| i4i\.xlarge | 4 | 32 | 
| i4i\.2xlarge | 8 | 64 | 
| i4i\.4xlarge | 16 | 128 | 
| i4i\.8xlarge | 32 | 256 | 
| i4i\.16xlarge | 64 | 512 | 
| i4i\.32xlarge | 128 | 1,024 | 
| i4i\.metal | 128 | 1,024 | 

The storage optimized instances use the following processors\.

**Intel processors**
+ **Intel Xeon Scalable processors \(Haswell E5\-2676 v3\)**: D2
+ **Intel Xeon Scalable processors \(Broadwell E5\-2686 v4\)**: H1, I3
+ **Intel Xeon Scalable processors \(Skylake 8175M or Cascade Lake 8259CL\)**: I3en
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8259CL\)**: D3, D3en
+ **3rd generation Intel Xeon Scalable processors \(Ice Lake 8375C\)**: I4i

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance performance<a name="storage-performance"></a>

For instances with NVMe instance store volumes, be sure to use the AWS NVMe driver\. For more information, see [AWS NVMe drivers for Windows instances](aws-nvme-drivers.md)\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some storage optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## Network performance<a name="storage-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Windows](enhanced-networking.md)\.

The following is a summary of network performance for storage optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| d2\.xlarge | Moderate | [Intel 82599 VF](sriov-networking.md) | 
| d2\.2xlarge \| d2\.4xlarge | High | [Intel 82599 VF](sriov-networking.md) | 
| i3\.4xlarge and smaller \| i4i\.xlarge and smaller | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d2\.8xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 
| i3\.8xlarge \| h1\.8xlarge | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| i4i\.2xlarge | Up to 12 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d3\.4xlarge and smaller | Up to 15 Gbps † | [ENA](enhanced-networking-ena.md) | 
| i4i\.8xlarge | 18\.75 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.2xlarge and smaller \| i3en\.3xlarge and smaller \| i4i\.4xlarge  | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d3\.8xlarge \| d3en\.4xlarge \| h1\.16xlarge \| i3\.16xlarge \| i3\.metal \| i3en\.6xlarge  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
| i4i\.16xlarge | 37\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.6xlarge | 40 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3\.8xlarge \| d3en\.8xlarge \| i3en\.12xlarge  | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.12xlarge \| i4i\.32xlarge \| i4i\.metal | 75 Gbps | [ENA](enhanced-networking-ena.md) | 
|  i3en\.24xlarge \| i3en\.metal  | 100 Gbps | [ENA](enhanced-networking-ena.md) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.<a name="baseline-bandwidth"></a>


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| d3\.xlarge | 3 | 15 | 
| d3\.2xlarge | 6 | 15 | 
| d3\.4xlarge | 12\.5 | 15 | 
| d3en\.large | 3 | 25 | 
| d3en\.xlarge | 6 | 25 | 
| d3en\.2xlarge | 12\.5 | 25 | 
| i3\.large | \.75 | 10 | 
| i3\.xlarge | 1\.25 | 10 | 
| i3\.2xlarge | 2\.5 | 10 | 
| i3\.4xlarge | 5 | 10 | 
| i3en\.large | 2\.1 | 25 | 
| i3en\.xlarge | 4\.2 | 25 | 
| i3en\.2xlarge | 8\.4 | 25 | 
| i3en\.3xlarge | 12\.5 | 25 | 
| i4i\.large | \.78125 | 10 | 
| i4i\.xlarge | 1\.875 | 10 | 
| i4i\.2xlarge | 4\.687 | 12 | 
| i4i\.4xlarge | 9\.375 | 25 | 

## SSD I/O performance<a name="storage-instances-diskperf"></a>

The primary data storage for D2, D3, and D3en instances is HDD instance store volumes\. The primary data storage for I3 and I3en instances is non\-volatile memory express \(NVMe\) SSD instance store volumes\.

Instance store volumes persist only for the life of the instance\. When you stop, hibernate, or terminate an instance, the applications and data in its instance store volumes are erased\. We recommend that you regularly back up or replicate important data in your instance store volumes\. For more information, see [Amazon EC2 instance store](InstanceStorage.md) and [SSD instance store volumes](ssd-instance-store.md)\.

If you use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| i3\.large | 100,125 | 35,000 | 
| i3\.xlarge | 206,250 | 70,000 | 
| i3\.2xlarge | 412,500 | 180,000 | 
| i3\.4xlarge | 825,000 | 360,000 | 
| i3\.8xlarge | 1,650,000 | 720,000 | 
| i3\.16xlarge | 3,300,000 | 1,400,000 | 
| i3\.metal | 3,300,000 | 1,400,000 | 
| i3en\.large | 42,500 | 32,500 | 
| i3en\.xlarge | 85,000 | 65,000 | 
| i3en\.2xlarge | 170,000 | 130,000 | 
| i3en\.3xlarge | 250,000 | 200,000 | 
| i3en\.6xlarge | 500,000 | 400,000 | 
| i3en\.12xlarge | 1,000,000 | 800,000 | 
| i3en\.24xlarge | 2,000,000 | 1,600,000 | 
| i3en\.metal | 2,000,000 | 1,600,000 | 
| i4i\.large | 50,000 | 27,500 | 
| i4i\.xlarge | 100,000 | 55,000 | 
| i4i\.2xlarge | 200,000 | 110,000 | 
| i4i\.4xlarge | 400,000 | 220,000 | 
| i4i\.8xlarge | 800,000 | 440,000 | 
| i4i\.16xlarge | 1,600,000 | 880,000 | 
| i4i\.32xlarge | 3,200,000 | 1,760,000 | 
| i4i\.metal | 3,200,000 | 1,760,000 | 

As you fill your SSD\-based instance store volumes, the I/O performance that you get decreases\. This is due to the extra work that the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance features<a name="storage-instances-features"></a>

The following is a summary of features for storage optimized instances\.


|  | EBS only | Instance store | Placement group | 
| --- | --- | --- | --- | 
| D2 | No | HDD  | Yes | 
| D3 | No | HDD \*  | Yes | 
| D3en | No | HDD \*  | Yes | 
| H1 | No | HDD \* | Yes | 
| I3 | No | NVMe \* | Yes | 
| I3en | No | NVMe \* | Yes | 
| I4i | No | NVMe \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Windows instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Release notes<a name="storage-instance-release-notes"></a>
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The current [AWS Windows AMIs](windows-ami-version-history.md) meet these requirements\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\.
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ The `d3.8xlarge` and `d3en.12xlarge` instances support a maximum of three attachments, including the root volume\. If you exceed the attachment limit when you add a network interface or EBS volume, this causes attachment issues on your instance\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.