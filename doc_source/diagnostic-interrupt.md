# Sending a diagnostic interrupt \(for advanced users\)<a name="diagnostic-interrupt"></a>

**Warning**  
Diagnostic interrupts are intended for use by advanced users\. Incorrect usage could negatively impact your instance\. Sending a diagnostic interrupt to an instance could trigger an instance to crash and reboot, which could lead to the loss of data\.

You can send a diagnostic interrupt to an unreachable or unresponsive Windows instance to manually trigger a *stop error*\. Stop errors are commonly referred to as *blue screen errors*\.

In general, Windows operating systems crash and reboot when a stop error occurs, but the specific behavior depends on its configuration\. A stop error can also cause the operating system to write debugging information, such as a kernel memory dump, to a file\. You can then use this information to conduct root cause analysis to debug the instance\.

The memory dump data is generated locally by the operating system on the instance itself\.

Before sending a diagnostic interrupt to your instance, we recommend that you consult the documentation for your operating system and then make the necessary configuration changes\.

**Topics**
+ [Supported instance types](#diagnostic-interrupt-instances)
+ [Prerequisites](#diagnostic-interrupt-prereqs)
+ [Sending a diagnostic interrupt](#diagnostic-interrupt-use)

## Supported instance types<a name="diagnostic-interrupt-instances"></a>

Diagnostic interrupt is supported on all Nitro\-based instance types, except A1\. For more information, see [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

## Prerequisites<a name="diagnostic-interrupt-prereqs"></a>

Before using a diagnostic interrupt, you should configure your instance's operating system to perform the actions you need when a stop error occurs\.

**To configure Windows to generate a memory dump when a stop error occurs**

1. Connect to your instance\.

1. Open the **Control Panel** and choose **System**, **Advanced system settings**\.

1. In the **System Properties** dialog box, choose the **Advanced** tab\.

1. In the **Startup and Recovery** section, choose **Settings\.\.\.**\.

1. In the **System failure** section, configure the settings as needed, and then choose **OK**\.

For more information about configuring Windows stop errors, see [ Overview of memory dump file options for Windows](https://support.microsoft.com/en-us/help/254649/overview-of-memory-dump-file-options-for-windows)\.

## Sending a diagnostic interrupt<a name="diagnostic-interrupt-use"></a>

After you have completed the necessary configuration changes, you can send a diagnostic interrupt to your instance using the AWS CLI or Amazon EC2 API\.

**To send a diagnostic interrupt to your instance \(AWS CLI\)**  
Use the [send\-diagnostic\-interrupt](https://docs.aws.amazon.com/cli/latest/reference/ec2/send-diagnostic-interrupt.html) command and specify the instance ID\.

```
aws ec2 send-diagnostic-interrupt --instance-id i-1234567890abcdef0
```

**To send a diagnostic interrupt to your instance \(AWS Tools for Windows PowerShell\)**  
Use the [Send\-EC2DiagnosticInterrupt](https://docs.aws.amazon.com/powershell/latest/reference/items/Send-EC2DiagnosticInterrupt.html) cmdlt and specify the instance ID\.

```
PS C:\> Send-EC2DiagnosticInterrupt-InstanceId i-1234567890abcdef0
```