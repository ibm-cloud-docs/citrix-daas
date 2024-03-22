---

copyright:
  years: 2020, 2024
lastupdated: "2024-03-22"

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
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}

# Provisioning {{site.data.keyword.cvad_full_notm}} on Virtual Private Cloud
{: #provisioning-citrix-daas-vpc}

{{site.data.keyword.cvad_full}} is deprecated. As of 18 April 2024, you can't create new instances, and access to existing instances will be removed. Existing premium plan instances are supported until 18 April 2024. Any instances that still exist on that date will be deleted. 
{: deprecated}

Use this task to provision your {{site.data.keyword.cvad_full}} solution on {{site.data.keyword.IBM}} Virtual Private Cloud. This task corresponds to the provisioning form that you see when you select the **VPC** tile from the {{site.data.keyword.cvad_full_notm}} page. Provisioning uses Schematics to provision your resources. For more information about Schematics, see [Schematics, {{site.data.keyword.cloud_notm}}’s deployment manager](https://cloud.ibm.com/schematics/overview).

## What is provisioned
{: #what-is-provisioned-vpc}

When you provision {{site.data.keyword.cvad_full_notm}} on VPC, you receive the following components in each zone:

|Size    |Lite     |Standard     | Enterprise     |
|-----|---------|-------------|---------------|
|VPC |1    |1    |1   |
|Subnet (256 IP addresses)  |1 x /24     |1 x /24     |1 x /24 |
|Public Gateway |1    |1    |1   |
|Floating IP |1    |1    |1   |
|Active Directory VSI |1    |1    |1    |
|Cloud Connector VSIs  |1 [^lite]   |2    |3   |
|Master Image VSI [^masterimage]  |1 |1   |1   |
|VDA VSI   |1 |1   |1   |
|Custom Image VSI (optional)  |1 |1   |1   |
|Security Groups  \n - Active Directory Security Group  \n - Cloud Connector Security Group  \n - VDAs Security Group  \n - Master Image Security Group (VSI deployed during Citrix Machine Catalog creation)  \n -  Custom Image Security Group (optional)|4 (5 if Custom Image VSI deployed)   |4 (5 if Custom Image VSI deployed) |4 (5 if Custom Image VSI deployed) |
|Volume Worker | n/a | n/a | \n - 1 IBM Cloud Databases for Redis with 2G RAM 4G of storage  \n - {{site.data.keyword.cloud_notm}} Functions instance   \n - 1 Cloud Object Storage bucket|

Lite is for testing and proof of concept, not production. 

[^lite]:Lite is 2 vCPU. 
[^masterimage]:Master image VSI is temporary and is used to create the machine catalog. The master image VSI is automatically created and reclaimed by {{site.data.keyword.cvad_full_notm}}. 

{{site.data.keyword.cvad_full_notm}} solutions on VPC do not require extra configuration for monitoring, storage, networking, or high availability.

All of the servers and virtual desktops are provisioned in the {{site.data.keyword.IBM}} private network.
{: note}

More technical information is available in the [readme file](https://github.com/IBM-Cloud/citrix-virtual-apps-and-desktops#readme). 

## Before you begin
{: #before-you-begin-provisioning-citrix-daas-vpc}

You can provision {{site.data.keyword.cvad_full_notm}} through the {{site.data.keyword.cloud_notm}} catalog and then manage the workloads through the Citrix Cloud Management console.
{: shortdesc}

Before you begin, review the following prerequisites.
1. Review and complete the prerequisites found in [Getting started with {{site.data.keyword.cvad_full_notm}}](/docs/citrix-daas?topic=citrix-daas-getting-started-tutorial).
2. You must have either an active trial or paid subscription to Citrix Cloud. To learn more or to create an account, see [Citrix Cloud](https://onboarding.cloud.com/){: external}.
3. You must have a Citrix API client and password pair. Download the client ID and Secret. For more information about creating the key pair, see [Create an API client](https://developer.cloud.com/getting-started){: external}
4. Log in to the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog){: external} by using your unique credentials. 
    1. Create an {{site.data.keyword.cloud_notm}} [API key](https://cloud.ibm.com/iam/apikeys) in the IAM dashboard before you create an order. Retrieve and verify your credentials before proceeding. 
    2. In the **Compute** section, locate the **Citrix Digital Workspace Solutions** section and select **{{site.data.keyword.cvad_short}}**.
5. (Optional) If you are using customer managed encryption, either Key Protect or Hyper Protect Crypto Services, set up the required encryption keys before you begin provisioning:
    *  Encryption service instance
    *  Key name
    *  Key ID

    For more information on encryption key set up, see the [Key Protect Getting started tutorial](/docs/key-protect?topic=key-protect-getting-started-tutorial) or [Getting started with IBM Cloud Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-get-started).

To provision {{site.data.keyword.cvad_full_notm}}, you enter the following information.

## Step 1 - Citrix connectivity
{: #citrix-connectivity-vpc}

1.  Enter your Citrix credentials.
    *  Citrix customer ID - This ID is your Citrix customer ID. For more information about locating your ID, see this Citrix [Getting started](https://developer.cloud.com/getting-started/docs/overview#customer-id){: external} information. If you need to create a new account ID, see [How to Create a New Citrix Account ID/Org ID](https://support.citrix.com/article/CTX106671){: external}. 
    *  Citrix API client ID - Enter your Citrix API client ID information. For more information about creating the key pair, see [Create an API client](https://developer.cloud.com/getting-started){: external} from the Citrix Developer site. 
    *  Citrix API client secret - Enter your Citrix API client secret key. 

2.  Select **Validate** to validate your credentials. You must have valid credentials to create an order.

3.  Enter the resource location for each zone. The resource location is the name that is given for the location of resources. It is not an identifier for {{site.data.keyword.cloud_notm}} data centers.

## Step 2 - Active Directory topology 
{: #active-directory-vpc}

1.  Select your Active Directory topology. You have two options: 

    * **{{site.data.keyword.cloud_notm}}** - Deploys a new Active Directory controller on {{site.data.keyword.cloud_notm}}.
    * **Extended (multisite)** - You use an existing Active Directory and deploy a Domain Controller on {{site.data.keyword.cloud_notm}}.

2.  Enter an Active Directory domain name.
3.  (Dedicated host only) Enter an Active Directory safe mode password.

## Step 3 - {{site.data.keyword.cloud_notm}} account information
{: #ibm-acct-info-vpc}

Enter your specific {{site.data.keyword.cloud_notm}} account information.

*   Resource Group - The {{site.data.keyword.cloud_notm}} resource group for your account. You can select an existing resource group, you cannot create a new one.
*   API key - Your API key.

## Step 4 - Infrastructure location and sizing configuration
{: #server-configuration-vpc}

All of your virtual server instances are configured identically based on your selections. Specify the:

*   Location  - The region and zones for this deployment.
*   Size of your control plane infrastructure, either Lite, Standard, or Enterprise with Volume Worker. Lite is designed for testing and proof of concept. Enterprise includes Volume Worker, which is used for distributed identity disk creation with large machine catalogs.

## Step 5 - Dedicated Host Specifics
{: #specifics-vpc-dedhost}

### Deployment Strategy
{: #deploymenstrategy-vpc-dedhost}

Select the Dedicated Host deployment strategy for your resources. When you deploy a dedicated host, all resources use the same family as the dedicated host profile family. You have two options:

*   Public - Provisions the control plane components (active directory, cloud connectors, and custom image VSI) onto public hosts and the VDAs to the dedicated hosts ordered with {{site.data.keyword.cvad_short}}.
*   Dedicated - Provisions the Control Plane components on their own dedicated hosts.
*   Shared Dedicated Control Plane - Provisions the control plane and VDAs to the dedicated host.

If 2 dedicated hosts are ordered for one host group in one zone, the default scheduling algorithm for VPC is used. The algorithm spreads the VSIs across the dedicated hosts in the group. The placement is random and you have no control on which hosts the VDAs or control plane are provisioned.
{: note}

### Dedicated Host profiles
{: #profiles-vpc-dedhost}

When you provision virtual server instances and dedicated hosts, the vCPU associated with these resources counts toward the vCPU quotas per region. For more information, see [Quotas and service limits](/docs/vpc?topic=vpc-quotas#vsi-quotas). If you reach the quota, contact your sales team.

1.  Select the number of Dedicated Hosts to deploy per zone. When you use the Dedicated hosts option, use at least 2 dedicated hosts to avoid single point of failure. 
2.  Select a profile for the Dedicated Hosts. If you are deploying a shared dedicated control plane, the ratio of memory to cpu is locked for all control plane virtual server instances based on the dedicated host profile that you select.

## Step 6 - Images, profiles, and identity volume encryption for Machine Catalog
{: #images-profiles-mach-catalog}

1.  Indicate whether you want to use a custom image to build the base image for your machine catalog.
    *  Select the version for the custom image. For Windows images, you can select provider images or custom OS images.
    *  Select the virtual server profile for the custom image to provision your virtual server instances in post provisioning. [^sizing]
2.  Select your SSH key or create a new one.
3.  Specify the size for your boot volume. The default is 100GB. If you require a larger boot volume size, you can specify 100-250GB inclusive. If you specify greater than 100GB, you must make the additional disk space accessible in post-provisioning. For more information, see [Step 6: Make additional boot volume space accessible](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas-vpc#citrix-daas-post-prov-vpc-boot-vol) in the post-provisioning steps for {{site.data.keyword.cvad_short}} on Virtual Private Cloud.
4.  Select how you want your identity volume encryption managed.  The default is provider managed.  For customer managed encryption, select *Key Protect* or *Hyper Protect Crypto Services*. You must also provide:

    *  Encryption service instance
    *  Key name

    For more information on encryption key set up, see the [Key Protect Getting started tutorial](/docs/key-protect?topic=key-protect-getting-started-tutorial) or [Getting started with IBM Cloud Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-get-started).

    Customer managed encryption for custom image instances is not currently implemented in the {{site.data.keyword.cvad_short}} order form. To use customer managed encryption for a custom image instance boot volume, you must first create a custom image with encryption, then select your custom image in the custom image instance section in the {{site.data.keyword.cvad_short}} order form.
    {: tip}

[^sizing]:In standardized testing, profiles 8x16 and higher showed the best results.

## Step 7 - Logging and monitoring
{: #log-analysis}

Centralized logging is provided by an {{site.data.keyword.la_full}} instance that provides one place for the various log files, including:

*   Cloud Connector VSIs (Connector Logs and Plug-in), 
*   Domain Controller logs from the Active Directory Server, 
*   Volume Worker logs that are on both {{site.data.keyword.cloud_notm}} Functions and the worker VSI logs that complete the jobs.

Centralized logging is enabled by default. You specify the instance to use.

Select a log instance option, either:

*   Use an existing instance and select the name of the instance to use.
*   Create a new instance and select a logging plan to use. Set whether to enable platform. Platform logs can be enabled for only one logging instance per region. 

## Step 8 - Custom configuration (optional)
{: #provision-vpc-custom-config}

{{site.data.keyword.cvad_full_notm}} provisioning uses Terraform scripts and {{site.data.keyword.cloud_notm}} Schematics to create your resources.  

If you want only the items on the provisioning form, you select **Create**. The scripts create a Schematics workspace for you where you can see your script logs and the resources that are provisioned.  

If you want to further customize your resources, you select **Enable configuration customization after 'Create'** and then **Create**. The scripts create the Schematics workspace for you and show you the parameters that you can customize. After you are done customizing the parameters, you select **Generate Plan** then **Apply plan**. The Terraform scripts run and create your resources. 

For more information about Schematics, see [Getting started: IBM Cloud Schematics](/docs/schematics?topic=schematics-getting-started).

## Next steps
{: #next-steps-provisioning-citrix-daas-vpc}

As the components are provisioned, you are directed to the Schematics workspace page for your deployment, where you can view the script logs. 

After the {{site.data.keyword.cvad_full_notm}} components are provisioned, you need to complete a few post-provisioning steps to get you up and running. For more information, see [Post-provisioning steps for {{site.data.keyword.cvad_short}} on VPC](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas-vpc).
