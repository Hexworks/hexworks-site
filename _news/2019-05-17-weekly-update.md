---
excerpt: "New tutorial article, HBox and VBox implemented + debug mode improvements"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Caves of Zircon Tutorial**

This week I finished [#12](https://hexworks.org/posts/tutorials/2019/05/15/how-to-make-a-roguelike-items-and-inventory.html)
in the tutorial which is about writing an inventory system complete with an inventory dialog.
You can read it [here](https://hexworks.org/posts/tutorials/2019/05/15/how-to-make-a-roguelike-items-and-inventory.html).

**Zircon**

This week was pretty intense. We worked on a wide array of things:

- First of all now *Zircon* has `HBox` which aligns its children from left to right horizontally
  ([screenshot](https://cdn.discordapp.com/attachments/363754040103796737/576844557619167273/hbox.gif))
- And `VBox` which complements `HBox` and aligns from top to bottom vertically
  ([screenshot](https://cdn.discordapp.com/attachments/363754040103796737/577826304561512458/vbox.gif)).
  With the combination of these two it is now a trivial task to implement complex UIs or components like
  [this table](https://cdn.discordapp.com/attachments/363754040103796737/577832143322087435/unknown.png).
- We also started working on some improvements to the debug mode, so now there is a way to highlight
  the grid when *Zircon* is run in debug mode. Take a look 
  [here](https://cdn.discordapp.com/attachments/363754040103796737/577765635925344296/unknown.png).

You can expect more upgrades to this mode in the near future!
