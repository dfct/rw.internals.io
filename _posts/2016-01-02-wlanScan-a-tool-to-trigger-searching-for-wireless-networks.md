---
layout: post
title: "WlanScan - A tool to trigger searching for wireless networks"
tags:
- wireless
- wlanscan
---


I wrote a tool to trigger scans for wireless networks on Windows. Annoyingly, no built-in executable will actually call the API to ask the wireless driver to scan for networks. netsh.exe, for example, just shows the list the system already has on hand. Anyway, so here's [WlanScan, available on Github](https://github.com/dfct/WlanScan).:

![]({{ site.url }}/assets/img/wlanscan.png)
Output from /?:

```
WlanScan - A small utility for triggering scans for wireless networks.

   /triggerscan                 Triggers a scan for wireless networks.
   /shownetworks                Shows visible wireless networks.
   /showprofiles                Shows saved wireless network profiles.

Version: 0.0.1
```

The x86 binary was compiled with Visual Studio 2013, so you'll need the [Visual C++ 2013 redistributables](http://www.microsoft.com/en-us/download/details.aspx?id=40784) installed to run it as is. Of course, you can also compile wlanscan.cpp with the compiler of your choice as well. Features like parsing profiles ended up in there as this also became a fun introduction to Wlan apis :)
