# Amazon EBS Volume Performance on Windows Instances<a name="EBSPerformance"></a>

Several factors, including I/O characteristics and the configuration of your instances and volumes, can affect the performance of Amazon EBS\. Customers who follow the guidance on our Amazon EBS and Amazon EC2 product detail pages typically achieve good performance out of the box\. However, there are some cases where you may need to do some tuning in order to achieve peak performance on the platform\. This topic discusses general best practices as well as performance tuning that is specific to certain use cases\. We recommend that you tune performance with information from your actual workload, in addition to benchmarking, to determine your optimal configuration\. After you learn the basics of working with EBS volumes, it's a good idea to look at the I/O performance you require and at your options for increasing Amazon EBS performance to meet those requirements\.

**Topics**
+ [Amazon EBS Performance Tips](#tips)
+ [Amazon EC2 Instance Configuration](ebs-ec2-config.md)
+ [I/O Characteristics and Monitoring](ebs-io-characteristics.md)
+ [Initializing Amazon EBS Volumes](ebs-initialize.md)
+ [RAID Configuration on Windows](raid-config.md)
+ [Benchmark EBS Volumes](benchmark_procedures.md)

## Amazon EBS Performance Tips<a name="tips"></a>

These tips represent best practices for getting optimal performance from your EBS volumes in a variety of user scenarios\.

### Use EBS\-Optimized Instances<a name="optimize"></a>

On instances without support for EBS\-optimized throughput, network traffic can contend with traffic between your instance and your EBS volumes; on EBS\-optimized instances, the two types of traffic are kept separate\. Some EBS\-optimized instance configurations incur an extra cost \(such as C3, R3, and M3\), while others are always EBS\-optimized at no extra cost \(such as M4, C4, C5, and D2\)\. For more information, see [Amazon EC2 Instance Configuration](ebs-ec2-config.md)\.

### Understand How Performance is Calculated<a name="performance_calculation"></a>

When you measure the performance of your EBS volumes, it is important to understand the units of measure involved and how performance is calculated\. For more information, see [I/O Characteristics and Monitoring](ebs-io-characteristics.md)\.

### Understand Your Workload<a name="workload_types"></a>

There is a relationship between the maximum performance of your EBS volumes, the size and number of I/O operations, and the time it takes for each action to complete\. Each of these factors \(performance, I/O, and latency\) affects the others, and different applications are more sensitive to one factor or another\. 

### Be Aware of the Performance Penalty When Initializing Volumes from Snapshots<a name="initialize"></a>

There is a significant increase in latency when you first access each block of data on a new EBS volume that was restored from a snapshot\. You can avoid this performance hit by accessing each block prior to putting the volume into production\. This process is called *initialization* \(formerly known as pre\-warming\)\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

### Factors That Can Degrade HDD Performance<a name="snapshotting_latency"></a>

When you create a snapshot of a Throughput Optimized HDD \(`st1`\) or Cold HDD \(`sc1`\) volume, performance may drop as far as the volume's baseline value while the snapshot is in progress\. This behavior is specific to these volume types\. Other factors that can limit performance include driving more throughput than the instance can support, the performance penalty encountered while initializing volumes restored from a snapshot, and excessive amounts of small, random I/O on the volume\. For more information about calculating throughput for HDD volumes, see [Amazon EBS Volume Types ](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)\. 

Your performance can also be impacted if your application isn’t sending enough I/O requests\. This can be monitored by looking at your volume’s queue length and I/O size\. The queue length is the number of pending I/O requests from your application to your volume\. For maximum consistency, HDD\-backed volumes must maintain a queue length \(rounded to the nearest whole number\) of 4 or more when performing 1 MiB sequential I/O\. For more information about ensuring consistent performance of your volumes, see [I/O Characteristics and Monitoring](ebs-io-characteristics.md)

### Use RAID 0 to Maximize Utilization of Instance Resources<a name="RAID"></a>

Some instance types can drive more I/O throughput than what you can provision for a single EBS volume\. You can join multiple `gp2`, `io1`, `st1`, or `sc1` volumes together in a RAID 0 configuration to use the available bandwidth for these instances\. For more information, see [RAID Configuration on Windows](raid-config.md)\.

### Track Performance Using Amazon CloudWatch<a name="cloudwatch"></a>

Amazon Web Services provides performance metrics for Amazon EBS that you can analyze and view with Amazon CloudWatch and status checks that you can use to monitor the health of your volumes\. For more information, see [Monitoring the Status of Your Volumes](monitoring-volume-status.md)\.