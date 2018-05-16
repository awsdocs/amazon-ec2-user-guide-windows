# Using CloudWatch Metrics to Monitor Your Elastic GPUs<a name="elastic-gpus-cloudwatch"></a>

You can monitor your elastic GPUs using Amazon CloudWatch, which collects metrics about your elastic GPU\. These statistics are recorded for a period of two weeks, so that you can access historical information and gain a better perspective on how your service is performing\.

By default, elastic GPUs send metric data to CloudWatch in 5\-minute periods\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)\.

## Elastic GPU Metrics and Dimensions<a name="elastic-gpus-metrics"></a>

You can use the following procedures to view the metrics for elastic GPUs\.

**To view metrics using the CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the region\. From the navigation bar, select the region where your elastic GPU resides\. For more information, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\.

1. Under **All metrics**, select a metrics category, and then scroll down to view the full list of metrics\.

**To view metrics using the AWS CLI**
+ At a command prompt, use the following command:

  ```
  aws cloudwatch list-metrics --namespace "AWS/ElasticGPUs"          
  ```

CloudWatch displays the following metrics for the Elastic GPU service\.


| Metric | Description | 
| --- | --- | 
|  GPUConnectivityCheckFailed  |  Reports whether connectivity to the elastic GPU is active or has failed\. A value of zero \(0\) indicates that the connection is active\. A value of one \(1\) indicates a connectivity failure\. Units: Count  | 
|  GPUHealthCheckFailed  |  Reports whether the elastic GPU has passed a status health check in the last minute\. A value of zero \(0\) indicates that the status check passed\. A value of one \(1\) indicates a status check failure\. Units: Count  | 
| GPUMemoryUtilization |  The GPU memory used\. Units: MiB  | 

You can filter the elastic GPU data using the following dimensions\.


| Dimension | Description | 
| --- | --- | 
| `EGPUId` |  This dimension filters the data by the elastic GPU\.  | 
| `InstanceId` |  This dimension filters the data by instance to which the elastic GPU is attached\.  | 

## Creating CloudWatch Alarms to Monitor Elastic GPUs<a name="elastic-gpus-cloudwatch-alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and sends a notification to an Amazon SNS topic based on the value of the metric relative to a given threshold over a number of time periods\. 

For example, you can create an alarm that monitors the health of an elastic GPU and sends a notification when the elastic GPU fails a status health check for three consecutive 5\-minute periods\.

**To create an alarm for elastic GPU health status**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **Create Alarm**\.

1. Choose **Elastic GPU Metrics**\.

1. Select the elastic GPU and the **GPUHealthCheckFailed** metric and choose **Next**\.

1. Configure the alarm as follows, and choose **Create Alarm** when you are done:
   + Under **Alarm Threshold**, enter a name and description for your alarm\. For **Whenever**, choose **=>** and enter `1`\. Enter **3** for the consecutive periods\.
   + Under **Actions**, select an existing notification list or choose **New list** to create a new one\. 
   + Under **Alarm Preview**, select a period of 5 minutes\.