---

copyright:
  years: 2020, 2021
lastupdated: "2020-07-10"

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

# Modifying capacity and IOPS of your storage
{: #modify-capacity-and-IOPS-storage}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

By default, the IOPS tier is 4 IOPS/GB when you provision your bare metal servers. If you want to modify the capacity and IOPS of your storage, complete the following steps.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials. 
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > File Storage**.
3. Select your storage name.
4. In the _Actions_ menu, you have options to modify the throughput, snapshot schedule, and size of the mount point. You can also create or delete manual snapshots.
