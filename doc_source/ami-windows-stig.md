# Amazon EC2 Windows Server AMIs for STIG compliance<a name="ami-windows-stig"></a>

Security Technical Implementation Guides \(STIGs\) are the configuration standards created by the Defense Information Systems Agency \(DISA\) to secure information systems and software\. To make your systems compliant with STIG standards, you must install, configure, and test a variety of security settings\. Amazon EC2 Windows Server AMIs for STIG Compliance are pre\-configured with over 160 required security settings\. STIG\-compliant operating systems include Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019\. The STIG\-compliant AMIs include updated Department of Defense \(DoD\) certificates to help you get started and achieve STIG compliance\. There are no additional charges for using STIG\-compliant AMIs\.

Amazon EC2 Windows Server AMIs for STIG compliance are available in all public AWS and GovCloud regions\. You can launch instances from these AMIs directly from the Amazon EC2 console\. They are billed using standard Windows pricing\.

The STIG\-compliant Amazon EC2 AMIs for Windows Server can be found in the Community AMIs when you create an instance\. The AMI names are as follows \(**YYYY\.MM\.DD** denotes the latest version\. You can search for the version without the date suffix\.\) 
+ Windows\_Server\-2019\-English\-STIG\-Full\-**YYYY\.MM\.DD**
+ Windows\_Server\-2019\-English\-STIG\-Core\-**YYYY\.MM\.DD**
+ Windows\_Server\-2016\-English\-STIG\-Full\-**YYYY\.MM\.DD**
+ Windows\_Server\-2016\-English\-STIG\-Core\-**YYYY\.MM\.DD**
+ Windows\_Server\-2012\-R2\-English\-STIG\-Full\-**YYYY\.MM\.DD**
+ Windows\_Server\-2012\-R2\-English\-STIG\-Core\-**YYYY\.MM\.DD**

**Compliance levels**
+ **High \(Category I\) **

  The most severe risk and includes any vulnerability that can result in loss of confidentiality, availability, or integrity\.
+ **Medium \(Category II\) **

  Any vulnerability that could result in loss of confidentiality, availability, or integrity\. These risks could be mitigated\.
+ **Low \(Category III\) **

  Any vulnerability that degrades measures to protect against loss of confidentiality, availability, or integrity\.

The following lists and tables show the STIGs that have *not* been applied to each Operating System, by category, unless otherwise indicated\. 

**Topics**
+ [Core and base operating system](#base-os-stig)
+ [Internet Explorer \(IE\) 11 STIG V1 Release 18](#ie-os-stig)
+ [Microsoft \.NET Framework 4\.0 STIG V1 Release 9: STIGS not applied](#dotnet-os-stig)
+ [Windows Firewall STIG V1 Release 7](#windows-firewall-stig)
+ [Version history](#stig-version-history)

## Core and base operating system<a name="base-os-stig"></a>

The following STIG settings have *not* been applied\. All other applicable STIGs have been applied\. For a complete list, see the [STIGs Document Library](https://public.cyber.mil/stigs/downloads/?_dl_facet_stigs=windows)\. For instructions on how to view the complete list, see [How to View SRGs and STIGs ](https://dl.dod.cyber.mil/wp-content/uploads/stigs/doc/HOW_TO_VIEW_SRGs_and_STIGs.doc)\.

------
#### [ Windows Server 2019 STIG V1 Release 3 ]

V\-92975, V\-92977, V\-93147, V\-93149, V\-93183, V\-93185, V\-93187, V\-93203, V\-93205, V\-93209, V\-93217, V\-93219, V\-93221, V\-93227, V\-93229, V\-93231, V\-93281, V\-93283, V\-93369, V\-93379, V\-93381, V\-93437, V\-93439, V\-93457, V\-93461, V\-93473, V\-93475, V\-93489, V\-93511, V\-93515, V\-93543, V\-93567, and V\-93571

------
#### [ Windows Server 2016 STIG V1 Release 12 ]

V\-73217, V\-73221, V\-73223, V\-73225, V\-73229, V\-73231, V\-73233, V\-73235, V\-73241, V\-73245, V\-73259, V\-73261, V\-73263, V\-73265, V\-73273, V\-73275, V\-73277, V\-73279, V\-73281, V\-73283, V\-73285, V\-73307, V\-73401, V\-73403, V\-73521, V\-73607, V\-73623, V\-73625, V\-73647, V\-73649, V\-73701, V\-78127, V\-90357, and V\-90357 

------
#### [ Windows Server 2012 R2 STIG V2 Release 18 ]

V\-1072, V\-1074, V\-1076, V\-1089, V\-1112, V\-1114, V\-1115, V\-1127, V\-1145, V\-2907, V\-3289, V\-3383, V\-3472, V\-3487, V\-6840, V\-7002, V\-14225, V\-15505, V\-26359, V\-36451, V\-36658, V\-36659, V\-36661, V\-36662, V\-36666, V\-36670, V\-36671, V\-36672, V\-36678, V\-36733, V\-36734, V\-36735, V\-36736, V\-40172, V\-40173, V\-42420, V\-57637, V\-57641, V\-57645, V\-57653, V\-57655, V\-57719, and V\-75915

------

## Internet Explorer \(IE\) 11 STIG V1 Release 18<a name="ie-os-stig"></a>

All STIG settings related to Internet Explorer have been applied\. For a complete list, see the [STIGs Document Library](https://public.cyber.mil/stigs/downloads/?_dl_facet_stigs=app-security%2Cbrowser-guidance)\. For instructions on how to view the complete list, see [How to View SRGs and STIGs ](https://dl.dod.cyber.mil/wp-content/uploads/stigs/doc/HOW_TO_VIEW_SRGs_and_STIGs.doc)\.

**Windows Server 2019, 2016, and 2012 R2**  
All STIG settings applied\.

## Microsoft \.NET Framework 4\.0 STIG V1 Release 9: STIGS not applied<a name="dotnet-os-stig"></a>

The following STIGs have *not* been applied\. All other applicable STIG settings have been applied\. For a complete list, see [STIGs U\_MS\_DotNet\_Framework\_4\-0\_V1R9\_STIG\.zip](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_MS_DotNet_Framework_4-0_V1R9_STIG.zip)\. For instructions on how to view the complete list, see [How to View SRGs and STIGs ](https://dl.dod.cyber.mil/wp-content/uploads/stigs/doc/HOW_TO_VIEW_SRGs_and_STIGs.doc)\.

**Windows Server 2019, 2016, and 2012 R2**  
V\-7055, V\-7061, V\-7063, V\-7067, V\-7069, V\-7070, V\-18395, V\-30926, V\-30935, V\-30937, V\-30968, V\-30972, V\-30986, V\-31026, and V\-32025

## Windows Firewall STIG V1 Release 7<a name="windows-firewall-stig"></a>

All STIG settings related to Windows Firewall have been applied\. For complete list, see [https://dl\.dod\.cyber\.mil/wp\-content/uploads/stigs/zip/U\_Windows\_Firewall\_V1R7\_STIG\.zip](https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_Windows_Firewall_V1R7_STIG.zip)\. For instructions on how to view the complete list, see [How to View SRGs and STIGs ](https://dl.dod.cyber.mil/wp-content/uploads/stigs/doc/HOW_TO_VIEW_SRGs_and_STIGs.doc)\.

## Version history<a name="stig-version-history"></a>

The following table shows STIG AMI version history updates\.


| Date | AMIs | Details | 
| --- | --- | --- | 
| 12/6/2019 |  Windows Firewall STIG V1 R17  | Initial release\. | 
| 12/6/2019 | Server 2012 R2 Core and Base V2 R17 Server 2016 Core and Base V1 R11 Internet Explorer 11 V1 R18 Microsoft \.NET Framework 4\.0 V1 R9  | Updated versions and applied STIGs\. | 
| 9/17/2019 | Server 2012 R2 Core and Base V2 R16 Server 2016 Core and Base V1 R9 Server 2019 Core and Base V1 R2 Internet Explorer 11 V1 R17 Microsoft \.NET Framework 4\.0 V1 R8 | Initial release\. | 