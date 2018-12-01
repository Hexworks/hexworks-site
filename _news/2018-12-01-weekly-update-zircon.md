---
excerpt: "We're now nearly halfway through with Caves of Zircon! We also added some minor improvements to Zircon"
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
---

This week:

- We had great progress with [Caves of Zircon](https://github.com/Hexworks/caves-of-zircon)!
  [This](https://cdn.discordapp.com/attachments/509142267735310338/518211950232993808/GIF.gif) gif shows my player
  digging through cave walls and dispatching fungi. What's interesting is not visible here: we dumped the idea of
  a straight port of Trystan's tutorial, instead we created a very simple ECS system for this game which works nicely!
  [This](https://cdn.discordapp.com/attachments/509142267735310338/518210579417137152/unknown.png),
  [this](https://cdn.discordapp.com/attachments/509142267735310338/518210476950290437/unknown.png),
  [this](https://cdn.discordapp.com/attachments/509142267735310338/518210218858119181/unknown.png),
  and [this](https://cdn.discordapp.com/attachments/509142267735310338/518209850233323521/unknown.png) shows
  alternative color palettes which we have tried. It is not final yet, but we're getting there.
- There are also some things we improved in Zircon like component alignment: [this](https://cdn.discordapp.com/attachments/363771631727804416/515978944948994048/component-positioning.png)
  shows all the permutations of aligning components within and around other components.
- @Milo implemented *ellipse drawing* (thx, Milo!)
- Layers now can be cleared
- Input handling was upgraded a bit, now you can listen to specific `KeyCombination`s and individual keys as well
