# Activate NVIDIA GRID Capabilities \(G3 Instances Only\)<a name="activate_grid"></a>

To activate the GRID capabilities on G3 instances, such as NVIDIA GRID Virtual Workstation or NVIDIA GRID Virtual Applications, you must define the product type for the driver in the registry and disable the licensing page in the control panel to prevent users from accidentally changing the product type\.

**To activate GRID features on G3 Windows instances**

1. Run regedit\.exe to open the registry editor\.

1. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\NVIDIA Corporation\Global\GridLicensing`\.

1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

1. For **Name**, enter **FeatureType** and type `Enter`\.

1. Open the context \(right\-click\) menu on **FeatureType** and choose **Modify**\.

1. For **Value data**, type the appropriate value below for the NVIDIA GRID feature to enable and choose **OK**\.

   + For NVIDIA GRID Virtual Workstation: 2

   + For NVIDIA GRID Virtual Applications: 0

1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

1. For **Name**, enter **NvCplDisableManageLicensePage** and type `Enter`\.

1. Open the context \(right\-click\) menu on **NvCplDisableManageLicensePage** and choose **Modify**\.

1. For **Value data**, type `1` and choose **OK**\.

1. For NVIDIA GRID Virtual Applications only:

   1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

   1. For **Name**, enter **IgnoreSP** and type `Enter`\.

   1. Open the context \(right\-click\) menu on **IgnoreSP** and choose **Modify**\.

   1. For **Value data**, type `1` and choose **OK**\.

1. Close the registry editor\.