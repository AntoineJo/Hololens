---
title: Enterprise Enrollment of HoloLens Devices in MAC address restricted Wi-Fi Environment
description: How to MAC address for network on HoloLens 2 devices
ms.service: hololens
ms.sitesec: library
ms.topic: article
ms.localizationpriority: high
ms.date: 10/01/2020
ms.reviewer: v-evmill
audience: ITPro

appliesto:
- HoloLens 2
---

# Enterprise Enrollment of HoloLens Devices in MAC address restricted Wi-Fi Environment

This document will describe a common scenario we have identified within customer environments where the Wi-Fi is restricted by MAC addresses, or certificates are required to join Wireless networks.

## Example Scenario

Many customers in secure environments have restrictions on their Wireless or wired networks that will only allow approved devices (based on MAC Addresses) to connect successfully. This may be enforced through MAC Address filtering on a Wireless Access Point or through a DHCP server. Additionally, some Wireless networks can be protected with PEAP, which requires that a certificate be applied to the device prior to authenticating on the Wireless network.

In this scenario, two key requirements may introduce delays or require manual intervention when joining HoloLens devices to the network:

- The Wireless PEAP certificate must be applied to the device prior to the device successfully joining the wireless network.
- The MAC Address of the HoloLens Wi-Fi adaptor must be registered.

The core challenges with the requirements above are:

1. The MAC Address can currently only be identified from the Settings app on the device, or from Intune after a successful enrollment.

2. Without the MAC address, the device cannot join the Wi-Fi Network to begin enrollment.

3. Manual workarounds to these challenges require a technician to interact with the device.

## Solutions

There are many ways to improve this situation, depending on the infrastructure available within the environment.

| Solution | Benefits | Requirements |
| --- | --- | --- |
| Provisioning Package with Ethernet Adaptor | Improves OOBE experience and allows for a quicker technician experience. | HoloLens compatible USB-C Hub + Ethernet adaptor, and technician will still need to interact with the device for MAC capture and OOBE finalization |
| Autopilot with Intune Registration over Ethernet | This is a single-step connection and registration of the device to the customer environment. MAC capture can be completed without needing to interact with the device | Intune enabled for the customer Microsoft Entra tenant and a HoloLens compatible USB-C Ethernet adaptor |
| Automated reporting of MAC Addresses | When devices are registered with the Intune tenant, a script can report the MAC address to the technician. | Intune PowerShell cmdlets |

## Provisioning Package with Ethernet Adaptor

> [!NOTE]
> If the wired network is also subject to MAC restrictions, then the MAC address of the USB-C Hub + Ethernet adaptor will also need to be pre-approved. Care should be taken with this adapter as it will allow access to the network from other devices.

### Requirements

- Wired network port with access to the customer network
- HoloLens Compatible USB-C Hub with Ethernet adaptor — Any adapter that doesn't require any additional drivers or application installs should be suitable.
- Provisioning Package containing:
  - Containing Wireless Network information and Certificate
  - Optionally containing enrollment information for the Organization's Microsoft Entra ID
  - Containing any other required provisioning settings

### Process

The Process may vary depending on the software level of the device. If the device has the [May 2004 update](hololens-release-notes-2004.md#windows-holographic-version-2004), follow the steps below.

1. Place the provisioning package onto the root of a USB stick, and plug into the Hub.
2. Connect Ethernet cable to the Hub + Ethernet adapter.
3. Connect USB-C Hub to HoloLens device.
4. Turn on the HoloLens and put on the device.
5. Press the **Volume Down and Power button** to apply the Provisioning Package.
6. The technician can now follow OOBE, and when complete, open the Settings App to retrieve the MAC Address of the device.

If the device has an OS build before the [May 2004 update](hololens-release-notes-2004.md#windows-holographic-version-2004), follow the steps below.

1. Turn on the HoloLens and plug the device into a PC.
2. The device should show up on the PC as a file storage device.
3. Copy the Provisioning Package to the Device
4. Connect Ethernet cable to the hub.
5. Connect USB-C Hub to HoloLens device.
6. Put on the HoloLens
7. Press the **Volume Down and Power** button to apply the Provisioning Package.
8. The technician can now follow OOBE, and when complete, open the Settings App to retrieve the MAC Address of the device.

### Benefits

This will allow a "Single touch" of the device, to apply the correct provisioning package and gather the MAC address of the device. [Provisioning packages can be created following the guidance here.](hololens-provisioning.md)

## Autopilot with Intune Enrollment

### Requirements

- Wired network port with access to the customer network
- HoloLens devices running Windows Holographic 2004
- HoloLens Compatible USB-C Ethernet adapter
- Intune set up and enabled for the customer Tenant
- Device registered for Autopilot and imported into the Customer Tenant
- Intune Policies defined for the device:
  - Containing Wireless Network information and Certificate
  - Containing any other required provisioning settings

This will allow a customer with advanced networking requirements to enroll the devices in a hands-off, scalable approach

Additional pre-requisites will be needed as below:

1. [Enable the Tenant for the Autopilot preview](hololens2-autopilot.md).
1. Create the HoloLens policies to replace the Provisioning Package within Intune.
1. Create the HoloLens Intune Policies.
1. Assign the devices to the correct group.

### Process

1. Connect the ethernet cable to the adapter and plug the adapter into the USB-C port on the HoloLens 2 device.

2. Turn on the HoloLens.

3. The device should automatically connect to the internet during OOBE via the Ethernet adaptor. It should detect the Autopilot configuration and automatically register with Microsoft Entra ID and Intune.

4. The Device will apply the required Wi-Fi Certificates and other configuration as needed via Intune.

5. When complete, the technician can load the Intune (Endpoint Manager) Portal and drill into the device properties page at **Home -> Devices -> DeviceName -> Hardware**.

6. The Wi-Fi MAC address will be visible within the Intune Portal.

   ![MAC Address via Intune.](images/mac-address-intune.jpg)

7. The technician will add this MAC address as an allowed device.

### Benefits

This will allow a "Heads off" deployment experience for the Technician, with the device being able to go from the box to enrolled in Microsoft Entra ID and Intune without the technician having to wear the device or manually interact with the HoloLens environment.

## Reporting of MAC addresses to the Technician

### Requirements

- Authorization of the "Intune Graph PowerShell" against the customer Tenant
- Installation of the Intune Graph PowerShell on the technicians machine.
- [https://www.powershellgallery.com/packages/Microsoft.Graph.Intune/6.1907.1.0](https://www.powershellgallery.com/packages/Microsoft.Graph.Intune/6.1907.1.0)
- Read access to the "Managed Devices" elements of Intune. (Help Desk Operator or above, or a custom role)

At present, there is no "simple" way to trigger an automation command based on the enrollment of a new device within Intune. Therefore, this command will provide the technician a simple way to retrieve the MAC address without needing to log onto the portal and manually retrieve it.

```powershell
Import-Module Microsoft.Graph.Intune

Connect-MSGraph

Get-IntuneManagedDevice -Filter "model eq 'Hololens 2'" | where {$_.enrolledDateTime -gt (get-date).AddDays(-30)}  | select deviceName, wiFiMacAddress 
```

This will return the name and MAC address of any HoloLens devices that have been enrolled in the last 30 days.

![MAC Address via PowerShell.](images/mac-address-powershell.jpg)

### Process

After the Intune enrollment has completed, the Technician would run the above script to retrieve the MAC address.
