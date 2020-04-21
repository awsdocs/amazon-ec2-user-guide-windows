# Modifying Reserved Instances<a name="ri-modifying"></a>

When your needs change, you can modify your Standard or Convertible Reserved Instances and continue to benefit from the billing benefit\. You can modify attributes such as the Availability Zone and scope of your Reserved Instance\.

**Note**  
You can also exchange a Convertible Reserved Instance for another Convertible Reserved Instance with a different configuration\. For more information, see [Exchanging Convertible Reserved Instances](ri-convertible-exchange.md)\.

After modification, the benefit of the Reserved Instances is applied only to instances that match the new parameters\. For example, if you change the Availability Zone of a reservation, the capacity reservation and pricing benefits are automatically applied to instance usage in the new Availability Zone\. Instances that no longer match the new parameters are charged at the On\-Demand rate, unless your account has other applicable reservations\.

If your modification request succeeds:
+ The modified reservation becomes effective immediately and the pricing benefit is applied to the new instances beginning at the hour of the modification request\. For example, if you successfully modify your reservations at 9:15PM, the pricing benefit transfers to your new instance at 9:00PM\. You can get the effective date of the modified Reserved Instances by using the [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command\.
+ The original reservation is retired\. Its end date is the start date of the new reservation, and the end date of the new reservation is the same as the end date of the original Reserved Instance\. If you modify a three\-year reservation that had 16 months left in its term, the resulting modified reservation is a 16\-month reservation with the same end date as the original one\.
+ The modified reservation lists a $0 fixed price and not the fixed price of the original reservation\.
+ The fixed price of the modified reservation does not affect the discount pricing tier calculations applied to your account, which are based on the fixed price of the original reservation\.

If your modification request fails, your Reserved Instances maintain their original configuration, and are immediately available for another modification request\.

There is no fee for modification, and you do not receive any new bills or invoices\.

You can modify your reservations as frequently as you like, but you cannot change or cancel a pending modification request after you submit it\. After the modification has completed successfully, you can submit another modification request to roll back any changes you made, if needed\.

**Topics**
+ [Requirements and restrictions for modification](#ri-modification-limits)
+ [Submitting modification requests](#ri-modification-process)
+ [Troubleshooting modification requests](#ri-modification-process-messages)

## Requirements and restrictions for modification<a name="ri-modification-limits"></a>

You can modify these attributes as follows\.


| Modifiable attribute | Supported platforms | Limitations | 
| --- | --- | --- | 
|  Change **Availability Zones** within the same Region  |  Linux and Windows  | \- | 
|  Change the **scope** from Availability Zone to Region and vice versa  |  Linux and Windows  |  If you change the scope from Availability Zone to Region, you lose the capacity reservation benefit\. If you change the scope from Region to Availability Zone, you lose Availability Zone flexibility and instance size flexibility \(if applicable\)\. For more information, see [How Reserved Instances are applied](apply_ri.md)\.  | 
|  Change the **instance size** within the same instance family  |  Linux/UNIX only Instance size flexibility is not available for Reserved Instances on the other platforms, which include Linux with SQL Server Standard, Linux with SQL Server Web, Linux with SQL Server Enterprise, Red Hat Enterprise Linux, SUSE Linux, Windows, Windows with SQL Standard, Windows with SQL Server Enterprise, and Windows with SQL Server Web\.  |  The reservation must use default tenancy\. Some instance families are not supported, because there are no other sizes available\. For more information, see [Support for modifying instance sizes](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ri-modifying.html#ri-modification-instancemove) in the *Amazon EC2 User Guide for Linux Instances*\.  | 
|  Change the **network** from EC2\-Classic to Amazon VPC and vice versa  |  Linux and Windows  |  The network platform must be available in your AWS account\. If you created your AWS account after 2013\-12\-04, it does not support EC2\-Classic\.  | 

**Requirements**

Amazon EC2 processes your modification request if there is sufficient capacity for your target configuration \(if applicable\), and if the following conditions are met:
+ The Reserved Instance cannot be modified before or at the same time that you purchase it
+ The Reserved Instance must be active
+ There cannot be a pending modification request
+ The Reserved Instance is not listed in the Reserved Instance Marketplace
+ The input Reserved Instances are all Standard Reserved Instances or all Convertible Reserved Instances, not some of each type
+ The input Reserved Instances must expire within the same hour, if they are Standard Reserved Instances
+ The Reserved Instance is not a G4 instance\.

## Submitting modification requests<a name="ri-modification-process"></a>

Before you modify your Reserved Instances, ensure that you have read the applicable [restrictions](#ri-modification-limits)\.

**To modify your Reserved Instances using the AWS Management Console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Reserved Instances** page, select one or more Reserved Instances to modify, and choose **Actions**, **Modify Reserved Instances**\.
**Note**  
If your Reserved Instances are not in the active state or cannot be modified, **Modify Reserved Instances** is disabled\.

1. The first entry in the modification table displays attributes of selected Reserved Instances, and at least one target configuration beneath it\. The **Units** column displays the total instance size footprint\. Choose **Add** for each new configuration to add\. Modify the attributes as needed for each configuration, and then choose **Continue**:
   + **Scope**: Choose whether the configuration applies to an Availability Zone or to the whole Region\.
   + **Availability Zone**: Choose the required Availability Zone\. Not applicable for regional Reserved Instances\.
   + **Count**: Specify the number of instances\. To split the Reserved Instances into multiple configurations, reduce the count, choose **Add**, and specify a count for the additional configuration\. For example, if you have a single configuration with a count of 10, you can change its count to 6 and add a configuration with a count of 4\. This process retires the original Reserved Instance after the new Reserved Instances are activated\.

1. To confirm your modification choices when you finish specifying your target configurations, choose **Submit Modifications**\.

1. You can determine the status of your modification request by looking at the **State** column in the Reserved Instances screen\. The following are the possible states\.
   + **active* \(pending modification\)*** — Transition state for original Reserved Instances
   + **retired* \(pending modification\)*** — Transition state for original Reserved Instances while new Reserved Instances are being created
   + **retired** — Reserved Instances successfully modified and replaced
   + **active** — One of the following:
     + New Reserved Instances created from a successful modification request
     + Original Reserved Instances after a failed modification request

**To modify your Reserved Instances using the command line**

1. To modify your Reserved Instances, you can use one of the following commands:
   + [modify\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-reserved-instances.html) \(AWS CLI\)
   + [Edit\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2ReservedInstance.html) \(AWS Tools for Windows PowerShell\)

1. To get the status of your modification request \(`processing`, `fulfilled`, or `failed`\), use one of the following commands:
   + [describe\-reserved\-instances\-modifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-modifications.html) \(AWS CLI\)
   + [Get\-EC2ReservedInstancesModification](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesModification.html) \(AWS Tools for Windows PowerShell\)

## Troubleshooting modification requests<a name="ri-modification-process-messages"></a>

If the target configuration settings that you requested were unique, you receive a message that your request is being processed\. At this point, Amazon EC2 has only determined that the parameters of your modification request are valid\. Your modification request can still fail during processing due to unavailable capacity\.

In some situations, you might get a message indicating incomplete or failed modification requests instead of a confirmation\. Use the information in such messages as a starting point for resubmitting another modification request\. Ensure that you have read the applicable [restrictions](#ri-modification-limits) before submitting the request\.

**Not all selected Reserved Instances can be processed for modification**  
Amazon EC2 identifies and lists the Reserved Instances that cannot be modified\. If you receive a message like this, go to the **Reserved Instances** page in the Amazon EC2 console and check the information for the Reserved Instances\.

**Error in processing your modification request**  
You submitted one or more Reserved Instances for modification and none of your requests can be processed\. Depending on the number of reservations you are modifying, you can get different versions of the message\. 

Amazon EC2 displays the reasons why your request cannot be processed\. For example, you might have specified the same target configuration—a combination of Availability Zone and platform—for one or more subsets of the Reserved Instances you are modifying\. Try submitting the modification requests again, but ensure that the instance details of the reservations match, and that the target configurations for all subsets being modified are unique\.