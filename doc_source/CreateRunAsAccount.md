# Step 3: Create an AWS Run As Account<a name="CreateRunAsAccount"></a>

You must set up credentials that grant AWS Management Pack access to your AWS resources\.

**To create an AWS Run As account**

1. We recommend that you create an IAM user with the minimum access rights required \(for example, the **ReadOnlyAccess** AWS managed policy works in most cases\)\. You'll need the access keys \(access key ID and secret access key\) for this user to complete this procedure\. For more information, see [Administering Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html) in the *IAM User Guide*\.

1. In the Operations console, on the **Go** menu, click **Administration**\.

1. In the **Administration** workspace, expand the **Run As Configuration** node, and then select **Accounts**\.

1. Right\-click the **Accounts** pane, and then click **Create Run As Account**\.

1. In the **Create Run As Account Wizard**, on the **General Properties** page, in the **Run As account type** list, select **Basic Authentication**\.

1. Enter a display name \(for example, "My IAM Account"\) and a description, and then click **Next**\.  
![\[Configuring an AWS Run As account\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/configure_runas_account.png)

1. On the **Credentials** page, enter the access key ID in the **Account name** box and the secret access key in the **Password** box, and then click **Next**\.  
![\[Storing the access key ID and secret access key\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/store_access_key.png)

1. On the **Distribution Security** page, select **More secure \- I want to manually select the computers to which the credentials will be distributed**, and then click **Create**\.  
![\[Distribution security option\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/distribution_security_option.png)

1. Click **Close**\.

1. In the list of accounts, select the account that you just created\.

1. In the **Actions** pane, click **Properties**\.

1. In the **Properties** dialog box, verify that the **More Secure** option is selected and that all management servers to be used to monitor your AWS resources are listed\.  
![\[Management servers\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/AccountDistribution.png)