# Modify an AMI<a name="modify-ami"></a>

You can modify a limited set of Amazon Machine Image \(AMI\) attributes, such as the AMI's description and sharing properties\. However, AMI content \(volume binary data\) can't be modified\. To modify the AMI content, you must [create a new AMI](Creating_EBSbacked_WinAMI.md)\.

**Important**  
You can't modify the content \(volume binary data\) of an EBS\-backed AMI because the snapshots that back them are immutable\.

For the AMI attributes that can be modified, see [ModifyImageAttribute](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyImageAttribute.html) in the *Amazon EC2 API Reference*\.

The following topics provide instructions for using the Amazon EC2 console and AWS CLI to modify the attributes of an AMI:
+ [Make an AMI public](sharingamis-intro.md)
+ [Share an AMI with specific organizations or organizational units](share-amis-with-organizations-and-OUs.md)
+ [Share an AMI with specific AWS accounts](sharingamis-explicit.md)
+ [Use paid support](paid-amis.md#using-paid-amis-support)
+ [Configure the AMI](configuring-IMDS-new-instances.md#configure-IMDS-new-instances-ami-configuration)