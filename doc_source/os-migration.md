# Performing a Server Migration<a name="os-migration"></a>

Migrating involves capturing settings, configurations, and data and porting these to a newer operating system on separate hardware\. After validation, the migrated system can be promoted to production\. You can migrate instances by launching a new instance from an AMI of the new operating system\. You can streamline the process further by using [AWS CloudFormation](https://aws.amazon.com/documentation/cloudformation/) and [Amazon EC2 Systems Manager](https://aws.amazon.com/documentation/ec2/#Amazon_EC2_Systems_Manager) to automatically apply settings and configurations to the new system with little manual work\.

**To migrate your server**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, **Owned by me**, and **Public images**\.

1. For **Search**, add the following filters and press Enter\. The **Search** values are case\-sensitive\.
   + **Owner:** Amazon images
   + **AMI Name:** Windows\_Server\-2008, Windows\_Server\-2012, or Windows\_Server\-2016

1. Launch a new instance from an AMI\.

1. Log on to the new instance and install all updates\.

1. Perform an application installation and configuration changes\.

1. Test the server\.

1. When validated, promote the server to production\.