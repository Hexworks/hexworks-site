---
excerpt: "Now that we have a Player in our game let's learn how to move him around!"
title: "How To Make a Roguelike: #5 Exploring the Cave"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #5 Exploring the Cave"
series: coz
comments: true
updated_at: 2020-12-19
---

> In the previous article we introduced [Amethyst](https://github.com/Hexworks/amethyst) which provides the means to handle game entities easily. Using it we added the Player to our game, but it is not doing much, so let's teach them how to move around the cave!

## Kicking our World into Motion

We've had a `World` class for quite some time now, but it is rather static, meaning that we don't
have the means to evolve it over time. Let's add now a function to it which will enable us to
`update` it whenever the player presses a button:
  

```kotlin
// Add this to World.kt

import org.hexworks.zircon.api.screen.Screen
import org.hexworks.zircon.api.uievent.UIEvent

fun update(screen: Screen, uiEvent: UIEvent, game: Game) {  // 1
    engine.executeTurn(GameContext(                         // 2
            world = this,
            screen = screen,                                // 3
            uiEvent = uiEvent,                              // 4
            player = game.player))                          // 5
}
```

In order for this to work we also need to specify the type of our `Engine` a bit more clearly in `World`:

```kotlin
private val engine: TurnBasedEngine<GameContext> = Engine.create()
```

Here we

1. Create the function `update` which takes all the necessary objects as parameters
2. We use the context object which we created before to update the engine. If you were wondering before why this class will be necessary now you know: a `Context` object holds all the information which might be necessary to update the entity objects within our world
3. We pass the screen because we'll be using it to display dialogs and similar things
4. We'll inspect the `UIEvent` to determine what the user wants to do (like moving around). We're using `UIEvent` instead of `KeyboardEvent` here because it is possible that at some time we also want to use mouse events.
5. Adding the player entity to the context is not mandatory, but since we use it almost everywhere this little optimization will make our life easier.
   
Now we have to modify our `PlayView` to update our world whenever the user presses a key:
  
```kotlin
import org.hexworks.zircon.api.uievent.KeyboardEventType

// Add this to init in PlayView.kt
screen.handleKeyboardEvents(KeyboardEventType.KEY_PRESSED) {event, _ ->
    game.world.update(screen, event, game)
    Processed
}
```

> You could argue that this code: `game.world.update` is an antipattern (also called a [Train Wreck](http://wiki.c2.com/?TrainWreck)) and should be discouraged. I agree and we could have added an `update` method to the `Game` class itself but note that here `Game` is just a data structure holding the `World` and the `Player` and nothing else, so we'll leave it as it is.
  
Congratulations! With these changes the world is updated whenever the player presses a button!
The only thing which is left to do is to add some systems which handle the different aspects of input handling.

## Enabling Movement

Let's break down what do we want to **do** when a key is pressed:

- We accept the keys `W`, `A`, `S` and `D` as movement keys (up, left, down, right respectively)
- We move the player in the proper direction when either of those keys are pressed
- We scroll the view (if necessary) when the player is moved

All of the above steps can be encapsulated into their own system in the *SEA* model (just like in 
a traditional ECS model) so let's create an `InputReceiver`, a `Movable` and a `CameraMover` `System`.

> "Why don't we just write a `switch` and move the player/character by hand instead of splitting it into multiple operations?" you might ask. The reason is that by having systems which are completely oblivious of the outside world and just do their own job we enable a lot of interesting features to be implemented with ease. One of those things is having a demo AI component which sends the proper messages to the player entity instead of using UI inputs. Here is a [great talk](https://www.youtube.com/watch?v=U03XXzcThGU) by Brian Bucklew, the creator of Caves of Qud where he explains these concepts in depth and why they are useful.
  
As you might have seen in the previous article systems are either `Facet`s (enable the outside world to interact with our entity)
or `Behavior`s (interact with the world on their own). In this case `Movable` and `CameraMover` are `Facet`s and `InputReceiver` is
a `Behavior`.

A `Behavior` is straightforward: whenever the world gets updated it calls `update` on all of its entities, which in turn call `update` on all of their `Behavior`s. `Facet`s work a bit differently. They accept `Message`s.

Let's create the `MoveTo` message which holds all the data which is necessary to move our player. *Amethyst* supplies a `Message` interface for us:

```kotlin
interface Message<C : Context> {

    val context: C

    val source: Entity<EntityType, C>

}
```

This seems pretty straightforward. We have a `context` object (remember the `GameContext` we added before?) which has all the data we might need to perform actions and a `source`. `source` holds a reference to the entity where the `Message` is sent from. This handy feature lets entity objects respond to messages without maintaining a reference to the callee.

As with `GameEntity` before, we're going to create a typealias for `Message` as well, which substitutes our `GameContext` object in place of the `C` type parameter:

```kotlin
// Add this to the TypeAliases.kt file which we have created before
import org.hexworks.amethyst.api.Message

typealias GameMessage = Message<GameContext>
```

Let's put the `MoveTo` message in a new package named messages:

```kotlin
package com.example.cavesofzircon.messages

import com.example.cavesofzircon.extensions.GameEntity
import com.example.cavesofzircon.extensions.GameMessage
import com.example.cavesofzircon.world.GameContext
import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.zircon.api.data.Position3D

data class MoveTo(
        override val context: GameContext,
        override val source: GameEntity<EntityType>,
        val position: Position3D
) : GameMessage
```

All `Message`s have 2 mandatory parameters: `context` and `source`. We only add `position` here which holds the position where we want to move the player. As this class indicates, the `Movable` `System` will be oblivious to inputs and other things, it only cares about **moving** an entity.

The class which knows about all entity objects is the `World`, so let's add the logic of moving
one to it:

```kotlin
// Add this to World.kt
fun moveEntity(entity: GameEntity<EntityType>, position: Position3D): Boolean { // 1
    var success = false                                                         // 2
    val oldBlock = fetchBlockAt(entity.position)
    val newBlock = fetchBlockAt(position)                       // 3

    if (bothBlocksPresent(oldBlock, newBlock)) {                                // 4
        success = true                                                          // 5
        oldBlock.get().removeEntity(entity)
        entity.position = position
        newBlock.get().addEntity(entity)
    }
    return success                                                              // 6
}

private fun bothBlocksPresent(oldBlock: Maybe<GameBlock>, newBlock: Maybe<GameBlock>) =  // 7
        oldBlock.isPresent && newBlock.isPresent
```

> A quick refresher: the `map` operation on the `Maybe` which is returned from `fetchBlockAt` will only run the lambda we pass to it if there **is** a `Block` at the given position.

The operation is rather simple. What we do here is:

1. We pass the `entity` we want to move and the `position` where we want to move it.
2. We create a `success` variable which holds a `Boolean` value representing whether the operation was successful
3. We fetch both blocks
4. We only proceed if both blocks are present
5. In that case `success` is true
6. Then we return `success`
7. This is an example of giving a name to a logical operation. In this case it is very simple but sometimes logical operations become very complex and it makes sense to give them a name like this ("both blocks present?") so they are easy to reason about.
   
Then our `Movable` class will be used to wire these things together:

```kotlin
package com.example.cavesofzircon.systems

import com.example.cavesofzircon.messages.MoveTo
import com.example.cavesofzircon.world.GameContext
import org.hexworks.amethyst.api.Consumed
import org.hexworks.amethyst.api.Pass
import org.hexworks.amethyst.api.Response
import org.hexworks.amethyst.api.base.BaseFacet

object Movable : BaseFacet<GameContext, MoveTo>(MoveTo::class) { // 1

    override suspend fun receive(message: MoveTo): Response {
        val (context, entity, position) = message               // 2
        val world = context.world
        var result: Response = Pass                             // 3
        if (world.moveEntity(entity, position)) {               // 4
            result = Consumed                                   // 5
        }

        return result
    }

}
```

This system is very simple but it is useful for learning how to write `Facet`s:

> Hey, what's `Pass` and `Consumed`? Why do we have to return anything? Good question! When an `Entity` receives a `Message` it tries to send the given message to its `Facet`s in order. Each `Facet` has to return a `Response`. There are 3 kinds: `Pass`, `Consumed` and `MessageResponse`. If we return `Pass`, the loop continues and the entity tries the next `Facet`. If we return `Consumed`, the loop stops. `MessageResponse` is special, we can return a new message using it and the entity will continue the loop using the new `Message`! This is useful for implementing complex interactions between entities like the one described in [this video](https://youtu.be/U03XXzcThGU?t=697).

1. A `Facet` accepts only a specific messagek so we have to indicate that we only handle `MoveTo`.
2. This funky `(context, entity, position)` code is called [Destructuring](https://kotlinlang.org/docs/reference/multi-declarations.html). This might be familiar for [Python folks](https://riptutorial.com/python/example/14981/destructuring-assignment) and what it does is that it unpacks the values from an object which supports it. So writing this:
   ```kotlin
   val (context, entity, position) = myObj
   ```
   is the equivalent of writing this:
   ```kotlin
   val context = myObj.context
   val entity = myObj.entity
   val position = myObj.position
   ```
3. Here we say that we'll return `Pass` as a default
4. Then we check whether moving the entity was successful or not (remember the `success` return value?)
5. And if it was, we tell *Amethyst*, that we `Consumed` the `Message`
6. Finally we return the `result`

## Handling Player Input

Now we know how to move entities, but we don't know how to handle input. Let's write a simple `Behavior` which we can add to our player entity for this purpose:

```kotlin
package com.example.cavesofzircon.systems

import com.example.cavesofzircon.extensions.GameEntity
import com.example.cavesofzircon.extensions.position
import com.example.cavesofzircon.messages.MoveTo
import com.example.cavesofzircon.world.GameContext
import org.hexworks.amethyst.api.base.BaseBehavior
import org.hexworks.amethyst.api.entity.Entity
import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.zircon.api.uievent.KeyCode
import org.hexworks.zircon.api.uievent.KeyboardEvent

object InputReceiver : BaseBehavior<GameContext>() {

    override suspend fun update(entity: Entity<EntityType, GameContext>, context: GameContext): Boolean {
        val (_, _, uiEvent, player) = context                           // 1
        val currentPos = player.position
        if (uiEvent is KeyboardEvent) {                                 // 2
            val newPosition = when (uiEvent.code) {                     // 3
                KeyCode.KEY_W -> currentPos.withRelativeY(-1)
                KeyCode.KEY_A -> currentPos.withRelativeX(-1)
                KeyCode.KEY_S -> currentPos.withRelativeY(1)
                KeyCode.KEY_D -> currentPos.withRelativeX(1)
                else -> {
                    currentPos                                          // 4
                }
            }
            player.receiveMessage(MoveTo(context, player, newPosition)) // 5
        }
        return true
    }
}
```

`InputReceiver` is pretty simple, it just checks for `WASD`, and acts accordingly:

1. We destructure our context object so its properties are easy to access. Destructuring is positional, so here `_` means that we don't care about that specific property.
2. We only want `KeyboardEvent`s for now so we check with the `is` operator. This is similar as the `instanceof` operator in Java but a [bit more useful](https://kotlinlang.org/docs/reference/typecasts.html).
3. We use `when` which is similar to `switch` in Java to check which key was pressed. Zircon has a `KeyCode` for all keys which can be pressed. `when` in Kotlin is also an expression, and not a statement so it returns a value. We can change it into our `newPosition` variable.
4. If some key is pressed other than `WASD`, then we just return the current position, so no movement will happen
5. We receive the `MoveTo` message on our player here.
   
Now with our `Movable` and `InputReceiver` systems in place we should be able to augment the player entity to be able to move around in the world! Let's do that by modifying the player creation code in our `EntityFactory`:

```kotlin
// new imports
import com.example.cavesofzircon.systems.InputReceiver
import com.example.cavesofzircon.systems.Movable

fun newPlayer() = newGameEntityOfType(Player) {
    attributes(EntityPosition(), EntityTile(GameTileRepository.PLAYER))
    behaviors(InputReceiver)
    facets(Movable)
}
```

Now if we run this code this is what we'll see:

![Move Through Walls](/assets/img/move_through_walls.gif)

There are some problems though. First, we can move through walls. This is not a problem if we're ghosts, but in our case this is not what we want. Second, the camera doesn't follow the player. This is not a problem if the world is so small that it fits on our screen, but we want something bigger. Let's address the second issue first:

## Scrolling the World

Let's start by making our world bigger to demonstrate the problem. Open the `GameConfig` class and modify `WORLD_SIZE` to this:

```kotlin
val WORLD_SIZE = Size3D.create(WINDOW_WIDTH * 2, WINDOW_HEIGHT * 2 , DUNGEON_LEVELS)
```

It is not too big but will do for demonstrational purposes:

![Leaving The World](/assets/img/leaving_the_world.gif)

Oops. We can move out of the world! Let's fix this by adding the `CameraMover` facet to our player entity. For this we're going to create a new message, `MoveCamera`:

```kotlin
package com.example.cavesofzircon.messages

import com.example.cavesofzircon.extensions.GameEntity
import com.example.cavesofzircon.extensions.GameMessage
import com.example.cavesofzircon.world.GameContext
import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.zircon.api.data.Position3D

data class MoveCamera(
        override val context: GameContext,
        override val source: GameEntity<EntityType>,
        val previousPosition: Position3D
) : GameMessage
```

Using this the `CameraMover` facet can be implemented. The logic might seem a bit tricky, but I'm going to explain below:

```kotlin
package com.example.cavesofzircon.systems

import com.example.cavesofzircon.extensions.position
import com.example.cavesofzircon.messages.MoveCamera
import com.example.cavesofzircon.world.GameContext
import org.hexworks.amethyst.api.Consumed
import org.hexworks.amethyst.api.Response
import org.hexworks.amethyst.api.base.BaseFacet

object CameraMover : BaseFacet<GameContext, MoveCamera>(MoveCamera::class) {
    
    override suspend fun receive(message: MoveCamera): Response {
        val (context, source, previousPosition) = message
        val world = context.world
        val screenPos = source.position - world.visibleOffset            // 1
        val halfHeight = world.visibleSize.yLength / 2                   // 2
        val halfWidth = world.visibleSize.xLength / 2
        val currentPosition = source.position
        when {                                                           // 3
            previousPosition.y > currentPosition.y && screenPos.y < halfHeight -> {
                world.scrollOneBackward()
            }
            previousPosition.y < currentPosition.y && screenPos.y > halfHeight -> {
                world.scrollOneForward()
            }
            previousPosition.x > currentPosition.x && screenPos.x < halfWidth -> {
                world.scrollOneLeft()
            }
            previousPosition.x < currentPosition.x && screenPos.x > halfWidth -> {
                world.scrollOneRight()
            }
        }
        return Consumed
    }
}
```

1. The player's position on the screen can be calculated by subtracting the `World`'s `visibleOffset`
   from the player's position. The `visibleOffset` is the top left position of the visible part of
   the `World` relative to the top left corner of the whole `World` (which is `0, 0`).
2. We calculate the center position of the visible part of the world here
3. And we only move the camera if we moved in a certain direction (left for example) and the `Entity`'s
   position on the screen is left of the middle position. The logic is the same for all directions, but
   we use the corresponding `x` or `y` coordinate.
   
Now if we start the application nothing happens yet, because no one sends the `MoveCamera` message! Where should we put it? One option is to put it in the `InputReceiver`, but a better solution is to use the `MessageResponse` functionality which is provided by *Amethyst* to return a new `Message` when we move the player. Why is it better this way? Because the `Movable` system has knowledge of all the related information (position and direction) and moving an entity is closely related to moving the camera so it maintains the cohesion of the code. Let's see what we need to change in `Movable`:

```kotlin
// add these to the imports
import com.example.cavesofzircon.attributes.types.Player
import com.example.cavesofzircon.extensions.position
import com.example.cavesofzircon.messages.MoveCamera
import org.hexworks.amethyst.api.MessageResponse

override suspend fun receive(message: MoveTo): Response {
    val (context, entity, position) = message
    val world = context.world
    val previousPosition = entity.position               // 1
    var result: Response = Pass
    if (world.moveEntity(entity, position)) {
        result = if (entity.type == Player) {            // 2
            MessageResponse(MoveCamera(                  // 3
                    context = context,
                    source = entity,
                    previousPosition = previousPosition
            ))
        } else Consumed                                  // 4
    }
    return result
}
```

1. First, we save the previous position before we change it
2. If the move was successful and the entity we moved is the player
3. We return the `MessageResponse`
4. Otherwise we keep the `Consumed` response

Now if we add `CameraMover` to our player:

```kotlin
// add this to the imports
import com.example.cavesofzircon.systems.CameraMover

fun newPlayer() = newGameEntityOfType(Player) {
    attributes(EntityPosition(), EntityTile(GameTileRepository.PLAYER))
    behaviors(InputReceiver)
    facets(Movable, CameraMover)
}
```

and run the program we see proper scrolling:

![Scrolling with Camera](/assets/img/scrolling_with_camera.gif)


## Conclusion

We've covered a lot of ground in this article. We added life to our world by making it updatable and we also added player interaction with the keyboard, moving and camera scrolling!

In the next article we'll take a look at how entities can interact with each other. This will also involve fixing our problem of being able to move through walls. *Exciting!*

Until then go forth and *kode on*!
 
> The code of this article can be found under the `5_MOVING_AROUND` tag.




















