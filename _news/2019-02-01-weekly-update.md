---
excerpt: "This week input handling was refactored to the new model, and now it supports event bubbling and capturing!"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon**

This week we refactored input handling in Zircon which also comes with 2 new features: *event bubbling* and
*event capturing*. The model works in a similar way as it works [in the browser](https://cdn.discordapp.com/attachments/527956007901724672/532686794798661653/eventflow.png).
When an event arrives into the system first we find the target `Component`, then traverse the path to that component:
this is the capture phase. Any listener can grab the event and say that it handled it, and the propagation stops.
After reaching the target, there is bubbling, which can also be stopped by listeners.

This will make a lot of things possible which were cumbersome to implement so far such as using [Mnemonics](https://en.wikipedia.org/wiki/Mnemonics_(keyboard))
in menus and key bindings for controls like buttons!

There were also some minor changes:

- [#187](https://github.com/Hexworks/zircon/issues/186) The `ToggleButton`'s builder ignored `isSelected`, so this got fixed.
- [#186](https://github.com/Hexworks/zircon/issues/186) There was a small bug in the `TextArea` as well which caused an
  exception when [Del] was pressed in certain situations.
  
**Caves of Zircon Tutorial**

With the new input handling in place we'll create a release next week for Zircon and we can continue with the tutorial!
Keep tuned for the next article soon!  
  
**Amethyst**

Ever since Caves of Zircon was released people have been perusing the source code and the ECS(ish) lib which we've started
working on got picked up by some folks so they asked for some features. So `Entity` objects are now modifiable:
you can add/remove `Attribute`s, `Facet`s and `Behavior`s as well. We also made the API more abstract which will
enable us to refactor the internals to use Kotlin coroutines later to enable Amethyst to be concurrent / parallel
without the problems which come with excessive thread usage.
