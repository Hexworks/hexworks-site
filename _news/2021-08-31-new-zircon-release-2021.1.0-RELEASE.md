---
excerpt: "A new Zircon version is released: 2021.1.0-RELEASE"
title: "New Zircon Release: 2021.1.0-RELEASEE"
tags: [github, zircon, project-update, project-update-zircon, release, release-zircon]
author: hexworks
short_title: "New Zircon Release: 2021.1.0-RELEASE"
comments: true
---

> Zircon is an extensible, multiplatform and user-friendly tile engine.
>
> If you are not yet familiar with the project take a look at our
> [Project Page](https://hexworks.org/projects/zircon/).
>
> The Javadoc / Kdoc can be found [here](https://hexworks.github.io/zircon/).
>
> You can grab this release from Maven Central [here](https://search.maven.org/search?q=g:org.hexworks.zircon). More on how to add *Zircon* as a dependency to your project can be found [here](https://hexworks.org/zircon/docs/2019-01-11-release-process-and-versioning-scheme).

> Also if you have any questions about this release, feel free to come up to our [Discord Server](https://discord.com/invite/vSNgvBh) and ask them!

## TL;DR For the Impatient:

- New fragments are added (`Table`, `HorizontalTabBar`, `VerticalTabBar`, `DropDownMenu`)
- A full-blown Kotlin DSL is added for components and fragments
- Getters received an upgrade (getOrNull, getOrElse)
- Component builders now auto-calculate size
- You can create custom tileset loaders now
- No global state: now you can use *Zircon* in renderer-only or tile grid only mode

## Highlights of this release

The main focus of this release was **usability** and **extensibility**:

On the usability front, we've deprecated `Maybe`, and started adding two accessor variants instead:

- `getOrNull`: this will either return an item you're looking for or a `null` if it is not present
- `getOrElse`: Just like `getOrNull` but you can provide a supplier for the "else" object (this will **never** return `null`)
- We're also going to add a simple `get` that will throw an exception instead of returning `null` if the value you're looking for is not present

More on usability: we added a *Kotlin* dsl for **all** components and fragments, so now it is easier than ever to create declarative UIs with Zircon:

```kotlin
SwingApplications.startTileGrid().toScreen().apply {
    display()
    theme = ColorThemes.adriftInDreams()
}.useComponentBuilder {
    val screenSize = size
    vbox {
        preferredSize = screenSize
        decoration = box(title = "DSL Example")
        header { +"This is a simple example" }
        hbox {
            vbox {
                decoration = box()
                label { +"Just a label" }
            }
            vbox {
                decoration = box()
                paragraph {
                    preferredContentSize = Size.create(20, 5)
                    +"This will take a lot more space, but we constrain its size with the preferred content size"
                }
            }
            vbox {
                decoration = box()
                paragraph {
                    preferredContentSize = Size.create(10, 20)
                    +"As you can see sizes are automatically calculated when you don't supply parameters explicitly. Text is also wrapped by default."
                }
            }
        }
    }
}
```

And it looks like this:

![Example](https://cdn.discordapp.com/attachments/603285896829206548/882268425475481700/unknown.png)

One important change that was a blocker for some other things was the reliance on the global `Zircon` object that also held some state. With [#404](https://github.com/Hexworks/zircon/issues/404) completed now there is no global state, and it is also possible to have `TileGrid` and / or `Renderer` objects without having an `Application` that continuously renders content.

What this means is that now it is possible to embed *Zircon* into other applications that don't use tile grids at all or to bridge *Zircon* with different component systems. All you'll need is to pass the relevant input (keyboard/mouse) events!

Another use case that was requested for a long time is to create tile / ASCII art with *Zircon* without having to deal with an `Application` object (eg: using just a `Renderer`). Now it is finally possible!

There is another improvement that helps with extensibility: now it is possible to [create custom tileset loaders](https://github.com/Hexworks/zircon/issues/386). There is already a WIP loader that uses *Tiled*'s format.

There was some confusion previously on how *Zircon* objects should be created. Now this is made explicit by [this](https://github.com/Hexworks/zircon/issues/382) issue. From now on the appropriate *builders* should be used and all builders have their constructors locked down, so usage is like this for all the things: `Thing.newBuilder() ... .build()`.

****Some notable `Fragment`s:****

The [Table fragment](Table control for Zircon ) is now released, usage is very simple:

```kotlin
data class Person(
    val name: String,
    val age: Int,
    val occupation: String
)

val persons = listOf(
    Person(
        name = "Joe",
        age = 28,
        occupation = "programmer"
    ), Person(
        name = "Jane",
        age = 30,
        occupation = "UX Expert"
    ), Person(
        name = "Tanya",
        age = 26,
        occupation = "Commando"
    )
).toProperty()

SwingApplications.startTileGrid().toScreen().apply {
    display()
}.useComponentBuilder {
    table<Person> {
        data = persons
        textColumn {
            name = "Name"
            width = 10
            valueProvider = Person::name
        }
        numberColumn {
            name = "Age"
            width = 4
            valueProvider = Person::age
        }
        textColumn {
            name = "Occupation"
            width = 15
            valueProvider = Person::occupation
        }
    }
}
```

And it looks like this:

![](https://cdn.discordapp.com/attachments/603285896829206548/882268624902033488/unknown.png)

Now it is also possible to create *Dropdown Menus*:

![](https://cdn.discordapp.com/attachments/603285896829206548/882268518467391549/unknown.png)

and Tab bars:

![](https://cdn.discordapp.com/attachments/603285896829206548/882268475295412245/unknown.png)

Vertical:

![](https://cdn.discordapp.com/attachments/603285896829206548/882271040980545566/unknown.png)

You can now also set up listeners [right in the component builders](https://github.com/Hexworks/zircon/issues/393) and now the component builders are also more smart about sizing: they will calculate the size that's necessary to display the component, but you can now also set either the **peferred content size** or the **preferred size**. The same stands for containers! This means that you can create a `HBox` with all of its children, and the occupied space for the `HBox` will be calculated for you.


Here is a full list of issues we finished for this release:

## New Features

- [#340](https://github.com/Hexworks/zircon/issues/340): Make Tilesets and Modifiers configurable from the AppConfig class
- [#404](https://github.com/Hexworks/zircon/issues/404): Add the ability to create a TileGrid and a Renderer without an Application
- [#397](https://github.com/Hexworks/zircon/issues/397): ScrollableList for Zircon
- [#185](https://github.com/Hexworks/zircon/issues/185): Table control for Zircon 
- [#135](https://github.com/Hexworks/zircon/issues/135): Introduction of menus in Zircon
- [#395](https://github.com/Hexworks/zircon/issues/395): Re-add clear to Container to enable removing all children at once without having to use the AttachedComponent reference
- [#394](https://github.com/Hexworks/zircon/issues/394): Finalize Tab bars
- [#393](https://github.com/Hexworks/zircon/issues/393): Add the option to set properties and listeners to ComponentBuilders
- [#104](https://github.com/Hexworks/zircon/issues/104): Add function to BaseComponentBuilder for defining a content-based size
- [#364](https://github.com/Hexworks/zircon/issues/364): Add Zircon Builder Dsl
- [#391](https://github.com/Hexworks/zircon/issues/391): Implement component autosizing
- [#386](https://github.com/Hexworks/zircon/issues/386): Custom Tileset Loader Support
- [#388](https://github.com/Hexworks/zircon/issues/388): Add orNull alternatives to operations that return a Maybe
- [#380](https://github.com/Hexworks/zircon/issues/380): AppConfig extensible custom properties API


## Enhancements

- [#412](https://github.com/Hexworks/zircon/issues/412): Add orElse variants of functions to objects with accessors
- [#326](https://github.com/Hexworks/zircon/issues/326): Move the contents of the Zircon object into Application
- [#382](https://github.com/Hexworks/zircon/issues/382): All classes that have builders should lock down their constructors
- [#19](https://github.com/Hexworks/zircon/issues/19): Implement box connecting in Box
- [#18](https://github.com/Hexworks/zircon/issues/18): Box connecting should be handled when the area surrounding the box changes
- [#399](https://github.com/Hexworks/zircon/issues/399): Add remainingSpace property ty HBox and VBox
- [#396](https://github.com/Hexworks/zircon/issues/396): Restore the original position on attachment failure or component reset
- [#309](https://github.com/Hexworks/zircon/issues/309): borderless window / simplified fullscreen
- [#331](https://github.com/Hexworks/zircon/issues/331): Box Decoration Title Alignment

## Bug fixes

- [#409](https://github.com/Hexworks/zircon/issues/409): Cursor is not displayed properly
- [#410](https://github.com/Hexworks/zircon/issues/410): Removing a non-last item from a HBox / VBox throws an exception
- [#407](https://github.com/Hexworks/zircon/issues/407): Some of the GameArea examples are misaligned
- [#406](https://github.com/Hexworks/zircon/issues/406): Transforming a Layer to contain transparent tiles will make it black instead
- [#405](https://github.com/Hexworks/zircon/issues/405): The Delayed TileString example is not working
- [#398](https://github.com/Hexworks/zircon/issues/398): Data binding loop when updating UNKNOWN tilesets
- [#365](https://github.com/Hexworks/zircon/issues/365): ComponentBuilder constructor parameter
- [#367](https://github.com/Hexworks/zircon/issues/367): Headers don't wrap on word
- [#376](https://github.com/Hexworks/zircon/issues/376): BaseGameArea sticks in memory after undocking

## Road Map
  
We've covered a lot of ground in this release, but there are still things to do:

- [Floating Components](https://github.com/Hexworks/zircon/issues/23)
- [Drag'n Drop Support](https://github.com/Hexworks/zircon/issues/22)
- [Custom Layout Support](https://github.com/Hexworks/zircon/issues/28)
- [Component Themes](https://github.com/Hexworks/zircon/issues/29)
- [Custom Component Support](https://github.com/Hexworks/zircon/issues/26)
- [Tree Component](https://github.com/Hexworks/zircon/issues/184)
- [Table Component](https://github.com/Hexworks/zircon/issues/185)
- [Accordion Component](https://github.com/Hexworks/zircon/issues/27)
- [Combo Box Component](https://github.com/Hexworks/zircon/issues/262)
- [IntelliJ Plugin](https://github.com/Hexworks/zircon/issues/191)

## Credits

Thank you for all of you out there who helped with this release! **Special thanks** to [Baret](https://github.com/Baret), [Abbi @lesbiangunshow](https://github.com/lesbiangunshow), and [Max @nanodeath](https://github.com/nanodeath) for their contributions!

## Contribute

> If you think that you can contribute or just have an idea feel free to join the discussion on our [Discord](https://discordapp.com/invite/vSNgvBh) server.
