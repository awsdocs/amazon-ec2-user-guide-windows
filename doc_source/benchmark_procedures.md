# Benchmark EBS volumes<a name="benchmark_procedures"></a>

You can test the performance of Amazon EBS volumes by simulating I/O workloads\. The process is as follows:

1. Launch an EBS\-optimized instance\.

1. Create new EBS volumes\.

1. Attach the volumes to your EBS\-optimized instance\.

1. Configure and mount the block device\.

1. Install a tool to benchmark I/O performance\.

1. Benchmark the I/O performance of your volumes\.

1. Delete your volumes and terminate your instance so that you don't continue to incur charges\.

**Important**  
Some of the procedures result in the destruction of existing data on the EBS volumes you benchmark\. The benchmarking procedures are intended for use on volumes specially created for testing purposes, not production volumes\.

## Set up your instance<a name="set_up_instance"></a>

To get optimal performance from EBS volumes, we recommend that you use an EBS\-optimized instance\. EBS\-optimized instances deliver dedicated throughput between Amazon EC2 and Amazon EBS, with instance\. EBS\-optimized instances deliver dedicated bandwidth between Amazon EC2 and Amazon EBS, with specifications depending on the instance type\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

To create an EBS\-optimized instance, choose **Launch as an EBS\-Optimized instance** when launching the instance using the Amazon EC2 console, or specify \-\-ebs\-optimized when using the command line\. Be sure that you launch a current\-generation instance that supports this option\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

### Setting up Provisioned IOPS SSD \(`io1` and `io2`\) volumes<a name="setupPIOPS"></a>

To create an `io1` or `io2` volume, choose **Provisioned IOPS SSD \(`io1`\)** or **Provisioned IOPS SSD \(`io2`\)** when creating the volume using the Amazon EC2 console, or, at the command line, specify \-\-volume\-type *io1*\|*io2* \-\-iops *n* where *n* is an integer between 100 and 64,000\. For more detailed EBS\-volume specifications, see [Amazon EBS volume types](ebs-volume-types.md)\. For information about creating an EBS volume, see [Creating an Amazon EBS volume](ebs-creating-volume.md)\. For information about attaching a volume to an instance, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

### Setting up Throughput Optimized HDD \(`st1`\) or Cold HDD \(`sc1`\) volumes<a name="set_up_hdd"></a>

To create an `st1` volume, choose **Throughput Optimized HDD** when creating the volume using the Amazon EC2 console, or specify \-\-type `st1` when using the command line\. To create an `sc1` volume, choose Cold HDD when creating the volume using the Amazon EC2 console, or specify \-\-type `sc1` when using the command line\. For information about creating EBS volumes, see [Creating an Amazon EBS volume](ebs-creating-volume.md)\. For information about attaching these volumes to your instance, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

## Install benchmark tools<a name="install_tools"></a>

The following table lists some of the possible tools you can use to benchmark the performance of EBS volumes\.


| Tool | Description | 
| --- | --- | 
| [DiskSpd](https://gallery.technet.microsoft.com/DiskSpd-A-Robust-Storage-6ef84e62) | DiskSpd is a storage performance tool from the Windows, Windows Server, and Cloud Server Infrastructure engineering teams at Microsoft\. It is available for download at [https://gallery\.technet\.microsoft\.com/DiskSpd\-A\-Robust\-Storage\-6ef84e62/file/199535/2/DiskSpd\-2\.0\.21a\.zip]( https://gallery.technet.microsoft.com/DiskSpd-A-Robust-Storage-6ef84e62/file/199535/2/DiskSpd-2.0.21a.zip)\. After you download the `diskspd.exe` executable file, open a command prompt with administrative rights \(by choosing "Run as Administrator"\), and then navigate to the directory where you copied the `diskspd.exe` file\.  Copy the desired `diskspd.exe` executable file from the appropriate executable folder \(`amd64fre`, `armfre` or `x86fre)` to a short, simple path like `C:\DiskSpd`\. In most cases you will want the 64\-bit version of DiskSpd from the `amd64fre` folder\.  The source code for DiskSpd is hosted on GitHub at: [https://github\.com/Microsoft/diskspd](https://github.com/Microsoft/diskspd)\. | 
|  CrystalDiskMark  | CrystalDiskMark is a simple disk benchmark software\. It is available for download at [https://crystalmark\.info/en/software/crystaldiskmark/](https://crystalmark.info/en/software/crystaldiskmark/)\. | 

These benchmarking tools support a wide variety of test parameters\. You should use commands that approximate the workloads your volumes will support\. These commands provided below are intended as examples to help you get started\.

## Choosing the volume queue length<a name="UnderstandingQueueLength"></a>

Choosing the best volume queue length based on your workload and volume type\.

### Queue length on SSD\-backed volumes<a name="SSD_queue"></a>

To determine the optimal queue length for your workload on SSD\-backed volumes, we recommend that you target a queue length of 1 for every 1000 IOPS available \(baseline for `gp2` volumes and the provisioned amount for `io1` and `io2` volumes\)\. Then you can monitor your application performance and tune that value based on your application requirements\.

Increasing the queue length is beneficial until you achieve the provisioned IOPS, throughput or optimal system queue length value, which is currently set to 32\. For example, a volume with 3,000 provisioned IOPS should target a queue length of 3\. You should experiment with tuning these values up or down to see what performs best for your application\.

### Queue length on HDD\-backed volumes<a name="HDD_queue"></a>

To determine the optimal queue length for your workload on HDD\-backed volumes, we recommend that you target a queue length of at least 4 while performing 1MiB sequential I/Os\. Then you can monitor your application performance and tune that value based on your application requirements\. For example, a 2 TiB `st1` volume with burst throughput of 500 MiB/s and IOPS of 500 should target a queue length of 4, 8, or 16 while performing 1,024 KiB, 512 KiB, or 256 KiB sequential I/Os respectively\. You should experiment with tuning these values value up or down to see what performs best for your application\.

## Disable C\-states<a name="cstates"></a>

Before you run benchmarking, you should disable processor C\-states\. Temporarily idle cores in a supported CPU can enter a C\-state to save power\. When the core is called on to resume processing, a certain amount of time passes until the core is again fully operational\. This latency can interfere with processor benchmarking routines\. For more information about C\-states and which EC2 instance types support them, see [Processor State Control for Your EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/processor_state_control.html)\.

### Disabling C\-states on Windows<a name="windows-cstates"></a>

You can disable C\-states on Windows as follows:

1. In PowerShell, get the current active power scheme\.

   ```
   C:\> $current_scheme = powercfg /getactivescheme
   ```

1. Get the power scheme GUID\.

   ```
   C:\> (Get-WmiObject -class Win32_PowerPlan -Namespace "root\cimv2\power" -Filter "ElementName='High performance'").InstanceID          
   ```

1. Get the power setting GUID\.

   ```
   C:\> (Get-WmiObject -class Win32_PowerSetting -Namespace "root\cimv2\power" -Filter "ElementName='Processor idle disable'").InstanceID                  
   ```

1. Get the power setting subgroup GUID\.

   ```
   C:\> (Get-WmiObject -class Win32_PowerSettingSubgroup -Namespace "root\cimv2\power" -Filter "ElementName='Processor power management'").InstanceID
   ```

1. Disable C\-states by setting the value of the index to 1\. A value of 0 indicates that C\-states are disabled\.

   ```
   C:\> powercfg /setacvalueindex <power_scheme_guid> <power_setting_subgroup_guid> <power_setting_guid> 1
   ```

1. Set active scheme to ensure the settings are saved\.

   ```
   C:\> powercfg /setactive <power_scheme_guid>
   ```

## Perform benchmarking<a name="perform_benchmarking"></a>

The following procedures describe benchmarking commands for various EBS volume types\. 

Run the following commands on an EBS\-optimized instance with attached EBS volumes\. If the EBS volumes were created from snapshots, be sure to initialize them before benchmarking\. For more information, see [Initializing Amazon EBS volumes](ebs-initialize.md)\.

When you are finished testing your volumes, see the following topics for help cleaning up: [Deleting an Amazon EBS volume](ebs-deleting-volume.md) and [Terminate your instance](terminating-instances.md)\.

### Benchmarking `io1` and `io2` volumes<a name="piops_benchmarking"></a>

Run DiskSpd on the volume that you created\.

The following command will run a 30 second random I/O test using a 20GB test file located on the `T:` drive, with a 25% write and 75% read ratio, and an 8K block size\. It will use eight worker threads, each with four outstanding I/Os, and a write entropy value seed of 1GB\. The results of the test will be saved to a text file called `DiskSpeedResults.txt`\. These parameters simulate a SQL Server OLTP workload\.

```
diskspd –b8K –d30 –o4 –t8 –h –r –w25 –L –Z1G –c20G T:\iotest.dat > DiskSpeedResults.txt
```

For more information about interpreting the results, see this tutorial: [Inspecting disk IO performance with DiskSPd](https://sqlperformance.com/2015/08/io-subsystem/diskspd-test-storage)\.