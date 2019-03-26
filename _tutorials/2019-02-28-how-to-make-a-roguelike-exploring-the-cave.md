---
excerpt: Now that we have a Player in our game let's learn how to move him around!
title: "How To Make a Roguelike: #5 Exploring the Cave""
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #5 Exploring the Cave"
series: coz
comments: true
---

> In the previous article we introduced [Amethyst](https://github.com/Hexworks/amethyst) which
  provides the means to handle game entities easily. Using it we added the Player to our game,
  but it is not doing much, so let's teach them how to move around and dig!

## Kicking our World into Motion

We've had a `World` class for quite some time now, but it is rather static, meaning that we don't
have the means to evolve it over time. Let's add now a function to it which will enable us to
`update` it whenever the player presses a button:
  

```kotlin
// Add this to World.kt

import org.hexworks.zircon.api.screen.Screen
import org.hexworks.zircon.api.uievent.UIEvent

fun update(screen: Screen, uiEvent: UIEvent, game: Game) { // 1
    engine.update(GameContext( // 2
            world = this,
            screen = screen, // 3
            uiEvent = uiEvent, // 4
            player = game.player)) // 5
}
```  

Here we

1. Create the function `update` which takes all the necessary objects as a parameter
2. We use the `GameContext` which we created before to update the `Engine`. If you were wondering before why this
   class will be necessary now you know: a `Context` object holds all the information which might be necessary
   to update the `Entity` objects within our `World`
3. We pass the `Screen` because we'll be using it to display dialogs and similar things
4. We'll inspect `UIEvent` to determine what the user wants to do (like moving around). We're using `UIEvent` instead
   of `KeyboardEvent` here because it is possible that at some time we also want to use mouse events.
5. Adding the player `Entity` to the `Context` is not mandatory, but since we use it almost everywhere this little
   optimization will make our life easier.
   
Now we have to modify our `PlayView` to update our `World` whenever the user presses a key:
  
```kotlin
org.hexworks.zircon.api.extensions.onKeyboardEvent
org.hexworks.zircon.api.uievent.KeyboardEventType
org.hexworks.zircon.api.uievent.Processed


// Add this to onDock in PlayView.kt
screen.onKeyboardEvent(KeyboardEventType.KEY_PRESSED) { event, _ ->
    game.world.update(screen, event, game)
    Processed
}
```

> You could argue that this code: `game.world.update` is an antipattern (also called a [Train Wreck](http://wiki.c2.com/?TrainWreck))
  and should be discouraged. I agree and we could have added an `update` method to the `Game` class itself but note that here
  `Game` is just a data structure holding the `World` and the `Player` and nothing else, so we'll leave it as it is.
  
With these changes the `World` is updated whenever the player presses a button! The only thing which is left to do is to add a
`System` which checks the user inputs and acts accordingly, the `PlayerInputHandler`

## Handling Player Input

Let's break down what do we want to **do** when a key is pressed:

- We accept the keys `W`, `A`, `S` and `D` as movement keys (up, left, down, right respectively)
- We move the player in the proper direction when either of those keys are pressed
- We scroll the view (if necessary) when the player is moved

All of the above steps can be encapsulated into their own `System` in the *SEA* model (just like in 
a traditional ECS model) so let's create a `InputReceiver`, `Movable` and a `CameraMover` `System`.

> "Why don't we just write a `switch` and move the player/character by hand instead of splitting it into multiple operations?"
  you might ask. The reason is that by having `System`s which are completely oblivious of the outside world and just do
  their own job we enable a lot of interesting features to be implemented with ease. One of those things is having a demo
  AI component which sends the proper commands to the player `Entity` instead of using UI inputs. Here is a [great talk](https://www.youtube.com/watch?v=U03XXzcThGU)
  by Brian Bucklew, the creator of Caves of Qud where he explains these concepts in depth and why they are useful.
  
As you might have seen in the previous article `System`s are either `Facet`s (enable the outside world to interact with our `Entity`)
or `Behavior`s (interact with the world on their own). In this case `Movable` and `CameraMover` are `Facet`s and `InputReceiver` is
a `Behavior`.

A `Behavior` is straightforward: whenever the `World` gets updated it calls `update` on all of its entities, which in turn
call `update` on all of their `Behavior`s. `Facet`s work a bit differently. They accept `Command`s.

> Wait, what? Command? Isn't that a design pattern? Yes it is. `Facet`s use the [Command Design Pattern](https://en.wikipedia.org/wiki/Command_pattern).

Let's create the `MoveTo` command which holds all the data which is necessary to move our player. Amethyst supplies
a `Command` interface for us:

```kotlin
interface Command<T : EntityType, C : Context> {

    val context: C
    
    val source: Entity<T, C>
    
    // ...
}
```

This seems pretty straightforward. We have a `context` object (remember the `GameContext` we added before?) which
has all the data we might need to perform actions and a `source`. `source` holds a reference to the `Entity` where
the `Command` is originated from. This handy feature lets `Entity` objects respond to command without maintaining
a reference to the callee.

As with `GameEntity` before we're going to create a `typealias` for `Command` as well, which substitutes our
`GameContext` object in place of the `C` type parameter:

```kotlin
// Add this to the TypeAliases.kt file which we have created before

/**
 * Specializes [Command] with our [GameContext] type.
 */
typealias GameCommand<T> = Command<T, GameContext>
```

Let's put the `MoveTo` `Command` in a new package named commands:

```kotlin
package org.hexworks.cavesofzircon.commands

import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.cavesofzircon.extensions.GameCommand
import org.hexworks.cavesofzircon.extensions.GameEntity
import org.hexworks.cavesofzircon.world.GameContext
import org.hexworks.zircon.api.data.impl.Position3D

/**
 * A [GameCommand] representing moving [source] to [position].
 */
data class MoveTo(override val context: GameContext,
                  override val source: GameEntity<EntityType>,
                  val position: Position3D) : GameCommand<EntityType>
```

All `Command`s have 2 mandatory parameters: `context` and `source`. We only add `position` here
which holds the position where we want to move the player. As this class indicates, the `Movable`
`System` will be oblivious to inputs and other things, it only cares about **moving** an `Entity`.

The class which knows about all `Entity` objects is the `World`, so let's add the logic of moving
one to it:

```kotlin
    fun moveEntity(entity: GameEntity<EntityType>, position: Position3D): Boolean { // 1
        var success = false                                 // 2
        val oldBlock = fetchBlockAt(entity.position)
        val newBlock = fetchBlockAt(position)               // 3

        if (bothBlocksPresent(oldBlock, newBlock)) {        // 4
            success = true                                  // 5
            oldBlock.get().removeEntity(entity) 
            entity.position = position
            newBlock.get().addEntity(entity)
        }
        return success                                      // 6
    }

    private fun bothBlocksPresent(oldBlock: Maybe<GameBlock>, newBlock: Maybe<GameBlock>) =  // 7
            oldBlock.isPresent && newBlock.isPresent
```

> A quick refresher: the `map` operation on the `Maybe` which is returned from `fetchBlockAt` will only run
the lambda we pass to it if there **is** a `Block` at the given position.

The operation is rather simple. What we do here is:

1. We pass the `entity` we want to move and the `position` where we want to move it.
2. We create a `success` variable which holds a `Boolean` value representing whether the operation was successful
3. We fetch both blocks
4. We only proceed if both blocks are present
5. In that case `success` is true
6. Then we return `success`
7. This is an example of giving a name to a logical operation. In this case it is very simple but sometimes logical
   operations become very complex and it makes sense to give them a name like this ("both blocks present?") so they
   are easy to reason about.
   
Then our actual `Command` class will be used to wire these things together:

```kotlin
package org.hexworks.cavesofzircon.systems

import org.hexworks.amethyst.api.Consumed
import org.hexworks.amethyst.api.Pass
import org.hexworks.amethyst.api.Response
import org.hexworks.amethyst.api.base.BaseFacet
import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.cavesofzircon.commands.MoveTo
import org.hexworks.cavesofzircon.extensions.GameCommand
import org.hexworks.cavesofzircon.world.GameContext

object Movable : BaseFacet<GameContext>() {

    override fun executeCommand(command: GameCommand<out EntityType>) = command.responseWhenCommandIs(MoveTo::class) { (context, entity, position) -> // 1
        val world = context.world
        var result: Response = Pass                     // 2
        if (world.moveEntity(entity, position)) {       // 3
            result = Consumed                           // 4
        }

        result                                          // 5
    }
}
```

This system is very simple but it is useful for learning how to write `Facet`s:

> Hey, what's `Pass` and `Consumed`? Why do we have to return anything? Good question! When an `Entity` receives
a `Command` it tries to delegate the given `Command` to its `Facet`s in order. Each `Facet` has to return a
`Response`. There are 3 kinds: `Pass`, `Consumed` and `CommandResponse`. If we return `Pass`, the loop continues
and the `Entity` tries the next `Facet`. If we return `Consumed`, the loop stops. `CommandResponse` is special,
we can return a new command using it and the `Entity` will continue the loop using the new `Command`! This is
useful for implementing complex interactions between entities like the one described in [this video](https://youtu.be/U03XXzcThGU?t=697).

1. A `Facet` accepts any `GameCommand` so we have to make sure that we only handle `MoveTo`. For this *Amethyst*
   supplies the `responseWhenCommandIs` function. What this function does is that it checks whether the `Class`
   we give to it is the same as the `command`. If `true`, our block is executed, if `false` `Pass` is returned instead.
























