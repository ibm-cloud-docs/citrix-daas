---

copyright:
  years: 2020, 2021
lastupdated: "2020-09-24"

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

# Ordering more networks for your virtual machines
{: #order-more-networks-for-vm}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

A default secondary subnet with 256 IP address space is provisioned in the same VLAN as the primary subnet when you provision your bare metal servers. If you want to order more portable subnets, complete the following steps.

1. Log in to the [{{site.data.keyword.cloud}} console](https://cloud.ibm.com/login){: external} by using your unique credentials. 
2. Navigate to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > Classic Infrastructure > Device List**.
3. Select your server and navigate to the **Network details** section.
4. Click **Order IPs**.
5. In the **Network** section, select **Private** and make sure the **Portable** option is selected.
6. Choose the same VLAN that corresponds with your servers, then click **Create**.

After the new networks are created, complete the following tasks for DHCP and proxy server.

## DHCP server
{: #dhcp-servers}

1. Create a separate subinterface on `eth0`, for example, `eth0:2` in `/etc/sysconfig/network-scripts/`, and restart the network by using the `service network restart` command.
2. Add a new subnet section in `/etc/dhcp/dhcpd.conf` for the new network.
3. Update `/etc/systemd/system/dhcpd.properties` file to reflect the new subinterface that you created.
4. Restart the DHCP service.

## Proxy server
{: #proxy-server}

1. Update `/etc/squid/allowed_ips.txt` file with the new network created.
2. Restart the squid service.
