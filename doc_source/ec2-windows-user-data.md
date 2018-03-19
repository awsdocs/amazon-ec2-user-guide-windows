# Running Commands on Your Windows Instance at Launch<a name="ec2-windows-user-data"></a>

When you launch an instance in Amazon EC2, you can pass user data to the instance that can be used to perform common automated configuration tasks and even run scripts after the instance starts\.


+ [User Data and Scripts](#user-data-scripts)
+ [User Data Execution](#user-data-execution)
+ [User Data and the Console](#user-data-console)
+ [User Data and the Tools for Windows PowerShell](#user-data-powershell)

## User Data and Scripts<a name="user-data-scripts"></a>

You can specify scripts to execute when an instance starts\.

For EC2Config or EC2Launch to execute user data scripts, you must enclose the lines of the specified script within one of the following special tags:

`<script></script>`  
Run any command that you can run in a Command Prompt window\.  
Example: `<script>dir > c:\test.log</script>`

`<powershell></powershell>`  
Run any command that you can run at the Windows PowerShell command prompt\.  
If you use an AMI that includes the [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell/), you can also use those cmdlets\. If an IAM role is associated with your instance, then you don't need to specify credentials to the cmdlets, as applications that run on the instance can use the role's credentials to access AWS resources such as Amazon S3 buckets\.  
Example: `<powershell>Read-S3Object -BucketName myS3Bucket -Key myFolder/myFile.zip -File c:\destinationFile.zip</powershell>`

You can separate the commands in a script using line breaks\.

If both `script` and `powershell` tags are present, the batch script are run first and the PowerShell script next, regardless of the order in which they appear\.

The `\Log` \(EC2Launch\) or `\Logs` \(EC2Config\) folder contains output from the standard output and standard error streams\.

If you're using the Amazon EC2 API or a tool that does not perform base64 encoding of the user data, you must encode the user data yourself\. If not, an error is logged about being unable to find `script` or `powershell` tags to execute\. The following is an example that encodes using PowerShell\.

```
$UserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Script))
```

The following is an example that decodes using PowerShell\.

```
$Script = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($UserData))
```

For more information about base64 encoding, see [http://tools\.ietf\.org/html/rfc4648](http://tools.ietf.org/html/rfc4648)\.

## User Data Execution<a name="user-data-execution"></a>

By default, all Amazon AMIs have user data execution enabled for the initial boot\. For instances using the EC2Config service, you can specify that user data must be executed on the next boot or restart of the service\. For more information, see [Ec2 Service Properties](UsingConfig_WinAMI.md#UsingConfigInterface_WinAMI)\.

### Initial Boot<a name="user-data-initial-boot"></a>

User data script execution happens under the local administrator user only when a random password is generated\. The EC2Config service generates the password and is aware of the credentials briefly \(prior to sending to the console\)\. EC2Config doesn't store or track password changes, so when you don't generate a random password, user data execution is performed by the EC2Config service account\. If you choose the option to **Shutdown with Sysprep** in EC2Config, user data script execution is enabled, regardless of the setting of the **User Data** check box\.

Similarly, for instances using the EC2Launch service, if you choose the option to **Shutdown with Sysprep**, user data script execution is enabled when the instance is restarted\.

### Subsequent Boots<a name="user-data-subsequent-boot"></a>

Because Amazon AMIs automatically disable user data script execution after the initial boot, you can do one of the following to make user data persist across reboots:

+ For EC2Config, specify that user data must be executed on the next boot or restart of the service\. For more information, see [Ec2 Service Properties](UsingConfig_WinAMI.md#UsingConfigInterface_WinAMI)\. You can also use this option if you want to add or change user data for an existing instance\.

+ For EC2Config, programmatically create a scheduled task to run at system start using `schtasks.exe /Create`, and point the scheduled task to the user data script \(or another script\) at `C:\Program Files\Amazon\Ec2ConfigService\Scripts\UserScript.ps1`\.

+ For EC2Config, programmatically enable the user data plug\-in in `Config.xml` using a script similar to the following:

  ```
  <powershell>
  $EC2SettingsFile="C:\Program Files\Amazon\Ec2ConfigService\Settings\Config.xml"
  $xml = [xml](get-content $EC2SettingsFile)
  $xmlElement = $xml.get_DocumentElement()
  $xmlElementToModify = $xmlElement.Plugins
  
  foreach ($element in $xmlElementToModify.Plugin)
  {
      if ($element.name -eq "Ec2SetPassword")
      {
          $element.State="Enabled"
      }
      elseif ($element.name -eq "Ec2HandleUserData")
      {
          $element.State="Enabled"
      }
  }
  $xml.Save($EC2SettingsFile)
  </powershell>
  ```

+ For EC2Config version 2\.1\.10 and later, or for EC2Launch, you can use `<persist>true</persist>` in the user data to enable the plug\-in after user data execution\.

  ```
  <powershell>
      insert script here
  </powershell>
  <persist>true</persist>
  ```

## User Data and the Console<a name="user-data-console"></a>

When you launch an instance, you specify the script in **Advanced Details**, **User data** on the **Step 3: Configure Instance Details** page of the Launch Instance wizard\. The example in the following image uses the `Rename-Computer` command to change the name of the instance when it boots\. When you select **As text**, the Amazon EC2 console performs base64 encoding on the input for you\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/configure_ec2config_userdata.png)

You can view the instance metadata for any instance, and you can change the instance metadata for a stopped instance\. Select the instance, and then choose **Actions**, **Instance Settings**, **View/Change User Data**\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/view-change-user-data.png)

## User Data and the Tools for Windows PowerShell<a name="user-data-powershell"></a>

You can use the Tools for Windows PowerShell to specify, modify, and view the user data for your instance\. For information about viewing user data from your instance using instance metadata, see [Retrieving User Data](ec2-instance-metadata.md#instancedata-user-data-retrieval)\. For information about user data and the AWS CLI, see [User Data and the AWS CLI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-api-cli) in the *Amazon EC2 User Guide for Linux Instances*\.

**Example: Specify User Data at Launch**  
To specify user data when you launch your instance, use the [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command\.

This command does not perform base64 encoding of the user data for you\. Use the following commands to encode the user data in a text file\.

```
PS C:\> $Script = Get-Content -Raw script.txt
PS C:\> $UserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Script))
```

Use the `-UserData` parameter to pass the user data to the command\.

```
PS C:\> New-EC2Instance -ImageId ami-abcd1234 -MinCount 1 -MaxCount 1 -InstanceType m3.medium \
-KeyName my-key-pair -SubnetId subnet-12345678 -SecurityGroupIds sg-1a2b3c4d \
-UserData $UserData
```

**Example: Modify the User Data of a Stopped Instance**  
You can modify the user data of a stopped instance using the [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command\.

This command does not perform base64 encoding of the user data for you\. Use the following commands to encode the user data in a text file\.

```
PS C:\> $NewScript = Get-Content -Raw new-script.txt
PS C:\> $NewUserData = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($NewScript))
```

Use the `-UserData` and `-Value` parameters to specify the user data\.

```
PS C:\> Edit-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -Attribute userData -Value $NewUserData
```

**Example: View User Data**  
To retrieve the user data for an instance, use the [Get\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) command\.

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
Rename-Computer -NewName user-data-test
</powershell>
```