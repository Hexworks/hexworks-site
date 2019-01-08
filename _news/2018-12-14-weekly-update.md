---
excerpt: "Preliminary LibGDX renderer implementation for Zircon is ready! Also there are some new fetures in Caves of Zircon."
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

This week was pretty exciting!

For **Caves of Zircon**:

- there is now [hunger](https://cdn.discordapp.com/attachments/509142267735310338/522176966984597545/GIF.gif) and equipment so the player can actually die of starvation
  and equip items. These items also have combat stats so they increase effectiveness.
- Food can be picked up from corpses (mushrooms from fungi and bat meat from bats) and eaten
- Zombies were also added and there was a fun bug with them [here](https://cdn.discordapp.com/attachments/509142267735310338/522532949950857226/GIF.gif).
  They tried to path to the player when the player become visible for them and the algorithm was bugged and returned the tile where the zombie
  were as the first tile to go to. So the zombie tried to enter the tile where it already was and found a zombie there (big surprise!). Default
  action when it bumps into an entity is `Attack` so zombies were literally beating themselves to death when they saw the player! This was
  my most fun bug this year!
  
  For **Zircon**:

- /u/Ender_L finished with the preliminary implementation of a LibGDX renderer for Zircon. [These](https://cdn.discordapp.com/attachments/363754040103796737/521809619186745347/unknown.png)
  screenshots [are](https://cdn.discordapp.com/attachments/363754040103796737/521808369242210347/unknown.png) using the
  [LibGDX renderer](https://cdn.discordapp.com/attachments/363754040103796737/521807314190073857/unknown.png). Basic functionality is
  working, but it is not yet ready for production use (some things like Modifiers and Animations are not added yet). This also means
  that with LibGDX you'll be able to use Zircon from the browser!

  What's nice is that to get this working you'll only need to switch the `swing` Maven dependency to the `libgdx` one and change this

```kotlin
val tileGrid = SwingApplications.startTileGrid(AppConfigs.newConfig()
        .withDefaultTileset(tileset)
        .withSize(Sizes.create(60, 30))
        .build())
```

  to this:

```kotlin
val tileGrid = LibgdxApplications.startTileGrid(AppConfigs.newConfig()
        .withDefaultTileset(tileset)
        .withSize(Sizes.create(60, 30))
        .build())

```

Apart from this there were some other improvements to Zircon, like:
- `EllipseFactory`: now you can draw ellipsis `Shape`s as well, not just lines, rectangles and triangles.
- Input handling was upgraded, now you can listen for specific characters and `InputType`s
- `Layer`s are now `Clearable`
- There are now only **7 tasks left** for the Christmas release!
