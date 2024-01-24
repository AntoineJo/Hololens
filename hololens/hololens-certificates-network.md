---
title: Prepare certificates and network profiles for HoloLens 2
description: Learn how to configure, use, deploy, and troubleshoot certificates for network on HoloLens 2 mixed reality devices.
ms.service: hololens
ms.sitesec: library
ms.reviewer: ruizhao
ms.topic: article
ms.localizationpriority: high
ms.date: 6/10/2022
audience: ITPro
appliesto:
- HoloLens 2
---
# Prepare certificates and network profiles for HoloLens 2

Certificate-based authentication is a common requirement for customers using HoloLens 2. You might require certificates to access Wi-Fi, to connect to VPN solutions, or for accessing internal resources in your organization.

Because HoloLens 2 devices are typically joined to Microsoft Entra and managed by Intune or other MDM provider, you'll need to deploy such certificates by using a Simple Certificate Enrollment Protocol (SCEP) or Public Key Cryptography Standard (PKCS) certificate infrastructure that is integrated with your MDM solution. 

>[!NOTE]
> If you do not have an MDM provider, you can still deploy certificates via a [provisioning package](hololens-provisioning.md#create-the-provisioning-package) in [Windows Configuration Designer](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22?rtc=1&activetab=pivot:regionofsystemrequirementstab) or through [Certificate Manager](certificate-manager.md) by going to **Settings > Update & Security > Certificate Manager**.

## Certificate requirements

Root certificates are required to deploy certificates through a SCEP or PKCS infrastructure. Other applications and services in your organization might require root certificates to be deployed to your HoloLens 2 devices as well. 

## Wi-Fi connectivity requirements

To allow a device to be automatically provided with the required Wi-Fi configuration for your enterprise network, you'll need a Wi-Fi configuration profile. You can configure Intune or other MDM provider to deploy these profiles to your devices. If your network security requires devices to be part of the local domain, you might also need to evaluate your Wi-Fi network infrastructure to make sure it's compatible with HoloLens 2 devices (HoloLens 2 devices are Microsoft Entra joined only).

## Deploy certificate infrastructure

If no SCEP or PKCS infrastructure already exists, you'll need to prepare one. 
To support the use of SCEP or PKCS certificates for authentication, Intune requires the use of a [certificate connector](/mem/intune/protect/certificate-connectors).

> [!NOTE]
> When you use SCEP with a Microsoft CA, you must also configure the [Network Device Enrollment Service (NDES)](/mem/intune/protect/certificates-scep-configure#set-up-ndes)

For more information, see [Configure a certificate profile for your devices in Microsoft Intune.](/intune/certificates-configure)

## Deploy certificates and Wi-Fi/VPN profile

> [!IMPORTANT]
> On HoloLens devices, VPN profiles are applied at the device scope and will apply to all users.

To deploy certificates and profiles, follow these steps:

1.	Create a profile for each of the Root and Intermediate certificates (see [Create trusted certificate profiles](/intune/protect/certificates-configure#create-trusted-certificate-profiles).) Each of these profiles must have a description that includes an expiration date in DD/MM/YYYY format. **Certificate profiles without an expiration date will not be deployed.**

2.	Create a profile for each SCEP or PKCS certificates (see [Create a SCEP certificate profile or Create a PKCS certificate profile](/intune/protect/certficates-pfx-configure#create-a-pkcs-certificate-profile)) Each of these profiles must have a description that includes an expiration date in DD/MM/YYYY format. **Certificate profiles without an expiration date will not be deployed.**

    > [!NOTE]
    > As the HoloLens 2 is considered for many to be a shared device, multiple users per device, it is recommended to deploy Device certificates instead of User certificates for Wi-Fi authentication where possible

3.	Create a profile for each corporate Wi-Fi network (see [Wi-Fi settings for Windows 10 and later devices](/intune/wi-fi-settings-windows)). 

    > [!NOTE]
    > It is recommended that the Wi-Fi profile be [assigned](/mem/intune/configuration/device-profile-assign) to Device groups rather than User groups where possible. 

    > [!TIP]
    > You also can export a working Wi-Fi profile from a Windows 10 PC on your corporate network. This export creates an XML file with all the current settings. Then, import this file into Intune, and use it as the Wi-Fi profile for your HoloLens 2 devices. See [Export and import Wi-Fi settings for Windows devices.](/mem/intune/configuration/wi-fi-settings-import-windows-8-1)

4.	Create a profile for each corporate VPN (see [Windows 10 and Windows Holographic device settings to add VPN connections using Intune](/intune/vpn-settings-windows-10)).

## Troubleshooting

### Issue - Unable to connect with network using certificate-based authentication ###

**Symptoms**

Device fails to establish network connection with certificate-based authentication.

**Troubleshooting steps**

1. If you need to validate that a certificate is deployed correctly please use the [Certificate Manager](certificate-manager.md) on the device to verify your certificate is present.  

    >[!WARNING]
    > Although you can view MDM-deployed certificates in Certificate Manager, you cannot uninstall them in Certificate Manager. You must uninstall them through MDM.

2. For Intune only, if Simple Certificate Enrollment Protocol (SCEP) is being used to deploy certificates, please ensure that Network Device Enrollment Service (NDES) server URL is reachable from device. [Refer to SCEP certificates in Intune](/mem/intune/protect/certificates-profile-scep) for setup-related information. If CNAME is being used instead of fully qualified domain for NDES server, please ensure that is getting resolved correctly by typing that URL in a web browser on the device.
