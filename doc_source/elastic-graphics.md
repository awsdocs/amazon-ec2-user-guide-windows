# Amazon Elastic Graphics<a name="elastic-graphics"></a>

Amazon Elastic Graphics provides flexible, low\-cost, and high performance graphics acceleration for your Windows instances\. Elastic Graphics accelerators come in multiple sizes and are a low\-cost alternative to using GPU graphics instance types \(such as G2 and G3\)\. You have the flexibility to choose an instance type that meets the compute, memory, and storage needs of your application\. Then, choose the accelerator for your instance that meets the graphics requirements of your workload\.

Elastic Graphics is suited for applications that require a small or intermittent amount of additional graphics acceleration, and that use OpenGL graphics support\. If you need access to full, directly attached GPUs and use of DirectX, CUDA, or Open Computing Language \(OpenCL\) parallel computing frameworks, use an accelerated computing instance type instance instead\. For more information, see [Windows accelerated computing instances](accelerated-computing-instances.md)\.

**Topics**
+ [Elastic Graphics basics](#elastic-graphics-basics)
+ [Pricing for Elastic Graphics](#elastic-graphics-pricing)
+ [Elastic Graphics limitations](#elastic-graphics-limitations)
+ [Working with Elastic Graphics](working-with-elastic-graphics.md)
+ [Using CloudWatch metrics to monitor Elastic Graphics](elastic-graphics-cloudwatch.md)
+ [Troubleshooting](elastic-graphics-troubleshooting.md)

## Elastic Graphics basics<a name="elastic-graphics-basics"></a>

To use Elastic Graphics, launch a Windows instance and specify an accelerator type for the instance during launch\. AWS finds available Elastic Graphics capacity and establishes a network connection between your instance and the Elastic Graphics accelerator\.

**Note**  
Bare metal instances are not supported\.

Elastic Graphics accelerators are available in the following AWS Regions: `us-east-1`, `us-east-2`, `us-west-2`, `ap-northeast-1`, `ap-southeast-1`, `ap-southeast-2`, `eu-central-1`, and `eu-west-1`\.

The following instance types support Elastic Graphics accelerators:
+ C3 \| C4 \| C5 \| C5a \| C5ad \| C5d \| C5n
+ D2
+ H1
+ I3 \| I3en
+ M3 \| M4 \| M5 \| M5d \| M5dn \| M5n
+ P2 \| P3 \| P3dn
+ R3 \| R4 \| R5 \| R5d \| R5dn \| R5n
+ `t2.medium` or larger \| `t3.medium` or larger 
+ X1 \| X1e
+ z1d

The following Elastic Graphics accelerators are available\. You can attach any Elastic Graphics accelerator to any supported instance type\.


| Elastic Graphics accelerator | Graphics memory \(GB\) | 
| --- | --- | 
| eg1\.medium | 1 | 
| eg1\.large | 2 | 
| eg1\.xlarge | 4 | 
| eg1\.2xlarge | 8 | 

An Elastic Graphics accelerator does not form part of the hardware of your instance\. Instead, it is network\-attached through a network interface, known as the *Elastic Graphics network interface*\. When you launch an instance with graphics acceleration, the Elastic Graphics network interface is created in your VPC for you\. 

The Elastic Graphics network interface is created in the same subnet and VPC as your instance and is assigned a private IPv4 address from that subnet\. The accelerator attached to your Amazon EC2 instance is allocated from a pool of available accelerators in the same Availability Zone as your instance\.

![\[An Elastic Graphics accelerator attached to an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/elastic-graphics.png)

Elastic Graphics accelerators support the API standards for OpenGL 4\.3 API and earlier, which can be used for batch applications or 3D\-graphics acceleration\. An Amazon\-optimized OpenGL library on your instance detects the attached accelerator\. It directs OpenGL API calls from your instance to the accelerator, which then processes the requests and returns the results\. Traffic between the instance and the accelerator uses the same bandwidth as the instance's network traffic so we recommend that you have adequate network bandwidth available\. Consult your software vendor for any OpenGL compliance and version questions\.

By default, the default security group for your VPC is associated with the Elastic Graphics network interface\. The Elastic Graphics network traffic uses the TCP protocol and port 2007\. Ensure that the security group for your instance allows for this\. For more information, see [Configuring your security groups](working-with-elastic-graphics.md#elastic-graphics-security)\.

## Pricing for Elastic Graphics<a name="elastic-graphics-pricing"></a>

You are charged for each second that an Elastic Graphics accelerator is attached to an instance in the `running` state when the accelerator is in the `Ok` state\. You are not charged for an accelerator attached to an instance that is in the `pending`, `stopping`, `stopped`, `shutting-down`, or `terminated` state\. You are also not charged when an accelerator is in the `Unknown` or `Impaired` state\.

Pricing for accelerators is available at On\-Demand rates only\. You can attach an accelerator to a Reserved, Scheduled, or Spot Instance\. The On\-Demand price for the accelerator applies in all cases\.

For more information, see [Amazon Elastic Graphics Pricing](https://aws.amazon.com/ec2/elastic-graphics/pricing/)\.

## Elastic Graphics limitations<a name="elastic-graphics-limitations"></a>

Before you start using Elastic Graphics accelerators, be aware of the following limitations:
+ You can attach accelerators only to Windows instances with Microsoft Windows Server 2012 R2 or later\. Linux instances are currently not supported\.
+ You can attach one accelerator to an instance at a time\.
+ You can attach an accelerator only during instance launch\. You cannot attach an accelerator to an existing instance\.
+ You can't share an accelerator between instances\.
+ You can't detach an accelerator from an instance or transfer it to another instance\. If you no longer require an accelerator, you must terminate your instance\. To change the accelerator type, create an AMI from your instance, terminate the instance, and launch a new instance with a different accelerator specification\.
+ The only supported versions of the OpenGL API are 4\.3 and earlier\. DirectX, CUDA, and OpenCL are not supported\.
+ The Elastic Graphics accelerator is not visible or accessible through the device manager of your instance\.
+ You can't reserve or schedule accelerator capacity\.
+ You can't attach accelerators to instances in EC2\-Classic\.
+ You can't attach accelerators to instances that are configured to use Instance Metadata Service v2 \(IMDSv2\)\. 