# Using CloudWatch metrics to monitor Elastic Graphics<a name="elastic-graphics-cloudwatch"></a>

You can monitor your Elastic Graphics accelerator using Amazon CloudWatch, which collects metrics about your accelerator performance\. These statistics are recorded for a period of two weeks, so that you can access historical information and gain a better perspective on how your service is performing\.

By default, Elastic Graphics accelerators send metric data to CloudWatch in 5\-minute periods\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

## Elastic Graphics metrics<a name="elastic-graphics-metrics"></a>

The `AWS/ElasticGPUs` namespace includes the following metrics for Elastic Graphics\.


| Metric | Description | 
| --- | --- | 
|  GPUConnectivityCheckFailed  |  Reports whether connectivity to the Elastic Graphics accelerator is active or has failed\. A value of zero \(0\) indicates that the connection is active\. A value of one \(1\) indicates a connectivity failure\. Units: Count  | 
|  GPUHealthCheckFailed  |  Reports whether the Elastic Graphics accelerator has passed a status health check in the last minute\. A value of zero \(0\) indicates that the status check passed\. A value of one \(1\) indicates a status check failure\. Units: Count  | 
| GPUMemoryUtilization |  The GPU memory used\. Units: MiB  | 

## Elastic Graphics dimensions<a name="elastic-graphics-dimensions"></a>

You can filter the metrics data for your Elastic Graphics accelerators using the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| EGPUId | Filters the data by the Elastic Graphics accelerator\. | 
| InstanceId | Filters the data by the instance to which the Elastic Graphics accelerator is attached\. | 

## Viewing CloudWatch metrics for Elastic Graphics<a name="viewing-elastic-graphics-metrics"></a>

Metrics are grouped first by the service namespace, and then by the supported dimensions\. You can use the following procedures to view the metrics for your Elastic Graphics accelerators\.

**To view Elastic Graphics metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the Region\. From the navigation bar, select the Region where your Elastic Graphics accelerator resides\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\.

1. For **All metrics**, select **Elastic Graphics**, **Elastic Graphics Metrics**\.

**To view Elastic Graphics metrics \(AWS CLI\)**  
Use the following [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command:

```
aws cloudwatch list-metrics --namespace "AWS/ElasticGPUs"
```

## Creating CloudWatch alarms to monitor Elastic Graphics<a name="elastic-graphics-cloudwatch-alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and sends a notification to an Amazon SNS topic based on the value of the metric relative to a given threshold over a number of time periods\. 

For example, you can create an alarm that monitors the health of an Elastic Graphics accelerator and sends a notification when the graphics accelerator fails a status health check for three consecutive 5\-minute periods\.

**To create an alarm for an Elastic Graphics accelerator health status**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **Select metric**, **Elastic Graphics**, **Elastic Graphics Metrics**\.

1. Select the **GPUHealthCheckFailed** metric and choose **Select metric**\.

1. Configure the alarm as follows:

   1. For **Alarm details**, type a name and description for your alarm\. For **Whenever**, choose **>=** and type `1`\.

   1. For **Actions**, select an existing notification list or choose **New list**\. 

   1. Choose **Create Alarm**\.