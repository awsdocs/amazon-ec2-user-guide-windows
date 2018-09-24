# Step 5: Configure Ports and Endpoints<a name="ConfigurePortsAndEndpoints"></a>

The AWS Management Pack for Microsoft System Center must be able to communicate with AWS services to monitor the performance of those services and provide alerts in System Center\. For monitoring to succeed, you must configure the firewall on the Management Pack servers to allow outbound HTTP calls on ports 80 and 443 to the AWS endpoints for the following services\.

This enables monitoring for the following AWS services:
+ Amazon Elastic Compute Cloud \(EC2\)
+ Elastic Load Balancing
+ Amazon EC2 Auto Scaling
+ AWS Elastic Beanstalk
+ Amazon CloudWatch
+ AWS CloudFormation

The AWS Management Pack uses the public APIs in the AWS SDK for \.NET to retrieve information from these services over ports 80 and 443\. Log on to each server and enable outbound firewall rules for ports 80 and 443\.

If your firewall application supports more detailed settings you can configure specific endpoints for each service\. An endpoint is a URL that is the entry point for a web service\. For example, ec2\.us\-west\-2\.amazonaws\.com is an entry point for the Amazon EC2 service\. To configure endpoints on your firewall, [locate the specific endpoint URLs](https://docs.aws.amazon.com/general/latest/gr/rande.html) for the AWS services you are running and specify those endpoints in your firewall application\.