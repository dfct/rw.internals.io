---
layout: post
title: "Defrag vs. Volume Shadow Copies: Everyone Loses"
tags:
- defrag
- vss
- winpe
- drives
---

I wasn't old enough to have seen the impact of defragging in the DOS and pre-Win2k days. When I joined the repair world Windows XP was the primary OS and there was years of built up defrag tribal knowledge. Without doubt, defragging was a key part of any tune up.

Windows Vista introduced scheduled, automatic defragging, and the E7 blog was explicit in stating that it worked[1]. Nowadays, SSDs are commonplace. Windows 7 stopped defragging them completely, going so far as to drop caching techniques like prefreching.[2] While I don't see defrag as something anyone needs to touch anymore, I imagine this still varies based on who you talk with. Tools like [Defraggler](http://filehippo.com/download_defraggler/history/) are still being developed.

I digress. I recently encountered a defrag casualty other than time itself that I couldn't let go. It turns out, defrag kills shadow copies.

### Don't touch my shadows

Shadow copies have become a critical part of repairing Windows. Need to roll back to a previous system state via System Restore? Just want some registry hives, but need them older than what's in regback? Delete a Word document, or were hit by the document-encrypting Moneypak infection? Shadows had your back on all of them.

Anything that caused the loss of shadow copies impacted the ability to repair a machine. People took note that mounting a Vista+ volume on a Windows XP machine deleted shadows[3]. This resurfaced again with mounting a Windows 8+ volume on a Windows 7- machine.[4] For me, if a machine mounts other Windows volumes it better be Windows 8+.

Even with those cases trained out and documented, I still saw reports that shadows were disappearing after repair operations in Windows PE. But, I wasn't able to reproduce it in my lab testing.

### Defrag, the Shadow Killer

I stumbled into the answer last week - [KB article 312067](http://support.microsoft.com/kb/312067) describes behavior where defragging kills shadow copies by tripping the copy-on-write protection until the shadow storage space has been completely exhausted. I found a machine that hadn't been reimaged recently in my lab and was able to reproduce this immediately. (Well, as immediately as defragging allows). Boot Windows PE, kick off defrag, and poof, shadows disappeared.

Similar behavior is described in [KB 977521](http://support.microsoft.com/kb/977521), where FCI classification jobs modify a bunch of files with metadata and exhaust available shadow storage space. And, in support I suppose, my test machines all reached a point where they were not fragmented enough to reproduce the issue anymore.

I didn't test inside of Windows, so I'm unsure if the scheduled defrag operations can impact shadows. If anyone has tested this, shoot me a note! The confirmed behavior in Windows PE ( [which is supposed to be safe](http://blogs.technet.com/b/filecab/archive/2007/01/17/how-third-party-tools-can-affect-restore-points.aspx) ) is enough to permanently remove defrag from my toolkit. If your OS is newer than Windows XP, you better have a hell of a case to manually defrag!

- - - -

[1] Check the intro in [this E7 blog post](http://blogs.msdn.com/b/e7/archive/2009/01/25/disk-defragmentation-background-and-engineering-the-windows-7-improvements.aspx). Sinofsky states, "In Windows Vista we decided to just put the process on autopilot with the intent that you’d never have to worry about it. In practice this turns out to be true [..]". The rest of the article talks about Defrag improvements partially in the context of appeasing people who were annoyed at the lack of visibility to Defrag in Vista. [2] See [Support and QA for Solid State Drives](http://blogs.msdn.com/b/e7/archive/2009/05/05/support-and-q-a-for-solid-state-drives-and.aspx) [3] The [File Cabinet Blog](http://blogs.technet.com/b/filecab/archive/2006/07/14/441829.aspx) has some discussion of why this happens. Microsoft also posted [this KB article](http://support.microsoft.com/kb/926185) for it. [4] I'm still not aware of a KB article that mentions this behavior.
