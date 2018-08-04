# Amazon EC2 Elastic GPUs<a name="elastic-gpus"></a>

An elastic GPU is a GPU resource that you can attach to your Amazon EC2 instance to accelerate the graphics performance of your applications\. Elastic GPUs come in multiple sizes and are a low\-cost alternative to using GPU graphics instance types \(such as the G2 instance type\)\. You have the flexibility to choose an instance type that meets the compute, memory, and storage needs of your application, and then choose an elastic GPU for your instance that meets the graphics acceleration requirements of your workload\.

Elastic GPUs are suited for applications that require a small or intermittent amount of additional GPU for graphics acceleration, and that use OpenGL graphics support\. If you need access to full, directly attached GPUs and use of DirectX, CUDA, or Open Computing Language \(OpenCL\) parallel computing frameworks, use an accelerated computing instance type instance instead\. For more information, see [Windows Accelerated Computing Instances](accelerated-computing-instances.md)\.

**Topics**
+ [Elastic GPU Basics](#elastic-gpus-basics)
+ [Working with Elastic GPUs](working-with-elastic-gpus.md)
+ [Using CloudWatch Metrics to Monitor Your Elastic GPUs](elastic-gpus-cloudwatch.md)
+ [Troubleshooting](elastic-gpus-troubleshooting.md)

## Elastic GPU Basics<a name="elastic-gpus-basics"></a>

To use an elastic GPU, launch an instance and specify an elastic GPU type to attach to the instance during launch\. AWS finds available elastic GPU capacity and establishes a network connection between your instance and the elastic GPU\.

The following instance types support elastic GPUs:
+ c3 \| c4 \| c5 \| c5d
+ m3 \| m4 \| m5 \| m5d
+ r3 \| r4
+ `t2.medium` \(or greater\)
+ x1 \| x1e
+ d2
+ h1
+ i3 \| i3p
+ p2 \| p3

The following elastic GPU types are available\. You can attach any elastic GPU type to any supported instance type\. 


| GPU type | GPU memory \(MB\) | 
| --- | --- | 
| eg1\.medium | 1024 | 
| eg1\.large | 2048 | 
| eg1\.xlarge | 4096 | 
| eg1\.2xlarge | 8192 | 

An elastic GPU does not form part of the hardware of your instance\. Instead, the elastic GPU is network\-attached through a network interface, known as the *elastic GPU network interface*\. When you launch an instance with an elastic GPU, the elastic GPU network interface is created in your VPC for you\. The elastic GPU network interface is created in the same subnet and VPC as your instance and is assigned a private IPv4 address from that subnet\. The elastic GPU attached to your Amazon EC2 instance is allocated from a pool of available elastic GPUs in the same Availability Zone as your instance\.

![\[An elastic GPU attached to an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/elastic-gpu-diagram.png)

Elastic GPUs support up to and including the OpenGL 4\.3 API standards, which can be used for batch applications or 3D graphics acceleration\. An Amazon\-optimized OpenGL library on your instance detects the attached elastic GPU\. It directs OpenGL API calls from your instance to the elastic GPU, which then processes the requests and returns the results\. Traffic between the instance and the elastic GPU uses the same bandwidth as the instance's network traffic so it is recommended that you have adequate network bandwidth available\. Consult your software vendor for any OpenGL compliance and version questions\.

To use an elastic GPU, you do not require a device driver but your instance must have the Amazon\-optimized OpenGL library and Elastic GPU agents installed\. For more information, see [Installing and Updating the Elastic GPU Packages](working-with-elastic-gpus.md#elastic-gpus-install-libraries)\.

**Note**  
An elastic GPU is not visible or accessible through the device manager of your instance\.

By default, the default security group for your VPC is associated with the elastic GPU network interface\. The elastic GPU network traffic uses the TCP protocol and port 2007\. Ensure that the security group for your instance allows for this\. For more information, see [Configuring Your Security Groups](working-with-elastic-gpus.md#elastic-gpus-security)\.

### Pricing for Elastic GPUs<a name="elastic-gpus-pricing"></a>

You are charged for each second that an elastic GPU is attached to an instance in the `running` state when the elastic GPU is in the `Ok` state\. You are not charged for an elastic GPU attached to an instance that is in the `pending`, `stopping`, `stopped`, `shutting-down`, or `terminated` state\. You are also not charged when an elastic GPU is in the `Unknown` or `Impaired` state\.

### Elastic GPU Limitations<a name="elastic-gpus-limitations"></a>

Before you start using elastic GPUs, be aware of the following limitations:
+ You can attach one elastic GPU to an instance at a time, and only during instance launch\.
+ You cannot share an elastic GPU between instances\.
+ You cannot detach an elastic GPU from an instance or transfer it to another instance\. If you no longer require an elastic GPU, you must terminate your instance\. If you want to change the elastic GPU type, create an AMI from your instance, terminate the instance, and launch a new instance with a different elastic GPU specification\.
+ Currently, only versions of the OpenGL API up to and including 4\.3 are supported\. DirectX, CUDA, and OpenCL are not supported\.
+ Currently, elastic GPUs can be attached to instances launched from Windows Server AMIs only\.
+ Elastic GPUs can only be attached to instances in a VPC\.
+ Pricing for elastic GPUs is available at On\-Demand rates only\. You can attach an elastic GPU to a Reserved, Scheduled, or Spot Instance; however, the On\-Demand price for the elastic GPU applies\. You cannot reserve elastic GPU capacity and you cannot schedule elastic GPU capacity\.