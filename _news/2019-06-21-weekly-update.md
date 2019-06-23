---
excerpt: "New tutorial article & Tileset Upgrades"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Caves of Zircon Tutorial**

This week was about [Weapons and Armor](https://hexworks.org/posts/tutorials/2019/06/21/how-to-make-a-roguelike-weapons-and-armor.html)
in *Caves of Zircon*. The article is ready and you can read it on [the site](https://hexworks.org/posts/tutorials/2019/06/21/how-to-make-a-roguelike-weapons-and-armor.html).

**Zircon**

Apart from that we worked on the fleshing out of the layout concept in Zircon. We already have the functions in
place to re-orient the `Component`s when resizing happens, so we just need to program the resizing itself.

@Seveen, became a **contributor** to Zircon this week (yay!), if you're interested in the stuff he is working
on you can find it [here](https://github.com/Seveen).

@MrPancake came up with an idea this week, we're going to implement soon! This is going to be a new `Tileset`
type ([take a look](https://cdn.discordapp.com/attachments/363754040103796737/590972510263312413/unknown.png))
which is very similar to CP437 but it is not constrained to `16x16` tiles but can be of arbitrary size. This is
like a combination of *Graphical Tilesets* and *CP437 Tilesets*. They can be colorized by using `TileColor`s
but are graphical tiles instead of ASCII. [Take a look](https://kenney.nl/assets/bit-pack/sample_fantasy.png) at an example.
