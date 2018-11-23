---
excerpt: If you want to display you TileGraphics on a TileGrid or Screen while still being able to move it around, try Layers!
title: How Layers Work
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: How Layers Work
---

A [Layer] is a specialized [TileGraphics] object which can be drawn upon a [Layerable] object.
A [Layer] differs from a [TileGraphics] in the way it is handled.
It can be repositioned relative to its parent while a [TileGraphics] cannot.
Both [Layer]s and [TileGraphics]s are in-memory objects which are drawn only when the underlying [Layerable] object is drawn.

## Usage
Both the [TileGrid] and the [Screen] interfaces implement [Layerable] so you can add [Layer]s on top of both of them.

[Layer]s are `push`ed from bottom to top (like a stack) and you can either `pop` a [Layer] (which will remove the last added [Layer])
or remove one by its reference (like when you use a `Set`):

```java
import org.hexworks.zircon.api.AppConfigs;
import org.hexworks.zircon.api.DrawSurfaces;
import org.hexworks.zircon.api.Layers;
import org.hexworks.zircon.api.Positions;
import org.hexworks.zircon.api.Sizes;
import org.hexworks.zircon.api.SwingApplications;
import org.hexworks.zircon.api.TileColors;
import org.hexworks.zircon.api.Tiles;
import org.hexworks.zircon.api.color.ANSITileColor;
import org.hexworks.zircon.api.graphics.Layer;
import org.hexworks.zircon.api.grid.TileGrid;

public class UsingLayers {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid(AppConfigs.newConfig()
                        .withSize(Sizes.create(20, 20))
                        .build());

        Layer layer0 = Layers.newBuilder()
                .withTileGraphics(DrawSurfaces.tileGraphicsBuilder()
                        .withSize(Sizes.create(3, 3))
                        .build()
                        .fill(Tiles.newBuilder()
                                .withForegroundColor(ANSITileColor.GREEN)
                                .withBackgroundColor(TileColors.transparent())
                                .withCharacter('X')
                                .build()))
                .withOffset(Positions.offset1x1())
                .build();

        Layer layer1 = Layers.newBuilder()
                .withTileGraphics(DrawSurfaces.tileGraphicsBuilder()
                        .withSize(Sizes.create(3, 3))
                        .build()
                        .fill(Tiles.newBuilder()
                                .withForegroundColor(ANSITileColor.RED)
                                .withBackgroundColor(TileColors.transparent())
                                .withCharacter('+')
                                .build()))
                .withOffset(Positions.create(3, 3))
                .build();

        tileGrid.pushLayer(layer0);
        tileGrid.pushLayer(layer1);
    }
}
```

After running this code you'll see this on your screen:

![How Layers Work](/assets/img/how-layers-work.png)

> Note that in the above example all objects are created by using the corresponding helper class:
> [Layers], [DrawSurfaces] and [Tiles]. This is a common pattern in Zircon and you should
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
