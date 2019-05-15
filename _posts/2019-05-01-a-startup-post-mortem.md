---
layout: post
title: "A Startup Post-Mortem"
tags:
- startup
---

For the last couple of years, I've been building up a software testing startup, Verrify. Unfortunately, I haven't been able to push it across the finish the line, and it's time to put this startup to bed.

Verrify's key idea is that you have to experience errors to handle errors, yet there are entire classes of errors -- especially network ones -- where tooling doesn't exist to do so. As a result, our best efforts can give us a beautiful, smooth happy paths full of unnoticed gaps in the guard rails. 

What happens when a network request times out? Or returns 301? Or when an internal service goes down and you can complete the first half of an operation but not the second? Maybe, raise your hand if you have a Gogo Inflight WiFi at your office to experience how your code handles captive portals? Without tooling to test these scenarios, we can't write code that handles them. We need to be able to crash our code into the guard rails. 

That's what Verrify set out to do. I still believe in this core idea -- so where did I fail?

---

There were plenty of unexpected technological hurdles to overcome -- such as Apple deprecating the macOS network kernel extension system I was using, or the undocumented 15MB RAM usage limit on iOS network extensions -- but it wasn't ever software holding me back. Instead, I believe I made two key mistakes that were both rather trope-ier. 

For one, I didn't start shipping straightaway. It's surely one of the most commonly repeated pieces of advice -- just start shipping, get something out there even if its bad, and iterate with user feedback. I knew this, but clearly hadn't internalized it. I spent a lot of time building and rebuilding until I was spinning wheels in polished ruts. I knew things /could/ be better and kept aiming above 'good enough', never quite reaching there. 

Second, I tried to go it alone. While I started fast out the gate, over time I became less and less productive. I now understand why VCs prefer you to have a cofounder -- an extra set of hands, a great check on my aim, yes, but also simply a person to share the journey with. At least personally, long term remote work isn't for me. I'm more effective working around/near/or with a team.  

---

Either shipping from the beginning or having a partner along for the ride would have shifted Verrify's course somewhere farther down the road, successful or otherwise. But, while I wasn't able to achieve what I set out to, it's not as if all was lost. I've become a far more capable programmer, with several years more experience in C, C++, Objective-C and Swift. My knowledge of networking is night and day what it was when I started. I have far greater understanding & respect for the inner workings of a business and what's involved in running one. And I better know myself, my strengths and my weaknesses.

I'm excited to jump back into a team and go again from there. 