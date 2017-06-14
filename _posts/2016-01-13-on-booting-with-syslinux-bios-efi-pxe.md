---
layout: post
title: "On Booting with SYSLINUX, BIOS, EFI, & PXE"
tags:
- syslinux
- bootmgr
- pxe
---


I looked into the history of SYSLINUX & PXE tooling around the time Windows 8 was making its way into the world. EFI was forcefully replacing BIOS machines, and multi-boot options were pretty limited. These are the notes I had.

- - - -

SYSLINUX, used for booting from FAT and NTFS filesystems (such as floppy disks and USB drives).
ISOLINUX, used for booting from CD-ROM ISO 9660 filesystems.
PXELINUX, used for booting from a network server using the Preboot Execution Environment (PXE) system.
EXTLINUX, used to boot from Linux ext2/ext3/ext4 or btrfs filesystems.
MEMDISK, used to boot older operating systems like MS-DOS from these media.

EXTLINUX merged into SYSLINUX with version 4.

- - - -

BIOS Booting & Modes
* Hard drive: BIOS reads first 512 bytes, copies to RAM, executes it
* USB Drives: Treated as hard drives.
* El Torito: BIOS reads reference to linear blocks in the ISO 9660 header, copies to RAM, redirects either floppy or hard drive access interrupts to that region, boot code proceeds as if it had been read off a floppy
FLOPPY EMULATION MODE
The boot information is stored in an image file of a FAT-formatted floppy disk, which is loaded from the CD and then behaves as a virtual floppy disk. This mode uses SYSLINUX.

NO EMULATION MODE
The boot information is stored directly on the CD (not in a floppy image). This mode uses ISOLINUX.

ISOLinux can directly boot disk image files but the BIOS support for this can be very sketchy. Not all BIOS support the INT 13h 4Ch disk emulation interrupt. [http://www.oldlinux.org/Linux.old/docs/interrupts/int-html/rb-0720.htm](http://www.oldlinux.org/Linux.old/docs/interrupts/int-html/rb-0720.htm) 

MEMDISK does not inherit those compatibility problems because MEMDISK simulates a disk by claiming a chunk of high memory for the disk and a (very small - 2K typical) chunk of low (DOS) memory for the driver itself, then hooking the INT 13h (disk driver) and INT 15h (memory query) BIOS interrupts.

Original ISOHybrid implementation takes an ISO image, adds an x86 partition table + boot sector + some fiddling so you simultaneously have a valid ISO image and an image you can dd straight to a disk.

- - - -

EFI Booting & Modes
* EFI Implementations are required to understand FAT (only).

* EFI supports both Emulation and No Emulation mode.
* EFI supports a type flag to distinguish between images for BIOS booting and EFI booting.
* Hard drive: FAT partition with file in well-known location
* USB drives: FAT partition with file in well-known location
* El Torito: Uses emulated FAT image. Image is otherwise identical as the requirements for hard/USB drives.
[http://mjg59.dreamwidth.org/4957.html](http://mjg59.dreamwidth.org/4957.html)

- - - -

PXE, gPXE, & iPXE History & Booting

[https://www.youtube.com/watch?v=GofOqhO6VVM](https://www.youtube.com/watch?v=GofOqhO6VVM) 

Etherboot project started in 1994

* mknbi, make network boot image
* Etherboot worked with DHCP, got back a filename, pulled it down via tftp, off you went
* gone once tftp process done
* saves a step (no 2nd stage loader), but cost is you have to wrap your kernel via the mknbi command to send it down

PXE spec started in 1999.

* DHCP, gets filename, pulls in a 2nd stage loader (nbp, network boot program), and THAT loads what you actually want, say, the kernel, or whatever.
* Early PXE implementations were super buggy, spec is very suboptimal / parts are unimplementable.
* The PXE spec said boot program could not be more than 32kb long. No one actually implemented this, but, spec is scary.

Etherboot project had to follow PXE, industry was totally PXE driven. So Etherboot created an open source PXE implementation. And HPA was being dragged into troubleshooting broken PXE stacks via his work on the SYSLINUX project, floppy based then. HPA took SYSLINUX, intended for floppies, and ported into PXELINUX.
Rom-O-Matic.net created around 2000. Boot rom vending machine. The Etherboot mailing list was full of people trying to get to the project but not wanting to compile things.

Etherboot was forked to gpxe.org, Etherboot project leader quit, Marty took over & moved gpxe back. They embraced and extended the PXE standard, added a TCP stack, an http stack. Was less than 2kb of code, crazy. Got involved with the Google Summer of Code, been involved for three+ years.

gPXE hooks the BIOS and fakes an additional disk in the system (int 13h). Needs a driver to get a proper OS to recognize it, same situation with memdisk. They drop a table in base memory and have the driver scan for it, has the info needed to find the disk.

gPXE will scale better than regular PXE due to the advantages of TCP over TFTP
How does gPXE work with integrated ethernet?

* No standard for how option ROMs should be integrated into a BIOS
* Some BIOS have flashing tools but lots do not
* But, for that there is PXE chain loading
PXE Chainloading

* PXE loads the 2nd stage nbp like normal, except the nbp is gPXE!
* Still too complex for many people, so, gpxelinux
* gpxelinux.0
* Drop in replacement for pxelinux.0
* Can do everything pxelinux can do
* gPXE can also just be run from floppy, dos, USB, etc.
gPXE became iPXE. Current state (at time of Win 8 launch)

* Can be chained via traditional PXE
* Can be launched via CD / floppy / USB, can use it's own PXE stack
* Supports HTTP HTTPS and other protocols, far faster
* Can boot directly via a WIM **without** the usual wim-in-memory requirement
* Partnered with pxelinux as ipxelinux, drops into the same infrastructure
* Check out [http://boot.slitaz.org/en/](http://boot.slitaz.org/en/) , boot.kernel.org, and
* This uses iPXE to load [http://mirror.slitaz.org/pxe/pxelinux.0](http://mirror.slitaz.org/pxe/pxelinux.0)
* [http://www.syslinux.org/~erwan/syslinux-osl.pdf](http://www.syslinux.org/~erwan/syslinux-osl.pdf)

