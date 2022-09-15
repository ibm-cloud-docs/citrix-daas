---

copyright:
  years: 2020, 2021
lastupdated: "2021-06-04"

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


# Ordering more bare metal servers
{: #order-bare-metal-servers}

## Citrix Hypervisor
{: #order-bm-servers-citrix-hypervisor}

If you want to increase your bare metal server count for {{site.data.keyword.cvad_full}} in your resource location, you can add more servers. Before you order more bare metal servers, make sure you identify your system's [network details](/docs/citrix-daas?topic=citrix-daas-post-provisioning-citrix-daas#view-network-details). You need to make sure to select the same VLAN and primary subnet when you order more servers.

You can order more bare metal servers through the [{{site.data.keyword.cloud}} catalog](https://cloud.ibm.com/catalog?category=compute#services){: external}. 

## VMware ESXi
{: #order-bm-servers-vmware-esxi}

When you add or delete a service or infrastructure component, you must wait until the modification is complete before you can add or delete another service or infrastructure component. 
{: note}

You can add more bare metal servers (ESXi hosts) to scale out your existing cluster.  

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Select **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Resources**. 
3. Select your vCenter Instance.
4. Click the **Infrastructure** tab. 
5. Click **Add** for the ESXi servers to add more hosts to the cluster.
6. Select the licensing option that you use.
7. Select the CPU generation, model, RAM, Storage, and Network Interface options for your solution.  
8. Accept the terms and click **Create**. 
9. Verify that the added ESXi server is not in Maintenance Mode. You cannot create new virtual machines if the new server is in Maintenance Mode. View the new server in the vSphere client under the cluster and take the server out of Maintenance Mode, if necessary. 


