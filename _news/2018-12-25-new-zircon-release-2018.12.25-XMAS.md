---
excerpt: "A new Zircon version is released: 2018.12.25-XMAS."
title: "New Zircon Release: 2018.12.25-XMAS"
tags: [github, zircon, project-update, project-update-zircon, release, release-zircon]
author: hexworks
short_title: "New Zircon Release: 2018.12.25-XMAS"
comments: true
---

> Zircon is an extensible, multiplatform and user-friendly tile engine.
>
> If you are not yet familiar with the project take a look at our
> [Project Page](https://hexworks.org/projects/zircon/).

> You can grab this release from Jitpack [here](https://jitpack.io/#org.hexworks/zircon/2018.12.25-XMAS).
> Maven Central synchronization will come soon as well.

## Highlights of this release

This release contains numerous usability upgrades and the introduction of new `Component` types
like `Modal`s and the `Icon` `Component`. We also worked on fleshing out the inner workings of
the `GameArea` based on feedback from our users.

We also started working on [Caves of Zircon](https://github.com/Hexworks/caves-of-zircon) which
is a tutorial project which has the goal to get you started with writing roguelike games!
The project is almost complete and when it is ready a series of tutorial articles will follow.
Take a look at some game footage [here](https://cdn.discordapp.com/attachments/509142267735310338/522176966984597545/GIF.gif).

Apart from a tutorial project there is now also a Java and a Kotlin skeleton project which you
can download to get started with Zircon without the need to manually set up a project.
The Java skeleton can be found [here](https://github.com/Hexworks/zircon.skeleton.java)
and the Kotlin one is [here](https://github.com/Hexworks/zircon.skeleton.kotlin). 

What's also important to mention is that now we have a **LibGDX Renderer** for Zircon! It is in
beta right now, but we'll get it to production-ready state for the next release. Thanks for @Ender_L
for contributing it to the project! With this it is now possible to **run Zircon in the browser**.
We'll put up an example with this soon.

## New Features

- [#1](https://github.com/Hexworks/zircon/issues/1): *LibGDX rendering support*
  Yea, **this was the first issue** in our issue tracker and now we have it in BETA.
  Thanks for the contribution, @Ender_L! [These](https://cdn.discordapp.com/attachments/363754040103796737/521809619186745347/unknown.png)
  screens [are](https://cdn.discordapp.com/attachments/363754040103796737/521808369242210347/unknown.png) rendered
  using the new [LibGDX renderer](https://cdn.discordapp.com/attachments/363754040103796737/521807314190073857/unknown.png).
  CP437 fonts are supported now together with `Input` handling. `Animation`, `Modifier` and the remaining tileset
  support will come in the next release! 
- [#24](https://github.com/Hexworks/zircon/issues/24): *Modal (dialog) support*
  This one was on the Road map for quite some time, but it is ready now! Take a
  look at this [example](https://cdn.discordapp.com/attachments/363771631727804416/514477514161389599/dialog_example.gif).
- [#121](https://github.com/Hexworks/zircon/issues/121): *Programmatic focus for Components*
  Now it is possible to programmatically focus `Component`s. Take a look at an example
  [here](https://cdn.discordapp.com/attachments/363771631727804416/510230515429670922/focus_handling_example.gif).
- [#153](https://github.com/Hexworks/zircon/issues/153): *Implement Ellipse Factory*
  Now you can draw not only triangles, rectangles and boxes, but also ellipse shapes!
  Thanks for @Milo for this contribution!
- [#156](https://github.com/Hexworks/zircon/issues/156): *Databinding Iteration 1*
  Zircon now supports databinding. It looks like [this](https://cdn.discordapp.com/attachments/363754040103796737/525042125151141939/data_binding_with_labels.gif).
  This feature might be familiar from JavaFX or some other UI framework. The basic premise is that
  you can create `Property` instances which you can bind to other properties and their values
  will change together. Databinding is possible uni-, and bidirectionally and it is implemented
  for the basic `Component`s in Zircon.
- [#171](https://github.com/Hexworks/zircon/issues/171): *Fragment Support*
  A `Fragment` is a re-usable `Component` container which contains a `root` `Component`
  (which can also be a `Container` and can be added to either a `Container` or a `Screen`.
- [#172](https://github.com/Hexworks/zircon/issues/172): *Implement the Icon Component*
  An `Icon` `Component` is a regular `Component` which has a size of 1x1 and holds a single `GraphicTile`.
  It looks like [this](https://cdn.discordapp.com/attachments/363754040103796737/526092651775393809/unknown.png)
  and can be used to put icons on your UI.
  


## Enhancements

- [#132](https://github.com/Hexworks/zircon/issues/132): `TextArea`s now can be [enabled and disabled](https://cdn.discordapp.com/attachments/363771631727804416/509836757798354954/text_area_enable_disable.gif)
  once again. This feature was removed previously because of some technical limitations, but now it is fixed.
- [#133](https://github.com/Hexworks/zircon/issues/133): `Layer`s are now clearable
- [#145](https://github.com/Hexworks/zircon/issues/145): `Component` alignment is now easier
  with the new alignment functions. Take a look [here](https://cdn.discordapp.com/attachments/363771631727804416/515978944948994048/component-positioning.png).
- [#152](https://github.com/Hexworks/zircon/issues/152): Borders now support custom width
  and also custom color like in [this](https://cdn.discordapp.com/attachments/363754040103796737/519083254284943360/unknown.png)
  example.
- [#154](https://github.com/Hexworks/zircon/issues/154): Handling `Input`s are now easier with
  some new convenience features like `whenInputTypeIs` and `whenCharacterIs`.
- [#155](https://github.com/Hexworks/zircon/issues/155) `GameArea` now supports adding overlays.
  This is useful for creating things like Fog of War effects as seen in [this](https://cdn.discordapp.com/attachments/509142267735310338/525811202551447553/coz_xp.gif)
  example.
  

## Bug fixes

- [#122](https://github.com/Hexworks/zircon/issues/122): Weird effect if label has FadeIn modifier
- [#158](https://github.com/Hexworks/zircon/issues/158): When different tilesets are set only one of them is used.
- [#128](https://github.com/Hexworks/zircon/issues/128): Bug in README.md document example

## Roadmap
  
There are some things which did not make it into this release, but will be present in the next release,
like the [Group Component](https://github.com/Hexworks/zircon/issues/123) and
[Event Bubbling](https://github.com/Hexworks/zircon/issues/126).

Apart from those we have some things which are quite interesting:

- Browser support with Pixi.js
- Floating windows
- Toolbars
- Drag'n drop support
- Concurrency / parallelism refactoring to use Kotlin Coroutines
- Scrollable `Component`s
- Slider widget
- Drop down menus
- Custom `Component` support
- Layout support

## Contribute

> If you think that you can contribute or just have an idea feel free to join the discussion on our [Discord](https://discord.gg/hbzytQJ) server.
