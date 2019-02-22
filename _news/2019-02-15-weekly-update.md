---
excerpt: "Input handling upgrades, infinite modals and integration testing!"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon**

This week was crunch time at my workplace so I didn't have much time to work
on Zircon. We're preparing a release for the end of the month and my time is
tightly packed right now.

What we did have time to do is to listen to the feedback which was given about
the new input handling so the bugs [#206](https://github.com/Hexworks/zircon/issues/206)
and [#207](https://github.com/Hexworks/zircon/issues/207) are now fixed.

We also implemented the *modal in modal* feature so now you can open a dialog
and another one within the previous one. In [this screenshot](https://cdn.discordapp.com/attachments/363754040103796737/544982831378726913/GIF.gif) you can see this in action!

We've also started writing user interface tests. Zircon comes with a `VirtualApplication`
and a `VirtualRenderer` which can be used to run Zircon with a renderer which only renders
in memory. We used this for calculating the framework overhead so far but as it turns out
it is also useful for testing the UIs. We'll add some examples in the near future to help
you set this up. The best thing is that everything in Zircon is decoupled from the GUI
framework behind the scenes (Swing, LibGDX) so you can test anything from keyboard input
to complex component events as well!
