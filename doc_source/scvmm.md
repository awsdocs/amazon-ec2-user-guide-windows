# AWS Systems Manager for Microsoft System Center VMM<a name="scvmm"></a>

AWS Systems Manager for Microsoft System Center Virtual Machine Manager \(SCVMM\) provides a simple, easy\-to\-use interface for managing AWS resources, such as EC2 instances, from Microsoft SCVMM\. It is implemented as an add\-in for the VMM console\. For more information, see [AWS Add\-ins for Microsoft System Center](https://aws.amazon.com/windows/system-center/)\.

![\[AWS Systems Manager add-in\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_systems_manager_scvmm.png)

## Features<a name="features"></a>
+ Administrators can grant permissions to users so that they can manage EC2 instances from SCVMM\.
+ Users can launch, view, reboot, stop, start, and terminate instances, if they have the required permissions\.
+ Users can get the passwords for their Windows instances and connect to them using RDP\.
+ Users can get the public DNS names for their Linux instances and connect to them using SSH\.
+ Users can import their Hyper\-V Windows virtual machines from SCVMM to Amazon EC2\.

## Limitations<a name="limitations"></a>
+ Users must have an account that they can use to log in to SCVMM\.
+ You can't import Linux virtual machines from SCVMM to Amazon EC2\.
+ This is not a comprehensive tool for creating and managing AWS resources\. The add\-in enables SCVMM users to get started quickly with the basic tasks for managing their EC2 instances\. Future releases might support managing additional AWS resources\.

## Requirements<a name="requirements"></a>
+ An AWS account
+ Microsoft System Center VMM 2012 R2 or System Center VMM 2012 SP1 with the latest update roll\-up

## Getting Started<a name="get-started"></a>

To get started, see the following documentation:
+ [Setting Up](scvmm-set-up.md)
+ [Managing EC2 Instances](scvmm-manage-ec2.md)
+ [Troubleshooting](scvmm-troubleshoot.md)