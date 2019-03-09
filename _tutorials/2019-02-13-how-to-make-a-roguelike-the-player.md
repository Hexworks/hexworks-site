---
excerpt: Our cave is ready to explore, so let's add a player to it!
title: "How To Make a Roguelike: #4 The Player"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #4 The Player"
series: coz
comments: true
published: false
future: false
---

> With our cave in place the next thing to do is to add a Player to move around the cave.
For this we'll use [Amethyst](https://github.com/Hexworks/amethyst), a very simple ECS(ish)
library. We'll also upgrade our Zircon version which now has more useful support for component
events!

## Updating Zircon

Since the last article Zircon received a major upgrade to input handling which will make our life
much easier in the long run, so let's start by updating our `gradle.properties` file with the
new versions:

```groovy
version=2019.0.1-PREVIEW

kotlin_version=1.3.21

cobalt_version=2018.1.1-PREVIEW
amethyst_version=2019.0.4-PREVIEW
zircon_version=2019.0.14-PREVIEW
```

In order for this to work we'll need to change some of our views which had buttons on them: `StartView`,
`WinView` and `LoseView`. The change is minor, but very important: a new kind of event was added to input
handling in Zircon: `ComponentEvent`s. So now we can listen to component activation events on all our
components like this:

```kotlin
myComponent.onComponentEvent(ComponentEventType.ACTIVATED) { // 1
    // 2
    Processed // 3
}
```

What happens here is:

1. We add a listener for component events to our component. Here we listen to the `ACTIVATED` event. This either
   means that the user clicked an interactable component (`Button`, `CheckBox` or something similar) or the user
   pressed the activation key on a focused component ([Spacebar] right now).
2. We process the event
3. We return `Processed` which means that we handled the event

> Clicking is straightforward, but what about "pressing the activation key"? Well, this is similar to what you do
when you navigate in your browser using [Tab] and [Shift] + [Tab]. If you do so focus wanders around between the
controls in your browser an when you press [Spacebar] the control is activated. Try it! This works the same way
in Zircon.

> And what's that `Processed` thingy we return from our listener? Well, that's part of the event propagation model
which Zircon supports. You can read up on that topic [here](/zircon/docs/2018-11-21-input-handling).

Armed with this knowledge let's modify our event listeners in the aforementioned views.

`StartView`:

```kotlin
        startButton.onComponentEvent(ComponentEventType.ACTIVATED) {
            replaceWith(PlayView())
            close()
            Processed
        }
```

`WinView`:

```kotlin
        restartButton.onComponentEvent(ComponentEventType.ACTIVATED) {
            replaceWith(PlayView())
            close()
            Processed
        }

        exitButton.onComponentEvent(ComponentEventType.ACTIVATED) {
            System.exit(0)
            Processed
        }
```

and finally `LoseView`:

```kotlin
        restartButton.onComponentEvent(ComponentEventType.ACTIVATED) {
            replaceWith(PlayView())
            close()
            Processed
        }

        exitButton.onComponentEvent(ComponentEventType.ACTIVATED) {
            System.exit(0)
            Processed
        }
```

If your IDE doesn't help you
these are the imports you will need to add to your views:

```kotlin
import org.hexworks.zircon.api.extensions.onComponentEvent
import org.hexworks.zircon.api.uievent.ComponentEventType
import org.hexworks.zircon.api.uievent.Processed
```

## Introducing Amethyst

So what is Amethyst, and why is it useful for us? Amethyst is a small library which takes
care of managing your game entities in a nutshell. It is similar to an [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system)
but it is a bit different in some important aspects.

"So what is a game entity anyway?", you might ask. A game entity can be anything which has its own behavior or state
in your game, form a goblin, to a wall, or even immaterial effects, like shadows. What's important about a game entity
is that it encapsulates all internal state of our goblin for example and it also contains all actions it can
perform. This means that by using this concept we can maintain the [Cohesion](https://en.wikipedia.org/wiki/Cohesion_(computer_science))
of our game objects while still retaining the flexibility.

So how does it work? Let's look at the *SEA*:

### Systems, Entities, Attributes

When working with an ECS, you'll probably see things like Entities, Components, and Systems. In Amethyst we have
Systems, Entities and Attributes. I'm not going into detail about the difference between ECS and SEA, but explain how
SEA works:

Let's say that we want to add a *goblin* to our game. If we think about how things can behave and change over time
we can conclude, that there are essentially two ways: internal, and external. For example a *goblin* can decide
that it wants to explore the dungeon, or just take a look around (**internal change**) or the player can decide to bash
the goblin to a pulpy mass with their club (**external change**). For this Amethyst has `System`s in place:

```kotlin
/**
 * A [System] is responsible for updating the internal state
 * ([Attribute]s) of an [Entity].
 */
interface System<C : Context>
```

There are several types of `System`s, and for our discussion here we need to know about `Behavior`:

```kotlin
/**
 * A [Behavior] is a [System] which performs actions autonomously
 * with entities whenever they are [update]d.
 */
interface Behavior<C : Context> : System<C>
```

which lets our *goblin* interact with the world and `Facet`:

```kotlin
/**
 * A [Facet] is a [System] which performs actions based on the
 * [Command]s they receive.
 */
interface Facet<C : Context> : System<C>
```

which lets whe **world** interact with our *goblin*.

> There is also `Actor` which combines `Facet` and `Behavior`. We'll talk about it later.

When a change happens over time to an entity (our *goblin* in this example) its state might change. To represent
this, Amethyst gives us `Attribute`s:

```kotlin
/**
 * An [Attribute] represents metadata about an entity which can
 * change over time.
 */
interface Attribute
```

An `Attribute` can be anything which you add to your entity, from health points to stomach contents. What's important
is that `Attribute`s should **never have** behavior, they are supposed to be dumb data structures.

On the other hand, `System`s should never have internal state. These two *important rules* allow us to compose `System`s
and `Attribute`s into `Entity` objects which are flexible, cohesive and safe to use.

So how do these entities work together? We have `Engine` for that which handles them so we don't have to
do it by hand:

```kotlin
interface Engine<T : Context> {

    /**
     * Adds the given [Entity] to this [Engine].
     */
    fun addEntity(entity: Entity<EntityType, T>)

    /**
     * Removes the given [Entity] from this [Engine].
     */
    fun removeEntity(entity: Entity<EntityType, T>)

    /**
     * Updates the [Entity] objects in this [Engine] with the given [context].
     */
    fun update(context: T)
}
```

We'll take a look at how this works in practice below, so prepare your keyboard!

## Enhancing our Game

So far our `Game` object was relatively simple. It only took a `World` as a parameter in its constructor
and we had a handy factory method for this:

```kotlin
class Game(val world: World) {

    companion object {

        fun create(worldSize: Size3D = GameConfig.WORLD_SIZE,
                   visibleSize: Size3D = GameConfig.WORLD_SIZE) = Game(WorldBuilder(worldSize)
                .makeCaves()
                .build(visibleSize))
    }
}
```




## Conclusion

**Well done!** Now we have a Player and also the means to add even more things to
our game easily. It is time to add monsters to our game, so we'll just do that in
the next article!

Until then go forth and *kode on*!
 
> The code of this article can be found under the `4_THE_PLAYER` tag.

