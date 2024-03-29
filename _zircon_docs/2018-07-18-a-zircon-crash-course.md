---
excerpt: This Crash Course is all you need to understand how Zircon works.
title: A Zircon Crash Course
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: A Zircon Crash Course
---

>This document is a Crash Course to get you familiar with the concepts which are essential if you
want to work with Zircon.

## DrawSurfaces Tiles and TileComposites

Zircon is all about drawing stuff on the screen so it is not a surprise that the core interfaces
revolve around this concept.

A good analogy for the [DrawSurface] is a piece of paper. It is *something* which you can draw stuff *on*
and it looks like this:

```kotlin
// Something to draw on, just like a piece of paper
interface DrawSurface : Clearable, TileComposite, TilesetOverride {

    fun draw(
        tile: Tile,
        drawPosition: Position
    )
    // ^^^--- this draws a Tile at a given Position

    fun draw(
        tileComposite: TileComposite,
        drawPosition: Position
    )
    // ^^^--- this draws an arbitrary TileComposite at a given Position

    
}
```

There are some new concepts in the above example: [Tile], [TileComposite] and [Position].

[TileComposite] is an object composed of [Tile]s which you can draw onto a [DrawSurface]. For example when you are
writing a letter to someone you are writing *letters* onto a *piece of paper*. In this analogy a [Tile] is a single
*character* in your letter. It looks like this:

```kotlin
interface TileComposite : Sizeable {

    val tiles: Map<Position, Tile>

    fun getTileAt(position: Position): Maybe<Tile>
}
```

As you can see it contains a `Map` of [Tile]s at given [Position]s and a way to query them.

> `Maybe` is a special object and you will see it throughout Zircon. It is very similar to `Optional` in Java.
> We use `Maybe` to denote something which might or might not be present. In `DrawSurface` for example it is possible
> that there is no [Tile] at a given [Position]. In this case an empty `Maybe` is returned.

A [Position] is *where* you draw. For example when you use a text editor you write characters after each other and
when you press `[Enter]` a new line starts. That's why we have [Position]:

```kotlin
interface Position : Comparable<Position> {
    val x: Int
    val y: Int
}
```

> The interfaces you can see [DrawSurface] implementing above are called *behaviors*. They are very similar to mixins
> in other languages. You can find more of them in [this package](https://github.com/Hexworks/zircon/tree/master/zircon.core/src/commonMain/kotlin/org/hexworks/zircon/api/behavior).

Zircon has interfaces for each behavior which is reused in other Zircon components, so here they perform the
following jobs:

- [Clearable] lets you *clear* the graphics. This means setting all its tiles to an empty tile.
- [DrawSurface] and [TileComposite] is already explained.
- [TilesetOverride] lets you pick a specific [TilesetResource] to be used.

> This makes both the development and use of Zircon much easier, since these *behaviors* can be combined in any way
to get different results but the terms used will stay the same throughout the system.
> This also makes the code more testable since there is (usually) only one implementation for each *behavior* which
is used by all components.

## TileGraphics

[TileGraphics] is a specialization of [DrawSurface] and it is used as the core abstraction for manipulating tiles
throughout Zircon:

```kotlin
interface TileGraphics : Copiable<TileGraphics>, DrawSurface {

    fun toSubTileGraphics(rect: Rect): TileGraphics

    fun toLayer(offset: Position = Position.zero()): Layer

    fun toResized(newSize: Size): TileGraphics

    fun toResized(newSize: Size, filler: Tile): TileGraphics
}
```

As you can see a [TileGraphics] can be copied and it contains some factory methods which you can use to derive
other [DrawSurface]s such as [Layer]s and sub tile graphics objects. The former is explained [here][how-layers-work],
the latter is an object which can be used as a "window" over a [TileGraphics] which will constrain read/write operations
positionally on the underlying [TileGraphics] object.

Let's create an *actual* [TileGraphics]:

```java
TileGraphics graphics = DrawSurfaces.tileGraphicsBuilder()
        .withSize(10, 10)
        .withTileset(CP437TilesetResources.rexPaint16x16())
        .withFiller(Tile.newBuilder()
                .withCharacter('x')
                .build())
        .build();
```

So what happens here? Let's see:

1. We create a new [builder](https://en.wikipedia.org/wiki/Builder_pattern) for our [TileGraphics]
2. We set its size to `10x10`
3. We fill it with the `x` character
4. We use the REXPaint16x16 tileset for it
5. And we finally create an actual instance of the [TileGraphics]

> Note that in Zircon there is a helper class for every object which you might want to create and for
> ease of use it is named after the object it creates.

Now we have a [TileGraphics] but what is all that stuff about colors, styles and modifiers?

## Colors, StyleSets, Modifiers

Objects like [Tile]s or [TileGraphics]s can have foreground and background colors. You can either use the [ANSITileColor]
`enum` to pick a pre-defined [TileColor] or you can create a new one by using the factory methods in [TileColor]. This class
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
you can draw [Tile]s, [TileGraphics]s and basically anything which is [TileComposite]:

```kotlin
interface TileGrid
    : AnimationRunner, Clearable, DrawSurface, Layerable,
              ShutdownHook, TypingSupport, UIEventSource, ViewContainer {

    val widthInPixels: Int
        get() = currentTileset().width * width

    val heightInPixels: Int
        get() = currentTileset().height * height

}
```

Again, we have a very simple `interface` and a bunch of *behavior*s which make a [TileGrid] a [TileGrid]:

- [AnimationRunner] adds the functionality to put [Animation]s on the grid. More about [Animation]s [here][animations].
- [Clearable] lets you *clear* the graphics. This means setting all its tiles to an empty tile
- [DrawSurface] is something you know by now
- [Layerable] lets you put multiple layers on your screen. With this you can have overlays, effects, and that kind of
  stuff. Layering is explained in depth in its own chapter [here][how-layers-work].
- [ShutdownHook] gives you the ability to listen to a shutdown event. (eg: when the user closes the screen) This is
  abstracted away from the actual underlying system (like Swing or LibGDX).
- [TypingSupport] adds `putCharacter` and `putTile` to the mix and acts as if you were typing on the screen (from left
  to right, and then top to bottom).
- [UIEventSource] emits all the inputs which were received from the underlying GUI framework as Zircon input events.
  This means that you don't have to know how input handling works in Swing or LibGDX, Zircon takes care of this for you.
- [ViewContainer] adds support for [View]s.
  
As you can see [TileGrid] is very similar to [TileGraphics] but there is a *very* important difference: what you `draw`
on a [TileGrid] is immediately visible on your screen. It is the end of the line, where all [TileComposite] go to
become visible on your screen.

It also comes with functionality that you can use to interact with the underlying GUI system. You don't need to worry about
how the actual GUI system works, the [TileGrid]s job is to abstract all that away and give you a clean interface.  

This seems like a lot of things to do at once so you might ask "How is this [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))?".
Zircon solves this problem with composition: All of the above mentioned behaviors are handled by an object 
within a [TileGrid] which is responsible for only one thing.
For example [TileGrid] implements the [Layerable] interface and internally all operations defined by it are 
delegated to an object which implements [Layerable] only.
You can peruse these [here](https://github.com/Hexworks/zircon/tree/master/zircon.core/src/commonMain/kotlin/org/hexworks/zircon/api/behavior).
In this sense you can consider a [TileGrid] as a [Facade](https://en.wikipedia.org/wiki/Facade_pattern).

### Creating a TileGrid

This is all very nice, but how do I create a [TileGrid]? How come it is drawn on my screen without me needing to
do anything? That's because an [Application] takes care of all that for you:

```java
TileGrid tileGrid = SwingApplications.startTileGrid(                        // 1
        AppConfig.newBuilder()                                              // 2
                .withSize(10, 10)                                           // 3
                .withDefaultTileset(CP437TilesetResources.rexPaint16x16())  // 4
                .build());  
```

In the above example we:

1. Create a new *Swing-backed* [Application]
2. Creates a configuration for that application
3. Sets the size of the window to be `10x10`
4. Sets the tileset to be used to REXPaint16x16

So what's an [Application] anyway? In short an [Application] is responsible for rendering the contents of a [TileGrid]
on the screen continuously and delegating all events, and actions which are coming from the underlying GUI system to
the [TileGrid]. It looks like this:

```kotlin
interface Application {

    val tileGrid: TileGrid

    fun start()

    fun pause()

    fun resume()

    fun stop()
}
```

Now we have come full circle: we have a [TileGrid] and it is displayed on the screen. What else is there which is
essential for doing actual work with Zircon?

## Layers

A [Layer] is a specialized [TileGraphics] which can be drawn upon a [Layerable] object (a [TileGrid] for example).
A [Layer] differs from a [TileGraphics] in the way it is handled.
It can be repositioned relative to its parent while a [TileGraphics] can only be *drawn*.
Both [Layer]s and [TileGraphics] objects are in-memory objects which are drawn only when the underlying [Layerable] object is drawn.

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
interface Screen : ComponentContainer, Themeable, TileGrid {

    fun display()

}
```

Pretty straightforward.

> If you are interested in how components work then [this][the-component-system] documentation page can help you.

## Conclusion

This concludes the Zircon crash course. If you feel that you want a better understanding of some of these concepts
take a look around the documentation.

> If you want to read more about the design philosophy behind Zircon check [this][design-philosophy] page on documentation!
> 
> If you are a practical person and you understand things from examples check out the [code examples][examples].

{% include zircon_links.md %}
