---
layout: post
title: "Windows 8.1 and Device Encryption"
tags:
- winpe
- bitlocker
- drives
---


I recently did some research into Device Encryption, an enabled-by-default BitLocker-encryption of the primary OS volume. Device Encryption was originally Windows RT only but Windows 8.1 brings the feature to [every Windows edition](http://www.microsoft.com/en-us/windows/enterprise/products-and-technologies/windows-8-1/compare/default.aspx). Furthermore, OEMs are [required via logo certification (PDF)](http://download.microsoft.com/download/7/0/E/70E74967-B0FE-477A-974F-C1ED16EE31DF/windows8-1-hardware-cert-requirements-system.pdf) to leave Device Encryption enabled on all machines that support [Connected Standby](http://msdn.microsoft.com/en-us/library/windows/hardware/jj248729.aspx). The feature never came up when it was RT only, but launching with the other Windows Skus meant I needed to understand it.

This was my first foray into anything BitLocker, so I began by reading a bunch of source material on it. If you've played with BitLocker before, Device Encryption will be easy. If not, I found these resources to be supremely informative:

* The BitLocker section in the [Windows Internals 6th Edition Part Two](http://shop.oreilly.com/product/0790145344403.do) (Pages 163 – 176)
* [The Understanding and Troubleshooting Bitlocker in Windows Server 8 guide](http://www.microsoft.com/en-us/download/details.aspx?id=29032) by Joseph Conway
* [What’s New in BitLocker in Windows 8.1](http://technet.microsoft.com/en-us/library/dn306081.aspx) on MSDN
* [Windows BitLocker Drive Encryption FAQ](http://technet.microsoft.com/en-us/library/cc766200(v=ws.10).aspx) on MSDN
With an understanding of the BitLocker mechanics under my belt, I set out to answer these questions:

* What is Device Encryption?
* How is the drive unlocked?
* How does this affect repairing Windows?
Let's jump in.

### What is Device Encryption?

Device Encryption is a fancy name for a specific BitLocker implementation that's designed to be consumer-friendly. BitLocker has traditionally been a Professional/Enterprise feature; Device Encryption is the first time the technology has been available to nonprofessional users. Machines that support Device Encryption will see the automatic BitLocker-encyrption of the OS volume and any additional fixed disks.

Device Encryption is established during the initial OS installation. Windows automatically BitLocker-encrypts the OS partition and any additional fixed disks. The BitLocker protection is intentionally left in the Suspended state with no established protectors -- effectively, the contents of the OS partition and additional fixed disks are encrypted, but a clear key is at the front of each volume to allow them to be automatically unlocked/mounted. This is the first detail of the consumer-friendly implementation. Windows waits to take BitLocker out of the Suspended state until a user to signs in with a Microsoft Account. Then, it can silently upload a BitLocker recovery key to protect against unexpected failures.

So let's jump forward a bit. The machine has left the factory with everything encrypted but BitLocker suspended. The user powers on the machine and is greeted by the Out of Box Experience. The OOBE asks for a Microsoft Account, the user signs in or creates one, and the key is uploaded to Microsoft. Windows removes the BitLocker clear key from the volumes, seals an unlock key in the TPM, and from then on relies on the TPM to reveal the key to unlock the volumes on boot. Device Encryption can then be considered truly enabled.

Quick Q&A:

* What happens if someone signs in with a local account instead?

	* Device Encryption remains in the Suspended state indefinitely until an administrator account is converted to a Microsoft Account

* Just Microsoft Accounts?

	* No, the requirement of a key backup being saved before BitLocker leaves the Suspended state can also be satisfied by joining a machine to a domain environment. If the Active Directory server supports it, Windows will upload the key there and BitLocker will leave the Suspend state. This was outside the scope of my research though.

* Are there any user-configurable options for Device Encryption during Setup?

	* No, there are no user-configurable options for disabling or otherwise affecting Device Encryption during setup. After setup, Device Encryption can be managed from the Control Panel and any of the regular interfaces to BitLocker (WMI, manage-bde.exe, etc.) If you're configuring your own Windows 8.1 install, you can opt out of Device Encryption via [setting the PreventDeviceEncryption value](http://technet.microsoft.com/en-us/library/dn306081.aspx) in your unattend.xml.

* Where is the Recovery key uploaded in the event it were to become necessary?

	* The key is uploaded to Microsoft. Traditional BitLocker can upload the key to a folder in SkyDrive; this is not what happens with Device Encryption. Instead, the recovery key can be [attained via an online process](http://windows.microsoft.com/en-US/windows-8/bitlocker-recovery-keys-faq) that involves signing into your Microsoft Account and verifying you're you. (A code is phoned_texted_emailed to information on file.)

### How is the Drive Unlocked?

Device Encryption is TPM only BitLocker solution. BitLocker itself supports a number of different styles of protection, such as a pin code, smart card, additional USB drive, etc. Device Encryption's choice of utilizing the TPM only is designed to provide protection with minimal user impact.

The TPM holds the unlock key sealed earlier as well as hashes of boot-critical components. When the computer turns on, boot components are compared against the hashes in the TPM to ensure none have been compromised. You can view the hashes as a combination to a safe that stores the unlock key. Get the combo right, and you get the key to unlock the volume. But if any hashes don't match, the key is not released and the volume cannot be unlocked. In such a case, Windows prompts for the BitLocker recovery key.

On UEFI systems, four sets of components hashed:

```
[0] The Core Root-of-Trust for Measurement (CRTM), EFI boot and run-time services, EFI drivers embedded in system ROM, ACPI static tables, embedded SMM code, and BIOS code
[2] Option ROM code, such as any additional drivers loaded from an adapter card
[4] EFI Boot file (usually bootx64.efi) and any other EFI applications loaded by it 
[11] BitLocker access control. Includes some BCD settings verification and other internal checks such as a hash of some configuration settings
```

These are defined by the Trusted Computing Group [here (PDF)](http://www.trustedcomputinggroup.org/files/temp/644EE853-1D09-3519-ADB133C09A5F2BDF/TCG%20EFI%20Platform%20Specification.pdf). The four chosen for Device Encryption have each been engineered to remain static every boot, hopefully preventing any false failures to validate. (In implementations for older versions of Windows, actions as simple as pressing F8 on boot would cause a prompt for a BitLocker key.) Of course, that's not to say you'll never see a prompt. Let's look at how trying to repair Windows issues can trigger them.

### How Does This Affect Repairing Windows?

I first looked at what actions would prevent the automatic unsealing of the unlock key on boot. For example, common repair steps are to boot to an offline environment, such as the built-in Windows Recovery Environment. In my testing, any attempt to interrupt or detour the boot process change the hashes of boot components and prevents the key from being released by the TPM. I tested the following scenarios:

* PC Settings -> .. -> Advanced Startup -> Start a Command Prompt -> Device reboots -> WinRE.wim loads -> KEY NEEDED
* PC Settings -> .. -> Advanced Startup -> Use a USB Device -> Device reboots -> Flash drive loads -> KEY NEEDED
* Power On Device -> Hold HW button to boot to flash drive -> KEY NEEDED
Windows RE is intelligent about detecting this failure and has a fancy prompt asking for the recovery key. A custom PE environment not using the Windows RE shell would need to facilitate unlocking the drive itself. Otherwise, in each scenario, rebooting the machine and allowing the regular boot proces to take place was enough to again allow the key to be released.

Of course, there are options for suspending the protection. Windows will periodically need to service the boot files and things like firmware updates will change hashed components, so functionality exists to return BitLocker to the Suspended state. While in the OS, this can be done via WMI, BitLocker management APIs, or via the manage-bde.exe command:

```
manage-bde.exe –protectors –disable C:
```

Windows 8 and 8.1 allow you to specify how many reboots you would like to leave BitLocker suspended -- they default to suspending for just one reboot and automatically unsuspending after that. An augmented command might be:

```
manage-bde.exe –protectors –disable C: -RebootCount 3
```

You can also simply print the key to unlock the volume.

```
manage-bde.exe -protectors -get C:
```

### Wrapping Up

For repair scenarios, this should only prove a minor annoyance with machines that can still boot and log in. There, we can suspend BitLocker as needed and even print the key. But, other scenarios will require sourcing the recovery key via the online process. I can think of at least these:

* Computer doesn’t boot or log in.
* Computer was sent to service and had hardware replaced (either changing the hashes of firmware or removing the motherboard that had the TPM with the sealed key)
* Drive was removed to perform a data backup
Each of those will require the key.
