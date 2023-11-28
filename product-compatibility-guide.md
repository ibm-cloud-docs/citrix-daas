---

copyright:
  years: 2020, 2021
lastupdated: "2022-04-01"

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
{:table: .aria-labeledby="caption"}
{: deprecated: .deprecated} 

# Product compatibility guide
{: #product-compatibility-guide}

{{site.data.keyword.cvad_full}} Classic automation is deprecated. As of 12-4-23, you can't create new classic instances with automation. 
{: deprecated}

{{site.data.keyword.cvad_short}} service supports several hosts and virtualization resources. For more information, see the Citrix documentation about [Hosts/virtualization resources](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/system-requirements.html#hosts--virtualization-resources){: external}. Review the following table for information about the products and versions that are supported in the current release of {{site.data.keyword.cvad_full}}.
{: shortdesc}

## Intel processors reference
{: #intel-processors-reference}

This hardware information applies to Classic configurations only.
{: deprecated}

| CPU series | Server configuration | Cores and Threads | IBM-supported hypervisors |
| --- | --- | --- | --- |
| Intel® Xeon® Silver 4210 Processor | Dual processor | 20c, 40t | Citrix XenServer 7.1 CU2, Citrix Hypervisor 8.2, VMware 6.7 U3 |
| Intel® Xeon® Gold 6140 Processor | Dual processor | 36c, 72t | Citrix XenServer 7.1 CU2, Citrix Hypervisor 8.2 |
| Intel® Xeon® Gold 6248 Processor | Dual processor | 40c, 80t | Citrix XenServer 7.1 CU2, Citrix Hypervisor 8.2, VMware 6.7 U3 |
| Intel® Xeon® Platinum 8260 Processor | Dual processor | 48c, 96t | Citrix XenServer 7.1 CU2, Citrix Hypervisor 8.2, VMware 6.7 U3 |
{: caption="Table 1. Supported Intel processors" caption-side="top"}


## VPC supported profiles
{: #citrix-daas-vpc-profiles}

For information about VPC profiles that are supported for {{site.data.keyword.cvad_short}} VPC, see [Instance Profiles](/docs/vpc?topic=vpc-profiles&interface=ui).[^sizing]

[^sizing]:In standardized testing, profiles 8x16 and above showed the best results. 
