# Amazon EC2 Windows instances<a name="EC2Win_Infrastructure"></a>

The following is an introduction to key components of Amazon EC2 and how a Windows instance compares to running Windows Server on premises\.

## Instances and AMIs<a name="ec2-instances-and-amis"></a>

An *Amazon Machine Image \(AMI\)* is a template that contains a software configuration \(for example, an operating system, an application server, and applications\)\. From an AMI, you launch *instances*, which are copies of the AMI running as virtual servers in the cloud\.

Amazon publishes many AMIs that contain common software configurations for public use\. In addition, members of the AWS developer community have published their own custom AMIs\. You can also create your own custom AMIs\. Doing so enables you to quickly and easily start new instances that have everything you need\. For example, if your application is a website or web service, your AMI could include a web server, the associated static content, and the code for the dynamic pages\. As a result, after you launch an instance from this AMI, your web server starts, and your application is ready to accept requests\.

To improve launch time for Windows instances, you can optimize your AMI for faster launching, which creates a set of pre\-provisioned snapshots to launch instances up to 65% faster\. To learn more, see [Configure your Windows AMI for faster launching](win-ami-config-fast-launch.md)

You can launch different types of instances from a single AMI\. An *instance type* determines the infrastructure that is used for your instance\. Some instance types are intended for general purpose use, while others support optimizations for specific uses such as high performance processors for computing, enhanced memory for processing large data sets, and fast I/O for storage\. Select an instance type that delivers the performance and size that you need for the applications or software that you plan to run on the instance\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

Your Windows instances keep running until you stop or terminate them, or until they fail\. If an instance fails, you can launch a new one from the AMI\.

Your AWS account has a limit on the number of instances that you can have running\. For more information about this limit, and how to request an increase, see [How many instances can I run in Amazon EC2](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 General FAQ\.

## Differences between Windows Server and Windows instances<a name="EC2InstanceAndWindowsServer"></a>

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

## Design your applications to run on Windows instances<a name="Win_AppDesign"></a>

It is important that you consider the differences mentioned in the previous section when you design your applications to run on Amazon EC2 Windows instances\.

Applications built for Amazon EC2 use the underlying computing infrastructure on an as\-needed basis\. They draw on necessary resources \(such as storage and computing\) on demand in order to perform a job, and relinquish the resources when done\. In addition, they often dispose of themselves after the job is done\. While in operation, the application scales up and down elastically based on resource requirements\. An application running on an Amazon EC2 instance can terminate and recreate the various components at will in case of infrastructure failures\. 

When designing your Windows applications to run on Amazon EC2, you can plan for rapid deployment and rapid reduction of compute and storage resources, based on your changing needs\. 

When you run an Amazon EC2 Windows instance, you don't need to provision the exact system package of hardware, software, and storage, the way you do with Windows Server\. Instead, you can focus on using a variety of cloud resources to improve the scalability and overall performance of your Windows application\. 

With Amazon EC2, designing for failure and outages is an integral and crucial part of the architecture\. As with any scalable and redundant system, architecture of your system should account for computing, network, and storage failures\. You have to build mechanisms in your applications that can handle different kinds of failures\. The key is to build a modular system with individual components that are not tightly coupled, can interact asynchronously, and treat one another as black boxes that are independently scalable\. Thus, if one of your components fails or is busy, you can launch more instances of that component without breaking your current system\.

Another key element to designing for failure is to distribute your application geographically\. Replicating your application across geographically distributed Regions improves high availability in your system\.

Amazon EC2 infrastructure is programmable and you can use scripts to automate the deployment process, to install and configure software and applications, and to bootstrap your virtual servers\.

You should implement security in every layer of your application architecture running on an Amazon EC2 Windows instance\. If you are concerned about storing sensitive and confidential data within your Amazon EC2 environment, you should encrypt the data before uploading it\.