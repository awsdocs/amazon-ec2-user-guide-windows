# EC2Launch v2 overview<a name="ec2launch-v2-overview"></a>

EC2Launch v2 is a service that performs tasks during instance startup and runs if an instance is stopped and later started, or restarted\. 

**Topics**
+ [Compare Amazon EC2 launch services](#ec2launch-v2-agent-compare)
+ [EC2Launch v2 concepts](#ec2launch-v2-concepts)
+ [EC2Launch v2 tasks](#ec2launch-v2-tasks)

## Compare Amazon EC2 launch services<a name="ec2launch-v2-agent-compare"></a>

The following table shows the major functional differences between EC2Config, EC2Launch v1, and EC2Launch v2\.


| Feature | EC2Config | EC2Launch v1 | EC2Launch v2 | 
| --- | --- | --- | --- | 
| Executed as | Windows Service |  PowerShell Scripts  | Windows Service | 
| Supports |  Windows 2003 Windows 2008 Windows 2008 R2 Windows 2012 Windows 2012 R2  |  Windows 2016 Windows 2019 \(LTSC and SAC\)  |  Windows 2008 Windows 2008 R2 Windows 2012 Windows 2012 R2 Windows 2016 Windows 2019 \(LTSC and SAC\)  | 
|  Configuration file  | XML | XML |  YAML  | 
|  Set Administrator username  | No | No |  Yes  | 
|  User data size  | 16 KB | 16 KB |  60 KB \(compressed\)  | 
|  Local user data baked on AMI  | No | No | Yes, configurable | 
| Task configuration in user data | No | No | Yes | 
|  Configurable wallpaper  | No | No |  Yes  | 
|  Customize task execution order  | No | No |  Yes  | 
|  Configurable tasks  | 15 |  9  |  20 at launch  | 
|  Supports Windows Event Viewer  | Yes |  No  | Yes | 
|  Number of Event Viewer event types  | 2 |  0  |  30  | 

## EC2Launch v2 concepts<a name="ec2launch-v2-concepts"></a>

The following concepts are useful to understand when considering EC2Launch v2\.

**Task**  
A task can be invoked to perform an action on an instance\. For a complete list of available tasks for EC2Launch v2, see [EC2Launch v2 tasks](#ec2launch-v2-tasks)\. Each task includes a set of stages in which it can run, a defined frequency, and inputs\. Tasks can be configured in the `agent-config` file or through `user-data`\.

**Stages**  
A stage is a logical grouping of tasks that are run by the service\. Some tasks can run only in a specific stage\. Others can run in multiple stages\. When using local data, you must specify the stage in which a task will run\. When using user data, the stage is implied\.

The following list shows the stages in the order in which they run:

1. Boot

1. Network

1. PreReady

1. PostReady

1. UserData

**Frequency**  
Task frequency is used to schedule when tasks should run, depending on the boot context\.

The following frequencies can be specified:
+ Once — The task runs once, when the AMI has booted for the first time \(finished Sysprep\)\.
+ Always — The task runs every time that the AMI boots, including the first time\.

**`agent-config`**  
`agent-config` is a file that is located in the configuration folder for EC2Launch v2\. It includes configuration for the boot, network, preready, and postready stages\. This file is used to specify the configuration for an instance for tasks that should run when the AMI is either booted for the first time or for subsequent times\.

By default, the EC2Launch v2 installation installs an `agent-config` file that includes recommended configurations that are used in standard Amazon Windows AMIs\. You can update the configuration file to alter the default boot experience for your AMI that EC2Launch v2 specifies\.

**User data**  
User data is data that is configurable when you launch an instance\. You can update user data to dynamically change how custom AMIs or quickstart AMIs are configured\. EC2Launch v2 supports 60 kB user data input length\. User data includes only the userdata stage, and therefore runs after the `agent-config` file\.

## EC2Launch v2 tasks<a name="ec2launch-v2-tasks"></a>

EC2Launch v2 can perform the following tasks at each boot:
+ Set up new and optionally customized wallpaper that renders information about the instance\.
+ Set the attributes for the administrator account that is created on the local machine\.
+ Add DNS suffixes to the list of search suffixes\. Only suffixes that do not already exist are added to the list\.
+ Set drive letters for any additional volumes and extend them to use available space\.
+ Write files to the disk, either from the internet or from the configuration\. If the content is in the configuration, it can be base64 decoded or encoded\. If the content is from the internet, it can be unzipped\.
+ Execute scripts either from the internet or from the configuration\. If the script is from the configuration, it can be base64 decoded\. If the script is from the internet, it can be unzipped\.
+ Execute a program with given arguments\.
+ Set the computer name\.
+ Send instance information to the Amazon EC2 console\.
+ Send the RDP certificate thumbprint to the EC2 console\.
+ Dynamically extend the operating system partition to include any unpartitioned space\.
+ Execute user data\. For more information about specifying user data, see [EC2Launch v2 task configuration](ec2launch-v2-settings.md#ec2launch-v2-task-configuration)\.
+ Set persistent static routes to reach the metadata service and KMS servers\.
+ Set non\-boot partitions to MBR or GPT\.
+ Start the Systems Manager \(SSM\) service following Sysprep\.
+ Optimize ENA settings\.
+ Enable OpenSSH for later Windows versions\.
+ Enable Jumbo Frames\.
+ Set Sysprep to run at with EC2Launch v2\.
+ Publish Windows event logs\.