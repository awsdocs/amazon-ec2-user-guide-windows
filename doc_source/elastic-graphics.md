# Amazon Elastic Graphics<a name="elastic-graphics"></a>

Amazon Elastic Graphics provides flexible, low\-cost, and high performance graphics acceleration for your Amazon EC2 instances\. Elastic Graphics accelerators come in multiple sizes and are a low\-cost alternative to using GPU graphics instance types \(such as the G2 and G3 instance types\)\. You have the flexibility to choose an instance type that meets the compute, memory, and storage needs of your application\. Then, choose the accelerator for your instance that meets the graphics requirements of your workload\.

Elastic Graphics is suited for applications that require a small or intermittent amount of additional graphics acceleration, and that use OpenGL graphics support\. If you need access to full, directly attached GPUs and use of DirectX, CUDA, or Open Computing Language \(OpenCL\) parallel computing frameworks, use an accelerated computing instance type instance instead\. For more information, see [Windows Accelerated Computing Instances](accelerated-computing-instances.md)\.

**Topics**
+ [Elastic Graphics Basics](#elastic-graphics-basics)
+ [Working with Elastic Graphics](working-with-elastic-graphics.md)
+ [Using CloudWatch Metrics to Monitor Elastic Graphics](elastic-graphics-cloudwatch.md)
+ [Troubleshooting](elastic-graphics-troubleshooting.md)

## Elastic Graphics Basics<a name="elastic-graphics-basics"></a>

To use Elastic Graphics, launch an instance and specify an accelerator type for the instance during launch\. AWS finds available Elastic Graphics capacity and establishes a network connection between your instance and the Elastic Graphics accelerator\.

The following instance types support Elastic Graphics accelerators:
+ C3 \| C4 \| C5 \| C5d
+ D2
+ H1
+ I3
+ M3 \| M4 \| M5 \| M5d
+ P2 \| P3
+ R3 \| R4 \| R5 \| R5d
+ `t2.medium` or larger
+ `t3.medium` or larger
+ X1 \| X1e
+ z1d

The following Elastic Graphics accelerator types are available\. You can attach any Elastic Graphics accelerator type to any supported instance type\.


| Elastic Graphics accelerator type | Graphics memory \(MB\) | 
| --- | --- | 
| eg1\.medium | 1024 | 
| eg1\.large | 2048 | 
| eg1\.xlarge | 4096 | 
| eg1\.2xlarge | 8192 | 

An Elastic Graphics accelerator does not form part of the hardware of your instance\. Instead, it is network\-attached through a network interface, known as the *Elastic Graphics network interface*\. When you launch an instance with graphics acceleration, the Elastic Graphics network interface is created in your VPC for you\. 

The Elastic Graphics network interface is created in the same subnet and VPC as your instance and is assigned a private IPv4 address from that subnet\. The accelerator attached to your Amazon EC2 instance is allocated from a pool of available accelerators in the same Availability Zone as your instance\.

![\[An Elastic Graphics accelerator attached to an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/elastic-graphics.png)

Elastic Graphics accelerators support up to and including the OpenGL 4\.3 API standards, which can be used for batch applications or 3D\-graphics acceleration\. An Amazon\-optimized OpenGL library on your instance detects the attached accelerator\. It directs OpenGL API calls from your instance to the accelerator, which then processes the requests and returns the results\. Traffic between the instance and the accelerator uses the same bandwidth as the instance's network traffic so we recommend that you have adequate network bandwidth available\. Consult your software vendor for any OpenGL compliance and version questions\.

To use Elastic Graphics accelerators, you do not require a device driver but your instance must have the Amazon\-optimized OpenGL library and the Elastic Graphics agents installed\. For more information, see [Installing and Updating the Elastic Graphics Packages](working-with-elastic-graphics.md#elastic-graphics-install-libraries)\.

**Note**  
The Elastic Graphics accelerator is not visible or accessible through the device manager of your instance\.

By default, the default security group for your VPC is associated with the Elastic Graphics network interface\. The Elastic Graphics network traffic uses the TCP protocol and port 2007\. Ensure that the security group for your instance allows for this\. For more information, see [Configuring Your Security Groups](working-with-elastic-graphics.md#elastic-graphics-security)\.

### Pricing for Elastic Graphics<a name="elastic-graphics-pricing"></a>

You are charged for each second that an Elastic Graphics accelerator is attached to an instance in the `running` state when the accelerator is in the `Ok` state\. You are not charged for an accelerator attached to an instance that is in the `pending`, `stopping`, `stopped`, `shutting-down`, or `terminated` state\. You are also not charged when an accelerator is in the `Unknown` or `Impaired` state\.

### Elastic Graphics Limitations<a name="elastic-graphics-limitations"></a>

Before you start using Elastic Graphics accelerators, be aware of the following limitations:
+ Currently, accelerators can be attached to instances launched from Windows Server AMIs only\.
+ You can attach one accelerator to an instance at a time, and only during instance launch\.
+ You cannot share an accelerator between instances\.
+ You cannot detach an accelerator from an instance or transfer it to another instance\. If you no longer require an accelerator, you must terminate your instance\. To change the accelerator type, create an AMI from your instance, terminate the instance, and launch a new instance with a different accelerator specification\.
+ Currently, only versions of the OpenGL API up to and including 4\.3 are supported\. DirectX, CUDA, and OpenCL are not supported\.
+ Accelerators can only be attached to instances in a VPC\.
+ Pricing for accelerators is available at On\-Demand rates only\. You can attach an accelerator to a Reserved, Scheduled, or Spot Instance\. The On\-Demand price for the accelerator applies in all cases\. You cannot reserve or schedule accelerator capacity\.