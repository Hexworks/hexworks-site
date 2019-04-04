---
excerpt: "While walking through walls is fun, it is not a good game mechanics. Let's improve on that with Entity Interactions!"
title: "How To Make a Roguelike: #6 Entity Interactions"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #6 Entity Interactions"
series: coz
comments: true
future: false
published: false
---

> In the previous article we added cave exploration and scrolling, but there is not much going on
between the entities of our world. Let's improve on that by enabling the player to interact with
the world with its own Entity Actions!

## A World of Entities

In a traditional [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system) almost all
*things* can be entities. This is also true with [Amethyst](https://github.com/Hexworks/amethyst). In our case
there are two things in the world right now: **floors** and **walls**. Both of them are just simple `Block`s
in our game world. We don't want to interact with floors yet, but let's make *wall* an `Entity`!

Our `GameBlock` already supports adding entities to it, so let's upgrade it a bit so that we can create one
with an initial `Entity`:

```kotlin

    // add this to the GameBlock class
    companion object {

        fun createWith(entity: GameEntity<EntityType>) = GameBlock(
                        currentEntities = mutableListOf(entity))
    }
```

> A quick refresher: [companion object](https://proandroiddev.com/a-true-companion-exploring-kotlins-companion-objects-dbd864c0f7f5)s
are singleton `object`s which are added to a regular class. They work in a similar way as the `static` keyword in Java, you can
call functions and access properties on them by using their parent class. Here we can invoke `create` in the following
way: `GameBlock.create()`. 

Here we add a static factory method which takes a `GameEntity` as a parameter. `also` here is a
[scoping function](https://kotlinlang.org/docs/reference/scope-functions.html).
You can read more about them [here](https://kotlinlang.org/docs/reference/scope-functions.html). The TL;DR is that
`also` creates a scope where we can invoke functions on the scoped object (the new `GameBlock` here) and after that
the scoped object is returned. This is very useful for initializing objects after creation as you can see here.

Now if we want a *wall* `Entity` we need to create a type for it first:

```kotlin

// put this in EntityTypes.kt
object Wall : BaseEntityType(
        name = "wall")
```

Apart from a type a *wall* also has a *position* and an attribute which tells us that it occupies a block (eg: you can't
just move into that block with the player). We already have `EntityPosition` so let's create our first **flag** attribute,
`BlockOccupier`:

```kotlin
package org.hexworks.cavesofzircon.attributes.flags

import org.hexworks.amethyst.api.Attribute

object BlockOccupier : Attribute
```

This is an `object` because we will only ever need a single instance of it. `BlockOccupier` works as a flag:
if an `Entity` has it the block is occupied, otherwise it is not. This will enable us to add walls, creatures
and other things to our dungeon which can't occupy the same space.

Now creating a *wall* `Entity` is pretty straightforward:

```kotlin
import org.hexworks.cavesofzircon.attributes.flags.BlockOccupier
import org.hexworks.cavesofzircon.attributes.types.Wall
import org.hexworks.zircon.api.data.impl.Position3D

// put this function to EntityFactory.kt
fun newWall() = newGameEntityOfType(Wall) {
    attributes(
            EntityPosition(),
            BlockOccupier,
            EntityTile(GameTileRepository.WALL))
}
```

Now to start using this we just make our `GameBlockFactory` create the *wall* blocks with the new factory method:

```kotlin
// put this in GameBlockFactory
fun wall() = GameBlock.createWith(EntityFactory.newWall())
```

There is one thing we need to fix. In the `WorldBuilder`'s smoothing algorithm we check whether a block is a floor.
This will no longer work because the `defaultTile` of a `GameBlock` is now always a floor tile so let's change it
to use `isEmptyFloor` instead:

```kotlin

// change this in WorldBuilder#smooth

if (block.isEmptyFloor) {
    floors++
} else rocks++
```

> Wait, what happened to our `defaultTile`? Well, the thing is that when we demolish a wall we want to see the default
tile in its place which is a floor in our case. Previously we did not have the ability to do this, but now we're
going to implement it now.

If we run the program now there won't be any visible changes to gameplay. The reason is that although we added
the `BlockOccupier` flag to our game it is not used by anything...yet. Let's take a look at how can we go about
implementing the interaction between entities.

## Adding Entity Interactions

Let's start for a second and think about how this works in most roguelike games. When the player is idle nothing
happens since updating the game world is bound to player movement. The usual solution is that whenever we try
to move into a new tile the game tries to figure out what to do. If it is an enemy creature we *attack* it.
If it is an empty tile we *move* into it. It is also possible to move off of a ledge in which case the
player usually suffers some *damage* or *dies*. To sum it all up these are the steps we want to perform
when the player presses a movement key:

- Check what's in the block *where* we want to move
- Take a look at what we *are able to do*
- Try each one on the target block and see what happens


## Conclusion

In this article we've learned how entities can interact with each other so now there is nothing stopping us
from adding new ways for changing our world which enriches the player experience. We've also learned how
everything can be an `Entity` and why this is very useful.

In the next article we'll add a new kind of `Entity`: **monsters**!

Until then go forth and *kode on*!
 
> The code of this article can be found under the `6_ENTITY_INTERACTIONS` tag.




















