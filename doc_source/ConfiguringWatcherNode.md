# Step 2: Configuring the Watcher Node<a name="ConfiguringWatcherNode"></a>

On System Center Operations Manager 2007 R2, the watcher node runs discoveries that go beyond the watcher node computer, so you must enable the proxy agent option on the watcher node\. The proxy agent allows those discoveries to access the objects on other computers\.

**Note**  
If your system is configured with a large number of resources, we recommend that you configure one management server as a Watcher Node\. Having a separate Watcher Node management server can improve performance\.

If you're using System Center 2012 — Operations Manager, you can skip this step\.

**To enable the proxy agent on System Center Operations Manager 2007 R2**

1. In the Operations console, on the **Go** menu, click **Administration**\.

1. In the **Administration** workspace, under **Device Management**, click **Agent Managed**\.

1. In the **Agent Managed** list, right\-click the watcher node, and then click **Properties**\.

1. In the **Agent Properties** dialog box, click the **Security** tab, select **Allow this agent to act as proxy and discover managed objects on other computers**, and then click **OK**\.