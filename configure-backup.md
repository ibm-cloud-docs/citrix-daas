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

# Configuring backup
{: #configuring-backup}

## Configuring backup for Citrix Hypervisor
{: #citrix-hypervisor-backup}

{{site.data.keyword.backup_full}} can be configured for backing up hypervisors and virtual machines either on Windows or Linux&reg; operating systems. The backup can be encrypted, compressed, and duplicated. For more details on backing up your system, see [Getting started with {{site.data.keyword.backup_notm}}](/docs/Backup?topic=Backup-getting-started).
{: shortdesc}

All of the virtual machines are stored on NFS storage, and [thin provisioning](https://docs.citrix.com/en-us/tech-zone/design/reference-architectures/image-management.html#choosing-the-right-provisioning-model){: external} can be used while creating the virtual machines by using machine catalogs. Snapshots can be manually created, or they can be scheduled at hourly, daily, or weekly intervals. For more information on scheduling snapshots and receiving notifications, see [Managing Snapshots](/docs/FileStorage?topic=FileStorage-managingSnapshots).

## Configuring backup for VMware ESXi
{: #vmware-backup}

When you add or delete a service or infrastructure component, you must wait until the modification is complete before you can add or delete another service or infrastructure component. 
{: note}

You can configure your virtual machine backups with Veeam. 

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Resources**. 
3. Select your vCenter instance.
4. Click the **Services** tab.
5. Click **Add**.
6. Click **Veeam** for backups. 
7. Click **Edit** and enter a Name for the backup. 
8. (Optional) Modify the backup settings.
9. Accept the terms and click **Create**.

For more information about Veeam as a backup solution, see [Ordering Veeam](/docs/VMwaresolutions?topic=VMwaresolutions-veeam_ordering).

If you have NFS storage and you want to enable snapshots on the NFS storage, complete the following steps:

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) Classic Infrastructure > File Storage**. 
3. Select your storage. 
4. Click the **Snapshots** tab.
5. Click **Edit Snapshot Schedule** to set the schedule for taking snapshots or click **Take Manual Snapshot** to add a single snapshot space to your storage. 
