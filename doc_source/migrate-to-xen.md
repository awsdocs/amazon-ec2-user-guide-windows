# Migrate to Xen instance types from Nitro instance types<a name="migrate-to-xen"></a>

The following procedure assumes that you are currently running on a Nitro\-based instance type, such as M5 or C5, and that you are migrating to an instance based on the Xen System, such as M4 or C4\. For instance type specifications, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\. Perform the following steps before the migration to avoid errors during the booting process\.

1. AWS PV drivers must be installed and upgraded on a Nitro instance before you migrate to a Xen instance\. For steps to install and upgrade AWS PV drivers, see [Part 1: Install and upgrade AWS PV drivers](migrating-latest-types.md#upgrade-pv)\.

1. Update to the latest EC2Launch v2 version\. See [Migrate to EC2Launch v2Migrate](ec2launch-v2-migrate.md) for steps\.

1. Open a PowerShell session and run the following command as an administrator to sysprep the driver devices\. Running sysprep ensures that early boot storage drivers required for booting on Xen instances are properly registered with Windows\.
**Note**  
Running the command using PowerShell \(x86\) versions will result in an error\. This command runs sysprep only on the driver devices\. It does not run the full sysprep preparation\.

   ```
   start rundll32.exe sppnp.dll,Sysprep_Generalize_Pnp -wait
   ```

1. Perform the migration to a Xen instance type when the sysprep process completes\.