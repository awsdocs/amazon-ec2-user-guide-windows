# Enhanced Networking on Windows<a name="enhanced-networking"></a>

Enhanced networking uses single root I/O virtualization \(SR\-IOV\) to provide high\-performance networking capabilities on [supported instance types](#supported_instances)\. SR\-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces\. Enhanced networking provides higher bandwidth, higher packet per second \(PPS\) performance, and consistently lower inter\-instance latencies\. There is no additional charge for using enhanced networking\.

**Topics**
+ [Enhanced Networking Types](#supported_instances)
+ [Enabling Enhanced Networking on Your Instance](#enabling_enhanced_networking)
+ [Enabling Enhanced Networking with the Intel 82599 VF Interface on Windows Instances](sriov-networking.md)
+ [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Windows Instances](enhanced-networking-ena.md)

## Enhanced Networking Types<a name="supported_instances"></a>

Depending on your instance type, enhanced networking can be enabled using one of the following mechanisms:

**Intel 82599 Virtual Function \(VF\) interface**  
The Intel 82599 Virtual Function interface supports network speeds of up to 10 Gbps for supported instance types\.  
C3, C4, D2, I2, M4 \(excluding `m4.16xlarge`\), and R3 instances use the Intel 82599 VF interface for enhanced networking\.

**Elastic Network Adapter \(ENA\)**  
The Elastic Network Adapter \(ENA\) supports network speeds of up to 25 Gbps for supported instance types\.  
C5, C5d, F1, G3, H1, I3, `m4.16xlarge`, M5, M5d, P2, P3, R4, and X1 instances use the Elastic Network Adapter for enhanced networking\.

To find out which instance types support 10 or 25 Gbps network speeds, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types)\.

## Enabling Enhanced Networking on Your Instance<a name="enabling_enhanced_networking"></a>

If your instance type supports the Intel 82599 VF interface for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Intel 82599 VF Interface on Windows Instances](sriov-networking.md)\.

If your instance type supports the Elastic Network Adapter for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Windows Instances](enhanced-networking-ena.md)\.

**Note**  
If you've configured your instance to use [static IP addressing](config-windows-multiple-ip.md#step1) and you resize the instance to an instance type that supports enhanced networking \(for example, from T2 to M4\), you might get a warning about a potential IP address conflict when you reconfigure static IP addressing\. To prevent this, enable DHCP on the network interface for your instance before you change the instance type\. From your instance, open the **Network and Sharing Center**, go to **Internet Protocol Version 4 \(TCP/IPv4\) Properties** for the network interface, and choose **Obtain an IP address automatically**\. Change the instance type and reconfigure static IP addressing on the network interface\.