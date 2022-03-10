# Elastic Graphics maintenance<a name="elastic-graphics-maintenance"></a>

AWS might determine that an Elastic Graphics accelerator is in an *unhealthy* state if:
+ A security or infrastructure update is needed
+ A software update is needed
+ There is an issue with the underlying host

When AWS determines that an Elastic Graphics accelerator is in an unhealthy state, it schedules the accelerator for retirement\. AWS notifies you of the accelerator's pending retirement, and provides you with the remedial steps that you need to take\.

**Topics**
+ [How will I be notified?](#egpu-retirement-notify)
+ [What do I need to do?](#egpu-retirement-actions)
+ [What happens when an accelerator reaches its retirement date?](#egpu-retirement-date)

## How will I be notified?<a name="egpu-retirement-notify"></a>

When AWS schedules an Elastic Graphics accelerator for retirement, it sends an accelerator retirement notice to your [AWS Health Dashboard](https://phd.aws.amazon.com/phd/home#/account/dashboard/open-issues)\. AWS also sends an email to the email address that is associated with your AWS account\. This is the same email address that you use to log in to the AWS Management Console\.

**Note**  
If you use an email account that you don't check regularly, use the AWS Health Dashboard to determine if any of your Elastic Graphics accelerators are scheduled for retirement\. You can also change the contact information for your AWS account on the [ Account Settings](https://console.aws.amazon.com/billing/home?#/account) page\.

The retirement notice provides the following:
+ The ID of instance to which the accelerator is attached
+ Information about the issue impacting the accelerator
+ The retirement date for the accelerator
+ The remedial steps that you should take

## What do I need to do?<a name="egpu-retirement-actions"></a>

When you are notified that your Elastic Graphics accelerator is scheduled for retirement, you must [stop and start the instance](Stop_Start.md) to which the accelerator is attached for the old, *unhealthy* accelerator to be replaced with a new, *healthy* accelerator\.

We recommend that you close graphic applications running on the instance before you stop and restart the instance\.

**Important**  
If you do not stop and start your instance before the scheduled retirement date, the accelerator associated with your instance is automatically stopped, which might cause your applications to stop working\.  
You must stop and start the instance\. Rebooting the instance will not replace the unhealthy accelerator with a healthy one\.

## What happens when an accelerator reaches its retirement date?<a name="egpu-retirement-date"></a>

When an unhealthy Elastic Graphics accelerator reaches its scheduled retirement date, AWS permanently terminates it\. To receive a replacement for your unhealthy accelerator, either before or after the retirement date, you must stop and start the instance to which the accelerator is attached\.

If you do not stop and start your instance before the scheduled retirement date, the accelerator associated with your instance is automatically stopped, which might cause your applications to stop working\.