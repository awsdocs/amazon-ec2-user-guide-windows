# Amazon Machine Images \(AMI\)<a name="AMIs"></a>

An Amazon Machine Image \(AMI\) provides the information required to launch an instance\. You must specify an AMI when you launch an instance\. You can launch multiple instances from a single AMI when you need multiple instances with the same configuration\. You can use different AMIs to launch instances when you need instances with different configurations\.

An AMI includes the following:
+ One or more Amazon Elastic Block Store \(Amazon EBS\) snapshots, or, for instance\-store\-backed AMIs, a template for the root volume of the instance \(for example, an operating system, an application server, and applications\)\.
+ Launch permissions that control which AWS accounts can use the AMI to launch instances\.
+ A block device mapping that specifies the volumes to attach to the instance when it's launched\.

**Topics**
+ [AWS Windows AMIs](windows-ami-version-history.md)
+ [Find a Windows AMI](finding-an-ami.md)
+ [Shared AMIs](sharing-amis.md)
+ [Paid AMIs](paid-amis.md)
+ [Automate the EBS\-backed AMI lifecycle](automating-amis.md)
+ [Use encryption with EBS\-backed AMIs](AMIEncryption.md)
+ [Copy an AMI](CopyingAMIs.md)
+ [Understand AMI billing information](ami-billing-info.md)