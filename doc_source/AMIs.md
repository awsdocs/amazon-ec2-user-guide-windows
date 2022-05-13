# Amazon Machine Images \(AMI\)<a name="AMIs"></a>

An Amazon Machine Image \(AMI\) is a supported and maintained image provided by AWS that provides the information required to launch an instance\. You must specify an AMI when you launch an instance\. You can launch multiple instances from a single AMI when you require multiple instances with the same configuration\. You can use different AMIs to launch instances when you require instances with different configurations\.

An AMI includes the following:
+ One or more Amazon Elastic Block Store \(Amazon EBS\) snapshots, or, for instance\-store\-backed AMIs, a template for the root volume of the instance \(for example, an operating system, an application server, and applications\)\.
+ Launch permissions that control which AWS accounts can use the AMI to launch instances\.
+ A block device mapping that specifies the volumes to attach to the instance when it's launched\.

**Topics**
+ [Boot modes](ami-boot.md)
+ [AWS Windows AMIs](windows-ami-version-history.md)
+ [Find a Windows AMI](finding-an-ami.md)
+ [Shared AMIs](sharing-amis.md)
+ [Paid AMIs](paid-amis.md)
+ [AMI lifecycle](ami-lifecycle.md)
+ [Use encryption with EBS\-backed AMIs](AMIEncryption.md)
+ [Monitor AMI events using Amazon EventBridge](monitor-ami-events.md)
+ [Understand AMI billing information](ami-billing-info.md)