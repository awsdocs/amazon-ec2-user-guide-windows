# Ports and Protocols for Windows Amazon Machine Images \(AMIs\)<a name="ports-and-protocols"></a>

The following tables list the ports, protocols, and directions by workload for Windows Amazon Machine Images\. 

**Topics**
+ [AllJoyn Router](#alljoyntcpin-ports)
+ [Cast to Device](#cast-to-device)
+ [Core Networking](#networking-ports)
+ [Delivery Optimization](#delivery-optimization)
+ [Diag Track](#diag)
+ [DIAL Protocol Server](#dial-protocol)
+ [Distributed File System \(DFS\) Management](#dfs)
+ [File and Printer Sharing](#file-and-print)
+ [File Server Remote Management](#file-server-remote)
+ [ICMP v4 All](#icmp-v4)
+ [Multicast](#multicast)
+ [Remote Desktop](#remote-desktop)
+ [Windows Device Management](#device-management)
+ [Windows Firewall Remote Management](#firewall-remote)
+ [Windows Remote Management](#remote-management)

## AllJoyn Router<a name="alljoyntcpin-ports"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## Cast to Device<a name="cast-to-device"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## Core Networking<a name="networking-ports"></a>

------
#### [ Windows Server 2012, 2012 R2, 2016, and 2019 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------
#### [ Windows Server 2008 R2 and SP2 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------

## Delivery Optimization<a name="delivery-optimization"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## Diag Track<a name="diag"></a>

------
#### [ Windows Server 2019 ]


| OS | Rule | Definition | Port | Protocol | Direction | 
| --- | --- | --- | --- | --- | --- | 
| Windows Server 2019 | Connected User Experiences and Telemetry | Unified Telemetry Client Outbound Traffic |  Local: Any Remote: 443  | TCP | Out | 

------
#### [ Windows Server 2016 ]


| OS | Rule | Definition | Port | Protocol | Direction | 
| --- | --- | --- | --- | --- | --- | 
| Windows Server 2016 | Connected User Experiences and Telemetry | Unified Telemetry Client Outbound Traffic |  Local: Any Remote: Any  | TCP | Out | 

------

## DIAL Protocol Server<a name="dial-protocol"></a>


| OS | Rule | Definition | Port | Protocol | Direction | 
| --- | --- | --- | --- | --- | --- | 
| Windows Server 2016 Windows Server 2019 | DIAL protocol server \(HTTP\-In\) | Inbound rule for DIAL protocol server to allow remote control of Apps using HTTP\.  |  Local: 10247 Remote: Any  | TCP | In | 

## Distributed File System \(DFS\) Management<a name="dfs"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## File and Printer Sharing<a name="file-and-print"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## File Server Remote Management<a name="file-server-remote"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## ICMP v4 All<a name="icmp-v4"></a>


| OS | Rule | Port | Protocol | Direction | 
| --- | --- | --- | --- | --- | 
| Windows Server 2012 Windows Server 2012 R2 | All ICMP v4 | Local: 139 Remote: Any | ICMPv4 | In | 

## Multicast<a name="multicast"></a>

------
#### [ Windows Server 2019 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------
#### [ Windows Server 2016 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------

## Remote Desktop<a name="remote-desktop"></a>

------
#### [ Windows Server 2012 R2, 2016, and 2019 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------
#### [ Windows Server 2012 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------
#### [ Windows Server 2008 SP2 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------
#### [ Windows Server 2008 R2 ]

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

------

## Windows Device Management<a name="device-management"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## Windows Firewall Remote Management<a name="firewall-remote"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ports-and-protocols.html)

## Windows Remote Management<a name="remote-management"></a>


| OS | Rule | Definition | Port | Protocol | Direction | 
| --- | --- | --- | --- | --- | --- | 
| Windows Server 2008 R2 Windows Server 2008 SP2 Windows Server 2012 Windows Server 2012 R2 Windows Server 2016 Windows Server 2019  | Windows Remote Management \(HTTP\-In\) | Inbound rule for Windows Remote Management via WS\-Management\.  | Local: 5985 Remote: Any | TCP | In | 

 For more information about Amazon EC2 security groups, see [Amazon EC2 Security Groups for Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/using-network-security.html)\.