---
excerpt: "New tutorial article, example upgrades, and progress with the new HBox"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon & Caves of Zircon Tutorial**

This week was about [Wandering Monsters](https://hexworks.org/posts/tutorials/2019/05/07/how-to-make-a-roguelike-wandering-monsters.html)
in *Caves of Zircon*. The article is ready and you can read it on [the site](https://hexworks.org/posts/tutorials/2019/05/07/how-to-make-a-roguelike-wandering-monsters.html).

We also did some minor upgrades on the site which improve readability on mobile devices.

This is also a great moment for us because we now have **more than 100 people on our [Discord Server](https://discordapp.com/invite/PE3qFmF)!**

**Zircon**

Some of the old examples got a retrofit, so now all examples work on the site properly.

We finally started working on the `HBox` and the `VBox` *component* which are just *containers* which automatically
aligns their items left to right (horizontally) or top to bottom (vertically). It looks like [this](https://cdn.discordapp.com/attachments/363754040103796737/576844557619167273/hbox.gif).
It also has automatic reordering if a *component* is removed from the middle. We took the idea from TornadoFX where
this two components are enough to cover 95% of the layout use cases!

Apart from this we also added some new ways to load *graphical tilesets* so now it is possible to bundle your game
into a jar and put all your *graphical* tilesets in it and use it with Zircon.

We upgraded the data binding in *Cobalt* a bit which means that *Zircon* also received the upgrades so
now `bind` and `unbind` works properly as you can see in [this](https://cdn.discordapp.com/attachments/363754040103796737/575041169734762498/bindings.gif)
example and now the whole thing is also much more easier to use. The databinding code necessary for
[this](https://cdn.discordapp.com/attachments/363754040103796737/575044598187950151/bindings.gif) example
to work is just this: `checkBox.enabledProperty.updateFrom(selectedProperty.not())` or if you want to use
operators this: `checkBox.enabledProperty.updateFrom(!selectedProperty)`.
