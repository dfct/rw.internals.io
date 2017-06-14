---
layout: post
title: "Some Notes on WinSxS and Windows Update"
tags:
- windowsupdate
- winsxs
---


I used to avoid Windows Update issues. One look at the CBS.log file and I was sprinting in the other direction. A few months back I decided it had been long enough, so I settled in for a weekend and found every piece of content I could on anything Windows Update. If it covered Side-By-Side, WinSxS, Windows Update, CheckSUR, or similar, I was reading it.

Now, I find Windows Update issues fun :)

I've been able to use the understanding I built here to do some cool stuff! I'm excited to get posts up that show pratical examples. In the meantime, I wanted to share my notes from that weekend. What follows is half quotes, half regurgitation, and a lot of citations. I hope it can help you as much as it helped me!

- - - -

**The High-Level Overview: Servicing Windows broken down**

Joseph Conway, the “Windows Servicing Guy”, is a former Windows support engineer that runs an awesome blog on TechNet. He's amazing. Many of the quotes I’ve pulled below are from posts on his site or posts written by him elsewhere. In particular, he authored two posts that describe how servicing windows works. The first covers the basic terminology. The second steps through how packages and manifests are read for a given feature of Windows. They’re wonderful overviews to jumpstart an understanding of component based servicing. Check them out here:

* [Servicing Windows: Part One](http://blogs.technet.com/b/joscon/archive/2010/06/15/servicing-windows-part-one.aspx)
* [Servicing Windows: Part Two](http://blogs.technet.com/b/joscon/archive/2010/07/06/servicing-windows-part-two.aspx)
He also later authored a post explaining things via a grocery list analogy. You can find it [here](http://blogs.technet.com/b/joscon/archive/2010/10/21/what-s-on-the-grocery-list.aspx).

- - - -

**Vista and it’s Component Store – Quotes**

* Windows Vista was a move from an INF described OS to componentization. In previous versions of Windows the atomic unit of servicing was the file, in Windows Vista it’s the component.

* A component in Windows is one or more binaries, a catalog file, and an XML file that describes everything about how the files should be installed. From associated registry keys and services to what kind security permissions the files should have.

* Components are grouped into logical units, and these units are used to build the different Windows editions.

* The WinSxS folder is the only location that the component is found on the system, all other instances of the files that you see on the system are “projected” by hard linking from the component store. Let me repeat that last point – there is only one instance (or full data copy) of each version of each file in the OS, and that instance is located in the WinSxS folder.

* When we update a particular binary we release a new version of the whole component, and that new version is stored alongside the original one in the component store. The higher version of the component is projected onto the system

* As each component on the system changes state that may in turn trigger changes in other components, and because the relationships between all the components are described on the system we can respond to those requirements in ways that we couldn’t in previous OS versions.

Reference: [TechNet AskCore Blog](http://blogs.technet.com/b/askcore/archive/2008/09/17/what-is-the-winsxs-directory-in-windows-2008-and-windows-vista-and-why-is-it-so-large.aspx)

- - - -

**Directories that make up the WinSxS component store – Quotes**

* \Winsxs\Catalogs: Contains security catalogs for each manifest on the system
* \Winsxs\InstallTemp: Temporary location for install events
* \Winsxs\Manifests: Component manifest for a specific component, used during operations to make sure files end up where they should
* \Winsxs\Temp: Temp directory used for various operations, you’ll find pending renames here
* \Winsxs\Backup: Backups of the manifest files in case the copy in \Winsxs\Manifests becomes corrupted
* \Winsxs\Filemaps: File system mapping to a file location
* \Winsxs\<big_long_file_name data-preserve-html-node="true">: The payload of the specific component, typically you will see the binaries here.
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2010/08/06/should-you-delete-files-in-the-winsxs-directory-and-what-s-the-deal-with-vss.aspx)

- - - -

**Breaking Down the Naming Convention in WinSxS**

If you ever wanted to know how the folder names in WinSxS came to be, a Microsoft employee has a great blog post that breaks down how components are named. Check it out [here](http://blogs.msdn.com/b/jonwis/archive/2005/12/28/507863.aspx).

- - - -

**On SFC – Quotes**

* From an elevated command prompt, run SFC /SCANNOW. This command will project files from the component store (\Windows\winsxs) to the proper location in the file system. Sometimes its as easy as just making sure that the right file is there for a fix to install properly.
* To see an example run,

```
>fsutil.exe hardlink list C:\Windows\System32\drivers\ntfs.sys

\Windows\System32\drivers\ntfs.sys
\Windows\winsxs\amd64_microsoft-windows-ntfs_31bf3856ad364e35_6.1.7600.16385_none_02661b64369ca03a\ntfs.sys
```

* Running an SFC /SCANFILE against it will check the directories above and if the version doesnt match, it will re-project it so that its working.

Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/06/16/help-me-help-you.aspx) Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2010/08/06/should-you-delete-files-in-the-winsxs-directory-and-what-s-the-deal-with-vss.aspx)

- - - -

**The CBS Log File: Structure, Terms, and Errors**

The file C:\Windows\logs\CBS\CBS.log is the primary log for all things Component Based Servicing, which is whole a lot. There’s some great documentation of how the log is structured as well as what the various installation states are and some common errors. Check it out on [the TechNet Library here](http://technet.microsoft.com/en-us/library/cc732334(v=WS.10).aspx#CBS).

- - - -

**Enabling Verbose CBS logging – Quotes**

* To enable verbose CBS logging is easy:

```
1. NET STOP TRUSTEDINSTALLER
2. Add the following system environment variable: WINDOWS_TRACING_FLAGS with a value of 10000.  *NOTE: This does not require a reboot to take affect.
3. NET START TRUSTEDINSTALLER
```

Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2010/11/18/how-to-enable-verbose-cbs-logging.aspx)

- - - -

**Windows Update Error Codes**

There’s a great list of Windows Update error codes provided by a Microsoft employee that trumps [what MSDN covers](http://technet.microsoft.com/en-us/library/cc720442(WS.10).aspx). Check it out [here on mvps.org](http://inetexplorer.mvps.org/archive/wuc.htm).

- - - -

**Microsoft Instructions on Reacting to CheckSur Errors**

Microsoft has an MSDN article that documents how to respond to various checksur errors. It includes instructions on where to place files so that CheckSur will find and use them. Check it out [here on the TechNet Library.](http://technet.microsoft.com/en-us/library/ee619779(v=ws.10).aspx)

- - - -

**On the Speed of CheckSur – Quotes from Joseph Conway**

* The main thing is the spindle speed of the disk on the system you are running the tool on. This is because the CheckSUR utility comes as a packaged payload. We create the \Windows\CheckSUR directory on your system and then unpackage the contents of the utlity to your local drive before we ever run the actual tool. This takes up the majority of the time (~75-80%). The rest of the time is used for running the tool and generating the log file. If you have a large amount of corruption on the system, such as a failed service pack, then it may take a little longer but in general the tool should be usable in about 15mins from the time the installer starts.

* If you need to run the utility again on the system, you should be able to just re-run the utility you've downloaded (assuming you kept it) and it will be much quicker because it doesnt need to rebuild the directories for the \Windows\CheckSUR directory and can just run the tool. All of the log files for the utility are held in the \Windows\Logs\CBS\CheckSUR.log file. We will recreate this file each time the utility is run so the only the most current entries will be in the log.”

In the comments, he is quickly attacked for that time. He defends it saying that's been his experience over hundreds of machines.

Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2010/05/11/why-does-checksur-take-a-long-time-to-run.aspx)

- - - -

**Windows Component Platform Interface API**

I’m not sure how helpful this may be, but Microsoft has Component Platform Interface API that’s designed to be used up at the imaging / WIM level. But it defines a whole bunch of information that’s still applicable. For example, it defines all of the package actions, the package types, the varies levels of installation, etc. Check it out [here on systemscenter.ru.](http://systemscenter.ru/cpiapi.en/index.html?page=html%2Fn_microsoft_componentstudio_componentplatforminterface.htm)

- - - -

**Installing a Side by Side Component**

At one point, I was trying to figure out how to install something into WinSxS myself. I wanted to know how to build the manifest, .cat file, etc. Microsoft has pretty terrible documentation on it, and no quality walkthroughs. But I did find a CodeProject page that explains what you need to do! It involves using a few binaries that ship with Visual Studio and then, to actually do the install, using Windows Installer. I found it pretty informative, check it out [here on codeproject](http://www.codeproject.com/Articles/29235/Installing-and-Using-side-by-side-native-assemblie).

- - - -

**Activation Contexts and how Side by Side works for Programmers**

There’s a blogger on the internet who got really annoyed with WinSxS and created a blog just to document how it works for programmers. It’s an informative read and links out to many resources.. I would caution that this isn’t really tied to Windows Update at all; it’s more of a light into another side of WinSxS. Check it out [here from omnicognate.](http://omnicognate.wordpress.com/2009/10/05/winsxs/)

There’s also a shorter article on the same subject by a Microsoft employee that has some super basic info on how it’s used at runtime. Not as informative as the first article, but [here it is regardless.](http://blogs.msdn.com/b/junfeng/archive/2006/03/19/sxs-activation-context-activate-and-deactivate.aspx)

- - - -

**Dism and Rolling Back Changes with Pending.xml – Quotes**

```
DISM.exe /Image:C:\test\offline /Cleanup-Image /RevertPendingActions
```

* Where this is handy is in situations where you have installed an update on an installation and the machine gets stuck applying "Stage 3 of 3". You can boot into WinRE in Windows 7, run this command against the installation from the command prompt and it should allow you to rollback the changes that the update was attempting to apply. When you enable this command and reboot, you should see a blue splash screen that shows the updates being reverted.
Windows 7+. Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/07/14/the-coolest-new-dism-command-to-me-at-least.aspx)

- - - -

**Package Manager (PkgMgr) vs. Dism – Windows 7+, pkgmgr was deprecated and DISM is what is used – Quotes**

* Because PKGMGR doesnt do any real checking against dependancies for things such as required reboots, customers would sometimes find themselves in the position of having random component store corruption, or post deployment corruption because of a few manually installed updates that both had reboot requirements.
* have a "one stop tool" that does both the online and offline servicing of images and installations in Windows 7. Enter DISM.exe. DISM (Deployment Image Servicing and Management Tool) will act as the replacement for several tools that existed with Windows Vista and 2008. The largest of which is PKGMGR. Anything you could do with PKGMGR you can do with DISM. The structure is a little different but the core concepts are the same. The main difference is that DISM has built in logging, which is logged to \Windows\Logs\DISM\DISM.log and that you can specify what kind of servicing you wish to do (online or offline).
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/07/09/doing-things-differently-with-windows-7.aspx)

- - - -

**On MSU Files – Quotes**

* Well an MSU is nothing more than the cabiner file in a wrapper. That's why you can always use the EXPAND command against the MSU to get the underlying cabinet files for the fix.
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2010/01/08/new-year-answers.aspx)

- - - -

**On the 'searching' stage during MSU installation – Quotes**

* What is going on there is we're checking the package to make sure its complete and pulling down any deltas that might be needed for the fix to your \SoftwareDistribution folder. Deltas are smaller packages that might be needed for the update to work properly.
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/07/15/addressing-some-comments-given-so-far.aspx)

- - - -

**On reboots to resolve pending update installation – Quotes**

* If you have an update that pends a reboot, please, reboot the machine. Because of the way the servicing stack works, we need to flush out information that is pertinent to that updates installation first before we can do additional servicing. This can, and often does, lead to corruption. You cannot QCHAIN updates like you could in the past.
* Deferring a reboot is not what is causing the corruption in this case, its attempting to force in other updates when another update has an operation pending. When this happens, you have mechanisms in the stack that are waiting to do something (registry, files, whatever) and if they arent done in order, you can hit something like this.
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/07/15/addressing-some-comments-given-so-far.aspx)

- - - -

**On the C:\Windows\servicing\sessions\sessions.xml log file – Quotes**

* One of the other cool new logs, as it relates to servicing, is the new sessions.xml log. Located in the \Windows\servicing\sessions directory, its a log of all of the different transactions that happen on the machine from a servicing perspective.
* Because this is log is in XML, you can collapse all but the transactions that you are interested in seeing. The one thing I like about this log is that it tells you exactly what is happening with each package in a particular fix or update.
* For example, in the sample above you can see that KB972636 was installed on this machine recently. It was installed by the WindowsUpdate Agent and it did not require a reboot. This is all really good information to know when trying to troubleshoot an issue with servicing in Win7. Using this, you might be able to tell if a particular package didn’t get staged properly. Or, if a reboot was required and you’re in a reboot loop issue, then you know that you can use the new DISM /revertpendingactions flag and roll yourself back.
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2009/07/22/more-win7-coolness.aspx)

- - - -

**On Uninstalling Updates – Quotes**

* Uninstalling an update moves it from the installed state to the staged state, files remain in the component store
Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2011/11/30/how-does-windows-choose-which-version-of-a-file-to-install.aspx#3471716)

- - - -

**On Fixing CSI Errors – Quotes**

* The only thing I see here is CSI corruption, that's not something IBCR [In Box Corruption Repair] is meant to recover as far as I know, which is why its failing. Unfortunately, a lot of CSI based issues aren't repairable without a repair install. Does your event log show anything like disk corruption or other errors recently?
The CBS log from that comment is here: http://pastebin.com/25G0FUrq

Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2012/09/26/fixing-component-store-corruption-in-windows-8-and-windows-server-2012.aspx#3553852)

- - - -

**On the In-Box Repair Functionality in DISM in Windows 8 – Quotes**

* And above all else, Inbox Corruption Repair can repair both payload files and manifests (CheckSUR only did manifests) which is a HUGE win for you and I as customers.
I imagine this refers to CheckSUR knowing how to rebuild a manifest but needing to have the files in the accompanying cab files to repair payload files, vs. Dism on Windows 8 that can now pull down files from Windows Update itself and no longer has that dependency.

Reference: [TechNet Joscon Blog](http://blogs.technet.com/b/joscon/archive/2012/09/26/fixing-component-store-corruption-in-windows-8-and-windows-server-2012.aspx)

- - - -

**On Installing Service Packs – They’re Pretty Different – Quotes**

* During service pack installation, we populate the pending.xml file with all of the files and registry values needed to install a particular update. Service packs are special in that they are broken into critical and non-critical transactions to allow us to recover more quickly and reduce the no-boot window that could occur during installation.
* During system shutdown, we process all of the critical transactions first and then the non-critical transactions. If we fail processing the critical transactions, the service pack will just fail and rollback. If the critical operations succeed but the non-critical operations fail, we attempt to process them on reboot using Session Manager (smss) and the SetupExecute registry value. When the system reboots and reads the SetupExecute key, it retries installation first and if that fails it will roll back the Service Pack installation. Deleting the registry value tells smss to not try and run the poqexec. It should be reattempted again during startup processing or fail outright. So effectively deleting the registry value breaks you out of the install fail reboot loop that the machine ends up being in.
Reference: [TechNet AskCore Blog](http://blogs.technet.com/b/askcore/archive/2011/03/11/why-you-don-t-want-to-edit-your-pending-xml-to-resolve-0xc0000034-issues.aspx)

- - - -

**Windows Update Process**

For Windows XP, the \Windows\WindowsUpdate.log tracks the Windows Update agent as it goes to install things. Public documentation of the log and its stages is only ok; the best I could find is [this KB article](http://support.microsoft.com/kb/902093).

Vista+ changed the update architecture and again there is only poor public documentation. There was once an Understand and Troubleshoot guide for servicing written by Joseph Conway, but it was pulled from Microsoft's Download Center temporarily yet never replaced. The content was awesome though. Someone uploaded [a copy of the guide here](http://www.scribd.com/doc/171906454/Understand-and-Troubleshoot-Servicing-in-Windows-Server-8-Beta), TBD on if it gets pulled later.
