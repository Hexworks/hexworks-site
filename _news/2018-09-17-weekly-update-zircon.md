---
excerpt: This week was spent devising how component wrapping should be done. We also made some upgrades to how TileGraphics works.
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
---

This week:

- We figured out a way to properly do wrapping / decorating of components. /u/zidsal and /u/coldwarrl already gave some feedback on the concept
  but we'd also like to show it to you: [here](https://cdn.discordapp.com/attachments/363754040103796737/489199738239844363/Component_rendering.jpg).
  The whole thing is intended to be easy to understand so if it is not clear feel free to point out the problems.
  In a nutshell we'll have `ComponentRenderer`s. Each `Component` will have its own and the decorators will also have one. Rendering will be done
  from the outside to the inside and each `Renderer` will only have access to the area which it is supposed to touch.
- We've also implemented `SubTileGraphics` which is just like a `TileGraphics` but it maintains a "window" around an actual `TileGraphics`.
  With this construct we can restrict which parts of a `TileGraphics` can be edited. You can also create `SubTileGraphics`' out of
  a `SubTileGraphics` leading to *SubTileCeption*.
- There were a lot of recent POCs and new additions with only exploratory tests written for them so far. This week we have paid down a lot of
  this technical debt so there were **a lot** of new unit tests added.
- [Here](https://cdn.discordapp.com/attachments/363771631727804416/490143537912872961/buttons.gif) is an example of the implemented component rendering (buttons) concept.


A next major release is coming up for this Autumn, so **stay tuned.**
