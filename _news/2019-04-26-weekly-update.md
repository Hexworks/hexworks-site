---
excerpt: "New tutorial article, progress bars and Graphical renderer for LibGDX"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon & Caves of Zircon Tutorial**

This week I finished [#9](https://hexworks.org/posts/tutorials/2019/04/17/how-to-make-a-roguelike-a-multi-level-dungeon.html)
in the tutorial which is about connecting the levels in our multi-level dungeon.
You can read it [here](https://hexworks.org/posts/tutorials/2019/04/17/how-to-make-a-roguelike-a-multi-level-dungeon.html).

**Zircon**

We were also working on upgrades to *Zircon*! Here are some:

- Now it is possible to `transform` `Layer`s and `TileGraphics` as well. This means that you don't have to call
  `setTile`/`getTile` but you can supply a lambda and it is done. This also works for `Tile`s as well with
  `transformTile`
- Now it is possible to set an `FPS` limit to Zircon! It only works for LibGDX, but we'll get it working for Swing
  as well
- `Button`s, and `CheckBox`es now accept empty strings as well
- Now `Layer`s support `hide`/`show`
- `TileGraphics` objects now only change when `setTile` is called if a different `Tile` is set (not equal to the
  previously existing `Tile`)
- `CheckBox` and `Button` now implement `disable`/`enable` fully with the corresponding `Property` so now you can
  use data binding with them!
