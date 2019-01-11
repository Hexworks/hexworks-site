---
excerpt: "We released the Christmas Release this week, and also the first version of Caves of Zircon."
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---
We released the next version of Zircon this week! More about it [here](https://hexworks.org/posts/news/2018/12/25/new-zircon-release-2018.12.25-XMAS.html).

For **Caves of Zircon**:

The first version of the project is now released! We've moved the ECS(ish) library to its
own project: [Amethyst](https://github.com/Hexworks/amethyst). This is in BETA right now,
but it will be production ready soon. If Ashley was simple than this is super-simple, it only
has like 10 classes right now but it gets the job done.

In the game itself the Player was nerfed a bit, and the monsters were buffed. The inventory
and the equipment screens were separated. [This](https://cdn.discordapp.com/attachments/509142267735310338/528338992249438239/unknown.png)
is how the equipment screen looks now and [this](https://cdn.discordapp.com/attachments/509142267735310338/528339267651502099/unknown.png)
is the new Inventory screen.

The goals were also re-aligned, so now the goal is to find the exit and gather as much Zircons
as possible along the way while keeping yourself alive against the onslaught of bats, zombies, and hunger.

[This](https://cdn.discordapp.com/attachments/206169610284826626/528302967514988545/coz_gameplay.gif) is a speed run which I did. I lowered the levels to 2 because the gifs were getting
too big. :)

For **Zircon**:

There were some minor upgrades and fixes this week:

- @Ender_L upgraded the LibGDX renderer this week, so the black borders are gone from the
  viewport. Asset loading was also upgraded, now it uses an Asset Manager
- Now the `text` of the `ToggleButton` is mutable. It uses data binding behind the scenes
  but now you can conveniently do `toggleButton.text = "foo"` instead of using the
  `Property` abstraction
- `Button`s now can be enabled / disabled
- Now options can be removed from the `RadioButtonGroup`
- Now there is a method in `Position3D` to determine whether the position is unknown (`isUnknown`)

