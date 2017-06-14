---
layout: post
title: "How to actually get a Borderless / Frameless window with Qt on Windows and macOS"
tags:
- windows
- macOS
- dpi
- qt
---


I’m picky about how my apps look, so I want to control the entire application area. Windows and macOS both draw title bar chrome on their windows that aren’t easily controllable from an application. Removing this chrome is surprisingly frustrating. Qt has a “Qt::FramelessWindowHint” attribute that draws a frameless window as described, but it comes at the cost of Min_Max_Close, resize, and on Windows, Aero Snap functionality. You can reimplement Min_Max_Close/Resizing yourself, but I find it super annoying not having Aero Snap.

There exist drop-in projects for Windows like [BorderlessWindow](https://github.com/deimos1877/BorderlessWindow) & [lib sleek](https://github.com/leafcode/libsleek) that aim to address this, but they a require min 1px border (yuck) to reveal the native window underneath your Qt widget for resizing support. They also ship with their own title bar implementation including Min_Max_Close buttons, which takes away from my goal of having full control of a true borderless/frameless window. The last deal breaker for me was that these projects don’t support Hi-DPI — if you set Qt::AA_EnableHighDpiScaling, their drawing logic ends up off by whatever the devicePixelRatio factor is (usually two). And then of course, I still need to implement macOS support.

Anyway.

If you’re looking for a true frameless window that can still be resized & Aero Snap’d on Windows, check out [TrueFramelessWindow](https://github.com/dfct/TrueFramelessWindow). It has both Windows and macOS support, and I did my best to comment every piece clearly.
