---
excerpt: This document explains how Screens work in Zircon and how to use them.
title: A Primer on Screens
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: A Primer on Screens
---

[Screen]s are in-memory [TileGrid]s. They have an internal representation of a grid and support all
functionality which is provided by one. They wrap your actual [TileGrid] and come with a `display` function which
makes their contents visible.

Multiple [Screen]s can be attached to the same [TileGrid] object which means that you can have more than one screen
in your app and you can switch between them simultaneously by using the `display` method. **Note that** only one
[Screen] can be displayed on the *actual* screen at the same time. With [Screen]s you can implement the *View* part of the
[Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) pattern.
 
[Screen]s also let you use [Component]s like [Button]s and [Panel]s. Let's see how we can use [Screen]s.

```java
import org.hexworks.zircon.api.*;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.screen.Screen;

public class CreatingAScreen {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid(
                AppConfigs.newConfig()
                        .withSize(Sizes.create(20, 8))
                        .withDefaultTileset(CP437TilesetResources.wanderlust16x16())
                        .build());

        final Screen screen = Screens.createScreenFor(tileGrid);
    }
}
```

You can interact with a [Screen] in the same way as you would use a [TileGrid]. All the drawing operations work
as expected:

```java
import org.hexworks.zircon.api.*;
import org.hexworks.zircon.api.component.ColorTheme;
import org.hexworks.zircon.api.graphics.TileGraphics;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.screen.Screen;

public class CreatingAScreen {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid(
                AppConfigs.newConfig()
                        .withSize(Sizes.create(20, 8))
                        .withDefaultTileset(CP437TilesetResources.wanderlust16x16())
                        .build());

        final Screen screen = Screens.createScreenFor(tileGrid);

        final ColorTheme theme = ColorThemes.adriftInDreams();

        final TileGraphics image = DrawSurfaces.tileGraphicsBuilder()
                .withSize(tileGrid.getSize())
                .build()
                .fill(Tiles.newBuilder()
                        .withForegroundColor(theme.getPrimaryForegroundColor())
                        .withBackgroundColor(theme.getPrimaryBackgroundColor())
                        .withCharacter('~')
                        .build());

        screen.draw(image, Positions.zero());

        screen.display();
    }
}
```

## Differences between Screens and TileGrids

Like a [TileGrid] a [Screen] has multiple layers of content.
 
There is a base layer which is modified when you use operations like `setTileAt` or `draw`. If the [Screen] is 
`display`ed the changes will be visible on your screen at once. Note that if you call `display` on another
[Screen] the previous [Screen] is *detached* from the underlying [TileGrid] so you won't see its changes.

**Above** the base layer there are the *component layers*. These are not present in [TileGrid]s since only
[Screen]s implement the [ComponentContainer] interface. When you add or remove components to a [Screen] you
implicitly modify these layers and the [Screen] is refreshed automatically. This also means that [Component]s
are basically specialized [Layer]s.

As with [TileGrid]s [Screen]s support adding and removing [Layer]s. Read the [relevant Wiki page][layers] to learn more.

> When you display a [Screen] the component layers are swapped. This is handled internally and you needn't worry about it.

## Usage

[Screen] have one method, which makes its use very simple:

`display` swaps the contents of its *internal* [TileGrid] with the one which you created by using an application
builder.

Use `display` when you want to overwrite **all** of the current contents of the [TileGrid] (for example if previously
another [Screen] was `display`ed on the [TileGrid]).

Here is how you can attach multiple [Screen]s to a [TileGrid] and switch between them:

```java
import org.hexworks.zircon.api.*;
import org.hexworks.zircon.api.component.Button;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.screen.Screen;

public class SwitchingScreens {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid();

        final Screen screen0 = Screens.createScreenFor(tileGrid);
        final Button next = Components.button()
                .withText("Next")
                .withPosition(Positions.offset1x1())
                .build();
        screen0.addComponent(next);

        final Screen screen1 = Screens.createScreenFor(tileGrid);
        final Button prev = Components.button()
                .withText("Prev")
                .withPosition(Positions.offset1x1())
                .build();
        screen1.addComponent(prev);

        next.onMouseReleased(mouseAction -> screen1.display());
        prev.onMouseReleased(mouseAction -> screen0.display());

        screen0.applyColorTheme(ColorThemes.adriftInDreams());
        screen1.applyColorTheme(ColorThemes.afterTheHeist());

        screen0.display();
    }
}
```

## Components

The component system gives you easy-to-use [Component]s like [Button]s and [Panel]s.
You can read about how they work [here][components].

## Caveats

There are some caveats when working with [Screen]s. When you `display` a [Screen] it is *activated* and it will
start listening to component-related events. Conversely if another [Screen] is displayed the currently displayed
one is *deactivated* so it won't get any events. This means that any listeners / callbacks you attach to components
will only be fired if their parent [Screen] is *active*.

The component system does not handle resizing yet so you should either *disable* resizing or handle it by hand.
**Note that** this feature is on the Roadmap and will be implemented later.


[screen-primer]:https://github.com/Hexworks/zircon/wiki/A-primer-on-Screens
[text-images]:https://github.com/Hexworks/zircon/wiki/How-to-work-with-TileGraphics

[discord]:https://discordapp.com/invite/PE3qFmF
[examples]:https://github.com/Hexworks/zircon/tree/master/zircon.examples/src/main
[api]:https://github.com/Hexworks/zircon/tree/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api
[internal]:https://github.com/Hexworks/zircon/tree/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/internal

[examples]:https://github.com/Hexworks/zircon.examples/tree/master/zircon.jvm.examples/src/main
[resource-handling]:https://github.com/Hexworks/zircon/wiki/Resource-Handling
[design-philosophy]:https://github.com/Hexworks/zircon/wiki/The-design-philosophy-behind-Zircon
[color-themes]:https://github.com/Hexworks/zircon/wiki/Working-with-ColorThemes
[layers]:https://github.com/Hexworks/zircon/wiki/How-Layers-work
[inputs]:https://github.com/Hexworks/zircon/wiki/Input-handling
[components]:https://github.com/Hexworks/zircon/wiki/The-component-system
[shapes]:https://github.com/Hexworks/zircon/wiki/Shapes
[animations]:https://github.com/Hexworks/zircon/wiki/Animation-support
[behaviors]:https://github.com/Hexworks/zircon/wiki/Behaviors

[Animation]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/animation/Animation.kt
[AnimationHandler]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/animation/AnimationHandler.kt
[AppConfigs]:https://github.com/Hexworks/zircon/blob/master/zircon.core/jvm/src/main/kotlin/org/hexworks/zircon/api/AppConfigs.kt
[Application]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/application/Application.kt
[ANSITileColor]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/color/ANSITileColor.kt
[Boundable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/Boundable.kt
[Button]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/component/Button.kt
[ColorTheme]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/component/ColorTheme.kt
[Clearable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/Clearable.kt
[Component]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/component/Component.kt
[Container]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/component/Container.kt
[BuiltInCP437TilesetResource]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/resource/BuiltInCP437TilesetResource.kt
[Drawable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/Drawable.kt
[DrawSurface]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/graphics/DrawSurface.kt
[Input]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/input/Input.kt
[InputEmitter]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/InputEmitter.kt
[Layerable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/Layerable.kt
[Layer]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/graphics/Layer.kt
[Layers]:https://github.com/Hexworks/zircon/blob/master/zircon.core/jvm/src/main/kotlin/org/hexworks/zircon/api/Layers.kt
[Modifier]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/modifier/Modifier.kt
[Modifiers]:https://github.com/Hexworks/zircon/blob/master/zircon.core/jvm/src/main/kotlin/org/hexworks/zircon/api/Modifiers.kt
[Panel]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/component/Panel.kt
[Position]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/data/Position.kt
[Renderer]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/internal/renderer/Renderer.kt
[Screen]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/screen/Screen.kt
[Shape]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/shape/Shape.kt
[ShapeFactory]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/shape/ShapeFactory.kt
[ShutdownHook]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/ShutdownHook.kt
[Size]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/data/Size.kt
[Styleable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/Styleable.kt
[StyleSet]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/graphics/StyleSet.kt  
[SwingApplications]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm.swing/src/main/kotlin/org/hexworks/zircon/api/SwingApplications.kt
[TileColor]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/color/TileColor.kt
[TileColors]:https://github.com/Hexworks/zircon/blob/master/zircon.core/jvm/src/main/kotlin/org/hexworks/zircon/api/TileColors.kt
[Tile]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/data/Tile.kt
[TileGraphics]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/graphics/TileGraphics.kt
[TileGrid]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/grid/TileGrid.kt
[TileBuilder]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/builder/data/TileBuilder.kt
[TilesetOverride]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/TilesetOverride.kt
[TypingSupport]:https://github.com/Hexworks/zircon/blob/master/zircon.core/common/src/main/kotlin/org/hexworks/zircon/api/behavior/TypingSupport.kt
