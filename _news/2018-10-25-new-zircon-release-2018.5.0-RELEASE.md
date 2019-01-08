---
excerpt: "A new Zircon version is released: 2018.5.0-RELEASE"
title: "New Zircon Release: 2018.5.0-RELEASE"
tags: [github, zircon, project-update, project-update-zircon, release, release-zircon]
author: hexworks
short_title: "New Zircon Release: 2018.5.0-RELEASE"
comments: true
---

## Highlights of this release

This release was mainly about making Zircon more robust and prepare it to be used in a multiplatform environment
(jvm, browser, native). There are also some new enhancements and features added, like:

- New True Type Fonts. Take a look [here](https://cdn.discordapp.com/attachments/363771631727804416/485181978031947787/unknown.png),
[here](https://cdn.discordapp.com/attachments/363771631727804416/485183071340199969/unknown.png),
[here](https://cdn.discordapp.com/attachments/363771631727804416/485189629817913344/unknown.png) and
[here](https://cdn.discordapp.com/attachments/363771631727804416/485192295604289560/unknown.png).
- Multi-size Tile support: [here](https://cdn.discordapp.com/attachments/205245036084985857/482557725432086538/unknown.png).
- Markov Chain based `Modifiers` like [bubbly lava effect](https://cdn.discordapp.com/attachments/363771631727804416/491268920217698304/lava.gif),
[glitch](https://cdn.discordapp.com/attachments/363771631727804416/496333815564730369/static.gif) and 
[static](https://cdn.discordapp.com/attachments/363771631727804416/496420195804381194/static.gif).
- Component decoration [refurbishment](https://cdn.discordapp.com/attachments/363771631727804416/493152143310585886/unknown.png).
- New components, like the [TextBox](https://cdn.discordapp.com/attachments/363771631727804416/497789071943401482/textbox.gif)
  which also supports inline `Component`s and typing effects
- The [LogArea component](https://cdn.discordapp.com/attachments/363771631727804416/497907392676233217/logscrolling.gif).
- New [color themes](https://cdn.discordapp.com/attachments/363771631727804416/480148560160096267/color_themes.gif).
- A new example [tutorial project](https://cdn.discordapp.com/attachments/363771631727804416/479419587507650580/GIF.gif) following
in the footsteps of Trystan's blog series.
- Embedded [images](https://cdn.discordapp.com/attachments/363771631727804416/473081792321159209/unknown.png)


## New Features

There are numerous new features in this release:

- [#100](https://github.com/Hexworks/zircon/issues/100): *Support all Input events on components properly.*

  Now all `Input` events can be properly listened to on `Component`s: `InputListener`s can be added or
  the specific listener methods can be called with a lambda like `onDrag` or `onMouseReleased`
- [#90](https://github.com/Hexworks/zircon/issues/90): *Add the option to enable beta features in Zircon*

  Now there is a switch in `AppConfig` which can be used to enable beta features like `Animation`s and
  the `GameComponent`
- [#89](https://github.com/Hexworks/zircon/issues/89): *Add the option to run Zircon in debug mode*

  This can also be enabled now from `AppConfig`. It will display specific log entries and also the current
  FPS. This feature will be enhanced later.
- [#76](https://github.com/Hexworks/zircon/issues/76): *Introduce ShutdownHook in Zircon*

  Now `ShutdownHook` can be used to notify the user before Zircon closes  
- [#75](https://github.com/Hexworks/zircon/issues/75): *Improve Animation to return an AnimationResult*

  Starting an `Animation` will now return a handle which can be used to interact with the `Animation` (like `stop`ping it).  
- [#54](https://github.com/Hexworks/zircon/issues/54): *Embedding images/bitmaps*  
- [#46](https://github.com/Hexworks/zircon/issues/46): *Implement "Log" Component*

  The `LogArea` component was implemented *(great thanks to **coldwarrl**!)*.
- [#8](https://github.com/Hexworks/zircon/issues/8): *TileColor transformations*

  The following transformations were implemented:
  - `tint`: adding a different color to this one
  - `shade`: lighten or darken the color
  - `invert`: flip the color to its opposite (blue vs red for example)
  

## Enhancements

These are the **enhancements** which were done in this release:

- [#112](https://github.com/Hexworks/zircon/issues/112): *Allow Position and Size to have negative values, and add a check in the components framework not to allow them there.*
- [#107](https://github.com/Hexworks/zircon/issues/107): *Reconcile the usage of properties vs functions (eg: size(), position() vs size and position*

  These were streamlined and the weird getters (`position()`, `size()`, etc) are now gone. Now there is a proper getter / setter
  for everything and mutator/transformer methods which return the object are prefixed with `with*`.
- [#105](https://github.com/Hexworks/zircon/issues/105): *Separate Modifier into tile transformer and texture transformer modifiers.*

  Now there are two kinds of `Modifier`s. One which modifies how textures are transformed, and one which can modify a `Tile` before
  it is rendered.
- [#103](https://github.com/Hexworks/zircon/issues/103): *Reconcile getTileAt, setTileAt, getRelativeTileAt and setRelativeTileAt.*

  These were "sanetized" now it is straightforward and well documented which is which and how to use them.
- [#102](https://github.com/Hexworks/zircon/issues/102): *Provide a proper `Snapshot` object for `snapshot()` instead of a `Map`*

  Now the `snapshot()` method returns a proper `Snapshot` object instead of just a `Map`. 
- [#84](https://github.com/Hexworks/zircon/issues/84): *Font to Tile refactor*

  This was a rather thorough change. Previously Zircon used the `Font` abstraction which was now refactored
  to `Tile` and `TilesetResource` which streamlines and simplifies how `Tile`s can be loaded and used.
  It is now also easier to write support for new kinds of fonts, tiles, and such.
- [#83](https://github.com/Hexworks/zircon/issues/83): *Refactor Blink handling and Animation handling to be part of the incremental rendering*

  `Blink` and `Animation` timing is now handled by the continuous renderer. This does not change how the API
  works, but makes the code of Zircon more robust, and less error prone.
- [#82](https://github.com/Hexworks/zircon/issues/82): *Continuous rendering*

  Now Zircon does continuous rendering: the user will no longer have to call `refresh` and other such methods
  in order to redraw the screen. The `Application` class was also introduced to accommodate this change.
- [#81](https://github.com/Hexworks/zircon/issues/81): *Multiplatform refactor*

  The majority of the code in `zircon.jvm` were moved to `zircon.core` which is a Kotlin common project.
  This will enable the development of javascript and native variants of Zircon.
  [#84](https://github.com/Hexworks/zircon/issues/84) made this possible. Now images can be drawn onto
  a `DrawSurface` using the `ImageDictionaryTilesetResource`. The related example can be found [here](https://github.com/Hexworks/zircon/blob/c9b3edebc2dc7ae66d58419900523f54579eb370/zircon.examples/src/main/kotlin/org/hexworks/zircon/examples/ImageTileExample.kt)
  and it looks like [this](https://cdn.discordapp.com/attachments/363771631727804416/473082395562737694/unknown.png). 
- [#74](https://github.com/Hexworks/zircon/issues/74): *Streamline how Positions and Sizes work*

  Previously it was not possible to create `Position`s with a negative component (`x`, or `y`), not it
  is possible, and it is only limited in specific places (like `Component`s)
- [#73](https://github.com/Hexworks/zircon/issues/73): *Improve TileGraphics performance*

  `TileGraphics` used arrays before, now it uses sparse `Map`s. This enhances performance, and also makes it possible
  to have `TileGraphics` with stupid size.  
- [#69](https://github.com/Hexworks/zircon/issues/69): *CombineWith should resize new image to fit TileGraphics*

  When `combineWith` is called on a `TileGraphics` it will properly be resized to accommodate the other image.
- [#67](https://github.com/Hexworks/zircon/issues/67): *Add word wrap to CharacterTileString*

  `CharacterTileString` now supports word wrap!
- [#61](https://github.com/Hexworks/zircon/issues/61): *Proper `FontTextureRegion` abstraction for different backends.*

  Previously `Tile` drawing for different backends (Swing, LibGDX, etc) was a bit convoluted. Now this is streamlined.
- [#60](https://github.com/Hexworks/zircon/issues/60): *Proper modularization depending on GUI backend*

  Zircon was split into multiple projects (`zircon.core`, `zircon.jvm`, `zircon.jvm.swing`, `zircon.jvm.libgdx`). This makes
  the artifacts more lightweight, and the codebase is also separated along the GUI framework backends.
- [#59](https://github.com/Hexworks/zircon/issues/59): *Component wrapping refurbishment*

  Component wrapping was refactored. Now `ComponentDecorationRenderer`s can be added to any `Component` during creation.
  This makes it possible to apply any decoration to any component. 
- [#55](https://github.com/Hexworks/zircon/issues/55): *Fix and test TextBox*

  This was a bit convoluted before, now it is better and has more tests.
- [#38](https://github.com/Hexworks/zircon/issues/38): *Half-width Tile support*

  Now it is possible to use `TilesetResource`s which are not square, there are also some added tilesets like
  `Acorn` and `Lord Nightmare`.
- [#32](https://github.com/Hexworks/zircon/issues/32): *Tile alignment by pixel*

  Now `Tile`s can be aligned by using `AbsolutePosition`. Note that this feature is still in beta.


## Bug fixes

The following **bugs** were *fixed*:

- [#111](https://github.com/Hexworks/zircon/issues/111): Fix RadioButton.select() to change the selection in its parent as well
- [#77](https://github.com/Hexworks/zircon/issues/77): Swing terminal is not being redrawn in response to exposure event 
- [#66](https://github.com/Hexworks/zircon/issues/66): Adding Components to a Container which is not yet attached to a ContainerHandler will lead to positioning problems
- [#65](https://github.com/Hexworks/zircon/issues/65): RadioButtonGroup throws stacktrace if you click empty space
- [#64](https://github.com/Hexworks/zircon/issues/64): Minimizing a swing terminal and then restoring it causes the screen to go grey
- [#47](https://github.com/Hexworks/zircon/issues/47): Adding a component to a container which has no font before attaching that container to a Screen leads to font error.


## Roadmap
  
There are several things on the radar, like:

- Refactor parallelism and concurrency to use Kotlin coroutines
- Implement a Javascript (browser) module and finish the LibGDX implementation
- Finish custom `Component` support
- Add **floating**, **modal** and **scrollable** components
- `Component` layout support
- Drag'n drop support
- Component themes
- Simple and easy to use data binding and MVC support

## Contribute

> If you think that you can contribute or just have an idea feel free to join the discussion on our [Discord](https://discord.gg/hbzytQJ) server.
