# Activate NVIDIA GRID Virtual Applications<a name="activate_grid"></a>

To activate the GRID Virtual Applications on G3 and G4 instances \(NVIDIA GRID Virtual Workstation is enabled by default\), you must define the product type for the driver in the registry\.

**To activate GRID Virtual Applications on Windows instances**

1. Run regedit\.exe to open the registry editor\.

1. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\NVIDIA Corporation\Global\GridLicensing`\.

1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

1. For **Name**, enter **FeatureType** and type `Enter`\.

1. Open the context \(right\-click\) menu on **FeatureType** and choose **Modify**\.

1. For **Value data**, enter `0` for NVIDIA GRID Virtual Applications and choose **OK**\.

1. Open the context \(right\-click\) menu on the right pane and choose **New**, **DWORD**\.

1. For **Name**, enter **IgnoreSP** and type `Enter`\.

1. Open the context \(right\-click\) menu on **IgnoreSP** and choose **Modify**\.

1. For **Value data**, type `1` and choose **OK**\.

1. Close the registry editor\.