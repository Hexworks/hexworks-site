---
excerpt: This is example about loading Rexpaint files in Zircon.
title: Rexpaint file loading example
tags: [zircon, documentation, zircon-example]
author: hexworks
short_title: Rexpaint file loading example
---

> The code of this example can be found in the `zircon.examples` project [here](https://github.com/Hexworks/zircon.examples/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/RexLoaderExample.java)

> In order for this example to work please download the `cp437_table.xp` file from [here](https://github.com/Hexworks/zircon.examples/blob/master/zircon.jvm.examples/src/main/resources/rex_files/cp437_table.xp)
  and copy it to the `src/main/resources/rex_files` folder in your project.

> If you don't have a project set up just yet you can download either the [Java][java-skeleton] or the [Kotin][kotlin-skeleton] 
  skeleton project to get started. 

Loading Rexpaint (`.xp`) files is very simple in Zircon. The following is a runnable code snippet which you can
copy and paste into your project and run:

```java
import org.hexworks.zircon.api.*;
import org.hexworks.zircon.api.application.Application;
import org.hexworks.zircon.api.data.Size;
import org.hexworks.zircon.api.graphics.Layer;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.resource.BuiltInCP437TilesetResource;
import org.hexworks.zircon.api.resource.REXPaintResource;
import org.hexworks.zircon.api.resource.TilesetResource;
import org.hexworks.zircon.api.screen.Screen;

import java.io.InputStream;
import java.util.List;

public class RexLoaderExample {
    private static final int TERMINAL_WIDTH = 16;
    private static final int TERMINAL_HEIGHT = 16;
    private static final TilesetResource TILESET = BuiltInCP437TilesetResource.YOBBO_20X20;
    private static final Size SIZE = Sizes.create(TERMINAL_WIDTH, TERMINAL_HEIGHT);
    private static final InputStream RESOURCE = RexLoaderExample.class.getResourceAsStream("/rex_files/cp437_table.xp");

    public static void main(String[] args) {
        REXPaintResource rex = REXPaintResource.loadREXFile(RESOURCE);

        Application app = SwingApplications.startApplication(AppConfigs.newConfig()
                .withDefaultTileset(BuiltInCP437TilesetResource.TAFFER_20X20)
                .withSize(SIZE)
                .withDebugMode(true)
                .build());

        final TileGrid tileGrid = app.getTileGrid();

        app.start();

        final Screen screen = Screens.createScreenFor(tileGrid);
        screen.setCursorVisibility(false);
        List<Layer> layers = rex.toLayerList(TILESET);
        for (Layer layer : layers) {
            screen.pushLayer(layer);
        }
        screen.display();
    }
}
```

After running this code you should see this on your screen:

![Zircon Rexpaint Loading Example](/assets/img/zircon_rexpaint_loading_example.png)

{% include zircon_links.md %}
