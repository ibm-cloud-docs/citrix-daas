---

copyright:
  years: 2020, 2021
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

# Post-provisioning steps for Citrix Hypervisor  
{: #post-provisioning-citrix-daas} 

After you provision your {{site.data.keyword.cvad_full}} ({{site.data.keyword.cvad_short}}) with the Citrix Hypervisor (formerly known as XenServer), complete the following tasks:

1. Verify status of provisioned servers and view network and storage details
2. (Optional) Provision virtual server instance for management
3. Establish network connectivity for Active Directory topologies
4. Access the Citrix Hypervisor
5. Create resource pools
6. Access the Active Directory, Cloud Connectors, DHCP, and proxy server
7. Create a master image of virtual machine

## Step 1. Verify status of provisioned servers and view network and storage details 
{: #check-health-of-installed-servers}  

1. Log in to the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/){: external}. 
2. Go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > Devices** to check the status of your servers and view your network and storage information. Make sure that provisioning is complete before proceeding. 

### Verify provisioned servers
{: #verify-provisioned-servers}  

Your {{site.data.keyword.cvad_short}} servers are provisioned with two tags: `{{site.data.keyword.cvad_short}}` and `Resource location name`. The resource location name is the name that you provided during the ordering process. To verify that your servers are provisioned, you can query the servers in your account by filtering for them using these tags in the **Devices** page.

### View network details
{: #view-network-details}

After you filter for your server by using the appropriate tags, select your device. In the **Network details** section of your selected device, you can access your network information. 

You must use this network information when you establish network connectivity, order more servers, or order more network capacity. 
{: important}

### View storage details
{: #view-storage-details}

Complete the following steps to identify and view your File Storage details.
 
1. In the {{site.data.keyword.cloud_notm}} console, go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > File Storage**.
2. Your File Storage has the `{{site.data.keyword.cvad_short}}` and `Resource location name` tags in the _Notes_ field. 

## Step 2. (Optional) Provision virtual server instance for management
{: #provision-vsi-for-management}

Optionally, you can provision a virtual server instance with a Windows operating system to manage your {{site.data.keyword.cvad_full_notm}}. You can use a jump server to start a Remote Desktop session to access the Active Directory server and Citrix Cloud Connectors. The jump server is also needed to start the XenCenter management interface. You can order a Windows virtual server instance through the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/gen1/infrastructure/provision/vs?cm_sp=Cloud-Product-_-OnPageNav-IBMCloudPlatform_IBMVirtualMachines-_-VSI_Prod_Midpage){: external}.

You must provision the virtual server instance with the same [network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details) as the other service components that are attached to the resource location.
{: important}

## Step 3. Establish network connectivity for Active Directory Topology
{: #ad-topology-network-connectivity}

### IBM Cloud 
{: #ad-topology-network-connectivity-cloud}

If you chose **IBM Cloud** Active Directory topology and are using the Active Directory in {{site.data.keyword.cloud_notm}} as your only domain controller, no further network configuration is required. The Cloud Connectors are joined to the Active Directory server domain and are configured to communicate with Citrix Cloud. You might have to create users with the necessary privileges. You can also create bulk users in Active Directory. For more information, see this step-by-step guide on how to [Create bulk users in Active Directory](https://activedirectorypro.com/create-bulk-users-active-directory/){: external}.

### Extended (Multisite)
{: #ad-topology-network-connectivity-hybrid}

1. With an Extended (Multisite) setup, you must establish network connectivity between {{site.data.keyword.cloud_notm}} and your on-premises location. 

   You can set up network connectivity by using any of the gateway appliances available in {{site.data.keyword.cloud_notm}}. For more information, see the **About** section in the [Gateway Appliance {{site.data.keyword.cloud_notm}} catalog page](https://cloud.ibm.com/gen1/infrastructure/provision/gateway){: external}. 
   
   You must set up another Gateway appliance on-premises too. Configure both the appliances and establish network connectivity between the two sites.

   When you order a gateway appliance from {{site.data.keyword.cloud_notm}}, the data center and backend VLAN must correspond to the same data center and VLAN where your other services are provisioned. See [View network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details).
   {: important}

2. Install the domain controller in {{site.data.keyword.cloud_notm}}  

    A Windows virtual server instance is provisioned in the same VLAN and network as your other servers. Complete the following steps to install the domain controller and join the secondary controller:

      1. Install the Active Directory Domain Services (AD DS) domain controller on this Windows server and join the secondary controller with your on-premises domain. 

      2. Domain join your Cloud Connectors, and then run the Cloud Connector registration scripts to add the connectors to the resource location.

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
{: #ad-topology-network-connectivity-onprem}

**Domain controller latency issues**

The Extended (Site-to-Site) option does not have a domain controller (DC) on {{site.data.keyword.cloud_notm}}. Sites that do not have domain controllers are less efficient because of slow AD authentication and GPO processing. If you have sites with no DCs, you can limit the latency issues if you:
* Create the AD subnets for these sites and link them to the closest site with a DC. The sites reach the on-premises side for AD authentication and GPO processing.
* Enable GPO processing over [slow links detection](https://technet.microsoft.com/en-us/library/cc781031%28v=ws.10%29.aspx?f=255&MSPPError=-2147217396).

Other Active Directory Best practices include:
* Establish as a site every geographic area that requires fast access to the most recent directory information. Establishing areas that require immediate access to up-to-date Active Directory information as separate sites provide the resources that are required to meet your needs.
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


## Step 4. Access and Configure the Citrix Hypervisor
{: #access-citrix-hypervisor}

Download the XenCenter software from [Citrix downloads](https://www.citrix.com/downloads/citrix-hypervisor/){: external} on the machine that you use to connect to your bare metal servers.
   1. If you are using a jump server to connect to the private network and your {{site.data.keyword.cvad_short}} resource, download XenCenter from the [Citrix site](https://www.citrix.com/downloads/citrix-hypervisor/product-software/?_ga=2.151686649.1165974256.1623098841-1286618284.1614696293).
   2. Sign in to your Citrix account.
   3. Select that XenServer option for your Citrix server, either Citrix Hypervisor 8.2 LTSR Premium Edition or Xencenter 7.1 LTSR Enterprise Edition.
   4. On the LTSR download page, go to the Components section and download the Windows Management Console file.  
   5. Install the Windows Management Console.  If you are using XenCenter 8.2, you must have Microsfot .NET 4.8 Framework installed. 

For Citrix Hypervisor 8.2, it is a good idea to enable NUMA placement on each hypervisor for optimal performance. To enable NUMA placement, append "numa-placement=true" to the Xen configuration file in /etc/xenopsd.conf and restart the Xen tool stack through XenCenter or the CLI.

## Step 5. Create resource pools  
{: #create-resource-pools}

You can run your hypervisors in pools. If you have both server VDA workloads and desktop VDA workloads, create a separate pool for each workload so that they are hosted in different pools. For more information, see [Hosts and resource pools](https://docs.citrix.com/en-us/citrix-hypervisor/hosts-pools.html){: external}.

If you ordered only one bare metal server, the storage is attached and mounted on the server. If you ordered multiple bare metal servers, the storage is attached on only one server, which is tagged as `master`. You can query your master node by filtering your device list with the tags `master` and `<your resource location name>`. This result is the master node in your resource pool. Create a resource pool with this node as master, and then add all other bare metal servers to this pool. The storage is then mounted on the remaining nodes. For more information, see [Requirements for creating resource pools](https://docs.citrix.com/en-us/citrix-hypervisor/hosts-pools.html#requirements-for-creating-resource-pools){: external}.


## Step 6. Access the Active Directory, Cloud Connectors, DHCP, and proxy server
{: #access-connectors-dhcp-proxy-server}

The Active Directory and Cloud Connectors can be accessed through a Remote Desktop session. DHCP and the proxy server can be accessed by an SSH client.

## Step 7. Create master image of virtual machine
{: #create-virtual-machine}
 
1. Create the master image.  The master image for virtual machines can be created through XenCenter, which you installed in [Step 4. Access and Configure the Citrix Hypervisor](#access-citrix-hypervisor). 

2.  Install XenServer Tools on the guest operating system of the virtual machine created. For more information on XenServer, see [Installing XenServer Tools](https://docs.citrix.com/en-us/xencenter/7-1/vms-installtools.html)
  
3. Join the virtual machine master image to the Active Directory server domain by using these [instructions](<https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain>).

4. Add the proxy server settings on your master image. 
   1. The settings can be copied from the Cloud Connector server to the Master VM Image. Follow the instructions to set up [proxy settings](https://docs.microsoft.com/en-us/troubleshoot/browsers/use-proxy-servers-with-ie). 

   2. Using the instructions in [proxy settings](https://docs.microsoft.com/en-us/troubleshoot/browsers/use-proxy-servers-with-ie), paste the copied settings to your Master VM image.  

5. While connected to the Master VM Image, enter this command on an elevated command prompt of the Master VM Image:

      ``netsh winhttp import proxy source=ie``

6. Install the VDA on the virtual machine. For more information, see [Install VDAs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/install-vdas.html#step-1-download-the-p[…]and-launch-the-wizard).

7. (Optional) To give access to all other non-administrator users, do one of the following methods:

      * Use Microsoft Group Policy settings
      * Update the windows registry on the master image and set **Proxysettingsperuser** to ***0***.
        1. Press the `Win+R` keys to optn the Run window. 
        2. Enter **regedit** and **Enter** to open the Registry Editor. 
        3. In registry Editor, navigate to the following registry path:
        HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\CurrentVersion\Internet Settings
        4. Double-click on the **ProxySettingsPerUser** registry.
        5. Change the Value to **0**.

Individual virtual machine performance varies depending on workload. However, to take advantage of optimizations that might result in improved performance, you can use the [Citrix Optimizer](https://support.citrix.com/article/CTX224676){: external} and this [tool for Windows 10](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-optimization-tool-now-available/m-p/1558614){: external}.
{: tip} 

## Next steps
{: #next-steps-post-provisioning}

You can now complete your management tasks in the [Citrix Cloud Portal](http://citrix.cloud.com){: external}.
