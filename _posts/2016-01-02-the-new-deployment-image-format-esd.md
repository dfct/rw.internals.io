---
layout: post
title: "The new deployment image format: ESD"
tags:
- adk
- esd
---


The Assessment and Deployment Kit for Windows 8.1 officially introduced a new deployment image format - .ESD files.

I say 'officially introduced' as the format was already being used by Microsoft. Consumers downloading Windows 8 upgrade bits were pulling down an install.esd file as opposed to the typical install.wim.

I did some light research into the format during the ADK preview. If you're already familiar with .ESD files there won't be anything new here. Otherwise, read on!

- - - -

Documentation on the new format is extremely minimal. I can find it referenced on TechNet in three places:

* [What's New in the Windows ADK for Windows 8.1](http://msdn.microsoft.com/en-us/library/dn247001.aspx)
* [DISM Image Management Command Line Options](http://technet.microsoft.com/en-us/library/hh825258.aspx)
* [Shrink the Push-Button Reset Image](http://technet.microsoft.com/en-us/library/dn293447.aspx) The last article best outlines the new format. ESD files are extremely compressed versions of a source WIM. They trade the ability to be mounted_serviced and decompression speed for a much smaller file size. According to the article, they have only one use: as an 'install.esd' file during the Reset_Refresh process.
I wanted to understand what degree of size savings ESD offered so I tested the different compression options against an unmodified PE 5 x64 WIM. These were the export results:

Original File Size | 167MB
Max Compression | 167MB
Fast Compression | 186MB
No Compression | 447MB
Recovery (.ESD) | 114MB

The Recovery (.ESD) option has a clear victory in file size. It's understandable why Microsoft would want to use it when facillitating internet-driven upgrades.

### Opening the ESD

Install.wim files have proved a useful source of known-good registry keys and system files in the past. Cracking them open with DISM or 7-Zip is a common repair step. Unfortunately, things have not gone well with the install.esd files.

There have been attempts in [forums like this one](http://forums.mydigitallife.info/threads/39597-Convert-ESD-to-WIM?p=809114&amp;viewfull=1#post809114) to try to figure out how to open the ESDs, but to my knowledge no one has an answer yet. Standard attempts to mount with dism or open the archive with 7-zip definitely fail. I'm sure tearing into the push button reset feature could reveal a bit more here, but I haven't taken time to try to do so. Windows ISOs from sources such as MSDN still use install.wim, so this currently only affects internet upgrades. As OEMs start to use install.esd files over install.wims, this may become a bigger pain point.

### Booting via an ESD file

Given the size savings, one question was whether a hypothetical 'boot.esd' file could be used in place of a boot.wim.

Unfortunately this does not appear to be possible! I tried booting to a boot.esd created with DISM and a boot.wim, but it errors instantly trying to access winload.exe. If you examine what Microsoft pulls down for the Windows 8 upgrades, a traditional boot.wim is still being used to head into PE and apply the install.esd. The bootmgr file was updated for Windows 8.1, but it wasn't with .esd support!

If anyone makes any headway with either opening or booting ESD files, I'd love to know! Shoot me a note :)
