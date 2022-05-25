# Enable or stop using NitroTPM on an instance<a name="nitrotpm-instance"></a>

When you launch an instance from an AMI that has NitroTPM support enabled, the instance launches with NitroTPM enabled\. You can configure the instance to stop using NitroTPM\. You can verify whether an instance is enabled for NitroTPM\.

**Topics**
+ [Launch an instance with NitroTPM enabled](#launch-instance-with-nitrotpm)
+ [Stop using NitroTPM on an instance](#disable-nitrotpm-support-on-instance)
+ [Verify whether NitroTPM is accessible inside the instance](#verify-nitrotpm-support-on-instance)

## Launch an instance with NitroTPM enabled<a name="launch-instance-with-nitrotpm"></a>

When you launch an instance with the [ prerequisites](enable-nitrotpm-prerequisites.md), NitroTPM is automatically enabled on the instance\. You can only enable NitroTPM on an instance at launch\. For information about launching an instance, see [Launch your instance](LaunchingAndUsingInstances.md)\.

## Stop using NitroTPM on an instance<a name="disable-nitrotpm-support-on-instance"></a>

After launching an instance with NitroTPM enabled, you can’t disable NitroTPM for the instance\. However, you can configure the operating system to stop using NitroTPM by disabling the TPM 2\.0 device driver on the instance by using the following tools:
+ For Windows, use the TPM management console, tpm\.msc\.

For more information about disabling the device driver, see the documentation for your operating system\.

## Verify whether NitroTPM is accessible inside the instance<a name="verify-nitrotpm-support-on-instance"></a>

**To verify whether an instance is enabled for NitroTPM support using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command and specify the instance ID\. Currently, the Amazon EC2 console does not display the `TpmSupport` field\.

```
aws ec2 describe-instances --instance-ids i-0123456789example
```

If NitroTPM support is enabled on the instance, `"TpmSupport": "v2.0"` appears in the output\.

```
"Instances": {
             "InstanceId":"0123456789example",
             "InstanceType":"c5.large",
             ...
             "BootMode": "uefi",
             "TpmSupport": "v2.0"
             ... 
             }
```

**To verify whether NitroTPM is accessible inside an Amazon EC2 Windows instance**

1. [Connect to your EC2 Windows instance\.](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html)

1. On the instance, run the tpm\.msc program\.

   The **TPM Management on Local Computer** window opens\.

1. Check the **TPM Manufacturer Information** field\. It contains the manufacturer's name and the version of the NitroTPM on the instance\.  
![\[The TPM Management on Local Computer window and the TPM Manufacturer Information field showing the version of the NitroTPM on the instance.\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/tpm-1.png)