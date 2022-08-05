# Find shared AMIs<a name="usingsharedamis-finding"></a>

You can use the Amazon EC2 console or the command line to find shared AMIs\. 

AMIs are a Regional resource\. When you search for a shared AMI \(public or private\), you must search for it from the same Region from which it is shared\. To make an AMI available in a different Region, copy the AMI to the Region, and then share it\. For more information, see [Copy an AMI](CopyingAMIs.md)\.

**Topics**
+ [Find a shared AMI \(console\)](#usingsharedamis-finding-console)
+ [Find a shared AMI \(Tools for Windows PowerShell\)](#find-shared-ami-powershell)
+ [Find a shared AMI \(AWS CLI\)](#usingsharedamis-finding-cli)

## Find a shared AMI \(console\)<a name="usingsharedamis-finding-console"></a>

**To find a shared private AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. 

1. In the first filter, choose **Private images**\. All AMIs that have been shared with you are listed\. To granulate your search, choose the **Search** bar and use the filter options provided in the menu\.

**To find a shared public AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. In the first filter, choose **Public images**\. To granulate your search, choose the **Search** field and use the filter options provided in the menu\.

**To find Amazon's shared public AMIs using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\.

1. In the first filter, choose **Public images**\.

1. Choose the **Search** field and then, from the menu options that appear, choose **Owner alias**, then **=**, and then **amazon** to display only Amazon's public images\.

**To find a shared public AMI from a verified provider using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMI Catalog**\.

1. Choose **Community AMIs**\.

1. The **Verified provider** label indicates the AMIs that are from Amazon or a verified partner\.

## Find a shared AMI \(Tools for Windows PowerShell\)<a name="find-shared-ami-powershell"></a>

Use the [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) command \(Tools for Windows PowerShell\) to list AMIs\. You can scope the list to the types of AMIs that interest you, as shown in the following examples\.

**Example: List all public AMIs**  
The following command lists all public AMIs, including any public AMIs that you own\.

```
PS C:\> Get-EC2Image -ExecutableUser all
```

**Example: List AMIs with explicit launch permissions**  
The following command lists the AMIs for which you have explicit launch permissions\. This list does not include any AMIs that you own\.

```
PS C:\> Get-EC2Image -ExecutableUser self
```

**Example: List AMIs owned by verified providers**  
The following command lists the AMIs owned by verified providers\. Public AMIs owned by verified providers \(either Amazon or verified partners\) have an aliased owner, which appears as `amazon` or `aws-marketplace` in the account field\. This helps you to easily find AMIs from verified providers\. Other users can't alias their AMIs\.

```
PS C:\> Get-EC2Image -Owner amazon aws-marketplace
```

**Example: List AMIs owned by an account**  
The following command lists the AMIs owned by the specified AWS account\.

```
PS C:\> Get-EC2Image -Owner 123456789012
```

**Example: Scope AMIs using a filter**  
To reduce the number of displayed AMIs, use a filter to list only the types of AMIs that interest you\. For example, use the following filter to display only EBS\-backed AMIs\.

```
-Filter @{ Name="root-device-type"; Values="ebs" }
```

## Find a shared AMI \(AWS CLI\)<a name="usingsharedamis-finding-cli"></a>

Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command \(AWS CLI\) to list AMIs\. You can scope the list to the types of AMIs that interest you, as shown in the following examples\.

**Example: List all public AMIs**  
The following command lists all public AMIs, including any public AMIs that you own\.

```
aws ec2 describe-images --executable-users all
```

**Example: List AMIs with explicit launch permissions**  
The following command lists the AMIs for which you have explicit launch permissions\. This list does not include any AMIs that you own\.

```
aws ec2 describe-images --executable-users self
```

**Example: List AMIs owned by verified providers**  
The following command lists the AMIs owned by verified providers\. Public AMIs owned by verified providers \(either Amazon or verified partners\) have an aliased owner, which appears as `amazon` or `aws-marketplace` in the account field\. This helps you to easily find AMIs from verified providers\. Other users can't alias their AMIs\.

```
aws ec2 describe-images \
    --owners amazon aws-marketplace \
    --query 'Images[*].[ImageId]' \
    --output text
```

**Example: List AMIs owned by an account**  
The following command lists the AMIs owned by the specified AWS account\.

```
aws ec2 describe-images --owners 123456789012
```

**Example: Scope AMIs using a filter**  
To reduce the number of displayed AMIs, use a filter to list only the types of AMIs that interest you\. For example, use the following filter to display only EBS\-backed AMIs\.

```
--filters "Name=root-device-type,Values=ebs"
```