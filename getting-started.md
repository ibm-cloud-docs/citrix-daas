---

copyright:
  years: 2020, 2021, 2022
lastupdated: "2022-07-06"

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
{:beta: .beta}
{:table: .aria-labeledby="caption"}

# Getting started with {{site.data.keyword.cvad_full_notm}} 
{: #getting-started-tutorial}

Citrix Virtual Apps and Desktops on IBM Cloud;reg; is now {{site.data.keyword.cvad_full}}. If you are using bookmarks to access the documentation, you should update them to the new product name path.
{: note}

Use {{site.data.keyword.cvad_full}} to create a dedicated {{site.data.keyword.cvad_short}} service environment at any of our global data centers. You have the option of creating a Classic solution, VMWare solution, or VPC solution. 
{: shortdesc}

## Before you begin
{: #before-you-begin-getting-started}

Before you begin, review the following information.

* Want to learn more about {{site.data.keyword.cvad_short}} and how it works? To learn more, see [About {{site.data.keyword.cvad_full_notm}}](/docs/citrix-daas?topic=citrix-daas-about-citrix-virtual-apps-and-desktops). 
* Review the supported products and versions in the [Product compatibility guide](/docs/citrix-daas?topic=citrix-daas-product-compatibility-guide).

You must have either an active trial or paid subscription to Citrix Cloud. To learn more or to create an account, see [Citrix Cloud](https://onboarding.cloud.com/){: external}.
{: important}

After you review this information, you are ready to get started. To begin, you need to complete the following prerequisites.
1. Sign up for an {{site.data.keyword.cloud_notm}} account
2. Create required API keys
3. Verify Citrix and operating system entitlements
4. Set up network connectivity for logging on to {{site.data.keyword.cloud_notm}}
5. (VMWare only) Enable VLAN spanning or VRF on your account
6. Set up user permissions in IAM

## Step 1. Sign up for an {{site.data.keyword.cloud_notm}} account
{: #sign-up-IBM-Cloud-account}

To order and use {{site.data.keyword.cloud_notm}} services, you need to sign up for an {{site.data.keyword.cloud_notm}} account. Billing information is associated with your {{site.data.keyword.cloud_notm}} account and the cost of the physical and virtual infrastructure and the resulting licenses are charged to your account. For more information about signing up, see [Setting up your {{site.data.keyword.cloud_notm}} account](/docs/account?topic=account-account-getting-started#signup).

## Step 2. Create {{site.data.keyword.cloud_notm}} API keys
{: #create-api-keys}

For Classic and VMWare solutions, you need to add a classic infrastructure API key to your {{site.data.keyword.cloud_notm}} account. For more information about creating or managing API keys, see [Managing classic infrastructure API keys](/docs/account?topic=account-classic_keys).

For VPC solutions, you need to add an {{site.data.keyword.cloud_notm}} API key to your {{site.data.keyword.cloud_notm}} account. For more information about creating or managing API keys, see [Managing user API keys](/docs/account?topic=account-userapikey&interface=ui).

## Step 3. Verify Citrix and operating system entitlements
{: #verify-Citrix-OS-entitlements}

Verify that you have the {{site.data.keyword.cvad_short}} service license available for the wanted environment size under the _Licensing_ section of [Citrix Cloud](http://citrix.cloud.com){: external}. During the provisioning process in the {{site.data.keyword.cloud_notm}} catalog, {{site.data.keyword.cloud_notm}} captures your Citrix account credentials and validates with Citrix Cloud before it completes the provisioning process. You also need to verify that you have server or desktop licenses, along with any additional third-party licenses, depending on the applications that you intend to use.

## Step 4. Set up network connectivity for logging on to {{site.data.keyword.cloud_notm}}
{: #set-up-network-connectivity}

All of the resources that are provisioned through this solution are in a private network space with security groups attached wherever needed. The IP addresses are internal to IBM, so you need to use a separate VPN account for logging on to hypervisors and creating the virtual machine images. For more information about enabling VPN access and setting the password, see [Getting started with {{site.data.keyword.cloud_notm}} Virtual Private Networking](/docs/iaas-vpn?topic=iaas-vpn-getting-started). After you enable your VPN access and set the password, download the [VPN client](/docs/iaas-vpn?topic=iaas-vpn-standalone-vpn-clients). You then have access to the private network where your resources are provisioned.

## Step 5. Enable VLAN spanning or VRF on your {{site.data.keyword.cloud_notm}} account (Classic and VMWare only)
{: #enable-vlan-spanning}

To enable VLAN spanning for your classic and VMWare solutions, complete the following steps:
1. Log in to the {{site.data.keyword.cloud_notm}} console by using your unique credentials. 
2. Select **Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > Classic Infrastructure > Network > IP Management > VLANs**. 
3. Click **VLAN Spanning** and select _Enable_. 

To enable VRF, see [Enabling VRF](/docs/account?topic=account-vrf-service-endpoint#vrf).

## Step 6. Set up user permissions in IAM 
{: #set-up-user-permissions}

### Classic
{: #set-up-user-permissions-classic}

To provision {{site.data.keyword.cvad_full_notm}}, classic infrastructure users need these permissions: 

| Category | Permission |
| -------- | ---------- |
| Account | - Add/Upgrade Storage (StorageLayer)  \n - Add Server   \n - Cancel Server   \n - Add/Upgrade Services   \n - Cancel Services   \n - Add/Upgrade Cloud Instances   \n - View Event Log |
| Devices | Select all |
| Network | - Add IP addresses   \n - Add Compute with Public Network Port|
| Services |- View licenses   \n - Storage Manage |
{: caption="Table 1. Required user permissions to provision Classic and VMwWre" caption-side="top"}

To access and update the classic infrastructure permissions, complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, go to **Manage > Access (IAM)** and select **Users**. 
2. Select the user's name that you want to update access for, and click **Classic infrastructure**. 
3. If the user is a _Basic User_, you need to add the permission sets as shown in Table 1. 

### VMware solutions
{: #set-up-user-permissions-vmware}

To access and update the VMware solutions permissions, complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, go to **Manage > Access (IAM)** and select **Users**. 
2. Identify the user's name that you want to update access for, select the Actions menu, and click **Assign Access**. 
3. Select **Access Policy**.
4. From **Services**, select **VMware Solutions**.
5. Select **All Resources**.
6. Select the permission levels that you want to assign.
7. Select **Add**.
8. Select **Assign**.
9. Retrieve your API credentials:
    1. Select **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Settings**.
    2. Select **Retrieve Credentials**.
    3. Select **Save Credentials**.

For more information, see [Managing classic infrastructure access](/docs/account?topic=account-mngclassicinfra).

### VPC
{: #set-up-user-permissions-vpc}

To access and update VPC solution permissions:

You must create the proper Schematics access policies for a user to be able to use the WES-UI or {{site.data.keyword.cloud_notm}} Schematics to deploy {{site.data.keyword.cvad_short}} on {{site.data.keyword.cloud_notm}} VPC. See [Managing user access](https://cloud.ibm.com/docs/schematics?topic=schematics-access).

| Service   | Resource Type | Permission |
| ----------|-------- | ---------- |
| VPC Infrastructure Services  | Virtual Private Cloud | - Operator to use an existing VPC  \n - Admin or Editor to create new VPCs  |
| |Floating IP for VPC | - Operator to attach Floating IP  \n - Admin or Editor to create a Floating IP |
| |Subnet | - either Admin or Editor |
| |Block Storage for VPC | - either Admin or Editor |
| |Block Storage Snapshots for VPC | - either Admin or Editor |
| |Security Group for VPC | - either Admin or Editor |
| |SSH Key for VPC | - Operator to select existing keys  \n - either Admin or Editor to create new keys |
| |Image Service for VPC | - Operator |
| |Security Group for VPC | - either Admin or Editor |
| |Public Gateway  | - either Admin or Editor  |
| |Virtual Server for VPC  | - either Admin or Editor  |
| |Dedicated Host for VPC | - either Admin or Editor  |
| Functions | All Resources  | - Admin and Manager  |
| Cloud Object Storage | All Resources  | -  Admin and Manager  |
| Databases for Redis  | All Resources   | -  Editor |
{: caption="Table 2. Required user permissions to provision VPC" caption-side="top"}

1. In the {{site.data.keyword.cloud_notm}} console, go to **Manage > Access (IAM)** and select **Users**. 
2. Identify the user's name that you want to update access for, select the Actions menu, and click **Assign Access**. 
3. Select **Access Policy**.
4. From **Services**, select the service to which you are granting access, for example VPC Infrastructure Service,  and select **Next**.
5. For each resource type, 
    1.  Select **Specific resouces** in the Resources section and specify:
    2.  _Resource Type_ for **Attribute type**.
    3.  The resource type value, for example Floating IP, for **Value**. If you are prompted for a condition, you can leave it blank.  
    4.  Select **Next**.  
    5.  Assign Roles and action for each of the Resource Types shown in Table 2.
    6.  Select **Add**.
    7.  Select **Assign**.

## Next steps
{: #getting-started-next-steps}

After you reviewed and completed these prerequisites, you are ready to provision {{site.data.keyword.cvad_full_notm}}. For more information, see:

*  [Provisioning {{site.data.keyword.cvad_full_notm}}](/docs/citrix-daas?topic=citrix-daas-provisioning-citrix-daas-classic) for Classic and VMWare solutions

* [Provisioning {{site.data.keyword.cvad_full_notm}} on Virtual Private Cloud](/docs/citrix-daas?topic=citrix-daas-provisioning-citrix-daas-vpc)

