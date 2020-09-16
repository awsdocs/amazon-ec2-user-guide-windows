# Upgrading an Amazon EC2 Windows instance to a newer version of Windows Server<a name="serverupgrade"></a>

There are two methods to upgrade an earlier version of Windows Server running on an instance: in\-place upgrade and migration \(also called side\-by\-side upgrade\)\. An in\-place upgrade upgrades the operating system files while your personal settings and files are intact\. A migration involves capturing settings, configurations, and data and porting these to a newer operating system on a fresh Amazon EC2 instance\.

Microsoft has traditionally recommended migrating to a newer version of Windows Server instead of upgrading\. Migrating can result in fewer upgrade errors or issues, but can take longer than an in\-place upgrade because of the need to provision a new instance, plan for and port applications, and adjust configurations settings on the new instance\. An in\-place upgrade can be faster, but software incompatibilities can produce errors\.

**Topics**
+ [Performing an in\-place upgrade](os-inplaceupgrade.md)
+ [Performing an automated upgrade](automated-upgrades.md)
+ [Migrating to latest generation instance types](migrating-latest-types.md)
+ [Windows to Linux replatforming assistant for Microsoft SQL Server Databases](replatform-sql-server.md)
+ [Troubleshooting an upgrade](os-upgrade-trbl.md)