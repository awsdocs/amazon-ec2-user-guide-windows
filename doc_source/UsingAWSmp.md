# Using the AWS Management Pack<a name="UsingAWSmp"></a>

You can use the AWS Management Pack to monitor the health of your AWS resources\.

**Topics**
+ [Views](#AWSmpViews)
+ [Discoveries](#Discoveries)
+ [Monitors](#Monitors)
+ [Rules](#Rules)
+ [Events](#EventIDs)
+ [Health Model](#HealthModel)
+ [Customizing the AWS Management Pack](#CustomizingAWSmp)

## Views<a name="AWSmpViews"></a>

The AWS Management Pack provides the following views, which are displayed in the **Monitoring** workspace of the Operations console\.

**Topics**
+ [EC2 Instances](#awsmp-ec2-instances-view)
+ [Amazon EBS Volumes](#awsmp-ebs-volumes-view)
+ [Elastic Load Balancers](#awsmp-load-balancers-view)
+ [AWS Elastic Beanstalk Applications](#awsmp-beanstalk-apps-view)
+ [AWS CloudFormation Stacks](#awsmp-cfn-stacks-view)
+ [Amazon Performance Views](#awsmp-performance-views)
+ [Amazon CloudWatch Metric Alarms](#awsmp-metric-alarms-view)
+ [AWS Alerts](#awsmp-aws-alerts-view)
+ [Watcher Nodes \(System Center Operations Manager 2007 R2\)](#awsmp-watcher-nodes-view)

### EC2 Instances<a name="awsmp-ec2-instances-view"></a>

View the health state of the EC2 instances for a particular AWS account, from all Availability Zones and regions\. The view also includes EC2 instances running in a virtual private cloud \(VPC\)\. The AWS Management Pack retrieves tags, so you can search and filter the list using those tags\.

![\[EC2 instances view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ec2_instance_view.png)

When you select an EC2 instance, you can perform instance health tasks:
+ **Open Amazon Console**: Launches the AWS Management Console in a web browser\.
+ **Open RDP to Amazon EC2 Instance**: Opens an RDP connection to the selected Windows instance\.
+ **Reboot Amazon EC2 Instance**: Reboots the selected EC2 instance\. 
+ **Start Amazon EC2 Instance**: Starts the selected EC2 instance\.
+ **Stop Amazon EC2 Instance**: Stops the selected EC2 instance\.

**EC2 Instances Diagram View**  
Shows the relationship of an instance with other components\.

![\[EC2 instance diagram view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/instance_diagram_view.png)

### Amazon EBS Volumes<a name="awsmp-ebs-volumes-view"></a>

Shows the health state of all the Amazon EBS volumes for a particular AWS account from all Availability Zones and regions\.

![\[Amazon Elastic Block Store volumes view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ebs_volume_view.png)

**Amazon EBS Volumes Diagram View**  
Shows an Amazon EBS volume and any associated alarms\. The following illustration shows an example:

![\[Amazon Elastic Block Store volume diagram view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/ebs_volume_diagram_view.png)

### Elastic Load Balancers<a name="awsmp-load-balancers-view"></a>

Shows the health state of all the load balancers for a particular AWS account from all regions\.

![\[Elastic Load Balancing view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/elb_view.png)

**Elastic Load Balancing Diagram View**  
Shows the Elastic Load Balancing relationship with other components\. The following illustration shows an example:

![\[Elastic Load Balancing diagram view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/elb_diagram_view.png)

### AWS Elastic Beanstalk Applications<a name="awsmp-beanstalk-apps-view"></a>

Shows the state of all discovered AWS Elastic Beanstalk applications\.

![\[AWS Elastic Beanstalk application state view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/beanstalk_state_view.png)

**AWS Elastic Beanstalk Applications Diagram View**  
Shows the AWS Elastic Beanstalk application, application environment, application configuration, and application resources objects\.

![\[AWS Elastic Beanstalk diagram view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/beanstalk_diagram_view.png)

### AWS CloudFormation Stacks<a name="awsmp-cfn-stacks-view"></a>

Shows the health state of all the AWS CloudFormation stacks for a particular AWS account from all regions\.

![\[AWS CloudFormation stack state view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/cloudformation_stack_view.png)

**AWS CloudFormation Stacks Diagram View**  
Shows the AWS CloudFormation stack relationship with other components\. An AWS CloudFormation stack might contain Amazon EC2 or Elastic Load Balancing resources\. The following illustration shows an example:

![\[AWS CloudFormation stack diagram view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/cloudformation_stack_diagram_view.png)

### Amazon Performance Views<a name="awsmp-performance-views"></a>

Shows the Amazon CloudWatch metrics for Amazon EC2, Amazon EBS, and Elastic Load Balancing, custom metrics, and metrics created from CloudWatch alarms\. In addition, there are separate performance views for each resource\. The **Other Metrics** performance view contains custom metrics, and metrics created from CloudWatch alarms\. For more information about these metrics, see [AWS Services That Publish CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html) in the *Amazon CloudWatch User Guide*\. The following illustration shows an example\. 

![\[Amazon performance views\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_performance_view.png)

### Amazon CloudWatch Metric Alarms<a name="awsmp-metric-alarms-view"></a>

Shows Amazon CloudWatch alarms related to the discovered AWS resources\.

![\[Amazon CloudWatch metric alarms\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/cloudwatch_alarms_view.png)

### AWS Alerts<a name="awsmp-aws-alerts-view"></a>

Shows the alerts that the AWS management pack produces when the health of an object is in a critical state\. 

![\[AWS Alerts\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/aws_alerts_view.png)

### Watcher Nodes \(System Center Operations Manager 2007 R2\)<a name="awsmp-watcher-nodes-view"></a>

View the health state of the watcher nodes across all of the AWS accounts that are being monitored\. A **Healthy** state means that the watcher node is configured correctly and can communicate with AWS\.

![\[Watcher node state view\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/check_watcher_node_state.png)

## Discoveries<a name="Discoveries"></a>

Discoveries are the AWS resources that are monitored by the AWS Management Pack\. The AWS Management Pack discovers the following objects:
+ Amazon EC2 instances
+ EBS volumes
+ ELB load balancers
+ AWS CloudFormation stacks
+ Amazon CloudWatch alarms
+ AWS Elastic Beanstalk applications
+ Amazon EC2 Auto Scaling groups and Availability Zones

Amazon CloudWatch metrics are generated for the following resources:
+ Amazon EC2 instance
+ EBS volume
+ Elastic Load Balancing
+ Custom Amazon CloudWatch metrics
+ Metrics from existing Amazon CloudWatch alarms

For Amazon CloudWatch metrics discovery, the following guidelines apply:
+ AWS CloudFormation stacks do not have any default Amazon CloudWatch metrics\.
+ Stopped Amazon EC2 instances or unused Amazon EBS volumes do not generate data for their default Amazon CloudWatch metrics\.
+ After starting an Amazon EC2 instance, it can take up to 30 minutes for the Amazon CloudWatch metrics to appear in Operations Manager\.
+ Amazon CloudWatch retains the monitoring data for two weeks, even if your AWS resources have been terminated\. This data appears in Operations Manager\.
+ An existing Amazon CloudWatch alarm for a resource that is not supported will create a metric and be associated with the Amazon CloudWatch alarm\. These metric can be viewed in the Other Metrics performance view\.

The AWS Management Pack also discovers the following relationships:
+ AWS CloudFormation stack and its Elastic Load Balancing or Amazon EC2 resources
+ Elastic Load Balancing load balancer and its EC2 instances
+ Amazon EC2 instance and its EBS volumes
+ Amazon EC2 instance and its operating system
+ AWS Elastic Beanstalk application and its environment, configuration, and resources

The AWS Management Pack automatically discovers the relationship between an EC2 instance and the operating system running on it\. To discover this relationship, the Operations Manager Agent must be installed and configured on the instance and the corresponding operating system management pack must be imported in Operations Manager\.

Discoveries run on the management servers in the resource pool \(System Center 2012\) or the watcher node \(System Center 2007 R2\)\.


| Discovery | Interval \(seconds\) | 
| --- | --- | 
|  Amazon Resources Discovery \(SCOM 2012\) Discovers EC2 instances, Amazon EBS volumes, load balancers, and CloudFront stacks\.  | 14400 | 
|  AWS Elastic Beanstalk Discovery Discovers AWS Elastic Beanstalk and its relationship with environment, resources, and configuration\.  | 14400 | 
|  CloudWatch Alarms Discovery Discovers alarms generated using CloudWatch metrics\.  | 900 | 
|  Custom CloudWatch Metric Discovery Discovers custom CloudWatch metrics\.  | 14400 | 
|  Watcher Node Discovery \(SCOM 2007 R2\) Targets the root management server and creates the watcher node objects\.  | 14400 | 

## Monitors<a name="Monitors"></a>

Monitors are used to measure the health of your AWS resources\. Monitors run on the management servers in the resource pool \(System Center 2012\) or the watcher node \(System Center 2007 R2\)\.


| Monitor | Interval \(seconds\) | 
| --- | --- | 
|  AWS CloudFormation Stack Status  |  900  | 
|  Amazon CloudWatch Metric Alarm  |  300  | 
|  Amazon EBS Volume Status  |  900  | 
|  Amazon EC2 Instance Status  |  900  | 
|  Amazon EC2 Instance System Status  |  900  | 
|  AWS Elastic Beanstalk Status  |  900  | 
|  Watcher Node to Amazon Cloud Connectivity \(SCOM 2007 R2\)  |  900  | 

## Rules<a name="Rules"></a>

Rules create alerts \(based on Amazon CloudWatch metrics\) and collect data for analysis and reporting\.


| Rule | Interval \(seconds\) | 
| --- | --- | 
|  AWS Resource Discovery Rule \(SCOM 2007 R2\) Targets the watcher node and uses the AWS API to discover objects for the following AWS resources: EC2 instances, EBS volumes, load balancers, and AWS CloudFormation stacks\. \(CloudWatch metrics or alarms are not discovered\)\. After discovery is complete, view the objects in the **Not Monitored** state\.  |  14400  | 
|  Amazon Elastic Block Store Volume Performance Metrics Data Collection Rule  |  900  | 
|  Amazon EC2 Instance Performance Metrics Data Collection Rule  |  900  | 
|  Elastic Load Balancing Balancing Performance Metrics Data Collection Rule  |  900  | 
|  Custom CloudWatch Metric Data Collection Rule  |  900  | 

## Events<a name="EventIDs"></a>

Events report on activities that involve the monitored resources\. Events are written to the Operations Manager event log\.


| Event ID | Description | 
| --- | --- | 
|  4101  |  Amazon EC2 Instance Discovery \(General Discovery\) finished  | 
|  4102  |  Elastic Load Balancing Metrics Discovery, Amazon EBS Volume Metrics Discovery, Amazon EC2 Instance Metrics Discovery finished  | 
|  4103  |  Amazon CloudWatch Metric Alarms Discovery finished  | 
|  4104  |  Amazon Windows Computer Discovery finished  | 
|  4105  |  Collecting Amazon Metrics Alarm finished  | 
|  4106  |  EC2 Instance Computer Relation Discovery finished  | 
|  4107  |  Collecting AWS CloudFormation Stack State finished  | 
|  4108  |  Collecting Watcher Node Availability State finished  | 
|  4109  |  Amazon Metrics Collection Rule finished  | 
|  4110  |  Task to change Amazon Instance State finished  | 
|  4111  |  EC2 Instance Status Monitor State finished  | 
|  4112  |  Amazon EBS Volume Status Monitor State finished  | 
|  4113  |  Amazon EC2 Instance Scheduled Events Monitor State calculated  | 
|  4114  |  Amazon EBS Scheduled Events Monitor State calculated  | 
|  4115  |  Elastic Beanstalk Discovery finished  | 
|  4116  |  Elastic Beanstalk Environment Status State calculated  | 
|  4117  |  Elastic Beanstalk Environment Operational State calculated  | 
|  4118  |  Elastic Beanstalk Environment Configuration State calculated  | 

## Health Model<a name="HealthModel"></a>

The following illustration shows the health model defined by the AWS Management Pack\.

![\[Health model for an EC2 instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AWSMPHealthModel.png)

The health state for a CloudWatch alarm is rolled up to its corresponding CloudWatch metric\. The health state for a CloudWatch metric for Amazon EC2 is rolled up to the EC2 instance\. Similarly, the health state for the CloudWatch metrics for Amazon EBS is rolled up to the Amazon EBS volume\. The health states for the Amazon EBS volumes used by an EC2 instance are rolled up to the EC2 instance\.

When the relationship between an EC2 instance and its operating system has been discovered, the operating system health state is rolled up to the EC2 instance\.

![\[Windows/Linux computer health rolls up to an EC2 instance\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AWSMPWindowsLinuxHealthRollup.png)

The health state of an AWS CloudFormation stack depends on the status of the AWS CloudFormation stack itself and the health states of its resources, namely the load balancers and EC2 instances\.

The following table illustrates how the status of the AWS CloudFormation stack corresponds to its health state\.


| Health State | AWS CloudFormation Stack Status | Notes | 
| --- | --- | --- | 
|  Error  |  `CREATE_FAILED` `DELETE_IN_PROGRESS` `DELETE_FAILED` `UPDATE_ROLLBACK_FAILED`  | Most likely usable | 
|  Warning  |  `UPDATE_ROLLBACK_IN_PROGRESS` `UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS` `UPDATE_ROLLBACK_COMPLETE`  | Recovering after some problem | 
|  Healthy  |  `CREATE_COMPLETE` `UPDATE_IN_PROGRESS` `UPDATE_COMPLETE_CLEANUP_IN_PROGRESS` `UPDATE_COMPLETE`  | Usable | 

The full health model for an AWS CloudFormation stack is as follows:

![\[Health model for an AWS CloudFormation stack\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AWSMPCloudformationHealthModel.png)

## Customizing the AWS Management Pack<a name="CustomizingAWSmp"></a>

To change the frequency of discoveries, rules, and monitors, you can override the interval time \(in seconds\)\.

**To change frequency**

1. In the **Operations Manager** toolbar, click **Go**, and then click **Authoring**\.

1. In the **Authoring** pane, expand **Management Pack Objects** and then click the object to change \(for example, **Object Discoveries**, **Rules**, or **Monitors**\)\.

1. In the toolbar, click **Scope**\.

1. In the **Scope Management Pack Objects** dialog box, click **View all targets**\.

1. To limit the scope to Amazon objects, type Amazon in the **Look for** field\.

1. Select the object want to configure and click **OK**\.

1. In the **Operations Manager** center pane, right\-click the object to configure, click **Overrides**, and then click the type of override you want to configure\.

1. Use the **Override Properties** dialog box to configure different values and settings for objects\.

**Tip**  
To disable a discovery, rule, or monitoring object right\-click the object to disable in the **Operations Manager** center pane, click **Overrides**, and then click **Disable the Rule**\. You might disable rules if, for example, you do not run AWS Elastic Beanstalk applications or use custom Amazon CloudWatch metrics\. 

For information about creating overrides, see [Tuning Monitoring by Using Targeting and Overrides](http://technet.microsoft.com/en-us/library/hh230704) on the *Microsoft TechNet* website\.

For information about creating custom rules and monitors, see [Authoring for System Center 2012 \- Operations Manager](http://technet.microsoft.com/en-us/library/hh457564) or [System Center Operations Manager 2007 R2 Management Pack Authoring Guide](http://technet.microsoft.com/en-us/library/ee957010.aspx) on the *Microsoft TechNet* website\.