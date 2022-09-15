---

copyright:
  years: 2020, 2021
lastupdated: "2021-03-11"

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


# Canceling bare metal servers
{: #cancel-bare-metal-servers}

## Citrix Hypervisor
{: #cancel-citrix-hypervisor}

If you want to cancel one or more of your {{site.data.keyword.cvad_full}}bare metal servers, complete the following steps.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > Device List.**
3. In the _Actions_ menu of the bare metal server that you want to cancel, select **Cancel Device.**

## VMware ESXi
{: #cancel-vmware-esxi}

When you add or delete a service or infrastructure component, you must wait until the modification is complete before you can add or delete another service or infrastructure component. 
{: note}

You can cancel one or more ESXi hosts from your cluster. You should have three hosts with NFS storage.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Resources**.
3. Select your vCenter Instance. 
4. Click the **Infrastructure** tab. 
5. Select your cluster.
6. Select the ESXi hosts you would like to delete. 
7. Click **Delete**.
