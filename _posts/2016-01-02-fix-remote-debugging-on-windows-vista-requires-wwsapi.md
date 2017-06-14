---
layout: post
title: "Fix: Remote Debugging on Windows Vista requires WWSAPI"
tags:
- debugging
- visualstudio
---


If you're looking to use the Remote Debugger on Windows Vista or Server 2008, it requires the Windows Web Services API. The WWSAPI binaries aren't available from Microsoft anymore; even the old get-them-from-Windows-Live-installers trick has stopped working.

If you have landed here in the same boat as I was in, here are the binaries you seek:

* [Windows6.0-KB959175-x86.msu]({{ site.url }}/assets/bin/Windows6.0-KB959175-x86.msu)
* [Windows6.0-KB959175-x64.msu]({{ site.url }}/assets/bin/Windows6.0-KB959175-x64.msu)
