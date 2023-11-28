---

copyright:
  years: 2020, 2021
lastupdated: "2020-09-01"

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
{: deprecated: .deprecated} 


# Ordering more storage on your bare metal servers
{: #order-extra-storage-vm}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

If you need more storage on your bare metal servers as a separate mount point for any operational reasons, complete the following steps.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > File Storage**.
3. Select **Order File Storage**.
4. Select the same **Region**, **Location**, and **Zone** where your provisioned servers are located.
5. Choose the **Billing Method**, **Size**, and **Snapshot space**.
6. Choose your **IOPS profile**, and then click **Create**.
7. After the storage provisioning is complete, select **Authorize hosts** in the _Actions_ menu on the **File Storage** page.
8. On the **Authorize hosts** page, select from the list of hosts. Only the authorized hosts are able to mount the storage.
9. Identify the mount point and mount the storage on the wanted hosts (either through the XenServer CLI or through XenCenter).
