# Optimizing GPU settings<a name="optimize_gpu"></a>

There are several GPU setting optimizations that you can perform to achieve the best performance on G3, G4dn, P2, P3, P3dn, and P4d instances\. With some of these instance types, the NVIDIA driver uses an autoboost feature, which varies the GPU clock speeds\. By disabling autoboost and setting the GPU clock speeds to their maximum frequency, you can consistently achieve the maximum performance with your GPU instances\.

**To optimize GPU settings**

1. Open a PowerShell window and navigate to the NVIDIA installation folder\.

   ```
   cd "C:\Program Files\NVIDIA Corporation\NVSMI"
   ```

1. G2, G3, and P2 instances: Disable the autoboost feature for all GPUs on the instance\.
**Note**  
GPUs on G4dn, P3, P3dn, and P4d instances do not support autoboost\.

   ```
   .\nvidia-smi --auto-boost-default=0
   ```

1. Set all GPU clock speeds to their maximum frequency\. Use the memory and graphics clock speeds specified in the following commands\.

   Some versions of the NVIDIA driver do not support setting the application clock speed, and display the error `"Setting applications clocks is not supported for GPU..."`, which you can ignore\.
   + G3 instances:

     ```
     .\nvidia-smi -ac "2505,1177"
     ```
   + G4dn instances:

     ```
     .\nvidia-smi -ac "5001,1590"
     ```
   + P2 instances:

     ```
     .\nvidia-smi -ac "2505,875"
     ```
   + P3 and P3dn instances:

     ```
     .\nvidia-smi -ac "877,1530"
     ```