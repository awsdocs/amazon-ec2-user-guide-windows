# Subscribe to EC2Launch v2 service notifications<a name="ec2launch-v2-sns"></a>

Amazon SNS can notify you when new versions of the EC2Launch v2 service are released\. Use the following procedure to subscribe to these notifications\.

**Subscribe to EC2Launch v2 notifications**

1. Sign in to the AWS Management Console and open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must select this Region because the SNS notifications that you are subscribing to were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. In the Create subscription dialog box, do the following:

   1. For **Topic ARN**, use the following Amazon Resource Name \(ARN\): **arn:aws:sns:us\-east\-1:309726204594:amazon\-ec2launch\-v2**\.

   1. For **Protocol**, choose **Email**\.

   1. For **Endpoint**, enter an email address that you can use to receive the notifications\.

   1. Choose **Create subscription**\.

1. You'll receive an email asking you to confirm your subscription\. Open the email and follow the directions to complete your subscription\.

Whenever a new version of the EC2Launch v2 service is released, we send notifications to subscribers\. If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

1. Open the Amazon SNS console\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the subscription and then choose **Actions**, **Delete subscriptions**\. When prompted for confirmation, choose **Delete**\.