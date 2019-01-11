---
excerpt: "New tutorial article is out! We also have a new splash screen from now on for Caves of Zircon."
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

This was an interesting week. Most importantly **the next tutorial article is out!**
Take a look at it [here](https://hexworks.org/posts/tutorials/2019/01/05/how-to-make-a-roguelike-generating-random-caves.html).
Commenting was also enabled on the site so you can leave your 2 cents there. This article
is an in-depth guide to design a game UI, and generate a game world (caves in our case).
In the next we'll explore input handling and add a simple ECS(ish) library to the project.


For **Caves of Zircon**:

- We finalized the **Zircon splash screen**! Take a look [here](https://cdn.discordapp.com/attachments/509142267735310338/532089835054301184/coz_splash.gif)
  We had [several versions](https://cdn.discordapp.com/attachments/509142267735310338/531962820523327488/unknown.png)
  [previously](https://cdn.discordapp.com/attachments/509142267735310338/531950637567574018/unknown.png)
  and we kept this in the end. It was also added to the game. These gifs are actual game
  footage.
- There were also some small problems which were fixed (the `LogArea` bug for example
  which I detail below).


For **Zircon**:

- **All components now support data binding!** This means that the problem which most
  of you mentioned (immutable components) is now history! The properties which can
  be changed were also extracted into a separate behavior. Each property can have
  listeners attached to it so you can listen to changes.
- We did the same with selections. Now you can do `isSelected` and `setSelected`
  and listen to the selection event with `onSelectionChanged`.
  So what this means is that now you can do all 3 operations on `text` and `selection`
  values which we intended to add: **listen on changes**, **get/set values** and
  **bind properties**. Binding is very simple. All you have to do to bind
  `fooLabel`'s text to `barLabel` is to do `fooLabel.textProperty.bind(barLabel.textProperty)`
  We'll add a convenience feature later which will look like this `fooLabel.bindTextPropertyTo(barLabel)`
- `Icon`s now support any kind of `Tile` not just `GraphicTile`. The `tile` of an
  `Icon` is a `Property` so you can change and/or bind it on the fly
- Fixed a bug in `Size` which tested `Position` containment
- A bug was fixed which occurred when no `size` was set for `TextArea`s. Now it is
  mandatory (as it was intended originally)
- `LogArea` had a bug which prevented it to be properly wrapped with a box. Now this
  works.
- The default width of a `TextBox` is now `1` (it was `0` originally) so now it is
  not possible to get an exception because of a division by 0.
- `View`s now have a default value for `theme`s so you don't need to specify it
  if you don't want to
- All of this is present in the latest [preview version of Zircon!](https://jitpack.io/#org.hexworks/zircon/2019.0.9-PREVIEW).  
