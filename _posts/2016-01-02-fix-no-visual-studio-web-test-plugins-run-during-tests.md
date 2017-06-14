---
layout: post
title: "Fix: No Visual Studio Web Test Plugins Run During Tests"
tags:
- webtests
- visualstudio
---


I ran into a problem last week where none of the three web test plugins I had in my project would run when executing the web test. Two were built-in Microsoft plugins, one was a custom one. There wasn't any error handling in Visual Studio to make the problem obvious.. They just didn't ever set context parameters nor could I hit break points in the custom plugin.

Long story short: if Visual Studio is unable to load a single Web Test Plugin, none of the web test plugins will fire.

In my case, Visual Studio was largely silently failing to load the YourProjectName.dll file that my custom web test code was compiled into. There was a single 'File Not Found' error in the Output tab, but this was surrounded by a lot of symbol loading messaging and did not trip an actual error dialog. After a bunch of hacking around, I landed at two causes:

1. Web Tests in a folder in a project fail to load custom plugins. I don't know why this is.
2. Changes to the namespace of a Custom Plugin are not reflected in .webtest files the plugin has been added to. You must remove the plugin, reload Visual Studio or the solution (it remembers the plugin and does not write new info unless you do this), then re-add the plugin to the webtest
I hope this helps if you run into something similar. If your situation doesn't completely match mine here, there were two tricks that aided me in figuring this out that may help. Specifically, while debugging, open up Debug -> Windows -> Modules and confirm that the project/plugin DLLs you expect to have loaded are in fact loaded. If something is missing, probably related! Additionally, try [enabling Exceptions for Common Language Runtime code](https://msdn.microsoft.com/en-us/library/d14azbfh.aspx). This turned my tiny output error message into a full stop, failed to load this file exception during debugging.

Good luck!
