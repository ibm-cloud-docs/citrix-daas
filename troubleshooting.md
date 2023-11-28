---

copyright:
  years: 2020, 2021
lastupdated: "2021-05-19"

keywords:

subcollection: citrix-daas

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}
{: deprecated: .deprecated} 

# Troubleshooting {{site.data.keyword.cvad_full_notm}} Classic
{: #citrix-daas-troubleshooting-classic}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

## Connection timeouts or long login times
{: #connection-timeouts}

If you are experiencing intermittent, long session login times, connection timeouts, or network instability, you can set a Citrix policy that disables the Citrix Enlightened Data Transport Protocol (EDT). For instructions, see [How to Configure HDX Enlightened Data Transport Protocol](https://support.citrix.com/article/CTX220732){: external}.

For a technical overview of EDT, see [Adaptive transport](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/technical-overview/hdx/adaptive-transport.html){: external} in the Citrix product documentation. 

## Citrix connector failure
{: #connector-failure}

If the Connectors are in spinning state, or if you get a message in the Citrix Cloud Connectivity Test that the connectivity could not be validated, either:

* The Background Intelligent Transfer Service (BITS) in Connectors do not have the correct proxy settings. 
* The virtual machine master image might not have the correct proxy settings. 

### Solution 1 - Background Intelligent Transfer Service (BITS) in Connectors do not have the correct proxy settings
{: #connector-failure-bits}

Citrix cloud connector downloads core component installer package by using the BITS service (Background Intelligent Transfer Service).
If the BITS service does not pick up the correct proxy settings, it can't connect to internet and the file download fails.

1.	Run the `netsh winhttp import proxy source =ie` on the cloud connector’s elevated command prompt. Restart the OS to make the changes effective.
2.	Use the `bitsadmin` command on the Cloud Connector (elevated command prompt - Run As Admin) to ensure that BITS is using the proxy properly. 

     **bitsadmin /util /setieproxy networkservice MANUAL_PROXY "proxy:port" "bypass list"**

    Replace the proxy:port and bypass list with actual proxy server, port, and bypass list in use here.

3. Wait for about 10 - 15 minutes (or restart the BITS service) and retest the connectivity test.

   **net stop BITS && net start BITS**

### Solution 2 - Virtual Machine master image does not have correct proxy settings
{: #connector-failure-image}

This change applies the proxy setting to all the users and not just the Administrator.  

1.  Update registry settings:

    a.  Open the Registry Editor and go to HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Windows\CurrentVersion\Internet Settings.

    b.  Create a 32-bit DWORD called **ProxySettingsPerUser** and set it to **0**.

2.  Run these commands as administrator:

    a. **netsh winhttp import proxy source=ie**

    b. **netsh winhttp show proxy**




