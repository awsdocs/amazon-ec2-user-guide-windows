# Troubleshooting the AWS Management Pack<a name="TroubleshootingAWSmp"></a>

The following are common errors, events, and troubleshooting steps\.

**Topics**
+ [Errors 4101 and 4105](#error-4101-4105)
+ [Error 4513](#error-4513)
+ [Event 623](#event-623)
+ [Events 2023 and 2120](#event-2023-2120)
+ [Event 6024](#event-6024)
+ [General Troubleshooting for System Center 2012 — Operations Manager](#general-troubleshooting-2012)
+ [General Troubleshooting for System Center 2007 R2](#general-troubleshooting-2007)

## Errors 4101 and 4105<a name="error-4101-4105"></a>

If you receive one of the following errors, you must upgrade the AWS Management Pack\. For more information, see [Upgrading the AWS Management Pack](upgrading-awsmp.md)\.

```
Error 4101
Exception calling "DescribeVolumes" with "1" argument(s): "AWS was not able to validate the 
provided access credentials"
```

```
Error 4105
Exception calling "DescribeApplications" with "0" argument(s): "The security token included 
in the request is invalid"
```

## Error 4513<a name="error-4513"></a>

If you receive one of the following error, you must upgrade the AWS Management Pack\. For more information, see [Upgrading the AWS Management Pack](upgrading-awsmp.md)\.

```
Error 4513
The callback method DeliverDataToModule failed with exception "Resolution of the dependency 
failed, type = "Amazon.SCOM.SDK.Interfaces.IMonitorSdk", name = "(none)".
Exception occurred while: Calling constructor Amazon.SCOM.SDK.CloudWatch.AwsMonitorSdk
(System.String awsAccessKey, System.String awsSecretKey).
Exception is: InvalidOperationException - Collection was modified; enumeration operation 
may not execute.
```

## Event 623<a name="event-623"></a>

If you find the following event in the Windows event log, follow the solution described in [KB975057](http://support.microsoft.com/kb/975057)\.

```
Event ID: 623
HealthService (process_id) The version store for instance instance ("name") has reached 
its maximum size of size MB. It is likely that a long-running transaction is preventing 
cleanup of the version store and causing it to build up in size. Updates will be rejected 
until the long-running transaction has been completely committed or rolled back. 
Possible long-running transaction:
SessionId: id
Session-context: value
Session-context ThreadId: id
Cleanup: value
```

## Events 2023 and 2120<a name="event-2023-2120"></a>

If you find the following events in the Windows event log, see [Event ID 2023 and 2120](https://social.technet.microsoft.com/Forums/systemcenter/en-US/56324edf-01bb-46c3-80de-bbaab529a585/event-id-2023-and-2120) for more information\.

```
Event ID: 2023
The Health Service has removed some items from the send queue for management group "Servers" 
since it exceeded the maximum allowed size of size megabytes.
```

```
Event ID: 2120
The Health Service has deleted one or more items for management group "Servers" which could 
not be sent in 1440 minutes.
```

## Event 6024<a name="event-6024"></a>

If you find the following event in the Windows event log, see [SCOM 2012 \- Event ID 6024](https://social.technet.microsoft.com/Forums/security/en-US/3f847c09-a4e9-497f-866c-c26440059aa2/scom-2012-event-id-6024-launching-restart-health-service-health-service-exceeded-processhandle?forum=operationsmanagerdeployment) for more information\.

```
Event ID: 6024
LaunchRestartHealthService.js : Launching Restart Health Service. Health Service exceeded 
Process\Handle Count or Private Bytes threshold.
```

## General Troubleshooting for System Center 2012 — Operations Manager<a name="general-troubleshooting-2012"></a>

Try the following to resolve any issues\.
+ Verify that you have installed the latest Update Rollup for System Center 2012 — Operations Manager\. The AWS Management Pack requires at least Update Rollup 1\.
+ Ensure that you have configured the AWS Management Pack after importing it by running the Add Monitoring Wizard\. For more information, see [Step 1: Installing the AWS Management Pack](ConfiguringAWSmp.md)\.
+ Verify that you have waited long enough for the AWS resources to be discovered \(10–20 minutes\)\.
+ Verify that the management servers are configured properly\.
  + Management servers must have Internet connectivity\.
  + The action account for a management server must have local administrator privileges on the management server\.
  + The management server must have the \.NET Framework 4\.5\. or later\.
+ Verify that the AWS Run As account is valid\.
  + The values for the access key ID and secret access key are correct\.
  + The access keys are active: In the AWS Management Console, click your name in the navigation bar and then click **Security Credentials**\.
  + The IAM user has at least read\-only access permission\. Note that read\-only access allows the user actions that do not change the state of a resource, such as monitoring, but do not allow the user actions like launching or stopping an instance\.
    + If an Amazon CloudWatch metric shows as **Not Monitored**, check whether at least one Amazon CloudWatch alarm has been defined for that Amazon CloudWatch metric\.
    + For further troubleshooting, use the information in the event logs\.
    + Check the Operations Manager event log on the management server\. For more information, see [Events](UsingAWSmp.md#EventIDs) for a list of the events that the AWS Management Pack writes to the Operations Manager event log\.

## General Troubleshooting for System Center 2007 R2<a name="general-troubleshooting-2007"></a>

Try the following to resolve any issues\.
+ Ensure that you have configured the AWS Management Pack after importing it by running the Add Monitoring Wizard\. For more information, see [Step 1: Installing the AWS Management Pack](ConfiguringAWSmp.md)\.
+ Verify that you have waited long enough for the AWS resources to be discovered \(10–20 minutes\)\.
+ Verify that the watcher node is configured properly\.
  + The proxy agent is enabled\. For more information, see [Step 2: Configuring the Watcher Node](ConfiguringWatcherNode.md)\.
  + The watcher node has Internet connectivity\.
  + The action account for the watcher node has local administrator privileges\.
  + The watcher node must have the \.NET Framework 3\.5\.1 or later\.
+ Verify that the watcher node is healthy and resolve all alerts\. For more information, see [Views](UsingAWSmp.md#AWSmpViews)\.
+ Verify that the AWS Run As account is valid\.
  + The values for the access key ID and secret access key are correct\.
  + The access keys are active: In the AWS Management Console, click your name in the navigation bar and then click **Security Credentials**\.
  + The IAM user has at least read\-only access permission\. Note that read\-only access allows the user actions that do not change the state of a resource, such as monitoring, but do not allow the user actions like launching or stopping an instance\.
    + If an Amazon CloudWatch metric shows as **Not Monitored**, check whether at least one Amazon CloudWatch alarm has been defined for that Amazon CloudWatch metric\.
    + For further troubleshooting, use the information in the event logs\.
    + Check the Operations Manager event log on the management server as well as the watcher node\. For more information, see [Events](UsingAWSmp.md#EventIDs) for a list of the events that the AWS Management Pack writes to the Operations Manager event log\.