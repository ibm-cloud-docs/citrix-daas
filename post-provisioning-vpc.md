---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-29"

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
{:beta: .beta}
{:table: .aria-labeledby="caption"}

# Post-provisioning steps for {{site.data.keyword.cvad_full_notm}} on Virtual Private Cloud
{: #post-provisioning-cvad-vpc}

After you provision your {{site.data.keyword.cvad_full}} ({{site.data.keyword.cvad_short}}) with the Virtual Private Cloud (VPC), complete the following tasks:

1.	Verify the status of provisioned servers and view network details.
2.  Establish network connectivity for Active Directory topologies (Extended only)
3.	Access the Active Directory, Cloud Connectors. (Extended only)
4.	Create a master image of virtual machine.
5.  Customize {{site.data.keyword.la_full}} (Optional)

## Step 1: Verify the status of provisioned servers and view network details (Optional)
{: #verify-vpc-status-server-network}

{{site.data.keyword.cvad_short}} provisions resources by using Terraform and Schematics. You can view the details of the IBM Cloud Schematics deployments and the IBM Cloud resources that you currently manage with IBM Cloud Schematics. For more information, see 
[Managing IBM Cloud resources with Schematics - Reviewing resource and deployment details](/docs/schematics?topic=schematics-manage-lifecycle#review-logs).

Log in to citrix.cloud.com and verify that your Active Directory is listed under Domains and that your resource location is Green.


## Step 2. Establish network connectivity for Active Directory Topology
{: #ad-topology-network-connectivity-vmware}

### IBM Cloud 
{: #ad-topology-network-connectivity-cloud-vmware}

If you chose **IBM Cloud** Active Directory topology and are using the Active Directory in {{site.data.keyword.cloud_notm}} as your only domain controller, no further network configuration is required. The Cloud Connectors are joined to the Active Directory server domain and are configured to communicate with Citrix Cloud. You might have to create users with the necessary privileges. You can also create bulk users in Active Directory. For more information, see this step-by-step guide on how to [Create bulk users in Active Directory](https://activedirectorypro.com/create-bulk-users-active-directory/){: external}.

### Extended
{: #ad-topology-network-connectivity-hybrid-vpc}

1. With an Extended setup, you must establish network connectivity between {{site.data.keyword.cloud_notm}} and your on-premises location. 

   You can set up network connectivity by using any of the gateway appliances available in {{site.data.keyword.cloud_notm}}. For more information, see the **About** section in the [Gateway Appliance {{site.data.keyword.cloud_notm}} catalog page](https://cloud.ibm.com/gen1/infrastructure/provision/gateway){: external}. 
   
   You can provision a VPN to access the Active Directory server and Citrix Cloud Connectors. For more information, see [About site-to-site VPN gateways](/docs/vpc?topic=vpc-using-vpn). Configure both the appliances and establish network connectivity between the two sites.

   When you order a gateway appliance from {{site.data.keyword.cloud_notm}}, the data center and backend VLAN must correspond to the same data center and VLAN where your other services are provisioned. See [View network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details).
   {: important}

2. Install the domain controller in {{site.data.keyword.cloud_notm}}  

    A Windows virtual server instance is provisioned in the same VLAN and network as your other servers. Complete the following steps to install the domain controller and join the secondary controller:

    Install the Active Directory Domain Services (AD DS) domain controller on this Windows server and join the secondary controller with your on-premises domain. 

    Domain join your Cloud Connectors, and then run the Cloud Connector registration scripts to add the connectors to the resource location.

    The Cloud Connector PowerShell scripts are available in `C:\postscript`.

3.  Run the configuration scripts on each of the Cloud Connector virtual server instances:
    
    1. Make sure your Connector and Active Directory can talk to each other. Run the `ping` command if `imp` is not blocked.
    2. Use PowerShell to go to the directory `C:\postscript`.
    3. Verify that the **ManualRegisterConnector.ps1** command is in the directory. 
    4. Run the **ManualRegisterConnector.ps1** command: 

       If you are using the APIClientID and APIClientSecret that you used to create the order, enter:

       `./ManualRegisterConnector.ps1 -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}`

       Replace the contents of the {} with the values for your system. 

       If you need to use a different APIClientID and APIClientSecret enter:
       
       `./ManualRegisterConnector.ps1 -APIClientID {APIClientID} -APIClientSecret {APIClientSecret} -ADDomainName {ADDomainName} -ADServerName {ADServerName} -ActiveDirectoryUserName {ActiveDirectoryUserName} -ActiveDirectoryPassword {ActiveDirectoryPassword} -PreferredDnsServer {PreferredDnsServer}`

       Replace the contents of the {} with the values for your system. 

## Step 3: Access the Active Directory, Cloud Connectors (Optional)
{: #access-ad-connectors-vpc}

You can access the Active Directory and Cloud Connectors through a Remote Desktop session. VSI naming conventions are:

*  Active Directory: cvad-ad (ex. cvad-vjbxd-ad)
*  Cloud Connector: cc- (ex. cc-vjbxd-1, cc-vjbxd-2)
*  Custom Image: cstm-img- (ex. cstm-img-vjbxd)

For VSI, make sure that security group that is associated with the instance allows inbound and outbound Remote Desktop Protocol traffic (TCP port 3389). You can remove this rule when you are finished accessing the VSI. 

You must create and assign a floating IP to the instance to connect on the public network. For more information, see [About networking](/docs/vpc?topic=vpc-about-networking-for-vpc).

For more information about connecting to instances, see [Connecting to Windows instances](/docs/vpc?topic=vpc-vsi_is_connecting_windows).


## Step 4: Create master image of virtual machine
{: #create-master-image-vmware}

If you do not have an existing master image, you can create a master image now. 

1.  Upload your SSH key from your local machine to the {{site.data.keyword.cloud_notm}}.
2.  Using the UI or Terraform with schematics order your VPC and select add a Custom Image Virtual Machine. Select your SSH key.
3.  Add a floating IP to your Custom Image Virtual Machine.
4.  After that is provisioned, decrypt your Custom Image Virtual Machine password.
5.  Using Remote Desktop login to your Custom Image Virtual Machine.
6.  Run Windows update (you might have to run this several times).
7.  Install the [Citrix VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure/install-vdas.html).
    *  In Step 4, choose **Create a master MCS image**.
    *  In Step 8, choose **Let Machine Creation Services do it automatically**.
8.  Run Windows update again.
9.  Log in to [Citrix Cloud Portal](http://citrix.cloud.com).
10.  Create a hosting connection for your resource location
11.  Select the **Create a Machine Catalog**. Make sure you select the proper Active Directory. You need your Active Directory password that was decrypted earlier. An example of a username is: youraddomain\Administrator. Select the Custom Image Virtual Machine boot volume that you provisioned under volumes and the resource group for your VPC.

## Step 5: Customizing {{site.data.keyword.la_full_notm}} (Optional)
{: #cvad-post-prov-vpc-logging}

You can define alerts and design custom views to monitor application and system logs  with{{site.data.keyword.la_full_notm}}. For more information, see [Getting Started](/docs/log-analysis?topic=log-analysis-getting-started#getting-started).

If you want to gather windows event logs, you must configure NXLog and deploy a syslog port from {{site.data.keyword.la_full_notm}}. For more information, see [Configuring NXLog](/docs/log-analysis?topic=log-analysis-windows_serv#windows_server-include-nxlog_config).

If you selected **Enable Platform** to view platform messages, you use {{site.data.keyword.la_full_notm}} to configure a logging instance in a region to monitor these logs. Platform logs can be enabled for only one logging instance per region. For more information, see [Configuring IBM Cloud platform logs](/docs/log-analysis?topic=log-analysis-config_svc_logs).

If you selected br-sao, ca-tor, or jp-oas as your deployment region you must also configure a logging instance in a nearby region to receive volume worker manager logs:

*   For br-sao and ca-tor, us-east is used.
*   For jp-osa, jp-tok is used.

For more information, see (Configuring IBM Cloud platform logs([/docs/log-analysis?topic=log-analysis-config_svc_logs]).

## Next steps
{: #cvad-post-prov-vpc-next}

After you complete these post-provisioning steps, you can:

*  Complete your management tasks in the [Citrix Cloud Portal](http://citrix.cloud.com){: external}.
