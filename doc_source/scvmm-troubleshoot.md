# Troubleshooting AWS Systems Manager for Microsoft SCVMM<a name="scvmm-troubleshoot"></a>

The following are common errors and troubleshooting steps\.

**Topics**
+ [Error: Add\-in cannot be installed](#scvmm-addin-error)
+ [Installation Errors](#scvmm-install-errors)
+ [Checking the Log File](#scvmm-log)
+ [Errors Importing a VM](#scvmm-import-errors)
+ [Uninstalling the Add\-In](#scvmm-uninstall)

## Error: Add\-in cannot be installed<a name="scvmm-addin-error"></a>

If you receive the following error, try installing [KB2918659](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=2918659) on the computer running the VMM console\. For more information, see [Description of System Center 2012 SP1 Update Rollup 5](http://support.microsoft.com/kb/2904730)\. Note that you don't need to install all the updates listed in this article to address this issue, just KB2918659\.

```
Add-in cannot be installed
The assembly "Amazon.Scvmm.Addin" referenced to by add-in component "AWS Systems Manager for 
Microsoft SCVMM" could not be found in the add-in package. This could be due to the following 
reasons:
1. The assembly was not included with the add-in package.
2. The AssemblyName attribute for the add-in does not match the name of the add-in assembly.
3. The assembly file is corrupt and cannot be loaded.
```

## Installation Errors<a name="scvmm-install-errors"></a>

If you receive one of the following errors during installation, it is likely due to an issue with SCVMM:

```
Could not update managed code add-in pipeline due to the following error:
Access to the path 'C:\Program Files\Microsoft System Center 2012\Virtual Machine Manager
\Bin\AddInPipeline\PipelineSegments.store' is denied.
```

```
Could not update managed code add-in pipeline due to the following error:
The required folder 'C:\Program Files\Microsoft System Center 2012\Virtual Machine Manager
\Bin\AddInPipeline\HostSideAdapters' does not exist.
```

```
Add-in cannot be installed
The assembly "Microsoft.SystemCenter.VirtualMachineManager.UIAddIns.dll" referenced by the 
add-in assembly "Amazon.Scvmm.AddIn" could not be found in the add-in package. Make sure 
that this assembly was included with the add-in package.
```

Try one of the following steps to work around this issue:
+ Grant authenticated users permission to read and execute the `C:\Program Files\Microsoft System Center 2012\Virtual Machine Manager\Bin\AddInPipeline` folder\. In Windows Explorer, right\-click the folder, select **Properties**, and then select the **Security** tab\.
+ Close the SCVMM console and start it one time as an administrator\. From the **Start** menu, locate SCVMM, right\-click, and then select **Run as administrator**\.

## Checking the Log File<a name="scvmm-log"></a>

If you have a problem using the add\-in, check the generated log file, `%APPDATA%\Amazon\SCVMM\ec2addin.log`, for useful information\.

## Errors Importing a VM<a name="scvmm-import-errors"></a>

The log file, `%APPDATA%\Amazon\SCVMM\ec2addin.log`, contains detailed information about the status of an import task\. The following are common errors that you might see in the log file when you import your VM from SCVMM to Amazon EC2\.

**Error: Unable to extract Hyper\-V VirtualMachine object**  
Solution: Configure the path to the Hyper\-V PowerShell module\.

**Error: You do not have permission to perform the operation**  
This error usually occurs when Hyper\-V can't save the VM image into the configured path\. To resolve this issue, do the following\.

1. Create a directory on the Hyper\-V server\. For example: C:\\vmimages\.

1. Share the directory you just created in Hyper\-V\. Any user running SCVMM should be given access to the directory\.

1. In the plugin, set the export path to `\\hyperv\vmimages`\.

1. Perform the export\.

The image will be exported to a local directory on the Hyper\-V server\. The SCVMM plugin will pull it from Hyper\-V, and upload into Amazon S3\.

## Uninstalling the Add\-In<a name="scvmm-uninstall"></a>

If you need to uninstall the add\-in, use the following procedure\.

**To uninstall the add\-in**

1. Open the VMM console\.

1. Select the **Settings** workspace, and then click **Console Add\-Ins**\.

1. Select **AWS Systems Manager for Microsoft SCVMM**\.

1. On the ribbon, click **Remove**\.

1. When prompted for confirmation, click **Yes**\.

If you reinstall the add\-in after uninstalling it and receive the following error, delete the path as suggested by the error message\.

```
Error (27301)
There was an error while installing the add-in. Please ensure that the following path does not 
exist and then try the installation again.
        
C:\Program Files\Microsoft System Center 2012\Virtual Machine Manager\Bin\AddInPipeline\
AddIns\EC2WINDOWS...
```