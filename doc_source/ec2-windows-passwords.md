# Setting the password for a Windows instance<a name="ec2-windows-passwords"></a>

When you connect to a Windows instance, you must specify a user account and password that has permission to access the instance\. The first time that you connect to an instance, you are prompted to specify the Administrator account and the default password\.

With AWS Windows AMIs for Windows Server 2012 R2 and earlier, the [EC2Config service](ec2config-service.md) generates the default password\. With AWS Windows AMIs for Windows Server 2016 and later, [EC2Launch](ec2launch.md) generates the default password\.

**Note**  
With Windows Server 2016 and later, `Password never expires` is disabled for the local administrator\. With Windows Server 2012 R2 and earlier, `Password never expires` is enabled for the local administrator\.

## Changing the Administrator password after connecting<a name="change-admin-password"></a>

When you connect to an instance the first time, we recommend that you change the Administrator password from its default value\. Use the following procedure to change the Administrator password for a Windows instance\.

**Important**  
Store the new password in a safe place\. You won't be able to retrieve the new password using the Amazon EC2 console\. The console can only retrieve the default password\. If you attempt to connect to the instance using the default password after changing it, you'll get a "Your credentials did not work" error\.

**To change the local Administrator password**

1. Connect to the instance and open a command prompt\.

1. Run the following command\. If your new password includes special characters, ensure that you enclose the password in double quotes:

   ```
   net user Administrator "new_password"
   ```

1. Store the new password in a safe place\.

## Changing a lost or expired password<a name="change-lost-expired-password"></a>

If you lose your password or it expires, you can generate a new password\. For password reset procedures, see [Resetting a lost or expired Windows administrator password](ResettingAdminPassword.md)\.