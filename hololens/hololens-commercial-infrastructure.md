---
title: Infrastructure Guidelines for HoloLens
description: Learn about the infrastructure guidelines for HoloLens devices, including wireless network support, remote assist, and mobile device management.
ms.prod: hololens
ms.sitesec: library
ms.topic: article
ms.localizationpriority: high
ms.date: 1/23/2020
ms.reviewer: 
audience: ITPro
appliesto:
- HoloLens (1st gen)
- HoloLens 2
---

# Configure Your Network for HoloLens

This portion of the document will require the following people:

1. Network Admin with permissions to make changes to the proxy/firewall
2. Microsoft Entra Admin
3. Mobile Device Manager Admin

## Infrastructure Requirements

HoloLens is, at its core, a Windows mobile device integrated with Azure.  It works best in commercial environments with wireless network availability (wi-fi) and access to Microsoft services.

Critical cloud services include:

- Microsoft Entra ID
- Windows Update (WU)

Commercial customers will need enterprise mobility management (EMM) or mobile device management (MDM) infrastructure to manage HoloLens devices at scale.  This guide uses [Microsoft Intune](https://www.microsoft.com/enterprise-mobility-security/microsoft-intune) as an example, though any provider with full support for Microsoft Policy can support HoloLens.  Ask your mobile device management provider if they support HoloLens 2.

HoloLens does support a limited set of cloud disconnected experiences.

### Wireless network EAP support

- PEAP-MS-CHAPv2
- PEAP-TLS
- TLS
- TTLS-CHAP
- TTLS-CHAPv2
- TTLS-MS-CHAPv2
- TTLS-PAP
- TTLS-TLS

### Multiple network bands

It's possible you have multiple network bands as part of your infrastructure. If you want to ensure the HL connects to a specific band, such as 5 GHz, then separate your network into different SSIDs.

### HoloLens Specific Network Requirements

Make sure that [this list](hololens-offline.md) of endpoints are allowed on your network firewall. This will enable HoloLens to function properly.

### Remote Assist Specific Network Requirements

1. The recommended bandwidth for optimal performance of Remote Assist is 1.5 Mbps. See the [detailed network requirements](/MicrosoftTeams/prepare-network) for additional information.
**(Please note, if you don't network have network speeds of at least 1.5 Mbps, Remote Assist will still work. However, quality may suffer).**
1. Make sure that these ports and URLs are allowed on your network firewall to enable Microsoft Teams to function. Stay up to date with the [latest list of ports](/office365/enterprise/urls-and-ip-address-ranges#skype-for-business-online-and-microsoft-teams).

- Learn more about the specific [Network Requirements for Remote Assist](/dynamics365/mixed-reality/remote-assist/requirements#network-requirements).
- Learn more about how to [prepare your organization's network for Microsoft Teams](/MicrosoftTeams/prepare-network)

### Guides Specific Network Requirements

Guides only require network access to download and use the app.

<a name='azure-active-directory-guidance'></a>

## Microsoft Entra Guidance

> [!NOTE]
> This step is only necessary if your company plans on managing the HoloLens.

1. Ensure that you have a Microsoft Entra ID license.
Please [HoloLens Licenses Requirements](hololens-licenses-requirements.md) for additional information.

1. If you plan on using Auto Enrollment, you'll have to [Configure Microsoft Entra enrollment.](/mem/intune/enrollment/windows-enrollment-methods)

1. Ensure that your company's users are in Microsoft Entra ID.
See the following [instructions](/azure/active-directory/fundamentals/add-users-azure-active-directory) for adding users.

1. We suggest that users who need similar licenses are added to the same group.
    1. [Create a Group](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)
    1. [Add users to groups](/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)

1. Ensure that your company's users (or group of users) are assigned the necessary licenses.
If you need to assign licenses, follow these [directions](/azure/active-directory/fundamentals/license-users-groups).

1. Only do this step if users are expected to enroll their HoloLens/Mobile device into you (There are three options)
These steps ensure that your company's users (or a group of users) can add devices.
    1. **Option 1:** Give all users permission to join devices to Microsoft Entra.
**Sign in to the Azure portal as an administrator** > **Microsoft Entra** > **Devices** > **Device Settings** >
**Set Users may join devices to Microsoft Entra to *All***

    1. **Option 2:** Give selected users/groups permission to join devices to Microsoft Entra 
**Sign in to the Azure portal as an administrator** > **Microsoft Entra** > **Devices** > **Device Settings** >
**Set Users may join devices to Microsoft Entra to *Selected***
![Image that shows Configuration of Microsoft Entra joined Devices.](images/azure-ad-image.png)

    1. **Option 3:** You can block all users from joining their devices to the domain. This means that all devices will need to be manually enrolled.

## Mobile Device Manager Guidance

### Ongoing device management

> [!NOTE]
> This step is only necessary if your company plans to manage the HoloLens.

Ongoing device management will depend on your mobile device management infrastructure.  Most have the same general functionality but the user interface may vary widely.

1. [CSPs (Configuration Service Providers)](/windows/client-management/mdm/configuration-service-provider-reference#csps-supported-in-hololens-devices) lets you create and deploy management settings for the devices on your network. See the [list of HoloLens CSPs](/windows/client-management/mdm/configuration-service-provider-reference#csps-supported-in-hololens-devices) for reference.

1. [Compliance policies](/intune/device-compliance-get-started) are rules and settings that devices must meet to be compliant in your corporate infrastructure. Use these policies with Conditional Access to block access to company resources for devices that are non-compliant. For example, you can create a policy that requires BitLocker be enabled.

1. [Create Compliance Policy](/intune/protect/compliance-policy-create-windows).

1. Conditional Access allows/denies mobile devices and mobile applications from accessing company resources. Two documents you may find helpful are [Plan your CA Deployment](/azure/active-directory/conditional-access/plan-conditional-access) and [Best Practices](/azure/active-directory/conditional-access/best-practices).

1. [This article](/intune/fundamentals/windows-holographic-for-business) talks about Intune's management tools for HoloLens.

1. [Create a device profile](/intune/configuration/device-profile-create)

### Manage updates

Intune includes a feature called Update rings for Windows 10 devices, including HoloLens 2 and HoloLens v1 (with Holographic for Business). Update rings include a group of settings that determine how and when updates are installed.

For example, you can create a maintenance window to install updates, or choose to restart after updates are installed.  You can also choose to pause updates indefinitely until you're ready to update.

Read more about [configuring update rings with Intune](/intune/windows-update-for-business-configure).

### Application management

Manage HoloLens applications through:

1. Microsoft Store  
  The Microsoft Store is the best way to distribute and consume applications on HoloLens.  There's a great set of core HoloLens applications already available in the store or you can [publish your own](/windows/uwp/publish/).  
  All applications in the store are available publicly to everyone, but if it isn't acceptable, check out the Microsoft Store for Business.  

1. [Microsoft Store for Business](/microsoft-store/)  
  Microsoft Store for Business and Education is a custom store for your corporate environment.  It lets you use the Microsoft Store built into Windows 10 and HoloLens to find, acquire, distribute, and manage apps for your organization.  It also lets you deploy apps that are specific to your commercial environment but not to the world.

1. Application deployment and management via Intune or another mobile device management solution  
  Most mobile device management solutions, including Intune, provide a way to deploy line of business applications directly to a set of enrolled devices.  See this article for [Intune app install](/intune/apps-deploy).

1. *not recommended* Device Portal  
  Applications can also be installed on HoloLens directly using the Windows Device Portal.  This isn't recommended since Developer Mode has to be enabled to use the device portal.

Read more about [installing apps on HoloLens](hololens-install-apps.md).

### Certificates

You can distribute certificates through your MDM provider. If your company requires certificates, Intune supports PKCS, PFX, and SCEP. It's important to understand which certificate is right for your company. Please visit the [certificate configurations](/intune/protect/certificates-configure) documentation to determine which cert is best for you. If you plan to use certificates for HoloLens Authentication, PFX, or SCEP may be right for you.

See the following steps for using [SCEP](/intune/protect/certificates-profile-scep).

### How to Upgrade to Holographic for Business Commercial Suite

> [!NOTE]
> Windows Holographic for Business (commercial suite) is only intended for HoloLens (1st gen) devices. The profile will not be applied to HoloLens 2 devices.

You can find directions for upgrading to the commercial suite in the [holographic upgrade](/intune/configuration/holographic-upgrade) documentation.

### How to Configure Kiosk Mode Using Microsoft Intune

1. Sync Microsoft Store to Intune (See the following [instructions](/intune/apps/windows-store-for-business)).

1. Check your app settings
    1. Log in to your Microsoft Store Business account
    1. **Manage > Products and Services > Apps and Software > Select the app you want to sync > Private Store Availability > Select "Everyone" or "Specific Groups"**
        >[!NOTE]
        >If you don't see the app you want, you will have to "get" the app by searching the store for your app. **Click the "Search" bar in the upper right-hand corner > type in the name of the app > click on the app > select "Get"**.
    1. If you don't see your apps in **Intune > Client Apps > Apps** , you may have to [sync your apps](/intune/apps/windows-store-for-business#synchronize-apps) again.

1. [Create a device profile for Kiosk mode](/intune/configuration/kiosk-settings#create-the-profile)

> [!NOTE]
> You can configure different users to have different Kiosk Mode experiences by using "Microsoft Entra ID" as the "User logon type". However, this option is only available in Multi-App kiosk mode. Multi-App kiosk mode will work with only one app as well as multiple apps.

![Image that shows Configuration of Kiosk Mode in Intune.](images/aad-kioskmode.png)

For other MDM services, check your provider's documentation for instructions. Refer to the [HoloLens kiosk](hololens-kiosk.md?tabs=intunecustom#steps-in-configuring-kiosk-mode-for-hololens) instructions if you need to use a custom setting and full XML configuration to set up a kiosk in your MDM service.

## Certificates and Authentication

Certificates can be deployed via you MDM (see "certificates" in the [MDM Section](hololens-commercial-infrastructure.md#mobile-device-manager-guidance)). Certificates can also be deployed to the HoloLens through package provisioning. Please see [HoloLens Provisioning](hololens-provisioning.md) for additional information.

### Additional Intune Quick Links

1. [Create Profiles:](/intune/configuration/device-profile-create) Profiles allow you to add and configure settings that will be pushed to the devices in your organization.
