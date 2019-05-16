---
excerpt: "Since we have combat, monsters, and fog of war, now is the time to add items to our game!"
title: "How To Make a Roguelike: #12 Items and Inventory"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #12 Items and Inventory"
series: coz
comments: true
published: false
future: false
---

> We have almost everything for our game. We can explore the cave, fight with monsters, traverse levels, but
there is an important piece which is missing: **items**. In this session we'll add items, and the corresponding
*inventory* into our game.

> For this article we need to update our Maven dependencies for the libraries we use:
>
>
>

## Between a Rock and a Hard Place

*Items* in a roguelike are very important. They can be used to open doors (*keys*), eaten to fight hunger (*food*)
or gathered to sell them later on (*treasures*). Let's start introducing items to our game by adding *rocks* which
we can pick up...more specifically **Zircons**!.

First of all let's think about how we would represent *items*. What's true for all *items* is that they have a
`Tile` just like the *player* and a *bat*. Because we plan to have an *inventory* as well it seems like a good idea
to have *icons* for our *items* as well. We're going to use `GraphicalTile`s for this as you'll see later.

Since we already have an `EntityTile` let's start by adding an `ItemIcon` *attribute*:

```kotlin
package org.hexworks.cavesofzircon.attributes

import org.hexworks.amethyst.api.Attribute
import org.hexworks.zircon.api.data.GraphicalTile

data class ItemIcon(val iconTile: GraphicalTile) : Attribute
```

With this we can create an interface for our `Item`s and also add some *extension properties* which will help when
using them:

```kotlin
package org.hexworks.cavesofzircon.attributes.types

import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.cavesofzircon.attributes.EntityTile
import org.hexworks.cavesofzircon.attributes.ItemIcon
import org.hexworks.cavesofzircon.extensions.GameEntity
import org.hexworks.zircon.api.data.GraphicalTile
import org.hexworks.zircon.api.data.Tile

interface Item : EntityType

val GameEntity<Item>.tile: Tile
    get() = findAttribute(EntityTile::class).get().tile

val GameEntity<Item>.iconTile: GraphicalTile
    get() = findAttribute(ItemIcon::class).get().iconTile
```

And we can also add a `typealias` for `GameEntity<Item>` to our `TypeAliases.kt` file:

```kotlin
typealias GameItem = GameEntity<Item>
```

so that we can refer to game items in a convenient way.
As for our *Zircon* we just have to add a new `EntityType` to our game:

```kotlin
// Add this to EntityTypes.kt

object Zircon : BaseEntityType(
        name = "Zircon",
        description = "A small piece of Zircon. Its value is unfathomable."), Item
```

As usual we're going to add a new `Tile` for this in our `GameTileRepository` and `GameColors`:

```kotlin
// put this in GameColors
val ZIRCON_COLOR = TileColors.fromString("#dddddd")

// and this in GameTileRepository
val ZIRCON  = Tiles.newBuilder()
        .withCharacter(',')
        .withBackgroundColor(GameColors.FLOOR_BACKGROUND)
        .withForegroundColor(GameColors.ZIRCON_COLOR)
        .buildCharacterTile()
```

All our items will also be `Entity` objects so to create a *Zircon* we just have to add it to our `EntityFactory`:

```kotlin
import org.hexworks.cavesofzircon.attributes.ItemIcon
import org.hexworks.cavesofzircon.attributes.types.Zircon
import org.hexworks.zircon.api.GraphicalTilesetResources
import org.hexworks.zircon.api.Tiles

fun newZircon() = newGameEntityOfType(Zircon) {
    attributes(ItemIcon(Tiles.newBuilder()
            .withName("white gem")
            .withTileset(GraphicalTilesetResources.nethack16x16())
            .buildGraphicTile()),
            EntityPosition(),
            EntityTile(GameTileRepository.ZIRCON))
}
```

What's interesting here is that we're using the graphical *tiles* from the *Nethack Tileset*. These come from the
Zircon distribution and you can mix and match *CP437* and *Graphical Tiles* as you see fit!

## Adding Zircons to our Game

Now that we have the *entities* we just need to add them to the game world so that our adventurer can pick them up.
Let's scatter them randomly throughout the levels.

First let's define a configuration value for our *zircons* in `GameConfig`:

```kotlin
const val ZIRCONS_PER_LEVEL = 20
``` 

then we can add a function for adding them in `GameBuilder`:

```kotlin
import org.hexworks.cavesofzircon.GameConfig.ZIRCONS_PER_LEVEL

private fun addZircons() = also {
    repeat(world.actualSize().zLength) { level ->
        repeat(GameConfig.ZIRCONS_PER_LEVEL) {
            EntityFactory.newZircon().addToWorld(level)
        }
    }
}
```

and call it in the `buildGame` function:

```kotlin
addZircons()
```

Let's see what we have:

![Finding Zircons](/assets/img/finding_zircons.gif)

So far so good! Now we just need a way to pick them up and store them! Let's introduce the `Inventory`:

## Adding an Inventory

An *inventory* is just an `Attribute` which is capable of storing *entities* which happen to be
`Item`s. A good *inventory* also has a size and some means to add/remove *items* from it. Let's take
a look at our implementation:

```kotlin
package org.hexworks.cavesofzircon.attributes

import org.hexworks.amethyst.api.Attribute
import org.hexworks.cavesofzircon.extensions.GameItem
import org.hexworks.cobalt.datatypes.Identifier
import org.hexworks.cobalt.datatypes.Maybe

class Inventory(val size: Int) : Attribute {                        // 1

    private val currentItems = mutableListOf<GameItem>()    

    val items: List<GameItem>                               
        get() = currentItems.toList()

    val isEmpty: Boolean                                            // 2
        get() = currentItems.isEmpty()

    val isFull: Boolean                                             // 3
        get() = currentItems.size >= size

    fun findItemBy(id: Identifier): Maybe<GameItem> {       // 4
        return Maybe.ofNullable(items.firstOrNull { it.id == id })
    }

    fun addItem(item: GameItem): Boolean {                  // 5
        return if (isFull.not()) {
            currentItems.add(item)
        } else false
    }

    fun removeItem(entity: GameItem): Boolean {             // 6
        return currentItems.remove(entity)
    }
}
```

This implementation is very simple, it:

1. has a size
2. we can tell whether it is empty
3. or full
4. we can find an item in it
5. add new ones
6. and remove them

Now that we have an actual `Inventory` `Entity` let's add a a counterpart for `Item`: `ItemHolder` which represents
an `Entity` which has an `Inventory`:

```kotlin
package org.hexworks.cavesofzircon.attributes.types

import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.cavesofzircon.attributes.Inventory
import org.hexworks.cavesofzircon.extensions.GameEntity
import org.hexworks.cavesofzircon.extensions.GameItem

interface ItemHolder : EntityType

fun GameEntity<ItemHolder>.addItem(item: GameItem) = inventory.addItem(item)

val GameEntity<ItemHolder>.inventory: Inventory
    get() = findAttribute(Inventory::class).get()

```

add a convenient `typealias` for `GameEntity<ItemHolder>` to `TypeAliases.kt`:

```kotlin
typealias GameItemHolder = GameEntity<ItemHolder>
```

and make the *player* type implement it:

```kotlin
object Player : BaseEntityType(
        name = "player"), Combatant, ItemHolder
```

Let's make our *player* have an `Inventory` now so add it in the `newPlayer` function in `EntityFactory`:

```kotlin
// new import
import org.hexworks.cavesofzircon.attributes.Inventory

fun newPlayer() = newGameEntityOfType(Player) {
    attributes(
            Vision(9),
            EntityPosition(),
            BlockOccupier,
            CombatStats.create(
                    maxHp = 100,
                    attackValue = 10,
                    defenseValue = 5),
            EntityTile(GameTileRepository.PLAYER),
            EntityActions(Dig::class, Attack::class),
            Inventory(10))
    behaviors(InputReceiver)
    facets(Movable, CameraMover, StairClimber, StairDescender, Attackable, Destructible)
}
```

Now to be able to pick *items* up we just need to add a shortcut for it. Let's use the `p` button for this so whenever
the player stands on a *tile* which has *items* on it and presses `p` the item at the top should appear in the `Inventory`.
You know the drill by now: first we need to add a `Command` for this:

```kotlin
package org.hexworks.cavesofzircon.commands

import org.hexworks.cavesofzircon.attributes.types.ItemHolder
import org.hexworks.cavesofzircon.extensions.GameCommand
import org.hexworks.cavesofzircon.extensions.GameItemHolder
import org.hexworks.cavesofzircon.world.GameContext
import org.hexworks.zircon.api.data.impl.Position3D

data class PickItemUp(override val context: GameContext,
                      override val source: GameItemHolder,                  // 1
                      val position: Position3D) : GameCommand<ItemHolder>

```

Here:

1. We used the `ItemHolder` [trait](https://en.wikipedia.org/wiki/Trait_(computer_programming)) here so this `Command`
   will only work for entities which have inventories. This is similar to what we did with `Combatant` previously where
   we made sure that only those entities can start a combat which have the necessary facets for it.
   
Now that we have a `Command` for picking items up we just need to add the appropriate `Behavior` which consumes it. For this
we need to add a helper function to our `EntityExtensions.kt` which we can use to filter the entities for a given type:

```kotlin
inline fun <reified T : EntityType> Iterable<AnyGameEntity>.filterType(): List<Entity<T, GameContext>> {
    return filter { T::class.isSuperclassOf(it.type::class) }.toList() as List<Entity<T, GameContext>>
}
```

With this we can check the items of a `Block` for a given type (`Item` in our case) in the following code:

```kotlin
package org.hexworks.cavesofzircon.systems

import org.hexworks.amethyst.api.Consumed
import org.hexworks.amethyst.api.base.BaseFacet
import org.hexworks.amethyst.api.entity.EntityType
import org.hexworks.cavesofzircon.attributes.types.Item
import org.hexworks.cavesofzircon.attributes.types.addItem
import org.hexworks.cavesofzircon.commands.PickItemUp
import org.hexworks.cavesofzircon.extensions.GameCommand
import org.hexworks.cavesofzircon.extensions.filterType
import org.hexworks.cavesofzircon.extensions.isPlayer
import org.hexworks.cavesofzircon.functions.logGameEvent
import org.hexworks.cavesofzircon.world.GameContext
import org.hexworks.cavesofzircon.world.World
import org.hexworks.cobalt.datatypes.Maybe
import org.hexworks.cobalt.datatypes.extensions.flatMap
import org.hexworks.cobalt.datatypes.extensions.map
import org.hexworks.zircon.api.data.impl.Position3D

object ItemPicker : BaseFacet<GameContext>() {

    override fun executeCommand(command: GameCommand<out EntityType>) = command.responseWhenCommandIs(PickItemUp::class) { (context, itemHolder, position) ->
        val world = context.world
        world.findTopItem(position).map { item ->       // 1
            if (itemHolder.addItem(item)) {             // 2
                world.removeEntity(item)                // 3
                val subject = if (itemHolder.isPlayer) "You" else "The $itemHolder" // 4
                val verb = if (itemHolder.isPlayer) "pick up" else "picks up"
                logGameEvent("$subject $verb the $item.")
            }
        }
        Consumed
    }

    private fun World.findTopItem(position: Position3D) =
            fetchBlockAt(position).flatMap { block ->       // 5
                Maybe.ofNullable(block.entities.filterType<Item>().firstOrNull())
            }
}
```

`ItemPicker` consumes `PickItemUp` commands and

1. It tries to find the top item on a block and
2. If there was an item there it tries to add it to the inventory of the item holder
3. If that succeeded it removes the item from the world
4. Then prints a nice log message
5. Here what `flatMap` does is that it unwraps the `block` from the `Maybe` (it does nothing if the Maybe
   is empty) and re-wraps it int another `Maybe` which holds our item. Note that this can also be empty if
   there were no `Item`s in the `Block`.
   
Now that we have the `Command` and the `Behavior` ready we just need to add the `p` shortcut to `InputReceiver`:


```kotlin
// new import
import org.hexworks.cavesofzircon.commands.PickItemUp

    override fun update(entity: GameEntity<out EntityType>, context: GameContext): Boolean {
        val (_, _, uiEvent, player) = context
        val currentPos = player.position
        if (uiEvent is KeyboardEvent) {
            when (uiEvent.code) {
                KeyCode.KEY_W -> player.moveTo(currentPos.withRelativeY(-1), context)
                KeyCode.KEY_A -> player.moveTo(currentPos.withRelativeX(-1), context)
                KeyCode.KEY_S -> player.moveTo(currentPos.withRelativeY(1), context)
                KeyCode.KEY_D -> player.moveTo(currentPos.withRelativeX(1), context)
                KeyCode.KEY_R -> player.moveUp(context)
                KeyCode.KEY_F -> player.moveDown(context)
                KeyCode.KEY_P -> player.pickItemUp(currentPos, context)     // 1
                else -> {
                    logger.debug("UI Event ($uiEvent) does not have a corresponding command, it is ignored.")
                }
            }
        }
        return true
    }

    private fun GameEntity<Player>.pickItemUp(position: Position3D, context: GameContext) {     // 2
        executeCommand(PickItemUp(context, this, position))
    }
    
    // ...
```

Here we:

1. Just add a new key handler for `KEY_P`
2. And a helper method which fires the `PickItemUp` command. `PickItemUp` only accepts `ItemHolder`s but we
   augmented the `Player` type with it so everything works just fine.

Now if we add the `ItemPicker` *facet* to our player entity:

```kotlin
// new import
import org.hexworks.cavesofzircon.systems.ItemPicker

    fun newPlayer() = newGameEntityOfType(Player) {

        // ...

        facets(/* ... */ ItemPicker)
    }
```

we're able to pick up items in our game:

![Pick Items Up]()

**Wow**, that's nice! But how do we check the *inventory*?

## Displaying the Inventory



## Conclusion


Next we're going to add more items, and some new mechanics to our game: *food* and *hunger*!

Until then go forth and *kode on*!
 
> The code of this article can be found under the `12_ITEMS_AND_INVENTORY` tag.
