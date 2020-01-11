---
excerpt: This document will help you understand how to compose Tiles into in-memory graphics objects.
title: How to Work With TileGraphics
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: How to Work With TileGraphics
source_code_url: https://github.com/Hexworks/zircon/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/docs/CreatingTileGraphicsFromShapes.java
---

> This document will help you understand how to compose Tiles into in-memory graphics objects called TileGraphics

A [TileGraphics] is a two dimensional in-memory image composed of [Tile]s. When you create one it is
held in memory but you can `draw` a [TileGraphics] onto any [DrawSurface] (like a [TileGrid], a [Screen] or another [TileGraphics]).

> You can also create [Layer]s from [TileGraphics] objects. Click [here][how-layers-work] to see how [Layer]s work.

## Usage

A [TileGraphics] can be created by either using the [DrawSurfaces] helper or the `toTileGraphics` factory method on a [Shape].

> You can read more about [Shape]s [here][shapes].

Let's see an example:

```java
AppConfig config = AppConfig.newBuilder()
        .withDefaultTileset(TILESET)
        .build();

TileGrid tileGrid = SwingApplications.startTileGrid(
        config);

final TileGraphics background = DrawSurfaces.tileGraphicsBuilder()
        .withSize(tileGrid.getSize()) // you can fetch the size of a TileGrid like this
        .withFiller(Tile.newBuilder()
                .withCharacter(Symbols.BULLET)
                .withBackgroundColor(ANSITileColor.BLUE)
                .withForegroundColor(ANSITileColor.CYAN)
                .build())
        .build();

final TileGraphics rectangle = Shapes.buildRectangle(
        Position.zero(),
        tileGrid.getSize())
        .toTileGraphics(Tile.newBuilder()
                        .withCharacter(Symbols.BLOCK_DENSE)
                        .withBackgroundColor(TileColor.transparent())
                        .withForegroundColor(ANSITileColor.RED)
                        .build(),
                config.getDefaultTileset());

background.draw(rectangle, Position.zero());

// the default position is (0x0) which is the top left corner
tileGrid.draw(background, Position.zero());
```

The result of running this code snippet should look like this:

![Creating Tile Graphics From Shapes](/assets/img/creating-a-tile-graphics-from-shapes.png)

> Note that the above example uses the [Shapes] helper class. You can read more about shapes [here][drawing-shapes].

## How TileGraphics works

As you can see in the above example the easiest way to create a[TileGraphics] is to use the [DrawSurfaces] helper.
Every [TileGraphics] must have a `Size` but it has no [Position].
 
When you `draw` a [TileGraphics] on a [DrawSurface] the original image will stay the same and only the [DrawSurface]
will change which means that `draw` is a copy operation. After `draw`ing a [TileGraphics] you won't be able to tell
about a [Tile] that it belongs to your image. If you need this kind of information look at how
[how-layers-work][how-layers-work] work.

You can draw [TileGraphics] objects onto each other (as you can see in the example above). Keep in mind that there is a special
empty [Tile] which is accessible from the [Tile] class like this: `Tile.empty()`.

`EMPTY` [Tile]s **will not be copied** over to the [DrawSurface] when you call `draw` so you can create
flattened layers this way (the same happens in the example above).

When you `draw` a [TileGraphics] onto a [DrawSurface] you also need to supply a [Position]. This will be the
[Position] on the [DrawSurface] from which the copying will start. If a [TileGraphics] is bigger than the target
[DrawSurface] then the characters which are out of bounds will be ignored.

{% include zircon_links.md %}
