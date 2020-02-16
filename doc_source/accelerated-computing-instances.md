# Windows Accelerated Computing Instances<a name="accelerated-computing-instances"></a>

If you require high processing capability, you'll benefit from using accelerated computing instances, which provide access to hardware\-based compute accelerators such as Graphics Processing Units \(GPUs\)\. Accelerated computing instances enable more parallelism for higher throughput on compute\-intensive workloads\.

GPU\-based instances provide access to NVIDIA GPUs with thousands of compute cores\. You can use GPU\-based accelerated computing instances to accelerate scientific, engineering, and rendering applications by leveraging the CUDA or Open Computing Language \(OpenCL\) parallel computing frameworks\. You can also use them for graphics applications, including game streaming, 3\-D application streaming, and other graphics workloads\.

If your application needs a small amount of additional graphics acceleration, but is better suited for an instance type with different compute, memory, or storage specifications, use an Elastic Graphics accelerator instead\. For more information, see [Amazon Elastic Graphics](elastic-graphics.md)\.

You can cluster accelerated computing instances into a cluster placement group\. Cluster placement groups provide low latency and high\-bandwidth connectivity between the instances within a single Availability Zone\. For more information, see [Placement Groups](placement-groups.md)\.

**Topics**
+ [Accelerated Computing Instance Families](#gpu-instance-families)
+ [Hardware Specifications](#gpu-instance-specifications)
+ [Instance Performance](#gpu-instance-performance)
+ [Network Performance](#gpu-network-performance)
+ [Instance Features](#gpu-instances-features)
+ [Release Notes](#gpu-instance-current-limitations)
+ [Installing NVIDIA Drivers on Windows Instances](install-nvidia-driver.md)
+ [Activate NVIDIA GRID Virtual Applications](activate_grid.md)
+ [Optimizing GPU Settings](optimize_gpu.md)

For information about Linux accelerated computing instances, see [Linux Accelerated Computing Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/accelerated-computing-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Accelerated Computing Instance Families<a name="gpu-instance-families"></a>

Accelerated computing instance families use hardware accelerators, or co\-processors, to perform some functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs\. The following accelerated computing instance families are available for you to launch in Amazon EC2\.
<a name="p3-instances"></a>
**P3 Instances**  
P3 instances use NVIDIA Tesla V100 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models or through a machine learning framework\. P3 instances provide high\-bandwidth networking, powerful half, single, and double\-precision floating\-point capabilities, and up to 32 GiB of memory per GPU, which makes them ideal for deep learning, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\. Tesla V100 GPUs do not support graphics mode\. For more information, see [Amazon EC2 P3 Instances](https://aws.amazon.com/ec2/instance-types/p3)\.

P3 instances support NVIDIA NVLink peer to peer transfers\.

To view topology information about the system, run the following command:

```
nvidia-smi topo -m
```

For more information, see [NVIDIA NVLink](https://devblogs.nvidia.com/parallelforall/how-nvlink-will-enable-faster-easier-multi-gpu-computing/)\.
<a name="p2-instances"></a>
**P2 Instances**  
P2 instances use NVIDIA Tesla K80 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models\. P2 instances provide high\-bandwidth networking, powerful single and double precision floating\-point capabilities, and 12 GiB of memory per GPU, which makes them ideal for deep learning, graph databases, high\-performance databases, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\.

P2 instances support NVIDIA GPUDirect peer to peer transfers\.

To view topology information about the system, run the following command:

```
nvidia-smi topo -m
```

For more information, see [NVIDIA GPUDirect](https://developer.nvidia.com/gpudirect)\.
<a name="g4-instances"></a>
**G4 Instances**  
G4 instances use NVIDIA Tesla GPUs and provide a cost\-effective, high\-performance platform for general purpose GPU computing using the CUDA or machine learning frameworks along with graphics applications using DirectX or OpenGL\. G4 instances provide high\- bandwidth networking, powerful half and single\-precision floating\-point capabilities, along with INT8 and INT4 precisions\. Each GPU has 16 GiB of GDDR6 memory, making G4 instances well\-suited for machine learning inference, video transcoding, and graphics applications like remote graphics workstations and game streaming in the cloud\.

G4 instances support NVIDIA GRID Virtual Workstation\. For more information, see [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g4dn.xlarge&searchTerms=NVIDIA%20GRID)\.
<a name="g3-instances"></a>
**G3 Instances**  
G3 instances use NVIDIA Tesla M60 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. G3 instances also provide NVIDIA GRID Virtual Workstation features, such as support for four monitors with resolutions up to 4096x2160, and NVIDIA GRID Virtual Applications\. G3 instances are well\-suited for applications such as 3D visualizations, graphics\-intensive remote workstations, 3D rendering, video encoding, virtual reality, and other server\-side graphics workloads requiring massively parallel processing power\. 

G3 instances support NVIDIA GRID Virtual Workstation and NVIDIA GRID Virtual Applications\. To activate either of these features, see [Activate NVIDIA GRID Virtual Applications](activate_grid.md)\.
<a name="g2-instances"></a>
**G2 Instances**  
G2 instances use NVIDIA GRID K520 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. NVIDIA GRID GPUs also support NVIDIA’s fast capture and encode API operations\. Example applications include video creation services, 3D visualizations, streaming graphics\-intensive applications, and other server\-side graphics workloads\.

## Hardware Specifications<a name="gpu-instance-specifications"></a>

The following is a summary of the hardware specifications for accelerated computing instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | Accelerators | 
| --- | --- | --- | --- | 
| p2\.xlarge | 4 | 61 | 1 | 
| p2\.8xlarge | 32 | 488 | 8 | 
| p2\.16xlarge | 64 | 732 | 16 | 
| p3\.2xlarge | 8 | 61 | 1 | 
| p3\.8xlarge | 32 | 244 | 4 | 
| p3\.16xlarge | 64 | 488 | 8 | 
| p3dn\.24xlarge | 96 | 768 | 8 | 
| g2\.2xlarge | 8 | 15 | 1 | 
| g2\.8xlarge | 32 | 60 | 4 | 
| g3s\.xlarge | 4 | 30\.5 | 1 | 
| g3\.4xlarge | 16 | 122 | 1 | 
| g3\.8xlarge | 32 | 244 | 2 | 
| g3\.16xlarge | 64 | 488 | 4 | 
| g4dn\.xlarge | 4 | 16 | 1 | 
| g4dn\.2xlarge | 8 | 32 | 1 | 
| g4dn\.4xlarge | 16 | 64 | 1 | 
| g4dn\.8xlarge | 32 | 128 | 1 | 
| g4dn\.12xlarge | 48 | 192 | 4 | 
| g4dn\.16xlarge | 64 | 256 | 1 | 
| f1\.2xlarge | 8 | 122 | 1 | 
| f1\.4xlarge | 16 | 244 | 2 | 
| f1\.16xlarge | 64 | 976 | 8 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="gpu-instance-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some accelerated computing instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](ebs-optimized.md)\.

## Network Performance<a name="gpu-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Windows](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for accelerated computing instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `f1.2xlarge` \| `f1.4xlarge` \| `g3.4xlarge` \| `p3.2xlarge`  |  Up to 10 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `g3s.xlarge` \| `g3.8xlarge` \| `p2.8xlarge` \| `p3.8xlarge`  |  10 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `g4dn.xlarge` \| `g4dn.2xlarge` \| `g4dn.4xlarge`  |  Up to 25 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `f1.16xlarge` \| `g3.16xlarge` \| `p2.16xlarge` \| `p3.16xlarge`  |  25 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `g4dn.8xlarge` \| `g4dn.12xlarge` \| `g4dn.16xlarge`  |  50 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
| `p3dn.24xlarge` | 100 Gbps |  [ENA](enhanced-networking-ena.md)  | 

## Instance Features<a name="gpu-instances-features"></a>

The following is a summary of features for accelerated computing instances\.


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| G2 | No | No | SSD | Yes | 
| G3 | Yes | No | No | Yes | 
| G4 | No | Yes | NVMe \* | Yes | 
| P2 | Yes | No | No | Yes | 
| P3 |  `p3dn.24xlarge`: No All other sizes: Yes  |  `p3dn.24xlarge`: Yes All other sizes: No  | `p3dn.24xlarge`: NVMe \* | Yes | 
| F1 | No | No | NVMe \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Windows Instances](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Release Notes<a name="gpu-instance-current-limitations"></a>
+ You must launch the instance using an HVM AMI\.
+ The following are requirements for G4 instances:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The current [AWS Windows AMIs](windows-ami-version-history.md) meet these requirements\.
+ GPU\-based instances can't access the GPU unless the NVIDIA drivers are installed\. For more information, see [Installing NVIDIA Drivers on Windows Instances](install-nvidia-driver.md)\.
+ There is a limit of 100 AFIs per region\.
+ There is a limit on the number of instances that you can run\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\. To request an increase in these limits, use the following form: [Request to Increase Amazon EC2 Instance Limit](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
+ If you launch a multi\-GPU instance with a Windows AMI that was created on a single\-GPU instance, Windows does not automatically install the NVIDIA driver for all GPUs\. You must authorize the driver installation for the new GPU hardware\. You can correct this manually in the Device Manager by opening the **Other** device category \(the inactive GPUs do not appear under **Display Adapters**\)\. For each inactive GPU, open the context \(right\-click\) menu, choose **Update Driver Software**, and then choose the default **Automatic Update** option\.
+ When using Microsoft Remote Desktop Protocol \(RDP\), GPUs that use the WDDM driver model are replaced with a non\-accelerated Remote Desktop display driver\. We recommend that you use a different remote access tool to access your GPU, such as [Teradici Cloud Access Software](http://www.teradici.com/products/cloud-access/cloud-access-software), [NICE Desktop Cloud Visualization \(DCV\)](https://docs.aws.amazon.com/dcv/latest/userguide/getting-started.html), or VNC\. You can also use one of the GPU AMIs from the AWS Marketplace because they provide remote access tools that support 3D acceleration\.