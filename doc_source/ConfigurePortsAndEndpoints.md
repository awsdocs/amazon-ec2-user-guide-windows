# Step 5: Configure ports and endpoints<a name="ConfigurePortsAndEndpoints"></a>

The AWS Management Pack for Microsoft System Center must be able to communicate with AWS services to monitor the performance of those services and provide alerts in System Center\. For monitoring to succeed, you must configure outbound access on the Management Pack servers to allow access to the AWS endpoints for the following services\. To configure outbound access from your instances, see [Amazon EC2 security groups for Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html)\.

This enables monitoring for the following AWS services:
+ Amazon Elastic Compute Cloud \(EC2\)
+ Elastic Load Balancing
+ Amazon EC2 Auto Scaling
+ AWS Elastic Beanstalk
+ Amazon CloudWatch
+ AWS CloudFormation

The AWS Management Pack uses the public APIs in the AWS SDK for \.NET to retrieve information from these services\. Log on to each server and enable outbound firewall rules to allow access to the AWS endpoints\.

If your firewall application supports more detailed settings, you can configure specific endpoints for each service\. An endpoint is a URL that is the entry point for a web service\. For example, ec2\.us\-west\-2\.amazonaws\.com is an entry point for the Amazon EC2 service\. To configure endpoints on your firewall, [locate the specific endpoint URLs](https://docs.aws.amazon.com/general/latest/gr/rande.html) for the AWS services you are running and specify those endpoints in your firewall application\.