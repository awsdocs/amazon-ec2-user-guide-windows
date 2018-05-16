# Sending Logs, Events, and Performance Counters to Amazon CloudWatch<a name="send_logs_to_cwl"></a>

You can configure your Amazon EC2 instances to send Windows Server logs, events, and performance counters to Amazon CloudWatch Logs and Amazon CloudWatch Events\. Amazon EC2 offers several methods for configuring your instances to export this data\. The method you choose will depend, in part, on the version of Windows Server you are running and the version of the configuration agent running on your instance\. It will also depend on whether you want to manually configure your instances to use a local configuration file or remotely configure them using Systems Manager Run Command or Systems Manager State Manager\.

**Important**  
The unified CloudWatch Agent has replaced the SSM Agent as the tool for sending log data to Amazon CloudWatch Logs\. Support for using the SSM Agent to send log data will be deprecated in the near future\. We recommend that you begin using the unified CloudWatch Agent for your log collection processes as soon as possible\. For more information, see the following topics:  
[Send Logs to CloudWatch Logs \(CloudWatch Agent\)](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html) in the *AWS Systems Manager User Guide*
[ Migrate Windows Server Instance Log Collection to the CloudWatch Agent](http://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html#monitoring-cloudwatch-agent-migrate) in the *AWS Systems Manager User Guide*
[Collect Metrics from Amazon Elastic Compute Cloud Instances and On\-Premises Servers with the CloudWatch Agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*

For more information about CloudWatch Logs, see the [Amazon CloudWatch Logs User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\. For more information about Systems Manager, see the [AWS Systems Manager User Guide](http://docs.aws.amazon.com/systems-manager/latest/userguide/)\.

**Note**  
We refer to CloudWatch Logs, CloudWatch Events, and CloudWatch collectively as CloudWatch, unless otherwise noted\.

Amazon EC2 instances use an agent to send log data to CloudWatch\. With Windows Server 2008 to Windows Server 2012 R2, the agent is either the EC2Config service or SSM Agent\. With Windows Server 2016, the agent is SSM Agent\. For more information, see [Installing and Configuring SSM Agent](http://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html)\.

**Topics**
+ [Methods to Send Instance Metrics to CloudWatch](#send_logs_to_cwl_diff)
+ [Preliminary Tasks for Configuring Integration with CloudWatch](send_logs_to_cwl_gs.md)
+ [Configure Instances for CloudWatch](send_logs_to_cwl_instances.md)

## Methods to Send Instance Metrics to CloudWatch<a name="send_logs_to_cwl_diff"></a>

The following table describes the methods available to integrate with CloudWatch\.


| Method | Description | 
| --- | --- | 
|  Systems Manager Run Command  |  Complete the prerequisites for Systems Manager\. Create a Systems Manager document and remotely send commands to the instances\. The agent on the instances \(SSM Agent\) starts to send data to CloudWatch within a few minutes\. For more information, see [Use Systems Manager Run Command](send_logs_to_cwl_instances.md#remote-commands-cloudwatch)\. **Pros** – Commands are executed remotely\. **Cons** – Requires additional setup\.  | 
|  Systems Manager State Manager  |  Complete the prerequisites for Systems Manager\. Create a Systems Manager document and associate it with one or more instances\. The agent on the instances starts to send data according to the schedule defined in the association\. For more information, see [Use Systems Manager State Manager](send_logs_to_cwl_instances.md#ec2-configuration-cwl)\. **Pros** – Commands are executed remotely on a schedule\. **Cons** – Requires additional setup\.  | 
|  Local configuration file  |  Create a Systems Manager document and copy it to each instance\. Copy any updates to the configuration file to each instance manually as well\. The agent on the instance \(SSM Agent or EC2Config\) starts sending data to CloudWatch within a few minutes\. For more information, see [Use a Local Configuration File](send_logs_to_cwl_instances.md#send_logs_cwl_configfile)\. **Pros** – No additional setup required\. **Cons** – Legacy process\. Requires logging in to each instance\.  | 