# Monitor network performance for your EC2 instance<a name="monitoring-network-performance-ena"></a>

The Elastic Network Adapter \(ENA\) driver publishes network performance metrics from the instances where they are enabled\. You can use these metrics to troubleshoot instance performance issues, choose the right instance size for a workload, plan scaling activities proactively, and benchmark applications to determine whether they maximize the performance available on an instance\.

Amazon EC2 defines network maximums at the instance level to ensure a high\-quality networking experience, including consistent network performance across instance sizes\. AWS provides maximums for the following for each instance:
+ **Bandwidth capability** – Each EC2 instance has a maximum bandwidth for aggregate inbound and outbound traffic, based on instance type and size\. Some instances use a network I/O credit mechanism to allocate network bandwidth based on average bandwidth utilization\. Amazon EC2 also has maximum bandwidth for traffic to AWS Direct Connect and the internet\. For more information, see [Amazon EC2 instance network bandwidth](ec2-instance-network-bandwidth.md)\.
+ **Packet\-per\-second \(PPS\) performance** – Each EC2 instance has a maximum PPS performance, based on instance type and size\.
+ **Connections tracked** – The security group tracks each connection established to ensure that return packets are delivered as expected\. There is a maximum number of connections that can be tracked per instance\. For more information, see [Security group connection tracking](security-group-connection-tracking.md)
+ **Link\-local service access** – Amazon EC2 provides a maximum PPS per network interface for traffic to services such as the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.

When the network traffic for an instance exceeds a maximum, AWS shapes the traffic that exceeds the maximum by queueing and then dropping network packets\. You can monitor when traffic exceeds a maximum using the network performance metrics\. These metrics inform you, in real time, of impact to network traffic and possible network performance issues\.

**Topics**
+ [Requirements](#network-performance-metrics-requirements)
+ [Metrics for the ENA driver](#network-performance-metrics)
+ [View the network performance metrics for your Windows instance](#view-network-performance-metrics)

## Requirements<a name="network-performance-metrics-requirements"></a>
+ Install ENA driver version 2\.2\.2 or later\. To verify the installed version, use Device Manager as follows\.

  1. Open Device Manager by running `devmgmt.msc`\.

  1. Expand **Network Adapters**\.

  1. Choose **Amazon Elastic Network Adapter**, **Properties**\.

  1. On the **Driver** tab, locate **Driver Version**\.

  To upgrade your ENA driver, see [Enhanced networking](enhanced-networking-ena.md)\.
+ To import these metrics to Amazon CloudWatch, install the CloudWatch agent\. For more information, see [Collect advanced network metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-ethtool-plugin.html) in the *Amazon CloudWatch User Guide*\.

## Metrics for the ENA driver<a name="network-performance-metrics"></a>

The ENA driver delivers the following metrics to the instance in real time\. They provide the cumulative number of packets queued or dropped on each network interface since the last driver reset\.


| Metric | Description | 
| --- | --- | 
| bw\_in\_allowance\_exceeded |  The number of packets queued or dropped because the inbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| bw\_out\_allowance\_exceeded |  The number of packets queued or dropped because the outbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| conntrack\_allowance\_exceeded |  The number of packets dropped because connection tracking exceeded the maximum for the instance and new connections could not be established\. This can result in packet loss for traffic to or from the instance\.  | 
| linklocal\_allowance\_exceeded |  The number of packets dropped because the PPS of the traffic to local proxy services exceeded the maximum for the network interface\. This impacts traffic to the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.  | 
| pps\_allowance\_exceeded |  The number of packets queued or dropped because the bidirectional PPS exceeded the maximum for the instance\.  | 

## View the network performance metrics for your Windows instance<a name="view-network-performance-metrics"></a>

You can view the metrics using any consumer of Windows performance counters\. The data can be parsed according to the EnaPerfCounters manifest\. This is an XML file that defines the performance counter provider and its countersets\.

**Manifest installation**

If you launched the instance using an AMI that contains ENA driver 2\.2\.2 or later, or used the install script in the driver package for ENA driver 2\.2\.2, the manifest is already installed\. To install the manifest manually, use the following steps:

1. Remove the existing manifest using the following command:

   ```
   unlodctr /m:EnaPerfCounters.man
   ```

1. Copy the manifest file `_EnaPerfCounters.man_` from the driver installation package to `%SystemRoot%\System32\drivers`\.

1. Install the new manifest using the following command:

   ```
   lodctr /m:EnaPerfCounters.man
   ```

**View metrics using Performance Monitor**

1. Open Performance Monitor\.

1. Press Ctrl\+N to add new counters\.

1. Choose **ENA Packets Shaping** from the list\.

1. Select the instances to monitor and choose **Add**\.

1. Choose **OK**\.