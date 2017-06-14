---
layout: post
title: "Loading PE 4 requires a new bootmgr"
tags:
- bootmgr
- winpe
---


I ran into an unexpected issue with PE 4.0 recently. It seems there's a bug specific to some part of PE 4.0 x86 that stops it from booting successfully with old versions of bootmgr. Interestingly, PE 4.0 x64 was unaffected.

I tested the following bootmgr versions against PE 4.0 x86 and x64:

**BootMgr Version** | **Operating Systems** | **PE 4 x86** | **PE 4 x64**
11/02/2006 - 24b0294e6866186685ba5f0e03daaf2f | Vista x86 SP0<br/>Vista x64 SP0 | Fail | Success
01/19/2008 - 9e24b834dc6fc0634c28004721df9d82 | Vista x86 SP1<br/>Vista x64 SP1<br/>Server 2008 x86 R1<br/>Server 2008 x64 R1 | Fail | Success
04/11/2009 - 14b9d882551ec9ffb3c51a7d94c4266c | Vista x86 SP2<br/>Vista x64 SP2 | Fail | Success
07/13/2009 - d6ae2d5521dd93aebc90d411d099fa36 | Windows 7 x86 SP0<br/>Windows 7 x64 SP0<br/>Server 2008 x64 R2 | Fail | Success
04/11/2011 - 259525cfb422e6ac8e87bc9777b1df73 | Windows 7 x86 SP1<br/>Windows 7 x64 SP1 | Fail | Success
07/26/2012 - 21bf183c15afe62a8d1137bb9007b2a3 | Windows 8 x86 SP0<br/>Windows 8 x64 SP0<br/>Server 2012 x64 R1 | Success | Success
08/22/2013 - 04c91d3ab4cdb766c34a8dc5372ba09e | Windows 8.1 x86 SP0<br/>Windows 8.1 x64 SP0<br/>Server 2012 x64 R2 | Success | Success


The Fail cases see the machine reboot, load the entire WIM into memory (as shown by the loading bar across the bottom of the screen), but then never transition from that loading bar to some portion of initializing the kernel / loading drivers. Visibly, the loading bar remains on screen forever and the Windows 8 or vendor logo never appears. By the by, the boot.sdi file has not changed at all since Vista SP0, which rules it out from being related to the issue.

If you replace bootmgr with a copy from Windows 8 or 8.1, PE 4.0 x86 loads without issue. Replacing bootmgr should be safe to do.. The boot sector and BCD have retained static formats since being introduced in Vista, so older versions of Windows should have no issue with a newer bootmgr file being present. But hey, I didn't predict this loading issue, so what do I know? :)

Whatever the bug was, the issue isn't present with PE 5.0 x86 or x64. Both load fine with all of the above bootmgr versions!
