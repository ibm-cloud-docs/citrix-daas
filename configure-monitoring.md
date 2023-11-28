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
{: deprecated: .deprecated} 

# Configuring monitoring
{: #configuring-monitoring}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

## Configuring monitoring for Citrix Hypervisor
{: #configuring-monitoring-citrix-hypervisor}

### {{site.data.keyword.cloud_notm}} monitoring services
{: #ibm-cloud-monitoring}

{{site.data.keyword.cvad_full}} Classic is deprecated. As of 12-4-23, you can't create new instances, and access to free instances will be removed.  Any instance that still exists on that date will be deleted. Migrate your information to Citrix Virtual Private Cloud.  
{: deprecated}

You can configure monitoring with {{site.data.keyword.mon_full}} to keep you aware of any issues with your devices. For more information, see [{{site.data.keyword.cloud_notm}} monitoring services](/docs/cloud-infrastructure?topic=cloud-infrastructure-monitoring).
{: deprecated}


### Virtual Apps and Desktops monitoring
{: #virtual-apps-desktops-monitoring}

You can also configure alerts and notifications for your {{site.data.keyword.cvad_short}} service. Log in to your [Virtual Apps and Desktops service](https://xenapp.cloud.com/monitor){: external} to configure monitoring. For more information, see the [Citrix monitoring documentation](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/monitor.html){: external}.
{: deprecated}

## Configuring monitoring for VMware ESXi
{: #configuring-monitoring-vmware-esxi}

When you add or delete a service or infrastructure component, you must wait until the modification is complete before you can add or delete another service or infrastructure component. 
{: note}

You can configure VMware Vrealize for monitoring your VMware cluster. 

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials.
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VMware > Resources**.
3. Select your vCenter instance.
4. Click the **Services** tab.
5. Click **Add**.
6. Click **vRealize Operations**. 
7. Accept the terms and click **Create**. 

For more information, see [vRealize Operations and Log Insight overview](/docs/VMwaresolutions?topic=VMwaresolutions-vrops_overview).
