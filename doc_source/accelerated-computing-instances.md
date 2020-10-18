# Windows accelerated computing instances<a name="accelerated-computing-instances"></a>

Accelerated computing instances use hardware accelerators, or co\-processors, to perform some functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs\. These instances enable more parallelism for higher throughput on compute\-intensive workloads\.

If you require high processing capability, you'll benefit from using accelerated computing instances, which provide access to hardware\-based compute accelerators such as Graphics Processing Units \(GPUs\)\.

**Topics**
+ [GPU instances](#gpu-instances)
+ [Instances with AWS Inferentia](#aws-inferentia-instances)
+ [Hardware specifications](#gpu-instance-specifications)
+ [Instance performance](#gpu-instance-performance)
+ [Network performance](#gpu-network-performance)
+ [Instance features](#gpu-instances-features)
+ [Release notes](#gpu-instance-current-limitations)
+ [Installing NVIDIA drivers on Windows instances](install-nvidia-driver.md)
+ [Activate NVIDIA GRID Virtual Applications](activate_grid.md)
+ [Optimizing GPU settings](optimize_gpu.md)

## GPU instances<a name="gpu-instances"></a>

GPU\-based instances provide access to NVIDIA GPUs with thousands of compute cores\. You can use these instances to accelerate scientific, engineering, and rendering applications by leveraging the CUDA or Open Computing Language \(OpenCL\) parallel computing frameworks\. You can also use them for graphics applications, including game streaming, 3\-D application streaming, and other graphics workloads\.

If your application needs a small amount of additional graphics acceleration, but is better suited for an instance type with different compute, memory, or storage specifications, use an Elastic Graphics accelerator instead\. For more information, see [Amazon Elastic Graphics](elastic-graphics.md)\.
<a name="g4-instances"></a>
**G4 instances**  
G4 instances use NVIDIA Tesla GPUs and provide a cost\-effective, high\-performance platform for general purpose GPU computing using the CUDA or machine learning frameworks along with graphics applications using DirectX or OpenGL\. G4 instances provide high\- bandwidth networking, powerful half and single\-precision floating\-point capabilities, along with INT8 and INT4 precisions\. Each GPU has 16 GiB of GDDR6 memory, making G4 instances well\-suited for machine learning inference, video transcoding, and graphics applications like remote graphics workstations and game streaming in the cloud\.

For more information, see [Amazon EC2 G4 Instances](http://aws.amazon.com/ec2/instance-types/g4/)\.

G4 instances support NVIDIA GRID Virtual Workstation\. For more information, see [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g4dn.xlarge&searchTerms=NVIDIA%20GRID)\.
<a name="g3-instances"></a>
**G3 instances**  
G3 instances use NVIDIA Tesla M60 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. G3 instances also provide NVIDIA GRID Virtual Workstation features, such as support for four monitors with resolutions up to 4096x2160, and NVIDIA GRID Virtual Applications\. G3 instances are well\-suited for applications such as 3D visualizations, graphics\-intensive remote workstations, 3D rendering, video encoding, virtual reality, and other server\-side graphics workloads requiring massively parallel processing power\. 

For more information, see [Amazon EC2 G3 Instances](http://aws.amazon.com/ec2/instance-types/g3/)\.

G3 instances support NVIDIA GRID Virtual Workstation and NVIDIA GRID Virtual Applications\. To activate either of these features, see [Activate NVIDIA GRID Virtual Applications](activate_grid.md)\.
<a name="g2-instances"></a>
**G2 instances**  
G2 instances use NVIDIA GRID K520 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. NVIDIA GRID GPUs also support NVIDIA’s fast capture and encode API operations\. Example applications include video creation services, 3D visualizations, streaming graphics\-intensive applications, and other server\-side graphics workloads\.
<a name="p3-instances"></a>
**P3 instances**  
P3 instances use NVIDIA Tesla V100 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models or through a machine learning framework\. P3 instances provide high\-bandwidth networking, powerful half, single, and double\-precision floating\-point capabilities, and up to 32 GiB of memory per GPU, which makes them ideal for deep learning, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\. Tesla V100 GPUs do not support graphics mode\.

For more information, see [Amazon EC2 P3 Instances](https://aws.amazon.com/ec2/instance-types/p3)\.

P3 instances support NVIDIA NVLink peer to peer transfers\. For more information, see [NVIDIA NVLink](https://devblogs.nvidia.com/parallelforall/how-nvlink-will-enable-faster-easier-multi-gpu-computing/)\.
<a name="p2-instances"></a>
**P2 instances**  
P2 instances use NVIDIA Tesla K80 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models\. P2 instances provide high\-bandwidth networking, powerful single and double precision floating\-point capabilities, and 12 GiB of memory per GPU, which makes them ideal for deep learning, graph databases, high\-performance databases, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\.

P2 instances support NVIDIA GPUDirect peer to peer transfers\. For more information, see [NVIDIA GPUDirect](https://developer.nvidia.com/gpudirect)\.

## Instances with AWS Inferentia<a name="aws-inferentia-instances"></a>

These instances are designed to accelerate machine learning using [AWS Inferentia](http://aws.amazon.com/machine-learning/inferentia/), a custom AI/ML chip from Amazon that provides high performance and low latency machine learning inference\. These instances are optimized for deploying Deep Learning \(DL\) models for applications, such as natural language processing, object detection and classification, content personalization and filtering, and speech recognition\.

There are a variety of ways that you can get started:
+ Use SageMaker, a fully\-managed service that is the easiest way to get started with machine learning models\. For more information, see [Compile and deploy a TensorFlow model on Inf1 instances](https://github.com/awslabs/amazon-sagemaker-examples/blob/master/sagemaker_neo_compilation_jobs/deploy_tensorflow_model_on_Inf1_instance/tensorflow_distributed_mnist_neo_inf1.ipynb) on github\.
+ Launch an Inf1 instance using the Deep Learning AMI\. For more information, see [AWS Inferentia with DLAMI](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-inferentia.html) in the *AWS Deep Learning AMI Developer Guide*\.
+ Launch an Inf1 instance using your own AMI and install the [AWS Neuron SDK](https://github.com/aws/aws-neuron-sdk), which enables you to compile, run, and profile deep learning models for AWS Inferentia\.
+ Launch a container instance using an Inf1 instance and an Amazon ECS\-optimized AMI\. For more information, see [Amazon Linux 2 \(Inferentia\) AMIs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Create an Amazon EKS cluster with nodes running Inf1 instances\. For more information, see [Inferentia support](https://docs.aws.amazon.com/eks/latest/userguide/inferentia-support.html) in the **Amazon EKS User Guide**\.

For more information, see [Machine Learning on AWS](http://aws.amazon.com/machine-learning/)\.

## Hardware specifications<a name="gpu-instance-specifications"></a>

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
| g4dn\.metal | 96 | 384 | 8 | 
| f1\.2xlarge | 8 | 122 | 1 | 
| f1\.4xlarge | 16 | 244 | 2 | 
| f1\.16xlarge | 64 | 976 | 8 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU options](instance-optimize-cpu.md)\.

## Instance performance<a name="gpu-instance-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some accelerated computing instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## Network performance<a name="gpu-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Windows](enhanced-networking.md)\.

The following is a summary of network performance for accelerated computing instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  f1\.2xlarge \| f1\.4xlarge \| g3\.4xlarge \| p3\.2xlarge  | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  g3s\.xlarge \| g3\.8xlarge \| p2\.8xlarge \| p3\.8xlarge  | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
|  g4dn\.xlarge \| g4dn\.2xlarge \| g4dn\.4xlarge  | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  f1\.16xlarge \| g3\.16xlarge \| p2\.16xlarge \| p3\.16xlarge  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
|  g4dn\.8xlarge \| g4dn\.12xlarge \| g4dn\.16xlarge  | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| g4dn\.metal \| p3dn\.24xlarge | 100 Gbps | [ENA](enhanced-networking-ena.md) | 

† These instances use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. They accrue credits when their bandwidth is below their baseline bandwidth, and can use these credits when they perform network data transfers\. For more information, open a support case and ask about baseline bandwidth for the specific instance types that you are interested in\.

## Instance features<a name="gpu-instances-features"></a>

The following is a summary of features for accelerated computing instances\.


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| G2 | No | No | SSD | Yes | 
| G3 | Yes | No | No | Yes | 
| G4 | No | Yes | NVMe \* | Yes | 
| P2 | Yes | No | No | Yes | 
| P3 |  24xlarge: No All other sizes: Yes  |  24xlarge: Yes All other sizes: No  | 24xlarge: NVMe \* | Yes | 
| F1 | No | No | NVMe \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Windows instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Release notes<a name="gpu-instance-current-limitations"></a>
+ You must launch the instance using an HVM AMI\.
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The current [AWS Windows AMIs](windows-ami-version-history.md) meet these requirements\.
+ GPU\-based instances can't access the GPU unless the NVIDIA drivers are installed\. For more information, see [Installing NVIDIA drivers on Windows instances](install-nvidia-driver.md)\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\.
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ There is a limit of 100 AFIs per Region\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
+ If you launch a multi\-GPU instance with a Windows AMI that was created on a single\-GPU instance, Windows does not automatically install the NVIDIA driver for all GPUs\. You must authorize the driver installation for the new GPU hardware\. You can correct this manually in the Device Manager by opening the **Other** device category \(the inactive GPUs do not appear under **Display Adapters**\)\. For each inactive GPU, open the context \(right\-click\) menu, choose **Update Driver Software**, and then choose the default **Automatic Update** option\.
+ When using Microsoft Remote Desktop Protocol \(RDP\), GPUs that use the WDDM driver model are replaced with a non\-accelerated Remote Desktop display driver\. We recommend that you use a different remote access tool to access your GPU, such as [Teradici Cloud Access Software](http://www.teradici.com/products/cloud-access/cloud-access-software), [NICE Desktop Cloud Visualization \(DCV\)](https://docs.aws.amazon.com/dcv/latest/userguide/getting-started.html), or VNC\. You can also use one of the GPU AMIs from the AWS Marketplace because they provide remote access tools that support 3D acceleration\.