---
excerpt: If you want to display you TileGraphics on a TileGrid or Screen while still being able to move it around, try Layers!
title: How Layers Work
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: How Layers Work
source_code_url: https://github.com/Hexworks/zircon/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/docs/HowLayersWork.java
---

> This document is about how [Layer]s work in *Zircon*. If you want to learn about how to perform simple drawing operations instead take a look at the page about tile graphics [here][tile-graphics].

A [Layer] is a specialized [TileGraphics] object which can be drawn upon a [Layerable] object. A [Layer] differs from a [TileGraphics] in the way it is handled. It can be repositioned relative to its parent while a [TileGraphics] cannot. Both [Layer]s and [TileGraphics] are in-memory objects which are drawn only when the underlying [Layerable] object is drawn. Another important difference is that when a [TileGraphics] is drawn it is a copy operation (it won't change the graphics itself), while a [Layer] is placed over other [Layer]s and the [TileGrid]. Zircon supports transparency, which means that all [Layer]s are visible.

## Usage

Both the [TileGrid] and the [Screen] interfaces implement [Layerable] so you can add [Layer]s on top of both of them.

[Layer]s are `push`ed from bottom to top (like a stack) and you can either `pop` a [Layer] (which will remove the last added [Layer])
or remove one by its reference (like when you use a `Set`):

```java
TileGrid tileGrid = LibgdxApplications.startTileGrid(AppConfig.newBuilder()
        .withSize(20, 10)
        .build());

Layer layer0 = Layer.newBuilder()
        .withTileGraphics(DrawSurfaces.tileGraphicsBuilder()
                .withSize(3, 3)
                .withFiller(Tile.newBuilder()
                        .withForegroundColor(ANSITileColor.GREEN)
                        .withBackgroundColor(TileColor.transparent())
                        .withCharacter('X')
                        .build())
                .build())
        .withOffset(1, 1)
        .build();

Layer layer1 = Layer.newBuilder()
        .withTileGraphics(DrawSurfaces.tileGraphicsBuilder()
                .withSize(3, 3)
                .withFiller(Tile.newBuilder()
                        .withForegroundColor(ANSITileColor.RED)
                        .withBackgroundColor(TileColor.transparent())
                        .withCharacter('+')
                        .build())
                .build())
        .withOffset(3, 3)
        .build();

tileGrid.addLayer(layer0);
tileGrid.addLayer(layer1);

```

After running this code you'll see this on your screen:

![How Layers Work](/assets/img/how-layers-work.png)

> Note that in the above example all objects are created by using the corresponding factory methods in
> [Layer], [DrawSurfaces] and [Tile]. This is a common pattern in Zircon and you should
> use `these Builder`s for creating all Zircon objects. Read about [The design philosophy behind Zircon][design-philosophy] for more info.

## Layer mechanics

When drawing either a [TileGrid] or a [Screen], [Layer]s are drawn on the screen from bottom to top. You can
also see layering as adding a 3rd dimension (depth, or z axis) to a two dimensional surface.

When adding a [Layer] to a [Layerable] you must supply a [Position] to it which is relative to its parent.

[Layer]s can hold [Tile]s which have transparent colors (like in the example above) so keep this in mind
when you `push` [Layer]s onto a [Layerable].

Changes in your [Layer]s (like their position) is automatically handled and visible on the screen.

You can move a [Layer] to a new [Position] by using the `moveTo` method without removing it from its parent.

{% include zircon_links.md %}
