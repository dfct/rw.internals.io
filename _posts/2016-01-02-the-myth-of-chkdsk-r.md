---
layout: post
title: "The Myth of chkdsk /r"
tags:
- chkdsk
- diagnostics
- drives
---


Advice I encounter consistently in the tech support world is to run chkdsk. Listening to this advice, you might think chkdsk could fix everything including boot issues, crashes, slow internets, bad Linux installations and the measles. It's great! If you have a problem and haven't run chkdsk yet, are you even _trying_?

This is grating to me because I prefer to understand the specific issue at play and solve the problem from there. Throwing a list of repair tricks like chkdsk at a problem is slow and uninteresting, even if doing so long enough may fix the problem.

Granted, this is equivalent of being mad at reality. I can't control what other people do, so why should I let this bother me? I can usually logic my way out of annoyance riiight up until the advice is to run chkdsk /r. No!

### /R for Repair, Right?

The chkdsk command to repair filesystem issues is /F. Optionally, /I or /C may be used to skip some of /F's checks for faster but less thorough repair. But that's it. Those are the filesystem repair choices. So why would someone run /R?

_R does the same work as /F, and then scans the volume for bad sectors with the ATA Read-Verify command. If it encounters a bad sector it tries poorly to recover the information. Thus, /R is an extremely slow way to get the repair benefits of /F with the added bonus of performing a bad hard drive diagnostic that loses data stored in bad areas. Citations: [1](http:technet.microsoft.com_en-US_library_cc730714(v=ws.10).aspx),[2](http://support.microsoft.com/kb/187941),[3](http://technet.microsoft.com/en-us/library/bb457122.aspx),[4](http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/chkdsk.mspx?mfr=true),[5](https://en.wikipedia.org/wiki/Chkdsk).

I mean, let's examine this. NTFS, during regular operation, automatically maps clusters with bad sectors into it's $BadClus file. In doing so, it takes the /R action of encountering, logging, and remapping a bad sector. So if you run chkdsk /R and something is found, it is in a part of the disk NTFS hasn't failed to access yet! It's not related to your problem!

Furthermore, _R's use Read-Verify is a poor diagnostic. /R only hits the parts of the drive under the NTFS volume, meaning you haven't scanned the entire disk. Anything found is logged internally but not messaged well as a problem to the user. The SMART logs aren't ever checked with /R, and, if NTFS /has_ remapped anything previously, those areas aren't touched. You can't count on _R to report something. (The fix for this is /not_ to move to /b which rechecks previously identified bad sectors). And the attempts to remap data can end up with NTFS trashing the record when can't be read, hampering any actual data recovery process that may be needed down the line.

There's no upside to /R. It's just /F with slow, poor, and destructive drive diagnostics. If you're trying to fix filesystem issues, stick to chkdsk /F. If you're worried about your hard disk failing, check the SMART and Event Logs for any logged issues, and run a proper diagnostic tool.* But whatever you do, please do not run chkdsk /R!

- - - -

*A proper diagnostic tool would be the diagnostic provided by the manufacturer. For hard drives, that'd be Seatools for Seagate, WD Diag for Western Digital, Hitachi's Drive Fitness Test, etc. If you can't find a diagnostic for your drive's manufacturer, perhaps look into a third party diagnostic like PC Doctor.
