---
excerpt: This is example about loading Rexpaint files in Zircon.
title: Rexpaint file loading example
tags: [zircon, documentation, zircon-example]
author: hexworks
short_title: Rexpaint file loading example
source_code_url: https://github.com/Hexworks/zircon/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/RexLoaderExample.java
---

> If you don't have a project set up just yet you can download either the [Java][java-skeleton] or the [Kotin][kotlin-skeleton] 
  skeleton project to get started. 
  
> The code of this example can be found in the `zircon.examples` project [here](https://github.com/Hexworks/zircon.examples/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/RexLoaderExample.java).

> In order for this example to work please download the `cp437_table.xp` file from [here](https://github.com/Hexworks/zircon.examples/blob/master/zircon.jvm.examples/src/main/resources/rex_files/cp437_table.xp)
  and copy it to the `src/main/resources/rex_files` folder in your project.

Loading Rexpaint (`.xp`) files is very simple in Zircon. The following is a runnable code snippet which you can
copy and paste into your project and run:

```java
REXPaintResource rex = REXPaintResource.loadREXFile(RESOURCE);

TileGrid tileGrid = SwingApplications.startTileGrid(AppConfig.newBuilder()
        .withDefaultTileset(CP437TilesetResources.taffer20x20())
        .withSize(SIZE)
        .withDebugMode(true)
        .build());

final Screen screen = Screen.create(tileGrid);
List<Layer> layers = rex.toLayerList(TILESET);
for (Layer layer : layers) {
    screen.addLayer(layer);
}
screen.display();
```

After running this code you should see this on your screen:

![Zircon Rexpaint Loading Example](/assets/img/zircon_rexpaint_loading_example.png)

{% include zircon_links.md %}
