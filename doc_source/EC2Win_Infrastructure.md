# Amazon EC2 Basic Infrastructure for Windows<a name="EC2Win_Infrastructure"></a>

As you get started with Amazon EC2, you'll benefit from understanding the components of its basic infrastructure and how they compare or contrast with your own data centers\.

**Topics**
+ [Amazon Machine Images and Instances](#AMISandInstances)
+ [Regions and Availability Zones](#EC2Win_Regions)
+ [Storage](#EC2Win_Storage)
+ [Root Device Volume](#RootDevice_WinAMI)
+ [Networking and Security](#EC2Win_Sec)
+ [AWS Identity and Access Management](#EC2Win_IAM)
+ [Differences between Windows Server and an Amazon EC2 Windows Instance](#EC2InstanceAndWindowsServer)
+ [Designing Your Applications to Run on Amazon EC2 Windows Instances](#Win_AppDesign)

## Amazon Machine Images and Instances<a name="AMISandInstances"></a>

An *Amazon Machine Image \(AMI\)* is a template that contains a software configuration \(for example, an operating system, an application server, and applications\)\. From an AMI, you launch *instances*, which are copies of the AMI running as virtual servers in the cloud\.

Amazon publishes many AMIs that contain common software configurations for public use\. In addition, members of the AWS developer community have published their own custom AMIs\. You can also create your own custom AMI or AMIs; doing so enables you to quickly and easily start new instances that have everything you need\. For example, if your application is a website or web service, your AMI could include a web server, the associated static content, and the code for the dynamic pages\. As a result, after you launch an instance from this AMI, your web server starts, and your application is ready to accept requests\.

You can launch different types of instances from a single AMI\. An *instance type* essentially determines the hardware of the host computer used for your instance\. Each instance type offers different compute and memory facilities\. Select an instance type based on the amount of memory and computing power that you need for the applications or software that you plan to run on the instance\. For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. You can also launch multiple instances from an AMI, as shown in the following figure\.

![\[Launch multiple instances from an AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/architecture_ami_instance.png)

Your Windows instances keep running until you stop or terminate them, or until they fail\. If an instance fails, you can launch a new one from the AMI\.

Your AWS account has a limit on the number of instances that you can have running\. For more information about this limit, and how to request an increase, see [How many instances can I run in Amazon EC2](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 General FAQ\.

### Bare Metal Instances<a name="bare-metal-instances"></a>

Amazon now offers EC2 bare metal instances which are available with the EC2 Storage Optimized [Amazon EC2 I3 instance family](https://aws.amazon.com/ec2/instance-types/i3) in the form of i3\.metal instances\. Bare metal instances allow you to run applications that benefit from deep performance analysis tools, specialized workloads that require direct access to bare metal infrastructure, legacy workloads not supported in virtual environments, licensing\-restricted Tier 1 business critical applications, as well as virtualization\-secured containers\.

## Regions and Availability Zones<a name="EC2Win_Regions"></a>

Amazon has data centers in different areas of the world \(for example, North America, Europe, and Asia\)\. Correspondingly, Amazon EC2 is available to use in different *Regions*\. By launching instances in separate Regions, you can design your application to be closer to specific customers or to meet legal or other requirements\. Prices for Amazon EC2 usage vary by Region \(for more information about pricing by Region, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing)\)\. 

Each Region contains multiple distinct locations called *Availability Zones*\. Each Availability Zone is engineered to be isolated from failures in other Availability Zones, and to provide inexpensive, low\-latency network connectivity to other zones in the same Region\. By launching instances in separate Availability Zones, you can protect your applications from the failure of a single location\. 

![\[Regions and Availability Zones\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_regions.png)

For more information about the available Regions and Availability Zones, see [Using Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Storage<a name="EC2Win_Storage"></a>

When using Amazon EC2, you may have data that you need to store\. Amazon EC2 offers the following storage options:
+  [Amazon Elastic Block Store \(Amazon EBS\)](https://aws.amazon.com/ebs) 
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+  [Amazon Simple Storage Service \(Amazon S3\)](https://aws.amazon.com/s3) 

The following figure shows the relationship between these types of storage\.

![\[Storage options for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/architecture_storage_windows.png)

### Amazon EBS Volumes<a name="EC2Win_StorageEBS"></a>

Amazon EBS volumes are the recommended storage option for the majority of use cases\. Amazon EBS provides your instances with persistent, block\-level storage\. Amazon EBS volumes are essentially hard disks that you can attach to a running instance\.

Amazon EBS is especially suited for applications that require a database, a file system, or access to raw block\-level storage\.

As illustrated in the previous figure, you can attach multiple volumes to an instance\. Also, to keep a backup copy of your data, you can create a *snapshot* of an EBS volume, which is stored in Amazon S3\. You can create a new Amazon EBS volume from a snapshot, and attach it to another instance\. You can also detach a volume from an instance and attach it to a different instance\. The following figure illustrates the life cycle of an EBS volume\.

![\[Life cycle of an EBS volume\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/state_diagram_ebs.png)

For more information about Amazon EBS volumes, see [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)\.

### Instance Store<a name="EC2Win_InstanceStore"></a>

*Instance store* provides your instances with temporary, block\-level storage\. This is storage that is physically attached to the host computer\. The data on an instance store volume doesn't persist when the associated instance is stopped or terminated\. For a list of instance store volumes available on each supported instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\. 

Instance store is an option for inexpensive temporary storage\. You can use instance store volumes if you don't require data persistence\. For more information about instance store volumes, see [Amazon EC2 Instance Store](InstanceStorage.md)\.

### Amazon S3<a name="EC2Win_StorageS3"></a>

Amazon S3 is storage for the Internet\. It provides a simple web service interface that enables you to store and retrieve any amount of data from anywhere on the web\. For more information about Amazon S3, see the [Amazon S3 product page](https://aws.amazon.com/s3)\.

## Root Device Volume<a name="RootDevice_WinAMI"></a>

When you launch an instance, the *root device volume* contains the image used to boot the instance\. When you launch a Windows instance, a root EBS volume is created from an EBS snapshot and attached to the instance\.

By default, the root volume is deleted when the instance terminates \(the `DeleteOnTermination` attribute is `true`\)\. Using the console, you can change `DeleteOnTermination` when you launch an instance\. To change this attribute for a running instance, you must use the command line\.

**To change the root device volume of an instance to persist at launch using the console**

1. Open the Amazon EC2 console\.

1. From the Amazon EC2 console dashboard, click **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose the AMI to use and click **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect the **Delete On Termination** check box for the root volume\.

1. Complete the remaining wizard pages, and then click **Launch**\.

You can verify the setting by viewing details for the root device volume on the instance's details pane\. Next to **Block devices**, choose the entry for the root device volume\. By default, **Delete on termination** is `True`\. If you change the default behavior, **Delete on termination** is `False`\.

**To change the root device volume of an instance to persist using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Networking and Security<a name="EC2Win_Sec"></a>

By default, an instance is assigned public IPv4 address only if it's launched into a default VPC\. An instance that's launched into a nondefault VPC must be specifically assigned a public IPv4 address at launch, or you must modify your subnet's default public IPv4 addressing behavior\.

Instances can fail or terminate for reasons outside of your control\. If one fails and you launch a replacement instance, the replacement has a different public IPv4 address than the original\. However, if your application needs a static IPv4 address, Amazon EC2 offers *Elastic IP addresses*\. For more information, see [Amazon EC2 Instance IP Addressing](using-instance-addressing.md)\.

You can use *security groups* to control who can access your instances\. These are analogous to an inbound network firewall that enables you to specify the protocols, ports, and source IP ranges that are allowed to reach your instances\. You can create multiple security groups and assign different rules to each group\. You can then assign each instance to one or more security groups, and we use the rules to determine which traffic is allowed to reach the instance\. You can configure a security group so that only specific IP addresses or specific security groups have access to the instance\. For more information, see [Amazon EC2 Security Groups for Windows Instances](ec2-security-groups.md)\. 

## AWS Identity and Access Management<a name="EC2Win_IAM"></a>

AWS Identity and Access Management \(IAM\) enables you to do the following:
+ Create users and groups under your AWS account
+ Assign unique security credentials to each user under your AWS account
+ Control each user's permissions to perform tasks using AWS resources
+ Allow the users in another AWS account to share your AWS resources
+ Create roles for your AWS account and define the users or services that can assume them
+ Use existing identities for your enterprise to grant permissions to perform tasks using AWS resources

By using IAM with Amazon EC2, you can control whether users in your organization can perform a task using specific Amazon EC2 API actions and whether they can use specific AWS resources\.

For more information about IAM, see the following:
+ [Creating an IAM Group and Users](security-iam.md#creating-an-iam-group)
+ [IAM Policies for Amazon EC2](iam-policies-for-amazon-ec2.md)
+ [IAM Roles for Amazon EC2](iam-roles-for-amazon-ec2.md)
+ [Identity and Access Management \(IAM\)](https://aws.amazon.com/iam)
+ [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)

## Differences between Windows Server and an Amazon EC2 Windows Instance<a name="EC2InstanceAndWindowsServer"></a>

After you launch your Amazon EC2 Windows instance, it behaves like a traditional server running Windows Server\. For example, both Windows Server and an Amazon EC2 instance can be used to run your web applications, conduct batch processing, or manage applications requiring large\-scale computations\. However, there are important differences between the server hardware model and the cloud computing model\. The way an Amazon EC2 instance runs is not the same as the way a traditional server running Windows Server runs\.

Before you begin launching Amazon EC2 Windows instances, you should be aware that the architecture of applications running on cloud servers can differ significantly from the architecture for traditional application models running on your hardware\. Implementing applications on cloud servers requires a shift in your design process\. 

The following table describes some key differences between Windows Server and an Amazon EC2 Windows instance\. 


| Windows Server | Amazon EC2 Windows Instance | 
| --- | --- | 
|  Resources and capacity are physically limited\.  |  Resources and capacity are scalable\.  | 
|  You pay for the infrastructure, even if you don't use it\.  |  You pay for the usage of the infrastructure\. We stop charging you for the instance as soon as you stop or terminate it\.  | 
|  Occupies physical space and must be maintained on a regular basis\.  |  Doesn't occupy physical space and does not require regular maintenance\.  | 
|  Starts with push of the power button \(known as *cold booting*\)\.  |  Starts with the launch of the instance\.  | 
|  You can keep the server running until it is time to shut it down, or put it in a sleep or hibernation state \(during which the server is powered down\)\.  |  You can keep the server running, or stop and restart it \(during which the instance is moved to a new host computer\)\.  | 
|  When you shut down the server, all resources remain intact and in the state they were in when you switched it off\. Information you stored on the hard drives persists and can be accessed whenever it's needed\. You can restore the server to the running state by powering it on\.  |  When you terminate the instance, its infrastructure is no longer available to you\. You can't connect to or restart an instance after you've terminated it\. However, you can create an image from your instance while it's running, and launch new instances from the image at any time\.  | 

A traditional server running Windows Server goes through the states shown in the following diagram\.

![\[The States of a Traditional Windows Server\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/windows_server_lifecycle.png)

An Amazon EC2 Windows instance is similar to the traditional Windows Server, as you can see by comparing the following diagram with the previous diagram for Windows Server\. After you launch an instance, it briefly goes into the pending state while registration takes place, then it goes into the running state\. The instance remains active until you stop or terminate it\. You can't restart an instance after you terminate it\. You can create a backup image of your instance while it's running, and launch a new instance from that backup image\.

![\[Instance and AMI lifecycle\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/instance_lifecycle.png)

## Designing Your Applications to Run on Amazon EC2 Windows Instances<a name="Win_AppDesign"></a>

It is important that you consider the differences mentioned in the previous section when you design your applications to run on Amazon EC2 Windows instances\.

Applications built for Amazon EC2 use the underlying computing infrastructure on an as\-needed basis\. They draw on necessary resources \(such as storage and computing\) on demand in order to perform a job, and relinquish the resources when done\. In addition, they often dispose of themselves after the job is done\. While in operation, the application scales up and down elastically based on resource requirements\. An application running on an Amazon EC2 instance can terminate and recreate the various components at will in case of infrastructure failures\. 

When designing your Windows applications to run on Amazon EC2, you can plan for rapid deployment and rapid reduction of compute and storage resources, based on your changing needs\. 

When you run an Amazon EC2 Windows instance, you don't need to provision the exact system package of hardware, software, and storage, the way you do with Windows Server\. Instead, you can focus on using a variety of cloud resources to improve the scalability and overall performance of your Windows application\. 

With Amazon EC2, designing for failure and outages is an integral and crucial part of the architecture\. As with any scalable and redundant system, architecture of your system should account for computing, network, and storage failures\. You have to build mechanisms in your applications that can handle different kinds of failures\. The key is to build a modular system with individual components that are not tightly coupled, can interact asynchronously, and treat one another as black boxes that are independently scalable\. Thus, if one of your components fails or is busy, you can launch more instances of that component without breaking your current system\.

Another key element to designing for failure is to distribute your application geographically\. Replicating your application across geographically distributed Regions improves high availability in your system\.

Amazon EC2 infrastructure is programmable and you can use scripts to automate the deployment process, to install and configure software and applications, and to bootstrap your virtual servers\.

You should implement security in every layer of your application architecture running on an Amazon EC2 Windows instance\. If you are concerned about storing sensitive and confidential data within your Amazon EC2 environment, you should encrypt the data before uploading it\.