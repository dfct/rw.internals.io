---
layout: post
title: "Removing the red bars from Parallels app icons on OS X"
tags:
- parallels
- macOS
---


If you, like me, despise the red bars overlaid onto app icons in the dock, then this is for you. Parallels doesn’t have an option to just not overlay things onto the icons. But in Parallels’ app package, you can edit the three files it pulls from to create them such that the overlay is gone. (A big thanks to this post on the Parallels forum - [https://forum.parallels.com/threads/fixed-the-red-icon-bars-heres-how.107273/](https://forum.parallels.com/threads/fixed-the-red-icon-bars-heres-how.107273/) )

If you want to skip editing work, here are the three files already modified in a zip: [Parallels%20Icons.zip]({{ site.url }}/assets/bin/Parallels%20Icons.zip)

Just replace the ones in the Parallels’ package Resources folder, disable shared applications, restart Parallels, and re-enable shared applications to regenerate the icons without the mask.
