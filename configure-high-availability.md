---

copyright:
  years: 2020, 2021
lastupdated: "2021-05-03"

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

# Configuring for high availability
{: #configuring-for-high-availability}

If you are running mission critical applications on your {{site.data.keyword.cvad_full}} environment, you can add more servers to improve the availability of these applications.

When you add any servers for high availability, you should order them within the same VLAN and network as your other services.
{: important}

## Best practices for configuring high availability
{: #best-practices-ha}

Review the following best practices for your relevant hypervisor.

### Citrix Hypervisor
{: #citrix-hypervisors-ha}

As a best practice, configure N+1 hypervisors for high availability. You can add more hypervisors beyond N+1 to give you more flexibility to move workloads to between servers during a planned outage. You can order more bare metal servers through the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog?category=compute#services){: external}. 

For application-level high availability, configure the hypervisors by using documentation from the hypervisor vendors. For more information, see [High Availability](https://docs.citrix.com/en-us/xencenter/7-1/pools-ha-about.html){: external}.

For optimal manageability and availability, you can run hypervisor hosts in pools – at least one pool for server VDAs and at least one pool for desktop VDAs. For more information, see [Ensuring High Availability- XenApp and XenDesktop Deployments on Citrix Cloud](https://www.citrix.com/content/dam/citrix/en_us/documents/white-paper/ensuring-high-availability-xenapp-and-xendesktop-deployments-on-citrix-cloud.pdf){: external} and the Citrix product documentation on [Workload Separation](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/citrix-vdi-best-practices/design/design-userlayer5.html#decision-workload-separation){: external}.

### VMware ESXi
{: #vmware-servers-ha}

The minimum recommendation for high availability is three ESXi hosts with NFS storage. These servers are provisioned in the cluster and are highly available within the data center.

## Cloud Connectors
{: #cloud-connectors-ha}

Three Cloud Connectors are provisioned in your resource location for your {{site.data.keyword.cvad_short}} service. All the three Cloud Connectors are stateless, and the load is evenly distributed. No additional configuration is needed for Cloud Connectors to make them highly available. For more information, see [Citrix Cloud Connector](https://docs.citrix.com/en-us/citrix-cloud/citrix-cloud-resource-locations/citrix-cloud-connector.html){: external}.

## DHCP server
{: #DHCP-server-ha}

All virtual server instances in {{site.data.keyword.cloud_notm}} classic infrastructure are configured with auto-evacuation. This means that instances are evacuated automatically to other servers if the underlying hypervisor fails.

The purpose of a DHCP server is to lease IP addresses on the newly created instances. In the case of failure, existing virtual servers might continue to operate. However, the system might not be able to lease IP addresses to newly created instances until DHCP VM comes up. If you have mission-critical applications and you need high availability beyond the auto-evacuation feature, you want to configure DHCP in hot standby mode. Complete the following steps.
1. Order another virtual server instance in the same data center and VLAN through the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog?category=compute#services){: external}. Select CPU, memory, and storage specifications similar to your existing DHCP server.
2. Create the sub interfaces on the secondary DHCP server similar to the primary.
3. Configure both DHCP servers with failover-peer tags. Configure one as primary and the second one as secondary. An example configuration is available from ISC [A Basic Guide to Configuring DHCP Failover](https://kb.isc.org/docs/aa-00502){: external}.
4. Restart and enable DHCP services.

## Proxy server
{: #proxy-server-ha}

All virtual servers are configured with an auto-evacuation feature. A proxy server is also configured on a virtual server. To improve availability, you can order and configure another virtual server instance as a second proxy server through the [{{site.data.keyword.cloud_notm}} catalog](https://cloud.ibm.com/catalog?category=compute#services){: external}. You can use a load balancer to manage traffic between the proxy servers. For more information, see [Getting started with {{site.data.keyword.cloud_notm}} Load Balancer](/docs/loadbalancer-service?topic=loadbalancer-service-getting-started).


## Active Directory server
{: #active-directory-server-ha}

If you deployed an active directory server in {{site.data.keyword.cloud_notm}}, you can improve resiliency by ordering another Windows virtual server instance and then installing a secondary domain controller.
