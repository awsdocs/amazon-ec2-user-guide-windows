# Running Commands on Your Windows Instance at Launch<a name="ec2-windows-user-data"></a>

When you launch a Windows instance in Amazon EC2, you can pass user data to the instance\. Instance user data is treated as opaque data; it is up to the instance to interpret it\. For example, you can specify data to be used by automated configuration tasks or specify scripts that are run after the instance starts\. User data is processed by [EC2Config](ec2config-service.md) on Windows Server 2012 R2 and earlier and by [EC2Launch](ec2launch.md) on Windows Server 2016\.

**Topics**
+ [User Data Scripts](#user-data-scripts)
+ [User Data Execution](#user-data-execution)
+ [User Data and the Console](#user-data-console)
+ [User Data and the Tools for Windows PowerShell](#user-data-powershell)

## User Data Scripts<a name="user-data-scripts"></a>

For EC2Config or EC2Launch to execute scripts, you must enclose the script within a special tag when you add it to user data\. The tag you use depends on whether the commands run in a Command Prompt window \(batch commands\) or using Windows PowerShell\.

If you specify both a batch script and a Windows PowerShell script, the batch script runs first and the Windows PowerShell script runs next, regardless of the order in which they appear in the instance user data\.

### Syntax for Batch Scripts<a name="user-data-batch-scripts"></a>

Specify a batch script using the `script` tag\. Separate the commands using line breaks\. For example:

```
<script>
echo Current date and time >> %SystemRoot%\Temp\test.log
echo %DATE% %TIME% >> %SystemRoot%\Temp\test.log
</script>
```

By default, the user data scripts are executed one time when you launch the instance\. To execute the user data scripts every time you reboot or start the instance, add `<persist>true</persist>` to the user data\.

```
<script>
echo Current date and time >> %SystemRoot%\Temp\test.log
echo %DATE% %TIME% >> %SystemRoot%\Temp\test.log
</script>
<persist>true</persist>
```

### Syntax for Windows PowerShell Scripts<a name="user-data-powershell-scripts"></a>

The AWS Windows AMIs include the [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell/), so you can specify these cmdlets in user data\. If you associate an IAM role with your instance, you don't need to specify credentials to the cmdlets, as applications that run on the instance use the role's credentials to access AWS resources \(for example, Amazon S3 buckets\)\.

Specify a Windows PowerShell script using the `powershell` tag\. Separate the commands using line breaks\. For example:

```
<powershell>
$file = $env:SystemRoot + "\Temp\" + (Get-Date).ToString("MM-dd-yy-hh-mm")
New-Item $file -ItemType file
</powershell>
```

By default, the user data scripts are executed one time when you launch the instance\. To execute the user data scripts every time you reboot or start the instance, add `<persist>true</persist>` to the user data\.

```
<powershell>
$file = $env:SystemRoot + "\Temp\" + (Get-Date).ToString("MM-dd-yy-hh-mm")
New-Item $file -ItemType file
</powershell>
<persist>true</persist>
```

### Base64 Encoding<a name="user-data-base64-encoding"></a>

If you're using the Amazon EC2 API or a tool that does not perform base64 encoding of the user data, you must encode the user data yourself\. If not, an error is logged about being unable to find `script` or `powershell` tags to execute\. The following is an example that encodes using Windows PowerShell\.

```
$UserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Script))
```

The following is an example that decodes using PowerShell\.

```
$Script = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($UserData))
```

For more information about base64 encoding, see [http://tools\.ietf\.org/html/rfc4648](http://tools.ietf.org/html/rfc4648)\.

## User Data Execution<a name="user-data-execution"></a>

By default, all AWS Windows AMIs have user data execution enabled for the initial launch\. You can specify that user data scripts are executed the next time the instance reboots or restarts\. Alternatively, you can specify that user data scripts are executed every time the instance reboots or restarts\.

User data scripts are executed from the local administrator account when a random password is generated\. Otherwise, user data scripts are executed from the System account\.

### Instance Launch<a name="user-data-scripts-launch"></a>

Any scripts in the instance user data are executed during the initial launch of the instance\. If the persist tag is found, user data execution is enabled for subsequent reboots or starts\. The log files for EC2Launch and EC2Config contain the output from the standard output and standard error streams\.

With EC2Launch, the log file is `C:\ProgramData\Amazon\EC2-Windows\Launch\Log\UserdataExecution.log`\. The following information is logged as user data is executed\.
+ Userdata execution begins — The start of user data execution
+ <persist> tag was provided: true — If the persist tag is found
+ Running userdata on every boot — If the persist tag is found
+ <powershell> tag was provided\.\. running powershell content — If the powershell tag is found
+ <script> tag was provided\.\. running script content — If the script tag is found
+ Message: The output from user scripts — If user data scripts are executed, their output is logged

With EC2Config, the log file is `C:\Program Files\Amazon\Ec2ConfigService\Logs\Ec2Config.log`\. The following information is logged as user data is executed\.
+ Ec2HandleUserData: Message: Start running user scripts — The start of user data execution
+ Ec2HandleUserData: Message: Re\-enabled userdata execution — If the persist tag is found
+ Ec2HandleUserData: Message: Could not find <persist> and </persist> — If the persist tag is not found
+ Ec2HandleUserData: Message: The output from user scripts — If user data scripts are executed, their output is logged

### Subsequent Reboots or Starts<a name="user-data-scripts-subsequent"></a>

When you update instance user data, user data scripts are not executed automatically when you reboot or start the instance\. However, you can enable user data execution so that user data scripts are executed one time when you reboot or start the instance or every time you reboot or start the instance\.

If you choose the **Shutdown with Sysprep** option, user data scripts are executed when the instance is rebooted or restarted, even if you did not enable user data execution for subsequent reboots or starts\.

**To enable user data execution on Windows Server 2016 \(EC2Launch\)**

1. Connect to your Windows instance\.

1. Open a PowerShell command window and run the following command:

   ```
   InitializeInstance.ps1 -Schedule
   ```

1. Disconnect from your Windows instance\. To execute updated scripts next time the instance is started, stop the instance and update the user data\. For more information, see [View and Update the Instance User Data](#user-data-view-change)\.

**To enable user data execution on Windows Server 2012 R2 and earlier \(EC2Config\)**

1. Connect to your Windows instance\.

1. Open `C:\Program Files\Amazon\Ec2ConfigService\Ec2ConfigServiceSetting.exe`\.

1. For **User Data**, select **Enable UserData execution for next service start**\.

1. Disconnect from your Windows instance\. To execute updated scripts next time the instance is started, stop the instance and update the user data\. For more information, see [View and Update the Instance User Data](#user-data-view-change)\.

## User Data and the Console<a name="user-data-console"></a>

You can specify instance user data when you launch the instance\. If the root volume of the instance is an EBS volume, you can also stop the instance and update its user data\.

### Specify Instance User Data at Launch<a name="user-data-launch-instance-wizard"></a>

When you launch an instance, you specify the script in **Advanced Details**, **User data** on the **Step 3: Configure Instance Details** page of the Launch Instance wizard\. The example in the following image creates a file in the Windows temporary folder, using the current date and time in the file name\. When you include `<persist>true</persist>`, the script is executed every time you reboot or start the instance\. When you select **As text**, the Amazon EC2 console performs the base64 encoding for you\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/configure_ec2config_userdata.png)

### View and Update the Instance User Data<a name="user-data-view-change"></a>

You can view the instance user data for any instance, and you can update the instance user data for a stopped instance\.

**To update the user data for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. the instance\.

1. Select the instance and choose **Actions**, **Instance State**, **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. When prompted for confirmation, choose **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, choose **Actions**, **Instance Settings**, **View/Change User Data**\. You can't change the user data if the instance is running, but you can view it\.

1. In the **View/Change User Data** dialog box, update the user data, and then choose **Save**\. To execute user data scripts every time you reboot or start the instance, add `<persist>true</persist>`, as shown in the following example:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/view-change-user-data.png)

1. Restart the instance\. If you enabled user data execution for subsequent reboots or starts, the updated user data scripts are executed as part of the instance start process\.

## User Data and the Tools for Windows PowerShell<a name="user-data-powershell"></a>

You can use the Tools for Windows PowerShell to specify, modify, and view the user data for your instance\. For information about viewing user data from your instance using instance metadata, see [Retrieve Instance User Data](ec2-instance-metadata.md#instancedata-user-data-retrieval)\. For information about user data and the AWS CLI, see [User Data and the AWS CLI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-api-cli) in the *Amazon EC2 User Guide for Linux Instances*\.

**Example: Specify Instance User Data at Launch**  
Create a text file with the instance user data\. To execute user data scripts every time you reboot or start the instance, add `<persist>true</persist>`, as shown in the following example:

```
<powershell>
$file = $env:SystemRoot + "\Temp\" + (Get-Date).ToString("MM-dd-yy-hh-mm")
New-Item $file -ItemType file
</powershell>
<persist>true</persist>
```

To specify instance user data when you launch your instance, use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command\. This command does not perform base64 encoding of the user data for you\. Use the following commands to encode the user data in a text file named `script.txt`\.

```
PS C:\> $Script = Get-Content -Raw script.txt
PS C:\> $UserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Script))
```

Use the `-UserData` parameter to pass the user data to the New\-EC2Instance command\.

```
PS C:\> New-EC2Instance -ImageId ami-abcd1234 -MinCount 1 -MaxCount 1 -InstanceType m3.medium \
-KeyName my-key-pair -SubnetId subnet-12345678 -SecurityGroupIds sg-1a2b3c4d \
-UserData $UserData
```

**Example: Update Instance User Data for a Stopped Instance**  
You can modify the user data of a stopped instance using the [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command\.

Create a text file with the new script\. Use the following commands to encode the user data in the text file named `new-script.txt`\.

```
PS C:\> $NewScript = Get-Content -Raw new-script.txt
PS C:\> $NewUserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($NewScript))
```

Use the `-UserData` and `-Value` parameters to specify the user data\.

```
PS C:\> Edit-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -Attribute userData -Value $NewUserData
```

**Example: View Instance User Data**  
To retrieve the user data for an instance, use the [Get\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) command\.

```
PS C:\> (Get-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -Attribute userData).UserData
```

The following is example output\. Note that the user data is encoded\.

```
PHBvd2Vyc2hlbGw+DQpSZW5hbWUtQ29tcHV0ZXIgLU5ld05hbWUgdXNlci1kYXRhLXRlc3QNCjwvcG93ZXJzaGVsbD4=
```

Use the following commands to store the encoded user data in a variable and then decode it\.

```
PS C:\> $UserData_encoded = (Get-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -Attribute userData).UserData
PS C:\> [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($UserData_encoded))
```

The following is example output\.

```
<powershell>
$file = $env:SystemRoot + "\Temp\" + (Get-Date).ToString("MM-dd-yy-hh-mm")
New-Item $file -ItemType file
</powershell>
<persist>true</persist>
```