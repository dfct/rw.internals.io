---
layout: post
title: "Sizing PE's RAM drive"
tags:
- winpe
- drives
---


A new feature of PE 5 has the RAM drive automatically resize up to 512MB if the machine has more than 1GB of memory ( [MSDN](http://technet.microsoft.com/en-us/library/dn293271.aspx) ). I'd used PE builds that resized the RAM drive and certainly played with the [documented support](http://technet.microsoft.com/en-us/library/hh824936.aspx) for it, but I didn't know much about the underlying functionality that powered things. I spent some time learning about the mechanisms at work and uncovered some unexpected side effects of the PE 5 change.

### The X Drive

Let's start with the base. PE's X: drive is actually backed by a physical file. On boot, bootmgr loads the tiny read-only 3MB disk image boot.sdi and ties it against the WIM. This is what you see described by the odd paths in the BCD:

```
Windows Boot Loader
-------------------
identifier              {default}
device                  ramdisk=[boot]\sources\boot.wim,{7619dcc8-fafe-11d9-b411-000476eba25f}

....

Device options
--------------
identifier              {7619dcc8-fafe-11d9-b411-000476eba25f}
ramdisksdidevice        boot
ramdisksdipath          \boot\boot.sdi
```

Originally, the XP-based PE 1.0 functioned with just that 3MB read-only disk. Many applications expected to be able to write temporary files to the OS volume, though, eventually leading to the File Based Write Filter (FBWF) being added in the Vista-based PE 2.0.

### The File Based Write Filter

The FBWF is actually a feature from the [Windows Embedded](https://en.wikipedia.org/wiki/Windows_XP_editions\#Features_2) world. It projects a [RAM-backed file system cache](http://msdn.microsoft.com/en-us/library/aa940818(v=winembedded.5).aspx) on top of a volume and redirects write attempts to the cache. It's designed specifically to protect read-only or write sensitive storage while still providing a writeable medium for applications that need to write temporary data as part of their operation. Credit to the Windows Embedded team as there is a [plethora of documentation](http://msdn.microsoft.com/en-us/library/jj979636(v=winembedded.81).aspx) as to how to interact with the FBWF and how it's implemented. There we can learn that the FBWF supports [cache sizes](http://msdn.microsoft.com/en-us/library/jj962983(v=winembedded.81).aspx) of up to 1GB on x86 and max RAM size on x64, [multiple cache types](http://msdn.microsoft.com/en-us/library/ee832762.aspx\#Offline), and that the FBWF can be managed [via WMI](http://msdn.microsoft.com/en-us/library/jj980507(v=winembedded.81).aspx), [fbwfmgr.exe](http://msdn.microsoft.com/en-us/library/jj980317(v=winembedded.81).aspx), or via [API](http://msdn.microsoft.com/en-us/library/jj980469(v=winembedded.81).aspx). I investigated how FBWF settings are stored in the Windows Embedded world as a means of comparing them against Windows PE. Initially, there are a few options for registry-based control of the FBWF in Windows Embedded. Older editions of Windows Embedded supported configuring the FBWF via the [First Boot Agent (FBA)](http://msdn.microsoft.com/en-us/library/ff793679(v=winembedded.60).aspx). The functionality later moved to the MaximumCacheSizeInMB unattend setting [documented here](http://msdn.microsoft.com/en-us/library/dd873582.aspx). Post installation, the settings are established in the fbwf.cfg file in \Windows\; it is not editable directly, only via the supported FBWF interfaces. Due to an explicit WinPE check in FBWF (FbwfCheckForWinPEBoot), none of these methods appear to be useable in Windows PE. With the newer Windows Embedded releases (Windows 8+), the FBWF has been deprecated. Its functionality and that of a few other filter drivers has been absorbed by the [Unified Write Filter (UWF)](http://msdn.microsoft.com/en-us/library/jj979962(v=winembedded.81).aspx). In Standard 8, you can still install, configure and enable the FBWF, but things are powered by UWF behind the scenes. This change has not yet affected PE - as of Windows PE 5 (based on Windows 8.1) the FBWF is still in use.

### The FBWF in PE

Windows PE utilizes the File Based Write Filter to create an overlay cache on top of the 3MB X drive. Setting aside the change in PE5, the default overlay cache size is 32MB. The cache is user-configurable via DISM's [Set-ScratchSpace](http://technet.microsoft.com/en-us/library/hh824936.aspx) command, with acceptable values of 32MB, 64MB, 128MB, 256MB, and 512MB. DISM's command sets a WinPECacheThreshold DWORD in the HKLM\System\ControlSet001\services\FBWF key. Following the FbwfCheckForWinPEBoot check, FBWF pulls the overlay cache size from this value. There are reports you can also specify the cache type via a CacheType DWORD in the same key, however I have not been able to replicate this functionality in testing.

Knowing that FBWF can be expanded up to 1GB on x86 and all the way up to max RAM size on x64, I tried adjusting the WinPECacheThreshold value to hit higher cache sizes. In practice, WinPECacheThreshold can be set to size the cache to any size up to 1024MB (0x400) including arbitrary values such as 0x260 (608MB). Unfortunately, all values above 0x400, such as 0x800 (2048MB), were not respected. My guess here is that the PE functionality was coded to the 1GB x86 limit, and nothing more? Though I have no guess at why 1GB was not exposed. People have reported issues writing through to a full 1GB but I have not encountered that in my testing.

### The FBWF in PE 5

The change in PE 5 to automatically resize cache to 512MB on machines with 1GB or more appears to have effectively removed the ability to size the RAM drive beyond 512MB. With machines under 1GB of RAM, the cache size can be set as before. However, with machines with at least 1GB of RAM the WinPECacheThreshold value is ignored and the RAM drive is forced to 512. There does not appear to be a way to restrict this or otherwise respect the WinPECacheThreshold value.

I imagine if you really needed to you could port in the FBWF from PE4 (perhaps the fbwflib dll as well), but I haven't test this.
