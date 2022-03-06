# Use Amazon S3 with Amazon EC2<a name="AmazonS3"></a>

Amazon S3 is a repository for internet data\. Amazon S3 provides access to reliable, fast, and inexpensive data storage infrastructure\. It is designed to make web\-scale computing easier by enabling you to store and retrieve any amount of data, at any time, from within Amazon EC2 or anywhere on the web\. Amazon S3 stores data objects redundantly on multiple devices across multiple facilities and allows concurrent read or write access to these data objects by many separate clients or application threads\. You can use the redundant data stored in Amazon S3 to recover quickly and reliably from instance or application failures\. 

Amazon EC2 uses Amazon S3 for storing Amazon Machine Images \(AMIs\)\. You use AMIs for launching EC2 instances\. In case of instance failure, you can use the stored AMI to immediately launch another instance, thereby allowing for fast recovery and business continuity\.

Amazon EC2 also uses Amazon S3 to store snapshots \(backup copies\) of the data volumes\. You can use snapshots for recovering data quickly and reliably in case of application or system failures\. You can also use snapshots as a baseline to create multiple new data volumes, expand the size of an existing data volume, or move data volumes across multiple Availability Zones, thereby making your data usage highly scalable\. For more information about using data volumes and snapshots, see [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)\.

Objects are the fundamental entities stored in Amazon S3\. Every object stored in Amazon S3 is contained in a bucket\. Buckets organize the Amazon S3 namespace at the highest level and identify the account responsible for that storage\. Amazon S3 buckets are similar to internet domain names\. Objects stored in the buckets have a unique key value and are retrieved using a URL\. For example, if an object with a key value `/photos/mygarden.jpg` is stored in the `DOC-EXAMPLE-BUCKET1` bucket, then it is addressable using the URL `https://DOC-EXAMPLE-BUCKET1.s3.amazonaws.com/photos/mygarden.jpg`\. 

For more information about the features of Amazon S3, see the [Amazon S3 product page](https://aws.amazon.com/s3)\.

## Usage examples<a name="S3UsageScenarios"></a>

Given the benefits of Amazon S3 for storage, you might decide to use this service to store files and data sets for use with EC2 instances\. There are several ways to move data to and from Amazon S3 to your instances\. In addition to the examples discussed below, there are a variety of tools that people have written that you can use to access your data in Amazon S3 from your computer or your instance\. Some of the common ones are discussed in the AWS forums\.

If you have permission, you can copy a file to or from Amazon S3 and your instance using one of the following methods\.

**AWS Tools for Windows PowerShell**  
Windows instances have the benefit of a graphical browser that you can use to access the Amazon S3 console directly; however, for scripting purposes, Windows users can also use the [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell) to move objects to and from Amazon S3\.

Use the following command to copy an Amazon S3 object to your Windows instance\.

```
PS C:\> Copy-S3Object -BucketName my_bucket -Key path-to-file -LocalFile my_copied_file.ext
```

**AWS Command Line Interface**  
The AWS Command Line Interface \(AWS CLI\) is a unified tool to manage your AWS services\. The AWS CLI enables users to authenticate themselves and download restricted items from Amazon S3 and also to upload items\. For more information, such as how to install and configure the tools, see the [AWS Command Line Interface detail page](https://aws.amazon.com/cli/)\.

The `aws s3 cp` command is similar to the `cp` command in Unix\. You can copy files from Amazon S3 to your instance, copy files from your instance to Amazon S3, and copy files from one Amazon S3 location to another\.

Use the following command to copy an object from Amazon S3 to your instance\.

```
aws s3 cp s3://my_bucket/my_folder/my_file.ext my_copied_file.ext
```

Use the following command to copy an object from your instance back into Amazon S3\.

```
aws s3 cp my_copied_file.ext s3://my_bucket/my_folder/my_file.ext
```

The `aws s3 sync` command can synchronize an entire Amazon S3 bucket to a local directory location\. This can be helpful for downloading a data set and keeping the local copy up\-to\-date with the remote set\. If you have the proper permissions on the Amazon S3 bucket, you can push your local directory back up to the cloud when you are finished by reversing the source and destination locations in the command\.

Use the following command to download an entire Amazon S3 bucket to a local directory on your instance\.

```
aws s3 sync s3://remote_S3_bucket local_directory
```

**Amazon S3 API**  
If you are a developer, you can use an API to access data in Amazon S3\. For more information, see the [Amazon Simple Storage Service User Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/)\. You can use this API and its examples to help develop your application and integrate it with other APIs and SDKs, such as the boto Python interface\.
