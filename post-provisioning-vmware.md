---

copyright:
  years: 2021
lastupdated: "2021-08-06"

keywords:

subcollection: citrix-daas

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:important: .important}
{:note: .note}
{:table: .aria-labeledby="caption"}

# Post-provisioning steps for VMware ESXi
{: #post-provisioning-citrix-daas-vmware}

After you provision your {{site.data.keyword.cvad_full}} ({{site.data.keyword.cvad_short}}) with the VMware Hypervisor, complete the following tasks:

1.	Verify the status of provisioned servers and view network and storage details.
2.	(Optional) Provision a virtual server instance for management.
3.  Establish network connectivity for Active Directory topologies
4.	Access the Active Directory, Cloud Connectors, and proxy server.
5.	Access the vCenter and ESXi hosts.
6.  Configure vSAN.
7.	Create master image of virtual machine.

## Step 1: Verify the status of provisioned servers and view network and storage details
{: #verify-status-server-network-storage}

1.	Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/){: external}.
1.	Click **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Resources** to check the status of your ESXi hosts and your network and storage information.
1. Select the vCenter instance that you created. 
1. Click the **Summary** tab to view the access details of vCenter.   
1. Click the **Infrastructure** tab to view summary details of the cluster and ESXi hosts. 
1. Check your VLAN information. On the **Infrastructure** tab, select the cluster that you created. On the cluster page, click **View Resource** under **Private VLAN** to view the:

    *	Private VLAN number and the Data Center in which it is created. You use this network information when you establish network connectivity to IBM Cloud Private network space.
    *	The devices configured under this VLAN. This list includes all the ESXi hosts and {{site.data.keyword.cvad_short}} components like Cloud Connectors and Active Directory. Click any of the links to view the server details. 
    *	/24 portable subnet with 256 IP addresses that is created per your {{site.data.keyword.cvad_short}} order. You need to create VMs on this portable subnet. Multiple portable subnets are shown. 
    *	Storage details like NFS.

You can search for details about your server on the Classic infrastructure Resource List page. Your {{site.data.keyword.cvad_short}} servers are provisioned with two tags: `CVAD` and `Resource` location name. The resource location name is the name that you provided during the ordering process. To verify that your servers are provisioned, use these tags to filter for the servers in the Resource List page.

## Step 2: (Optional) Provision a virtual server instance for management
{: #provision-vsi-vmware}

You can provision a virtual server instance with a Windows operating system to manage your {{site.data.keyword.cvad_full_notm}} for IBM Cloud. You can use a jump box to start a Remote Desktop session to access the Active Directory server and Citrix Cloud Connectors. You can also add vCenter and esxi details in the hosts files for name resolution.

You must provision the virtual server instance with the same network details as the other service components that are attached to the resource location.
{: important}

## Step 3. Establish network connectivity for Active Directory Topology
{: #ad-topology-network-connectivity-vmware}

### IBM Cloud 
{: #ad-topology-network-connectivity-cloud-vmware}

If you chose **IBM Cloud** Active Directory topology and are using the Active Directory in {{site.data.keyword.cloud_notm}} as your only domain controller, no further network configuration is required. The Cloud Connectors are joined to the Active Directory server domain and are configured to communicate with Citrix Cloud. You might have to create users with the necessary privileges. You can also create bulk users in Active Directory. For more information, see this step-by-step guide on how to [Create bulk users in Active Directory](https://activedirectorypro.com/create-bulk-users-active-directory/){: external}.

### Extended (Multisite)
{: #ad-topology-network-connectivity-hybrid-vmware}

1. With an Extended (Multisite) setup, you must establish network connectivity between {{site.data.keyword.cloud_notm}} and your on-premises location. 

   You can set up network connectivity by using any of the gateway appliances available in {{site.data.keyword.cloud_notm}}. For more information, see the **About** section in the [Gateway Appliance {{site.data.keyword.cloud_notm}} catalog page](https://cloud.ibm.com/gen1/infrastructure/provision/gateway){: external}. 
   
   You must set up another Gateway appliance on-premises too. Configure both the appliances and establish network connectivity between the two sites.

   When you order a gateway appliance from {{site.data.keyword.cloud_notm}}, the data center and backend VLAN must correspond to the same data center and VLAN where your other services are provisioned. See [View network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details).
   {: important}

2. Install the domain controller in {{site.data.keyword.cloud_notm}}  

    A Windows virtual server instance is provisioned in the same VLAN and network as your other servers. Complete the following steps to install the domain controller and join the secondary controller:

    Install the Active Directory Domain Services (AD DS) domain controller on this Windows server and join the secondary controller with your on-premises domain. 

    Domain join your Cloud Connectors, and then run the Cloud Connector registration scripts to add the connectors to the resource location.

    The Cloud Connector PowerShell scripts are available in `C:\postscript`.

3.  Run the configuration scripts on each of the three connector virtual server instances:
    
    1. Make sure your Connector and Active Directory can talk to each other. Run the `ping` command if `imp` is not blocked.
    2. Use PowerShell to go to the directory `C:\postscript`.
    3. Verify that the **ManualRegisterConnector.ps1** command is in the directory. 
    4. Run the **ManualRegisterConnector.ps1** command: 

       If you are using the APIClientID and APIClientSecret that you used to create the order, enter:
       ```
       ./ManualRegisterConnector.ps1 -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}
       ```

       Replace the contents of the {} with the values for your system. 

       If you need to use a different APIClientID and APIClientSecret enter:
         ```
       ./ManualRegisterConnector.ps1 -APIClientID {APIClientID} -APIClientSecret {APIClientSecret} -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}
         ```

       Replace the contents of the {} with the values for your system. 

### Extended (Site-to-Site)
{: #ad-topology-network-connectivity-onprem-vmware}

**Domain controller latency issues**

The Extended (Site-to-Site) option does not have a domain controller (DC) on {{site.data.keyword.cloud_notm}}. Sites that do not have domain controllers are less efficient because of slow AD authentication and GPO processing. If you have sites with no DCs, you can limit the latency issues if you:
* Create the AD subnets for these sites and link them to the closest site with a DC. The sites reach the on-premises side for AD authentication and GPO processing.
* Enable GPO processing over [slow links detection](https://technet.microsoft.com/en-us/library/cc781031%28v=ws.10%29.aspx?f=255&MSPPError=-2147217396).

Other Active Directory Best practices include:
* Establish as a site every geographic area that requires fast access to the latest directory information. Establishing areas that require immediate access to up-to-date Active Directory information as separate sites provide the resources that are required to meet your needs.
* Place at least one domain controller in every site, and make at least one domain controller in each site a global catalog. Sites that do not have their own domain controllers and at least one global catalog depend on other sites for directory information and are less efficient. For more information, see [Active Directory Best Practices](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc778219%28v=ws.10%29)

**Network Connectivity** 

1. With an Extended (Site-to-Site) setup, you must establish network connectivity between {{site.data.keyword.cloud_notm}} and your on-premises location. 

   You can set up network connectivity by using any of the gateway appliances available in {{site.data.keyword.cloud_notm}}. For more information, see the **About** section in the [Gateway Appliance {{site.data.keyword.cloud_notm}} catalog page](https://cloud.ibm.com/gen1/infrastructure/provision/gateway){: external}. 
   
   You must set up another Gateway appliance on-premises too. Configure both the appliances and establish network connectivity between the two sites.

   When you order a gateway appliance from {{site.data.keyword.cloud_notm}}, the data center and backend VLAN must correspond to the same data center and VLAN where your other services are provisioned. See [View network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details).
   {: important}

2. Domain join Cloud Connectors with Active Directory server

    After you establish network connectivity between {{site.data.keyword.cloud_notm}} and your on-premises location, you can domain join the Cloud Connectors with the Active Directory server. Run the Cloud Connector registration scripts so that the connectors are added to the resource location.

    The Cloud Connector PowerShell scripts are available in `C:\postscript`.

3.  Run the configuration scripts on each of the three connector virtual server instances:
    
    1. Make sure your Connector and Active Directory can talk to each other. Run the `ping` command if `imp` is not blocked.
    2. Use PowerShell to go to the directory `C:\postscript`.
    3. Verify that the **ManualRegisterConnector.ps1** command is in the directory. 
    4. Run the **ManualRegisterConnector.ps1** command: 

       If you are using the APIClientID and APIClientSecret that you used to create the order, enter:
       ```
       ./ManualRegisterConnector.ps1 -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}
       ```

       Replace the contents of the {} with the values for your system. 

       If you need to use a different APIClientID and APIClientSecret enter:
         ```
       ./ManualRegisterConnector.ps1 -APIClientID {APIClientID} -APIClientSecret {APIClientSecret} -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}
         ```

       Replace the contents of the {} with the values for your system. 

4. Manually update proxy and DHCP server configuration files

   1. Log in to your proxy server and update /etc/squid/squid.conf file with your Active Directory server IP `dns_nameservers <AD server IP> 8.8.8.8` 
   2. Restart proxy servers by entering `systemctl restart squid.service`.
   3. Log in to your DHCP server and update /etc/dhcp/dhcpd.conf file with your AD server IP option: `domain-name-servers < AD server IP>`.
   4. Restart DHCP service on the nodes by entering `systemctl restart dhcpd.service`.

## Step 4: Access the Active Directory, Cloud Connectors, and proxy server
{: #access-ad-connectors-proxy-vmware}

You can access the Active Directory and Cloud Connectors through a Remote Desktop session. The proxy server can be accessed by an SSH client.

## Step 5: Access the vCenter and ESXi Hosts
{: #access-vcenter-esxi-vmware}

vCenter access information is available in the summary tab. You can access the vCenter with IP or FQDN from the jump host or your local computer that is connected to the IBM private [network space](https://cloud.ibm.com/docs/citrix-daas?topic=citrix-daas-getting-started-tutorial#set-up-network-connectivity).

## Step 6: Configure vSAN
{: #configure-vsan}

1.  Make sure the vsanDatastore is created under Storage. For more information, see [View vSAN Datastore](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vsan-planning.doc/GUID-66BFA39F-C3D8-4DE0-806F-58A756E77399.html).

1.  Create a folder named VMimages under the vsanDatastore and Upload the Windows ISO Image for a Guest Operating System.  For more information, see the [Upload ISO Image Installation Media for a Guest Operating System](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-492D6904-7471-4D66-9555-9466CCCA6931.html).

## Step 7: Create master image of Virtual Machine
{: #create-master-image-vmware}

1. Create a New Distributed Port Group (DPG) attached to the private distributed switch. In the configuration settings of the window, leave the VLAN type as NONE. 

2. Create the master image for virtual machines with vCenter. If you are using vSAN, when you get to the Select storage step of creating New Virtual Machine, select the vsanDatastore. When you create the VM, make sure to have the VM Network pointing to the DPG you created.

3. Install VMware tools on the guest operating system of the virtual machine created. See [Installing VMware tools](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.html.hostclient.doc/GUID-ED3ECA21-5763-4919-8947-A819A17980FB.html?hWord=N4IghgNiBcIG4FsDuYBOBTABAFwPa4gGcQBfIA)

4. Join the virtual machine master image to the Active Directory server domain.

5. Add the proxy server settings to your master image. 

    1. The settings can be copied from the Cloud Connector server to the Master VM Image. Follow the instructions to set up [proxy settings](https://docs.microsoft.com/en-us/troubleshoot/browsers/use-proxy-servers-with-ie). 

    2. Using the instructions in [proxy settings](https://docs.microsoft.com/en-us/troubleshoot/browsers/use-proxy-servers-with-ie), paste the copied settings to your Master VM image.  

6. While connected to the Master VM Image, enter this command on an elevated command prompt of the Master VM Image:

      ``netsh winhttp import proxy source=ie``

7. Install the VDA on the virtual machine. For more information, see [Install VDAs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/install-vdas.html#step-1-download-the-p[…]and-launch-the-wizard).

8. (Optional) To give access to all other non-administrator users, do one of the following methods:

      * Use Microsoft Group Policy settings
      * Update the windows registry on the master image and set **Proxysettingsperuser** to ***0***.
        1. Press the `Win+R` keys to optn the Run window. 
        2. Enter **regedit** and **Enter** to open the Registry Editor. 
        3. In registry Editor, navigate to the following registry path:
        HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\CurrentVersion\Internet Settings
        4. Double-click on the **ProxySettingsPerUser** registry.
        5. Change the Value to **0**.

Individual virtual machine performance varies depending on workload. However, to take advantage of optimizations that might result in improved performance, you can use [this tool for Windows 10](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-optimization-tool-now-available/m-p/1558614).
{: tip}


## Next steps
{: #post-prov-vm-next}

After you complete these post-provisioning steps, you can:

*  Order more bare metal servers for your cluster. See [Ordering more bare metal servers](/docs/citrix-daas?topic=citrix-daas-order-bare-metal-servers).
*  Complete your management tasks in the [Citrix Cloud Portal](https://accounts.cloud.com/core/login?ReturnUrl=%2Fcore%2Fconnect%2Fauthorize%2Fcallback%3Fclient_id%3DRtmydVjvjLZBbU3qU3b8eQ%253D%253D%26redirect_uri%3Dhttps%253A%252F%252Fcitrix.cloud.com%252Foauth%26response_type%3Dcode%26scope%3Dopenid%2520email%2520profile%2520ctx_principal_aliases%2520offline_access%2520ctx_universal%26state%3Dhttps%253A%252F%252Fcitrix.cloud.com%252Foauth.8d6b5e58-752c-447e-865a-492d56f08d2a).
