---
excerpt: This Crash Course is all you need to understand how Zircon works.
title: A Zircon Crash Course
tags: [zircon, documentation, zircon-documentation]
author: addamsson
short_title: A Zircon Crash Course
---

>This document is a Crash Course to get you familiar with the concepts which are essential if you
want to work with Zircon.

## Drawables and DrawSurfaces

Zircon is all about drawing stuff on the screen so it is not a surprise that the two core interfaces
in Zircon are [Drawable] and [DrawSurface].

A good analogy for the [DrawSurface] is a piece of paper. It is *something* which you can draw stuff *on*
and it looks like this:

```kotlin
// Something to draw on, just like a piece of paper
interface DrawSurface {

    fun draw(drawable: Drawable, position: Position)
    // ^^^--- this draws an arbitrary Drawable at a given Position
}
```

There are some new concepts in the above example: [Drawable] and [Position].

[Drawable] is *something* which you can draw onto a [DrawSurface]. For example when you are writing a letter to
someone you are writing *letters* onto a *piece of paper*. 

A [Position] is *where* you draw. For example when you use a text editor you write characters after each other and
when you press `[Enter]` a new line starts. That's why we have [Position]:

```kotlin
interface Position : Comparable<Position> {

    val x: Int
    val y: Int
}
```

Now, that we cleaned up how do we draw [Drawable]s onto [DrawSurface]s at given [Position]s, let's take a look at what
kind of [Drawable]s and [DrawSurface]s do we have.

## Tiles and TileGraphics

[Tile]s are a kind of [Drawable] which only occupy a single [Position]. For example a [CharacterTile] can be used to
draw an actual character (like `x`) onto a [DrawSurface]. This operation is so common that it is included in
the actual code of [DrawSurface]:

```kotlin
// Something to draw on, just like a piece of paper
interface DrawSurface {

    fun getTileAt(position: Position): Maybe<Tile>
    // ^^^--- this one lets you take a peek at a Tile at a given Position

    fun setTileAt(position: Position, tile: Tile)
    // ^^^--- this one lets you take set a Tile at a given Position

    fun draw(drawable: Drawable, position: Position)
    // ^^^--- this draws an arbitrary Drawable at a given Position
}
```

> `Maybe` is a special object and you will see it throughout Zircon. It is very similar to `Optional` in Java.
> We use `Maybe` to denote something which might or might not be present. In `DrawSurface` for example it is possible
> that there is no [Tile] at a given [Position]. In this case an empty `Maybe` is returned.

Now that we know an actual [Drawable] let's see how a [DrawSurface] looks like. Enter the [TileGraphics].
A [TileGraphics] is an *in-memory* image (piece of paper if you will). It is not visible on the *actual screen*
but you can draw [Tile]s and other [Drawable]s on it nevertheless. [TileGraphics]s also work like blueprints:
you can `draw` them on any other [DrawSurface]. Let's take a look at it:

```kotlin
interface TileGraphic : Clearable, Boundable, DrawSurface, Drawable, Styleable, TilesetOverride {

    fun fetchFilledPositions(): List<Position>
    // ^^^--- returns all the positions which contain a Tile

    fun fetchFilledTiles(): Iterable<Tile>
    // ^^^--- returns all the tiles in this object

    fun fetchCells(): Iterable<Cell>
    // ^^^--- returns the cells (position + tile pairs) of this graphic
    
    fun fetchCellsBy(offset: Position, size: Size): Iterable<Cell>
    // ^^^--- returns a subset of this graphic as cells

    fun resize(newSize: Size, filler: Tile): TileGraphic
    // ^^^--- resizes this graphics to the given size using the given filler to fill empty positions

    fun resize(newSize: Size): TileGraphics
    // ^^^--- resizes this tile graphics using the given size

    fun fill(filler: Tile): TileGraphics
    // ^^^--- fills the empty parts of this graphics with the given filler

    fun putText(text: String, position: Position = Position.defaultPosition())
    // ^^^--- draws Character Tiles using the given text at the given position

    fun applyStyle(styleSet: StyleSet)
    // ^^^--- applies the given style to the tiles in this graphics
    
    fun toTileImage(): TileImage
    // ^^^--- creates an immutable "snapshot" of this graphics. We'll talk about this later.
    
    fun toSubTileGraphics(rect: Rect): SubTileGraphics
    // ^^^--- returns a portion of this graphics as a new graphics
}
```

This is much more complex than our simple [DrawSurface] interface, so what's going on? First, [TileGraphics]
implements a lot of interfaces: `Clearable, DrawSurface, Drawable, Styleable`.

> The interfaces mentioned above are called *behaviors*. You can read more about them on [this][behaviors] page.

Zircon has interfaces for each behavior which is reused in other Zircon components, so here they perform the
following jobs:

- [Clearable]: Lets you *clear* the graphics. This means setting all its tiles to an empty tile.
- [DrawSurface] and [Drawable] is already explained.
- [Styleable]: Lets you get and set a [StyleSet] for this graphics. More on this later.

> This makes both the development and use of Zircon much easier, since these *behaviors* can be combined in any way
to get different results but the terms used will stay the same throughout the system.
> This also makes the code more testable since there is (usually) only one implementation for each *behavior* which
is used by all components.

The rest of the functions in the [TileGraphics] interface lets you manipulate the graphic itself, like combining it
with other graphic objects, transforming it, or slicing it with the `toSubImage` function.

Let's create an *actual* [TileGraphics]:

```java
import org.hexworks.zircon.api.CP437TilesetResources;
import org.hexworks.zircon.api.Modifiers;
import org.hexworks.zircon.api.Sizes;
import org.hexworks.zircon.api.StyleSets;
import org.hexworks.zircon.api.DrawSurfaces;
import org.hexworks.zircon.api.Tiles;
import org.hexworks.zircon.api.color.ANSITileColor;
import org.hexworks.zircon.api.graphics.TileGraphics;

public class CreatingATileGraphics {

    public static void main(String[] args) {

        TileGraphics graphics = DrawSurfaces.tileGraphicsBuilder()
                .withSize(Sizes.create(10, 10))
                .withStyle(StyleSets.newBuilder()
                        .withBackgroundColor(ANSITileColor.RED)
                        .withForegroundColor(ANSITileColor.MAGENTA)
                        .withModifiers(Modifiers.glow())
                        .build())
                .withTileset(CP437TilesetResources.rexPaint16x16())
                .build()
                .fill(Tiles.newBuilder()
                        .withCharacter('x')
                        .build());
    }
}
```

So what happens here? Let's see:

1. We create a new [builder](https://en.wikipedia.org/wiki/Builder_pattern) for our [TileGraphics]
2. We set its size to `10x10`
3. We fill it with the `x` character
4. We use the REXPaint16x16 tileset for it
5. We set a `StyleSet` to be used by this [TileGraphics]
6. And we finally create an actual instance of the [TileGraphics]

> Note that in Zircon there is a helper class for every object which you might want to create and for
> ease of use it is named after the object it creates using plural form.
> So here we use `TileGraphics` to create instances of `TileGraphics` and `Sizes` to create `Size` objects.

Now we have a [TileGraphics] but what is all that stuff about colors, styles and modifiers?

## Colors, StyleSets, Modifiers

Objects like [Tile]s or [TileGraphics]s can have foreground and background colors. You can either use the [ANSITileColor]
`enum` to pick a pre-defined [TileColor] or you can create a new one by using [TileColors]. This class
has some useful factory methods for this like: `fromRGB` and `fromString`. The latter can be
called with simple CSS-like strings (eg: `#334455`).

When working with [Tile]s apart from giving them color you might want to apply some special
[Modifier] to them like `UNDERLINE` or `VERTICAL_FLIP`.
You can do this by picking the right [Modifier] from the [Modifiers] class.
You can set any number of [Modifier]s to each [Tile] individually.

If you don't want to set all these by hand or you want to have a template and use it to give a consistent
style to multiple things you can use a [StyleSet] which is basically a [Value Object](https://martinfowler.com/bliki/ValueObject.html)
which holds fore/background colors and modifiers.

Now that we got the basics out of the way, let's see how we can put some *actual* stuff on the screen.


## Applications, AppConfigs and TileGrids

For drawing things on your screen Zircon has the [TileGrid]. This provides you with a surface on which 
you can draw [Tile]s, [TileGraphics]s and basically anything which is [Drawable]:

```kotlin
interface TileGrid
    : AnimationHandler, Clearable, DrawSurface, InputEmitter, Layerable, ShutdownHook, Styleable, TypingSupport {

    val widthInPixels: Int
        get() = currentTileset().width * width

    val heightInPixels: Int
        get() = currentTileset().height * height

}
```

Again, we have a very simple `interface` and a bunch of *behavior*s which make a [TileGrid] a [TileGrid]:

- [AnimationHandler]: Adds the functionality to put [Animation]s on the grid. More about [Animation]s [here][animations].
- [Clearable]: Lets you *clear* the graphic. This means setting all its tiles to an empty tile
- [DrawSurface] is something you know by now
- [InputEmitter]: Emits all the inputs which were received from the underlying GUI framework as Zircon [Input] events.
  This means that you don't have to know how input handling works in Swing or LibGDX, Zircon takes care of this for you.
- [Layerable]: Lets you put multiple layers on your screen. With this you can have overlays, effects, and that kind of
  stuff. Layering is explained in depth in its own chapter [here][layers].
- [ShutdownHook]: Gives you the ability to listen to a shutdown event. (eg: when the user closes the screen) This is
  abstracted away from the actual underlying system (like Swing or LibGDX).
- [Styleable]: Lets you get and set a [StyleSet] for this grid.
- [TypingSupport]: Adds `putCharacter` and `putTile` to the mix and acts as if you were typing on the screen (from left
  to right, and then top to bottom).
  
As you can see [TileGrid] is very similar to [TileGraphics] but there is a *very* important difference: what you `draw`,
`set` or `put` on a [TileGrid] is immediately visible on your screen. That's why a [TileGrid] is not a [Drawable]. It
is the end of the line, where all [Drawable] go to become visible on your screen.

It also comes with functionality which you can use to interact with the underlying GUI system. You needn't worry about
how the actual GUI system works, the [TileGrid]s job is to abstract all that away and give you a clean interface.  

This seems like a lot of things to do at once so you might ask "How is this [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))?".
Zircon solves this problem with composition: All of the above mentioned behaviors are handled by an object 
within a [TileGrid] which is responsible for only one thing.
For example [TileGrid] implements the [Layerable] interface and internally all operations defined by it are 
delegated to an object which implements [Layerable] only.
You can peruse these [here](https://github.com/Hexworks/zircon/tree/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior).
In this sense you can consider a [TileGrid] as a [Facade](https://en.wikipedia.org/wiki/Facade_pattern).

### Creating a TileGrid

This is all very nice, but how do I create a [TileGrid]? How come it is drawn on my screen without me needing to
do anything? That's because an [Application] takes care of all that for you:

```java
import org.hexworks.zircon.api.AppConfigs;
import org.hexworks.zircon.api.CP437TilesetResources;
import org.hexworks.zircon.api.Sizes;
import org.hexworks.zircon.api.SwingApplications;
import org.hexworks.zircon.api.grid.TileGrid;

public class CreatingAnApplication {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid(                        // 1
                AppConfigs.newConfig()                                              // 2
                        .withSize(Sizes.create(10, 10))                             // 3
                        .withDefaultTileset(CP437TilesetResources.rexPaint16x16())  // 4
                        .build());                                                  // 5

    }
}
```

In the above example we:

1. Create a new *Swing-backed* [Application]
2. Creates a configuration for that application
3. Sets the size of the window to be `30x20`
4. Sets the tileset to be used to the REXPaint16x16 one
5. Starts the [Application]

So what's an [Application] anyway? In short an [Application] is responsible for rendering the contents of a [TileGrid]
on the screen continuously and delegating all events, and actions which are coming from the underlying GUI system to
the [TileGrid]. It looks like this:

```kotlin
interface Application {

    val tileGrid: TileGrid

    /**
     * Initializes this [Application] and starts continuous rendering.
     */
    fun start()

    /**
     * Pauses rendering.
     */
    fun pause()

    /**
     * Resumes rendering.
     */
    fun resume()

    /**
     * Stops this [Application] and frees all of its resources.
     * Once an [Application] is stopped it can't be started again.
     */
    fun stop()
}
```

Now we have come full circle: we have a [TileGrid] and it is displayed on the screen. What else is there which is
essential for doing actual work with Zircon?

## Layers

A [Layer] is a specialized [TileGraphics] which can be drawn upon a [Layerable] object (a [TileGrid] for example).
A [Layer] differs from a [TileGraphics] in the way it is handled.
It can be repositioned relative to its parent while a [TileGraphics] can only be *drawn*.
Both [Layer]s and [TileGraphics]s are in-memory objects which are drawn only when the underlying [Layerable] object is drawn.

Using the previous analogy with the sheet of paper: when you *draw* a [TileGraphics] onto a [TileGrid] is like when
you take a look at a piece of paper and copy its contents onto another piece of paper.

[Layer]s and [Layerable]s are like putting multiple transparent sheets over an [overhead projector](https://en.wikipedia.org/wiki/Overhead_projector).

> Zircon supports transparency, so this analogy is rather apt here.

## Screens

[Screen]s are in-memory [TileGrid]s. They have an internal representation of a grid and support all
functionality which is provided by one. They wrap your actual [TileGrid] and come with a `display` function which
makes their contents visible.

Multiple [Screen]s can be attached to the same [TileGrid] object which means that you can have more than one screen
in your app and you can switch between them simultaneously by using the `display` method. **Note that** only one
[Screen] can be displayed on the *actual* screen at the same time. With [Screen]s you can implement the *View* part of the
[Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) pattern.
 
[Screen]s also let you use [Component]s like [Button]s and [Panel]s. This is how the [Screen] interface looks like:

```kotlin
interface Screen : TileGrid, ComponentContainer {

    fun display()

}
```

Pretty straightforward.

> If you are interested in how components work then [this][components] Wiki page can help you.

## Conclusion

This concludes the Zircon crash course. If you feel that you want a better understanding of some of these concepts
take a look around the Wiki.

> If you want to read more about the design philosophy behind Zircon check [this][design-philosophy] page on Wiki!
> 
> If you are a practical person and you understand things from examples check out the [code examples][examples].


[examples]:https://github.com/Hexworks/zircon/tree/master/zircon.examples/src/main
[resource-handling]:https://github.com/Hexworks/zircon/wiki/Resource-Handling
[design-philosophy]:https://github.com/Hexworks/zircon/wiki/The-design-philosophy-behind-Zircon
[color-themes]:https://github.com/Hexworks/zircon/wiki/Working-with-ColorThemes
[layers]:https://github.com/Hexworks/zircon/wiki/How-Layers-work
[inputs]:https://github.com/Hexworks/zircon/wiki/Input-handling
[components]:https://github.com/Hexworks/zircon/wiki/The-component-system
[shapes]:https://github.com/Hexworks/zircon/wiki/Shapes
[animations]:https://github.com/Hexworks/zircon/wiki/Animation-support
[behaviors]:https://github.com/Hexworks/zircon/wiki/Behaviors

[Animation]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/animation/Animation.kt
[AnimationHandler]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/animation/AnimationHandler.kt
[AppConfigs]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/hexworks/zircon/api/AppConfigs.kt
[Application]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/application/Application.kt
[ANSITileColor]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/color/ANSITileColor.kt
[Boundable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/Boundable.kt
[Button]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/component/Button.kt
[CharacterTile]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/data/CharacterTile.kt
[ColorTheme]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/component/ColorTheme.kt
[Clearable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/Clearable.kt
[Component]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/component/Component.kt
[Container]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/component/Container.kt
[CP437TilesetResources]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/hexworks/zircon/api/CP437TilesetResources.kt
[Drawable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/Drawable.kt
[DrawSurface]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/graphics/DrawSurface.kt
[Input]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/input/Input.kt
[InputEmitter]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/InputEmitter.kt
[Layerable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/Layerable.kt
[Layer]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/graphics/Layer.kt
[Layers]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/hexworks/zircon/api/Layers.kt
[Modifier]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/modifier/Modifier.kt
[Modifiers]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/hexworks/zircon/api/Modifiers.kt
[Panel]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/component/Panel.kt
[Position]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/data/Position.kt
[Renderer]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/internal/renderer/Renderer.kt
[Screen]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/screen/Screen.kt
[Shape]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/shape/Shape.kt
[ShapeFactory]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/shape/ShapeFactory.kt
[ShutdownHook]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/ShutdownHook.kt
[Size]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/data/Size.kt
[Styleable]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/Styleable.kt
[StyleSet]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/graphics/StyleSet.kt  
[SwingApplications]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm.swing/src/main/kotlin/org/hexworks/zircon/api/SwingApplications.kt
[TileColor]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/color/TileColor.kt
[TileColors]:https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/hexworks/zircon/api/TileColors.kt
[Tile]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/data/Tile.kt
[TileGraphics]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/graphics/TileGraphics.kt
[TileGrid]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/grid/TileGrid.kt
[TileBuilder]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/builder/data/TileBuilder.kt
[TilesetOverride]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/TilesetOverride.kt
[TypingSupport]:https://github.com/Hexworks/zircon/blob/master/zircon.core/src/main/kotlin/org/hexworks/zircon/api/behavior/TypingSupport.kt
