# Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance<a name="network_mtu"></a>

The maximum transmission unit \(MTU\) of a network connection is the size, in bytes, of the largest permissible packet that can be passed over the connection\. The larger the MTU of a connection, the more data that can be passed in a single packet\. Ethernet packets consist of the frame, or the actual data you are sending, and the network overhead information that surrounds it\.

Ethernet frames can come in different formats, and the most common format is the standard Ethernet v2 frame format\. It supports 1500 MTU, which is the largest Ethernet packet size supported over most of the Internet\. The maximum supported MTU for an instance depends on its instance type\. All Amazon EC2 instance types support 1500 MTU, and many current instance sizes support 9001 MTU, or jumbo frames\.


+ [Jumbo Frames \(9001 MTU\)](#jumbo_frame_instances)
+ [Path MTU Discovery](#path_mtu_discovery)
+ [Check the Path MTU Between Two Hosts](#check_path_mtu)
+ [Check and Set the MTU on Your Windows Instance](#set_mtu_windows)
+ [Troubleshooting](#mtu-troubleshooting)

## Jumbo Frames \(9001 MTU\)<a name="jumbo_frame_instances"></a>

Jumbo frames allow more than 1500 bytes of data by increasing the payload size per packet, and thus increasing the percentage of the packet that is not packet overhead\. Fewer packets are needed to send the same amount of usable data\. However, outside of a given AWS region \(EC2\-Classic\), a single VPC, or a VPC peering connection, you will experience a maximum path of 1500 MTU\. VPN connections and traffic sent over an Internet gateway are limited to 1500 MTU\. If packets are over 1500 bytes, they are fragmented, or they are dropped if the `Don't Fragment` flag is set in the IP header\.

Jumbo frames should be used with caution for Internet\-bound traffic or any traffic that leaves a VPC\. Packets are fragmented by intermediate systems, which slows down this traffic\. To use jumbo frames inside a VPC and not slow traffic that's bound for outside the VPC, you can configure the MTU size by route, or use multiple elastic network interfaces with different MTU sizes and different routes\.

For instances that are collocated inside a cluster placement group, jumbo frames help to achieve the maximum network throughput possible, and they are recommended in this case\. For more information, see [Placement Groups](placement-groups.md)\.

The following instances support jumbo frames:

+ General purpose: M3, M4, M5, T2

+ Compute optimized: C3, C4, C5, CC2

+ Accelerated computing: F1, G2, G3, P2, P3

+ Memory optimized: CR1, R3, R4, X1

+ Storage optimized: D2, H1, HS1, I2, I3

## Path MTU Discovery<a name="path_mtu_discovery"></a>

Path MTU Discovery is used to determine the path MTU between two devices\. The path MTU is the maximum packet size that's supported on the path between the originating host and the receiving host\. If a host sends a packet that's larger than the MTU of the receiving host or that's larger than the MTU of a device along the path, the receiving host or device returns the following ICMP message: `Destination Unreachable: Fragmentation Needed and Don't Fragment was Set` \(Type 3, Code 4\)\. This instructs the original host to adjust the MTU until the packet can be transmitted\. 

By default, security groups do not allow any inbound ICMP traffic\. To ensure that your instance can receive this message and the packet does not get dropped, you must add a **Custom ICMP Rule** with the **Destination Unreachable** protocol to the inbound security group rules for your instance\. For more information, see [Path MTU Discovery](security-group-rules-reference.md#sg-rules-path-mtu)\.

**Important**  
Modifying your instance's security group to allow path MTU discovery does not guarantee that jumbo frames will not be dropped by some routers\. An Internet gateway in your VPC will forward packets up to 1500 bytes only\. 1500 MTU packets are recommended for Internet traffic\.

## Check the Path MTU Between Two Hosts<a name="check_path_mtu"></a>

You can check the path MTU between two hosts using the mturoute\.exe command,  which you can download and install from [http://www\.elifulkerson\.com/projects/mturoute\.php](http://www.elifulkerson.com/projects/mturoute.php)\. 

**To check path MTU using mturoute\.exe**

1. Download mturoute\.exe from [http://www\.elifulkerson\.com/projects/mturoute\.php](http://www.elifulkerson.com/projects/mturoute.php)\.

1. Open a Command Prompt window and change to the directory where you downloaded mturoute\.exe\.

1. Use the following command to check the path MTU between your EC2 instance and another host\. You can use a DNS name or an IP address as the destination\. If the destination is another EC2 instance, verify that the security group allows inbound UDP traffic\. This example checks the path MTU between an EC2 instance and `www.elifulkerson.com`\.

   ```
   .\mturoute.exe www.elifulkerson.com
   * ICMP Fragmentation is not permitted. *
   * Speed optimization is enabled. *
   * Maximum payload is 10000 bytes. *
   + ICMP payload of 1472 bytes succeeded.
   - ICMP payload of 1473 bytes is too big.
   Path MTU: 1500 bytes.
   ```

   In this example, the path MTU is 1500\.

## Check and Set the MTU on Your Windows Instance<a name="set_mtu_windows"></a>

Some drivers are configured to use jumbo frames, and others are configured to use standard frame sizes\. You might want to use jumbo frames for network traffic within your VPC or standard frames for Internet traffic\. Whatever your use case, we recommend that you verify that your instances behave as expected\.

### ENA Driver<a name="set-mtu-windows-ena-driver"></a>

You can change the MTU setting using Device Manager or the Set\-NetAdapterAdvancedProperty command\.

To get the current MTU setting using the Get\-NetAdapterAdvancedProperty command, use the following command\. Check the entry for the interface name `MTU`\. A value of 9000 indicates that Jumbo frames are enabled\. Jumbo frames are disabled by default\.

```
Get-NetAdapterAdvancedProperty -Name "Ethernet"
```

Enable jumbo frames as follows:

```
Set-NetAdapterAdvancedProperty -Name "Ethernet" -RegistryKeyword "MTU" -RegistryValue 9000
```

Disable jumbo frames as follows:

```
Set-NetAdapterAdvancedProperty -Name "Ethernet" -RegistryKeyword "MTU" -RegistryValue 1500
```

### Intel SRIOV 82599 Driver<a name="set-mtu-windows-sriov-driver"></a>

You can change the MTU setting using Device Manager or the Set\-NetAdapterAdvancedProperty command\.

To get the current MTU setting using the Get\-NetAdapterAdvancedProperty command, use the following command\. Check the entry for the interface name `*JumboPacket`\. A value of 9014 indicates that Jumbo frames are enabled\. \(Note that the MTU size includes the header and the payload\.\) Jumbo frames are disabled by default\.

```
Get-NetAdapterAdvancedProperty -Name "Ethernet"
```

Enable jumbo frames as follows:

```
Set-NetAdapterAdvancedProperty -Name "Ethernet" -RegistryKeyword "*JumboPacket" -RegistryValue 9014
```

Disable jumbo frames as follows:

```
Set-NetAdapterAdvancedProperty -Name "Ethernet" -RegistryKeyword "*JumboPacket" -RegistryValue 1514
```

### AWS PV Driver<a name="set-mtu-windows-pv-driver"></a>

You cannot change the MTU setting using Device Manager, but you can change it using the netsh command\.

Get the current MTU setting using the following command\. The name of the interface can vary\. In the output, look for an entry with the name "Ethernet," "Ethernet 2," or "Local Area Connection"\. You'll need the interface name to enable or disable jumbo frames\. A value of 9000 indicates that Jumbo frames are enabled\.

```
netsh interface ipv4 show subinterface
```

Enable jumbo frames as follows:

```
netsh interface ipv4 set subinterface "Ethernet" mtu=9000
```

Disable jumbo frames as follows:

```
netsh interface ipv4 set subinterface "Ethernet" mtu=1500
```

## Troubleshooting<a name="mtu-troubleshooting"></a>

If you experience connectivity issues between your EC2 instance and an Amazon Redshift cluster when using jumbo frames, see [Queries Appear to Hang](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-drop-issues.html) in the *Amazon Redshift Cluster Management Guide*