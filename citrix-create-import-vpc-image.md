---

copyright:
  years: 2023
lastupdated: "2023-03-30"

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
{:beta: .beta}
{:table: .aria-labeledby="caption"}


# Creating and importing a Windows-managed desktop custom image for Citrix-DaaS
{: #create-import-windows-custom-image-citrix-daas}

You can create your own custom Windows desktop-based image to import the custom image into {{site.data.keyword.vpc_full}}. You can then use the custom image to deploy a virtual server or bare metal server in the {{site.data.keyword.vpc_full}} infrastructure.
{: shortdesc}

## Creating a Windows desktop image for Citrix-DaaS
{: #creating-windows-custom-image-citrix-daas}

All custom images must meet the following requirements：

*  Contain a single file or volume
*  Is in qcow2 or vhd format
*  Is cloud-init enabled
*  Size doesn't exceed 250 GB
*  The minimum size is 10 GB. For any image that is less than 10 GB, the size is rounded up to 10 GB.

You can't create an image from an encrypted boot volume (image from a volume) that is not 100 GB. The operation is blocked.
{: note}

Use the following steps to create a Windows custom image to deploy in the {{site.data.keyword.vpc_short}} infrastructure environment. The procedure encompasses these high-level tasks:
* Use VirtualBox to create a Windows image in VHD format.
* Customize the image with virtIO drivers and Cloudbase-init.
* Upload the image to {{site.data.keyword.cos_full_notm}}.

### Before you begin
{: #before-citrix-create-vpc}

As part of creating the VPC image, you must ensure that:

*  All drives that are mounted are properly disconnected before you run the final sysprep step. 
*  The local admin account is enabled and the original user is deleted so that when cloud init runs you are not left with an account you cannot log in to and another account that is some other user. 

Obtain the required virtio-win drivers by [provisioning](/docs/vpc?topic=vpc-creating-virtual-servers) or accessing an existing Red Hat Enterprise Linux virtual server in {{site.data.keyword.vpc_short}}. Then, install the virtio-win package on the server. Finally, copy the virtio-win ISO file, for example, *virtio-win-1.9.15.iso*, to use for your Windows custom image.

   1. On your Red Hat Enterprise Linux virtual server in {{site.data.keyword.vpc_short}}, install the virtio-win package by running the following command:

      ```sh
      yum install virtio-win
      ```
      {: codeblock}

      In this example, the virtio-win package is being installed on Red Hat Enterprise Linux version 8. Your returned output is similar to the following example:

      ```sh
      Installed:
        virtio-win-1.9.15-0.el8.noarch
      ```
      {: screen}

   2. Access the virtio-win ISO in the `/usr/share/virtio-win` directory.  

      ```sh
      cd /usr/share/virtio-win/
      ```
      {: codeblock}

   3. Use SCP to copy the virtio-win ISO file, for example `virtio-win-1.9.15.iso`, to use for your Windows custom image.  


Locate and read these documents:
*  [Sysprep (Generalize) a Windows installation](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation?view=windows-10)
*  [Enable and disable the built-in administrator account](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/enable-and-disable-the-built-in-administrator-account?view=windows-10).

### Initial steps for creating a Windows custom image
{: #create-win-custom-image-first-steps}

Complete the following steps to start creating a Windows custom image.

1. Begin with a Windows ISO image file. For example, `Windows.iso`.
2. Create a VHD image where you can install Windows.

   ```sh
   qemu-img create -f vpc Windows.vhd 100G
   ```
   {: codeblock}

    {{site.data.keyword.cloud}} supports custom image import with VHD or qcow2. However, Virtual Box does not support the qcow2 format.
    {: note}

3. Use VirtualBox to create a virtual machine with the VHD image that you created in step 2. For more information, see [Oracle VM VirtualBox User Manual](https://www.virtualbox.org/manual/){: external}.

If you choose to use a method other than VirtualBox to create the custom image, such as VMware, you must remove all drivers that are specific to that hypervisor from the custom image.
{: important}

### Customizing the virtual machine bvy using Virtual Box
{: #customize-virtual-machine}

Complete the following steps to customize the virtual machine.

1. In Storage settings, add the Windows installation ISO and the virtio-win driver ISO as optical drives. For example, `Windows.iso` and `virtio-win-1.9.15.iso`.

2. Start the virtual machine and begin the Windows installation. When you see the page **Where do you want to install Windows?** you must load all of the `virtio-win\` drivers. You might need to clear "Hide drivers that aren't compatible with this computer's hardware" to access all of the required drivers. Then, you can select **Drive 0** and continue with the installation. When the installation is complete, shut down the virtual machine and remove the optical drives that you added: Windows installation ISO and virtio-win driver ISO. You can ignore any warnings about removing an optical drive.

3. Enable local administrator account and reset the local admin password. For more information, see [Enable and disable the built-in administrator account](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/enable-and-disable-the-built-in-administrator-account?view=windows-10).

4. Use the default Windows updater to download and install Windows updates. Repeat the process of downloading and installing updates until no updates are available.

5. Install [cloudbase-init](https://cloudbase.it/cloudbase-init/){: external}. For more information, see [cloudbase-init’s documentation](https://cloudbase-init.readthedocs.io/en/latest/index.html){: external}.

6. Modify the cloudbase-init configuration file, `C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\cloudbase-init.conf` to match the values that are shown in the following example.

   ```text
   [DEFAULT]
   #  "cloudbase-init.conf" is used for every boot
   config_drive_types=vfat,iso
   config_drive_locations=hdd,partition
   mtu_use_dhcp_config=false
   real_time_clock_utc=false
   bsdtar_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\bsdtar.exe
   mtools_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\
   debug=true
   log_dir=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\log\
   log_file=cloudbase-init.log
   default_log_levels=comtypes=INFO,suds=INFO,iso8601=WARN,requests=WARN
   local_scripts_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\LocalScripts\
   metadata_services=cloudbaseinit.metadata.services.configdrive.ConfigDriveService,
   # enabled plugins - executed in order
   plugins=cloudbaseinit.plugins.common.mtu.MTUPlugin,
             cloudbaseinit.plugins.windows.ntpclient.NTPClientPlugin,
             cloudbaseinit.plugins.windows.licensing.WindowsLicensingPlugin,
             cloudbaseinit.plugins.windows.extendvolumes.ExtendVolumesPlugin,
             cloudbaseinit.plugins.common.userdata.UserDataPlugin,
             cloudbaseinit.plugins.common.localscripts.LocalScriptsPlugin
   ```
   {: screen}

7. Modify the `cloudbase-init-unattend.conf` file in `C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\cloudbase-init-unattend.conf` to match the values shown in the following example.

   ```text
   [DEFAULT]
   #  "cloudbase-init-unattend.conf" is used during the Sysprep phase
   username=Administrator
   inject_user_password=true
   first_logon_behaviour=no
   config_drive_types=vfat,iso
   config_drive_locations=hdd,partion
   allow_reboot=false
   stop_service_on_exit=false
   mtu_use_dhcp_config=false
   bsdtar_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\bsdtar.exe
   mtools_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\
   debug=true
   log_dir=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\log\
   log_file=cloudbase-init-unattend.log
   default_log_levels=comtypes=INFO,suds=INFO,iso8601=WARN,requests=WARN
   local_scripts_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\LocalScripts\
   metadata_services=cloudbaseinit.metadata.services.configdrive.ConfigDriveService,
   # enabled plugins - executed in order
    plugins=cloudbaseinit.plugins.common.mtu.MTUPlugin,
             cloudbaseinit.plugins.common.sethostname.SetHostNamePlugin,
             cloudbaseinit.plugins.windows.createuser.CreateUserPlugin,
             cloudbaseinit.plugins.windows.extendvolumes.ExtendVolumesPlugin,
             cloudbaseinit.plugins.common.setuserpassword.SetUserPasswordPlugin,
             cloudbaseinit.plugins.common.localscripts.LocalScriptsPlugin
   ```
   {: screen}

8. Modify the `Unattend.xml` file in `C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\Unattend.xml` to set **PersistAllDeviceInstalls** to *false*.

9. Disconnect all mounted drives. For more information, see [Sysprep (Generalize) a Windows installation](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation?view=windows-10).

10. Run Sysprep by using the following command:

      ```sh
      C:\Windows\System32\Sysprep\Sysprep.exe /oobe /generalize /shutdown "/unattend:C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\Unattend.xml"
      ```
   
      If you are customizing a virtual server to migrate from the Classic infrastructure, return to [Migrating a virtual server from the classic infrastructure](/docs/vpc?topic=vpc-migrate-vsi-to-vpc#migrate-customize-image-vpc) and continue completing migration steps.
      {: tip}

### Uploading the custom image
{: #complete-custom-image-win}

Upload your image to {{site.data.keyword.cos_full_notm}}. On the **Objects** page of your {{site.data.keyword.cloud}} Object Storage bucket, click **Upload**. You can use the Aspera high-speed transfer plug-in to upload images that are larger than 200 MB. For more information about uploading to {{site.data.keyword.cos_full_notm}}, see [Upload data](/docs/cloud-object-storage?topic=cloud-object-storage-upload).


## Importing and validating custom images into VPC for Citrix-DaaS
{: #importing-custom-images-vpc-citrix-daas}

You can create your own custom image on premises and then import it to {{site.data.keyword.vpc_full}} infrastructure from {{site.data.keyword.cos_full}}. Then, you can use your custom image to create new virtual server instances that runs on the KVM hypervisor. If you plan to use your custom image in a private catalog, you must first import and validate the custom image into {{site.data.keyword.vpc_short}}.
{: shortdesc}

You can also create a custom image of a boot volume that is attached to a server at import time. For more information, see [About creating an image from volume](/docs/vpc?topic=vpc-image-from-volume-vpc). 
{: note}

### Importing a custom image
{: #import-custom-image-cloud-object-storage}

When you have an image available in {{site.data.keyword.cos_full_notm}}, you can import it to {{site.data.keyword.vpc_short}} infrastructure by using the {{site.data.keyword.cloud_notm}} console.

1. Make sure that your compatible custom image is available in {{site.data.keyword.cos_full_notm}}. To upload an image to {{site.data.keyword.cos_full_notm}}, on the **Objects** page of your bucket, click **Upload**. You can use the Aspera high-speed transfer plug-in to upload images larger than 200 MB. For more information, see [Creating a Windows custom image](/docs/vpc?topic=vpc-create-windows-custom-image), [Bring your own license](/docs/vpc?topic=vpc-byol-vpc-about) and [Uploading data](/docs/cloud-object-storage?topic=cloud-object-storage-upload) to {{site.data.keyword.cos_full_notm}}.
2. In [{{site.data.keyword.cloud_notm}} console](https://console.cloud.ibm.com/vpc-ext){: external}, go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Compute > Custom Images**.
3. Click **Create**.
4. Complete the required fields that are described in Table 1 and click **Create Custom Image**.

| Field | Value |
|-------|-------|
| Name  | A name is required for your custom image. |
| Resource group | Select a resource group for the server. |
| Tags |  You can assign a label to this resource so that you can easily filter resources in your resource list. |
| Region | Select the location, or specific geographic area, where you want your custom image to be available for provisioning.|
| Source | Select **Cloud Object Storage** as the source. A list displays from which you select a Cloud Object Storage service instance where the image that you want to import is stored. \n Need to create a custom image from a volume instead? Select the source for the custom image by choosing either **Virtual server instance boot volume** or **Block storage boot volume**. For more information, see [Creating an image from a volume](/docs/vpc?topic=vpc-create-ifv). |
| Location | Select the specific geographic region where your image is stored. |
| Bucket | Select the {{site.data.keyword.cos_full_notm}} bucket where your image is stored.|
| Name | Select the image file in the {{site.data.keyword.cos_full_notm}} service instance that you want to import. If you are importing an encrypted image, the image must be encrypted with LUKS encryption by using QEMU and your own passphrase. For more information, see [Encrypting the image](/docs/vpc?topic=vpc-create-encrypted-custom-image#manually-encrypt-image). |
| Operating System | Select the operating system that is included in your image. There is no stock image for Windows 10 and Windows 11. If you are using Windows 10, select a Windows 2019 image. If you are using Windows 11, select a Windows 2019 or 2022 image. \n \n For custom images with Windows operating systems, you can bring your own license (BYOL) or license through {{site.data.keyword.cloud_notm}}. \n \n For Windows [BYOL custom images](/docs/vpc?topic=vpc-byol-vpc-about), you must select the OS with `-byol` appended to the name. For example, if you have a Windows 2019 BYOL custom image, select *Windows-2019-amd64-byol* for the operating system. Failure to select the `-byol` version of the operating system when you import a BYOL custom image might result in a virtual server that is unable to start. \n \n If you configured your Windows custom image to license through {{site.data.keyword.cloud_notm}}, you must select the non-byol operating system. For example, if you have a Windows 2019 custom image that you plan to license through {{site.data.keyword.cloud_notm}}, select *Windows-2019-amd64* as the operating system when you import the custom image. |
| Encryption | The default selection is **No encryption**. If you have not encrypted your image by using QEMU, use the default value, **No encryption**. If you are importing an image that you have encrypted by using QEMU and your own passphrase, select the key management service where your customer root key (CRK) that protects your passphrase is stored. Select either **Key Protect** or **Hyper Protect Crypto Services**. VHD format images are not supported for encryption. |
| Encryption service instance | For an encrypted image, select the specific instance of the key management service where your CRK that wraps your encryption passphrase is stored. For more information, see [Setting up your key management service and keys](/docs/vpc?topic=vpc-create-encrypted-custom-image#kms-prereqs). |
| Key name | Select the customer root key (CRK) that you used to wrap your encryption passphrase. For more information, see [Setting up your key management service and keys](/docs/vpc?topic=vpc-create-encrypted-custom-image#kms-prereqs). |
| Wrapped data encryption key | For an encrypted image, specify the ciphertext that is associated with the wrapped data encryption key (WDEK). The WDEK is produced by wrapping the passphrase that you used to encrypt your image with your customer root key. For more information, see [Setting up your key management service and keys](/docs/vpc?topic=vpc-create-encrypted-custom-image#kms-prereqs).|
{: caption="Table 1. Import custom image user interface fields" caption-side="bottom"}

### Validating an imported custom image
{: #validate-custom images-cloud-object-storage}

After you import a custom image, you can view the checksum that was generated for the image when it is imported to {{site.data.keyword.vpc_short}}.

If you generate a checksum locally for your image before you import it, you can compare the two checksums to make sure that they are identical. Matching checksums indicate that the image is unaltered.

1. In [{{site.data.keyword.cloud_notm}} console](https://console.cloud.ibm.com/vpc-ext){: external}, go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Compute > Custom Images**.
2. From your list of custom images, click the name of the custom image that you want to validate. 
3. In the image details side panel, locate the **Checksum (SHA256)** field. You see content similar to, *6809606da67eb83670e6249e54e94043eb43c0471669fb96ea4050c4c07e2df7*. 
4. Compare the Checksum (SHA256) value to the output that is generated when you calculate a checksum for the image locally. 

   * Example Linux command: `sha256sum ubuntu_image.qcow2`
   * Example Mac command: `shasum -a 256 ubuntu_image.qcow2`
   * You receive output similar to: `6809606da67eb83670e6249e54e94043eb43c0471669fb96ea4050c4c07e2df7`

5. [Create a virtual server instance](/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui) that uses this image.

### Next steps
{: #next-validate-custom-image}

After you validate the custom images, you can deploy and manage your custom images. For more information, see [Managing custom images](/docs/vpc?topic=vpc-managing-custom-images&interface=ui). If you plan to manage your custom images with a private catalog, see [Onboarding a virtual server image for VPC](/docs/account?topic=account-catalog-vsivpc-tutorial&interface=ui).


