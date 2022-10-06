---

copyright:
  years: 2020, 2021
lastupdated: "2021-08-24"

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

# Provisioning {{site.data.keyword.cvad_full_notm}} Classic infrastructure
{: #provisioning-cvad-classic}

## What is provisioned
{: #what-is-provisioned-classic}

When you provision {{site.data.keyword.cvad_full}}, you receive the following components:
* Hypervisors - You get to choose the configuration and quantity of bare metal servers during the ordering process.
* 3 x Cloud Connectors on public virtual server instances with Windows 2019 OS.
* 1 x Active Directory server on a public virtual server instance with Windows 2016 OS.
* 1 x DHCP server on a public virtual server instance with CentOS to dynamically allocate IPs to the virtual machines.
* 1 x proxy server on a public virtual server instance with CentOS to allow access to the internet for both Cloud Connectors and desktops.
* File Storage with the capacity that you choose during the ordering process.
* Primary and portable subnets on a VLAN. 

All of the servers and virtual desktops are provisioned in IBM's private network.
{: note}

If you ordered {{site.data.keyword.cvad_short}} with Citrix Hypervisors, you receive:

*	Citrix Hypervisors - You get to choose the configuration and quantity of bare metal servers during the ordering process.
*	File Storage with the capacity that you choose during the ordering process.

If you ordered {{site.data.keyword.cvad_short}} with VMware ESXi Hosts, you receive:

*	VMware ESXi hosts -  You need to choose the configuration and quantity of bare metal servers during the ordering process. The VMware cluster is configured with these ESXi hosts.
*	Fully configured vCenter server with NSX-V. You can choose to purchase the [licenses](/docs/citrix-daas?topic=citrix-daas-provisioning-cvad#licensing-vmware) either from IBM or use your own license keys.
*	Fully configured Active Directory server in the VMware cluster configured to enable deployments. No post-provisioning is required for this Active Directory server. This Active Directory server is in addition to the Active Directory servers used by {{site.data.keyword.cvad_full_notm}}. For more information, see [Active Directory Domain Services introduction](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-adds-intro). 
*	File Storage with the capacity that you choose during the ordering process.
* vSAN storage based on the capacity of the disks you chose during the ordering process.


## Before you begin
{: #before-you-begin-provisioning-cvad}

You can provision {{site.data.keyword.cvad_full}} through the {{site.data.keyword.cloud_notm}} catalog and then manage the workloads through the Citrix Cloud management console.
{: shortdesc}

Before you begin, review the following prerequisites.
1. Review and complete the prerequisites found in [Getting started with {{site.data.keyword.cvad_full_notm}}](/docs/citrix-daas?topic=citrix-daas-getting-started-tutorial).
2. You must have either an active trial or paid subscription to Citrix Cloud. To learn more or to create an account, see [Citrix Cloud](https://onboarding.cloud.com/){: external}.
3. You must have a Citrix API client and password pair. Download the client ID and Secret. For more information about creating the key pair, see [Create an API client](https://developer.cloud.com/getting-started){: external}
4. Log in to the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog){: external} by using your unique credentials. 
5. Create a [Classic Infrastructure API key](https://cloud.ibm.com/iam/apikeys) in the IAM dashboard before you create an order. Retrieve and verify your credentials before proceeding. 
6. In the **Compute** section, locate the **Citrix Digital Workspace Solutions** section and select **{{site.data.keyword.cvad_full_notm}}**.

To provision {{site.data.keyword.cvad_full_notm}}, you need to enter the following information.

### Citrix connectivity
{: #citrix-connectivity}

After you provide your Citrix credentials, make sure to select **Validate** to verify your connectivity.
{: note}

| Field     | Details     |
| --------- | ----------- |
| Citrix customer ID | This ID is your Citrix customer ID. For more information about locating your ID, see this Citrix [Getting started](https://developer.cloud.com/getting-started/docs/overview#customer-id){: external} information. If you need to create a new account ID, see [How to Create a New Citrix Account ID/Org ID](https://support.citrix.com/article/CTX106671){: external}. |
| Citrix API client ID | Enter your Citrix API client ID information. For more information about creating the key pair, see [Create an API client](https://developer.cloud.com/getting-started){: external} from the Citrix Developer site. |
| Citrix API client secret | Enter your Citrix API client secret key. |
| Resource location | This Citrix resource location must be in the same geographic location as your selected {{site.data.keyword.cloud_notm}} data center. You can either create a resource location or use an existing one. The **Use existing** resource location option is only available after your account is validated. If you create a resource location, you must follow [Citrix naming restrictions](https://docs.citrix.com/en-us/citrix-cloud/citrix-cloud-resource-locations/resource-locations.html#naming-restrictions){: external}. When you create a resource location, use the [resource location limits](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/limits.html#resource-location-limits){: external}. |
| Hostname for Cloud Connector | These fields are optional for naming the Cloud Connector virtual machines. You can edit the names or keep the pre-populated defaults. If you edit the names, you must follow [Microsoft naming conventions](https://support.microsoft.com/en-us/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and){: external}. |
{: caption="Table 1. Citrix connectivity details" caption-side="top"}


Select **Validate** to validate your credentials.  You must have valid credentials to create an order.

### Active Directory
{: #active-directory}

| Field     | Details     |
| --------- | ----------- |
|Active Directory Topology |How your Active Directory is installed. You have three options:  \n **IBM Cloud**  \n Deploy a new Active Directory controller on {{site.data.keyword.cloud_notm}}.  \n **Extended (Multisite)**  \n You use an existing Active Directory and deploy a Domain Controller on {{site.data.keyword.cloud_notm}}.  \n **Extended (Site-to-Site)**  \n You use your existing Active Directory and Domain Controller installed on-premises for this {{site.data.keyword.cvad_full_notm}} for IBM Cloud application. |
|Cloud Connector and Active Directory domain name | The domain name is applied to both the Cloud Connectors and the Active Directory. You must follow [Microsoft naming conventions](https://support.microsoft.com/en-us/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and){: external}. |
|Active Directory safe mode password |The password must meet the following password complexity requirements:  \n Cannot contain the user's account name or parts of the user's full name that exceed two consecutive characters.  \n  Must be at least 7 characters in length.  \n  Must contain characters from three of the following four categories:  \n - English uppercase characters (A through Z)  /n - English lowercase characters (a through z)  \n - Base 10 digits (0 - 9)  \n - Non-alphabetic characters (for example, !, $, #, %) |
{: caption="Table 2. Active Directory details" caption-side="top"}

### Workload requirements
{: #workload-requirements}

Your bare metal server hardware and configuration recommendations are based on your workload requirements.   

| Field     | Details     |
| --------- | ----------- |
| Hosted-Shared Apps and Desktops | Deliver an application or desktop experience from multi-session OS machines to multiple, simultaneously connected users. |
| Server Virtual Desktop Infrastructure | Deliver a desktop experience from a server OS for a single user. |
| Desktop Virtual Desktop Infrastructure | Deliver a pooled or dedicated desktop experience from single-session OS machines. |
{: caption="Table 3. Workload requirements options" caption-side="top"}

When you select your workload requirements, you need to select how many of each type of user who needs to be supported. For more information about the user types, see the following table.

| Field    | Details    |
| -------- | ---------- |
| Task Worker | Uses a light workload and runs fewer applications that are not resource-intensive. Starts and stops applications less frequently. |
| Knowledge Worker | Uses multiple applications in well-balanced intensive workloads. |
| Power User | Uses resource-intensive applications, such as graphics or software development applications. |
{: caption="Table 4. Workload user types" caption-side="top"}

### Bare metal server configuration
{: #server-configuration}

All of your bare metal servers are configured identically based on your selections.
{: note}

| Field     | Details     |
| --------- | ----------- |
| Hostname root and domain | Your hostname and domain are saved in the server operating system and are used to identify servers. |
| Location  | The data center location must match the geographical area of your Citrix resource location. |
| Image | An image is the deployed operating system for your servers. |
| Profile | Consider selecting from the listed server profiles, which are based on provided workload sizings by using Citrix best practices. You can select the **All servers** tab to choose from more supported server profiles. |
{: caption="Table 5. Server configuration details" caption-side="top"}


### File storage
{: #file-storage}

NFS file storage options:

| Field     | Details     |
| --------- | ----------- |
| Size      | You can enter a file storage size 20 - 12,000 GB. The minimum recommended size is 100 GB.|
| Snapshot space | Select how much snapshot space you want for your file storage volume. For more information about snapshots, see [Snapshots](/docs/FileStorage?topic=FileStorage-snapshots). |
| IOPS tier | By default, the IOPS tier is 4 IOPS/GB, which is designed for higher-intensity workloads. These workloads are typically characterized by having a high percentage of data active at any time. |
{: caption="Table 6. NFS File storage details" caption-side="top"}

vSAN storage for VMware options:

| Field     | Details     |
| --------- | ----------- |
| Disk type and size for vSAN capacity disks      | You select an option for the capacity disks that you need.|
| Number of vSAN capacity disks | You enter the number of capacity disks that you want to add.|
|vSAN license|You can order a vSAN license or provide your own. If you provide your own license, you must enter your license key.|
{: caption="Table 7. vSAN File storage details" caption-side="top"}

After you select the disk type and number of disks, a summary of your vSAN configuration is shown.
![vSAN Summary Example.](images/vsan.summary.png){: caption="Figure 1. vSAN Summary Example" caption-side="bottom"}

### Licensing
{: #licensing-vmware}

Licensing applies to VMware configurations only. 

|Field   |Details   |
|--------|----------|
|vCenter Server License - Standard|You can order a Standard vCenter license or provide your own. If you provide your own license, you must enter your license key.|
|vSphere Server license - Enterprise Plus|You can order an Enterprise Plus vSphere Server license or provide your own. If you provide your own license, you must enter your license key.|
|VMWare license - NSX|You can order a VMWare NSX license or provide your own. You can order a Base, Advanced, or Enterprise license. If you provide your own license, you must enter your license key.|
|vSAN license |You can order a vSAN license or provide your own. You can order an Advanced or Enterprise license. If you provide your own license, you must enter your license key.|
{: caption="Table 8. VMWare Licensing details" caption-side="top"}

## Next steps
{: #next-steps-provisioning-citrix-daas}

When the components are provisioned, you receive an email with information about the components ordered.

After the {{site.data.keyword.cvad_full_notm}} components are provisioned, you need to complete a few post-provisioning steps to get you up and running. For more information, see [Post-provisioning steps for Citrix Hypervisor](/docs/citrix-daas?topic=citrix-daas-post-provisioning-cvad).
