---
excerpt: "A new Zircon version is released: 2020.2.0-RELEASE"
title: "New Zircon Release: 2020.2.0-RELEASE"
tags: [github, zircon, project-update, project-update-zircon, release, release-zircon]
author: hexworks
short_title: "New Zircon Release: 2020.2.0-RELEASE"
comments: true
---

> Zircon is an extensible, multiplatform and user-friendly tile engine.
>
> If you are not yet familiar with the project take a look at our
> [Project Page](https://hexworks.org/projects/zircon/).
>
> The kdocs can be found [here](https://hexworks.github.io/zircon/). Javadocs are also coming soon!
>
> You can grab this release from Maven Central [here](https://search.maven.org/search?q=g:org.hexworks.zircon). More on how to add *Zircon* as a dependency to your project can be found [here](https://hexworks.org/zircon/docs/2019-01-11-release-process-and-versioning-scheme).

> Also if you have any questions about this release, feel free to come up to our [Discord Server](https://discord.com/invite/vSNgvBh) and ask them!

## TL;DR For the Impatient:

- [Stacked Tiles](https://cdn.discordapp.com/attachments/363754040103796737/775365392498819092/unknown.png)
- [Android Renderer prototype](https://cdn.discordapp.com/attachments/363754040103796737/720279635325354015/android.gif)
- [Javadocs](https://hexworks.github.io/zircon/) (finally)
- (not just) [Color filters for the Game Area](https://cdn.discordapp.com/attachments/363754040103796737/778393384380792833/pyramids.gif)
  - Another example [here](https://cdn.discordapp.com/attachments/603285896829206548/787418009080823838/unknown.png)
- [Switching between projection modes on the fly](https://cdn.discordapp.com/attachments/603285896829206548/784811176080572416/map_generation_3d.gif)
- Proper depreciation cycle!
- New [Color Themes](https://cdn.discordapp.com/attachments/363754040103796737/703397248838664201/themes.gif)
- [Padding support](https://cdn.discordapp.com/attachments/363754040103796737/786288342945103912/unknown.png)
- Performance optimization: **8x** FPS!
- New Fragments:
    - [Tab Bar](https://cdn.discordapp.com/attachments/363754040103796737/786348926289707028/tabs.gif)
    - [Selector](https://cdn.discordapp.com/attachments/363754040103796737/769588806981517332/M84kh690XN.gif)
    - [Dropdowns](https://cdn.discordapp.com/attachments/363754040103796737/786718994212192286/menus.gif)
    - [Table](https://cdn.discordapp.com/attachments/363754040103796737/786600570667925534/unknown.png)

## Highlights of this release

This was a very busy year for us. After the previous release we started focusing on the internals of Zircon. Previously we didn't have an
FPS goal nor performance optimizations in the code. The Swing benchmark was running with `~60 FPS` with a `80x24` grid and `16x16` tiles.
If someone used a full HD screen it was significantly slower, so we added some optimizations. Right now with a full HD screen and `16x16` tiles (that's a size of `120x67`) Zircon runs with `~100 FPS` in the benchmarks. This means that we achieved a **~8x** speedup! The goal
for the next release is to have `100 FPS` with `8x8` tiles.

This performance optimization was made possible by some internal refactorings. The rendering model was modified in a way that the renderer
no longer queries the state of the `TileGrid`, but lets all individual `Layer`s and `Component`s `render` themselves. This also means
that internal state is no longer stored in `Component`s, they are rendered whenever it is necessary instead.

There is also a `FastTileGraphics` implementation that enables this optimization. It uses an `Array` internally and it is significantly faster than the old `TileGraphics` that was using a `PersistentList`.

Zircon now also supports `StackedTile`s. a `StackedTile` is a composite that has a *stack* of `Tile`s within it. This is very useful to create composite tile objects and also for rendering. It also enables the `Component` system (and the `GameArea`) to be significantly easier to implement. It is is not yet clear what stacking means take a look at [this](https://cdn.discordapp.com/attachments/363754040103796737/775365392498819092/unknown.png) screenshot!

There are some new prototypes as well. One of them is an [Android renderer](https://cdn.discordapp.com/attachments/363754040103796737/720279635325354015/android.gif)!

Many of you were looking for Javadocs so we also added this to the project! You can find it [here](https://hexworks.github.io/zircon/). This is kdocs (Kotlin Docs) for now, but soon we'll add proper Javadocs as well! We also started to add code samples within the docs, so you'll be able to see what's what without having to navigate away.

We also started a proper depreciation cycle now, that Zircon has a significant userbase. When we deprecate something it will be annotated with the `@Deprecated` annotation, and these things will get removed in the next release. We also documented (in the source code) what to use instead of the things we're going to remove.

The `GameArea` received some refurbishments as well. Now the `GameArea` is rendered using a simple `ComponentRenderer` which means that you can use any `Component` for rendering a `GameArea`. This also means that this component can be a `Container` and can have additional children! Another addition to the `GameArea` is filtering. Now you can add filters to a `GameArea` that looks similar to a `Modifier`, but it has more parameters including the `Position3D` of the `Block` that's being modified in the `GameArea`. One useful example for this is to implement depth and distance filters. In [this](https://cdn.discordapp.com/attachments/363754040103796737/778393384380792833/pyramids.gif) example there is a *depth* filter. The pyramid's colors are all the same, but the filter changes *front* and *top* tiles to add a visual effect, and also makes blocks that have a lower `z` level appear darker. [This](https://cdn.discordapp.com/attachments/603285896829206548/787418009080823838/unknown.png) is how a building looks like with a filter applied.

Another thing you might have noticed fromt he GIF is that now you can switch between projections on the fly. You can also see it in action in [this](https://cdn.discordapp.com/attachments/603285896829206548/784811176080572416/map_generation_3d.gif) map generation example.

We usually refurbish the `ColorTheme`s with each release and this one is no different. There are some now ones as you can see in [this](https://cdn.discordapp.com/attachments/363754040103796737/703397248838664201/themes.gif) example. You can also use them in your [games](https://cdn.discordapp.com/attachments/603286045240590336/701468223031214241/falsedoor.png).

You can now add **padding** to your `Component`s. Padding is implemented with a `ComponentDecoration` so it can be added to any component, and it looks like [this](https://cdn.discordapp.com/attachments/363754040103796737/786288342945103912/unknown.png).

With this release now we have almost everything in place to implement the rest of the backlog! Here are some prototypes for what's going to be added to the next release including some `Fragment`s that you were waiting for a while now!

- [Tab Bar](https://cdn.discordapp.com/attachments/363754040103796737/786348926289707028/tabs.gif)
- [Selector](https://cdn.discordapp.com/attachments/363754040103796737/769588806981517332/M84kh690XN.gif)
- [Dropdowns](https://cdn.discordapp.com/attachments/363754040103796737/786718994212192286/menus.gif)
- [Table](https://cdn.discordapp.com/attachments/363754040103796737/786600570667925534/unknown.png)


Here is a full list of issues we finished for this release:

## New Features

- [#360](https://github.com/Hexworks/zircon/issues/360): Enable the ModalComponentContainer to try dispatching UIEvents to multiple containers if a container returns Pass
- [#357](https://github.com/Hexworks/zircon/issues/357): Add the option to add padding to a component
- [#355](https://github.com/Hexworks/zircon/issues/355): Add the option to keep the Component's properties when it is attached.
- [#350](https://github.com/Hexworks/zircon/issues/350): Create a filtering mechanism for the GameArea that would enable adding effects to individual Blocks.
- [#359](https://github.com/Hexworks/zircon/issues/359): Enable multiple Fragments to be added to a Container in one go.
- [#91](https://github.com/Hexworks/zircon/issues/91): Add frame rate limiting capability
- [#348](https://github.com/Hexworks/zircon/issues/348): Add a function that will create a TileBuilder out of a Tile
- [#349](https://github.com/Hexworks/zircon/issues/349): Add a function that will create a BlockBuilder out of a Block
- [#339](https://github.com/Hexworks/zircon/issues/339): Create a Tile implementation that's composed of multiple Tiles.
- [#297](https://github.com/Hexworks/zircon/issues/297): Set application icon
- [#299](https://github.com/Hexworks/zircon/issues/299): Create matchers for UIEvents
- [#315](https://github.com/Hexworks/zircon/issues/315): New Fragment: TilesetSelector and ColorThemeSelector
- [#300](https://github.com/Hexworks/zircon/issues/300): Enable the user to set default keyboard shortcuts
- [#296](https://github.com/Hexworks/zircon/issues/296): Add callbacks for Rendering

## Enhancements

- [#345](https://github.com/Hexworks/zircon/issues/345): Document all the API classes properly 
- [#325](https://github.com/Hexworks/zircon/issues/325): Create docs pages for each release using Dokka
- [#329](https://github.com/Hexworks/zircon/issues/329): Make Layer implement Layerable 
- [#328](https://github.com/Hexworks/zircon/issues/328): Refactor Components to be Layers 
- [#353](https://github.com/Hexworks/zircon/issues/353): Use fun interfaces where applicable 
- [#352](https://github.com/Hexworks/zircon/issues/352): Refactor builders to use prototypes with defaults. 
- [#146](https://github.com/Hexworks/zircon/issues/146): Generate javadoc (Dokka) during build and deploy it to a site 
- [#344](https://github.com/Hexworks/zircon/issues/344): Harmonize mutable and accessor mixin names. 
- [#338](https://github.com/Hexworks/zircon/issues/338): Optimize Rendering Performance 
- [#327](https://github.com/Hexworks/zircon/issues/327): Use a push model instead of a pull one for rendering 
- [#332](https://github.com/Hexworks/zircon/issues/332): Checkbox - Alignment & Text 
- [#319](https://github.com/Hexworks/zircon/issues/319): Size3d#containsPosition(position: Position3D) should also check for negative values 
- [#318](https://github.com/Hexworks/zircon/issues/318): Add new Color Themes  
- [#308](https://github.com/Hexworks/zircon/issues/308): Refactor Internal State handling to use Properties 
- [#87](https://github.com/Hexworks/zircon/issues/87): Review tint, shade and tone in TileColor 

## Bug fixes

- [#336](https://github.com/Hexworks/zircon/issues/336): Moving a Component with anything other than moveTo can lead to failed bounds check 
- [#316](https://github.com/Hexworks/zircon/issues/316): RectangleFactory (possibly others, too) ignores shape position 
- [#321](https://github.com/Hexworks/zircon/issues/321): MouseEventType.MOUSE_WHEEL_ROTATED_UP not getting triggered 
- [#311](https://github.com/Hexworks/zircon/issues/311): Components are not visible in AllComponentsExampleJava 
- [#307](https://github.com/Hexworks/zircon/issues/307): Clean up tilesets 
- [#305](https://github.com/Hexworks/zircon/issues/305): Libgdx not working in 2020.0.2-PREVIEW 
- [#304](https://github.com/Hexworks/zircon/issues/304): Clearing a TileGrid breaks draw operations 
- [#230](https://github.com/Hexworks/zircon/issues/230): Occassional incorrect window size 
- [#341](https://github.com/Hexworks/zircon/issues/341): Fix the rendering logic in GameArea 
- [#302](https://github.com/Hexworks/zircon/issues/302): Blocks are not rendered properly in a GameArea when tiles are not opaque. 
- [#277](https://github.com/Hexworks/zircon/issues/277): Hiding with transforming is leaving black background in LayerTransformerExample 
- [#301](https://github.com/Hexworks/zircon/issues/301): Component remains activated when space is pressed and focus is lost (pressing Tab)  
- [#298](https://github.com/Hexworks/zircon/issues/298): Focus handling keys don't work when using LibGDX 

## Road Map
  
We've covered a lot of ground in this release, but there are still things to do:

- [Scrollable Components](https://github.com/Hexworks/zircon/issues/25)
- [Floating Components](https://github.com/Hexworks/zircon/issues/23)
- [Drag'n Drop Support](https://github.com/Hexworks/zircon/issues/22)
- [Custom Layout Support](https://github.com/Hexworks/zircon/issues/28)
- [Component Themes](https://github.com/Hexworks/zircon/issues/29)
- [Custom Component Support](https://github.com/Hexworks/zircon/issues/26)
- [Menus](https://github.com/Hexworks/zircon/issues/135)
- [Tree Component](https://github.com/Hexworks/zircon/issues/184)
- [Table Component](https://github.com/Hexworks/zircon/issues/185)
- [Accordion Component](https://github.com/Hexworks/zircon/issues/27)
- [Combo Box Component](https://github.com/Hexworks/zircon/issues/262)
- [IntelliJ Plugin](https://github.com/Hexworks/zircon/issues/191)
- [Grid / Screen Filters](https://github.com/Hexworks/zircon/issues/271)

## Credits

Thank you for all of you out there who helped with this release! **Special thanks** to [Baret](https://github.com/Baret), [Bergin](https://www.reddit.com/user/Jordanbergin), [Coldwarrl](https://github.com/coldwarrl), [Luke](https://github.com/LukeLetourneau) and [Seveen](https://www.reddit.com/user/Seeveen) for their contributions!

## Contribute

> If you think that you can contribute or just have an idea feel free to join the discussion on our [Discord](https://discordapp.com/invite/vSNgvBh) server.
