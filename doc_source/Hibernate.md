# Hibernate Your Instance<a name="Hibernate"></a>

When you hibernate an instance, we signal the operating system to perform hibernation \(suspend\-to\-disk\), which saves the contents from the instance memory \(RAM\) to your Amazon EBS root volume\. We persist the instance's Amazon EBS root volume and any attached Amazon EBS data volumes\. When you restart your instance, the Amazon EBS root volume is restored to its previous state, the RAM contents are reloaded, and the processes that were previously running on the instance are resumed\. Previously attached data volumes are reattached and the instance retains its instance ID\. For more information, see [Hibernate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
Hibernation is currently not supported on Windows instances\.