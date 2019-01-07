---
excerpt: Now, we generate an actual dungeon, or rather a cave we can explore in our game.
title: "How To Make a Roguelike: #3 Scrolling Through Random Caves"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #3 Scrolling Through Random Caves"
series: coz
---

> Now, that we have set up our Views it is time to start using the `GameArea` and generate caves
which the player can explore! I'd also like to mention that it is useful most of the time if we
check whether there are never versions of the libraries we're using. In our case Zircon was
updated since the last article, so let's open `gradle.properties` and update `zircon_version`
to `2019.0.9-PREVIEW`!
  
## Cleaning up Our PlayView

We have already used `Component`s in the previous article but now we're gonna design an UI for our
game before we start generating our game world, so let's get started!

First of all let's remove the `winButton` and the `loseButton` from our `PlayView`. It was fun while it
lasted but now we want to get some serious work done:

```kotlin
import org.hexworks.zircon.api.ColorThemes
import org.hexworks.zircon.api.mvc.base.BaseView

class PlayView : BaseView() {

    override val theme = ColorThemes.arc()

    override fun onDock() {

        
    }
}
```

If you take a look at our `View`s you'll see that we set a `theme` in all of them by hand so it is time to move
this into our own config file. We'll also configure some other things while we're at it:

```kotlin
object GameConfig {

    // look & feel
    val TILESET = CP437TilesetResources.rogueYun16x16() // 1
    val THEME = ColorThemes.zenburnVanilla() // 2
    const val SIDEBAR_WIDTH = 18
    const val LOG_AREA_HEIGHT = 8  // 3

    // sizing
    const val WINDOW_WIDTH = 80
    const val WINDOW_HEIGHT = 50

    fun buildAppConfig() = AppConfigs.newConfig()  // 4
            .enableBetaFeatures()
            .withDefaultTileset(TILESET)
            .withSize(Sizes.create(WINDOW_WIDTH, WINDOW_HEIGHT))
            .build()

}
```

> Hey, isn't **globals bad**? Well, shared **mutable** state is bad, but in this config file everything is
immutable, so it can't cause problems for us. We could have used a `json` file or something similar instead,
but we'll keep this for simplicity's sake.

So what happens here is that we:

1. choose a tileset which is better for the dungeon vibe we are trying to achieve
2. pick a theme which has more eerie colors than the previous one
3. configure the width of our sidebar and the height of our log area which we'll add momentarily
4. add a function which builds an `AppConfig` for us based on these settings. This can be used
   for building our `Application`
   
Now we just modify our `main.kt` a bit to use this new config:

```kotlin
import org.hexworks.cavesofzircon.view.StartView
import org.hexworks.zircon.api.SwingApplications

fun main(args: Array<String>) {

    val application = SwingApplications.startApplication(GameConfig.buildAppConfig())

    application.dock(StartView())

}
```

and now all is left to do is to add the main UI components to our `PlayView`.

## Using Panels

`Panel` is a `Component` which we can add to our `View`s and they can hold other `Components`. This means that
if we add a bunch of `Button`s and other controls to a `Panel` and move the `Panel` around, its child controls
will move with it!

Let's add a sidebar to the `PlayView` now:

```kotlin
import org.hexworks.cavesofzircon.GameConfig
import org.hexworks.zircon.api.ColorThemes
import org.hexworks.zircon.api.Components
import org.hexworks.zircon.api.mvc.base.BaseView

class PlayView : BaseView() {

    override val theme = ColorThemes.arc()

    override fun onDock() {

        val sidebar = Components.panel()
                .withSize(GameConfig.SIDEBAR_WIDTH, GameConfig.WINDOW_HEIGHT)
                .wrapWithBox()
                .build()

        screen.addComponent(sidebar)
    }
}
```

If you run it you'll see something like this:

![Sidebar](/assets/img/sidebar.png)

Nice, isn't it? Now let's add a `LogArea` at the bottom as well:

```kotlin
val logArea = Components.logArea()
        .withTitle("Log") // 1
        .wrapWithBox()  // 2
        .withSize(GameConfig.WINDOW_WIDTH - GameConfig.SIDEBAR_WIDTH, GameConfig.LOG_AREA_HEIGHT)
        .withAlignmentWithin(screen, ComponentAlignment.BOTTOM_RIGHT)  // 3
        .build()

screen.addComponent(logArea)
```

Let's take a look at what happens here:

1. We set the title of our `LogArea` to "Log"
2. We wrap it with a box. This is an important step because `title` is only visible if our component is wrapped
3. We align the log within our `Screen` to the `BOTTOM_RIGHT`. We can either align *within* a component or
   *around* a component. In this case we chose the former.

Now, let's start up and bask in the "warm" colors of our UI:

![warm colors](/assets/img/warm_colors.png)

So far so good, right? The problem is that we still don't see a **game** so let's explore how to create one.

## The Game Component

Zircon comes with a `GameComponent` and the corresponding `GameArea`. We can use these two to display our game
world in an easy to use manner: the `GameComponent` is just a component, like the `Panel` and the `LogArea` we
used above. It is special because we can add a `GameArea` to it which holds the in-memory representation of our
world.

A `GameArea` consists of `Block`s which are very similar to blocks in Minecraft. They have 6 sides (top, bottom, left, right, front, back)
and they can also hold internal content (called layers). A `Block` contains `Tile`s so we can re-use everything
we had so far.

This setup clearly separates the **storing** of game data from **displaying** it so we won't end up with
convoluted render logic!

The `GameArea` supports **top down oblique** projection, like in this example:

![top down oblique](/assets/img/top_down_oblique.gif)

and simple **top down** projection:

![top down](/assets/img/top_down.gif)

We'll use the latter here.

## Setting Up the Game








## Conclusion



Until then go forth and *kode on*!
 
> The code of this article can be found under the `2_SCROLLING_THROUGH_RANDOM_CAVES` tag.

