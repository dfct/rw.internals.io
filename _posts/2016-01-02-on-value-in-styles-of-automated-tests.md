---
layout: post
title: "On Value in Styles of Automated Tests"
tags:
- automation
- testing
- quotes
---


Test suites, in particular regression suites, see diminishing returns over time.

Its "beating a dead horse" of sorts, over time, there is only so much value to be derived by putting the same inputs into a system.

This leaves me with an uneasy feeling as to where, precisely, the value is in some automated tests. Selenium tests in particular generate this feeling more than other automation at levels under the GUI.

- - - -

This tweet from Michael Bolton really resonated:

![]({{ site.url }}/assets/img/bolton.png)

It suddenly clicked why [the Galen framework](http://galenframework.com/), [fuzzing](https://en.wikipedia.org/wiki/Fuzz_testing), or rolling tests where you continuously export that file &#124;&#124; continuously run that calculation and compare results just to the previous run, seemed so powerful, felt ‘right’ in some way. These techniques are not locked to a specific set of steps, a specific start_end_expected result. They’re not even as tied to specific on-page logic, IDs, etc.; they can have far less dependency on the actual product code than say, Selenium or Coded UI tests.
