---
excerpt: "We can kill a lot of monsters now but we don't gain anything else apart from the loot. Let's add leveling to our game!"
title: "How To Make a Roguelike: #17 Experience and Leveling Up"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #17 Experience and Leveling Up"
series: coz
comments: true
future: false
published: false
---

> Killing *zombies*, *fungi* and *bats* are fun, especially when we get loot and food from them. What's
still missing is *gaining experience levels*, so we're going to add it now.

## Designing Experience Levels

If you have ever played an RPG before you might be familiar with the concept. After the *player* gains
a significant amount of experience they level up which means that they can pick some benefit which will
make the *player* stronger, then we go back to accumulating *experience*. Each level is harder to
achieve than the previous one, and there is usually a level cap.

For our game we're going to implement something simple. The amount of experience an `Entity` gains will
be calculated in the following way: 

```
(target hp + target attack value + target defense value) - attacker's current level * 2
```

This will make the gainer get more *xp* if the target is more powerful and less *xp* with each
level.

The threshold for leveling up is calculated in the following way:

```kotlin
if current xp > current level ^ 1.5 * 20 then level up
```

So we will need more and more xp with each level. Let's take a look at how we can implement this!

## Accumulating Experience



## Conclusion


Until then go forth and *kode on*!
 
> The code of this article can be found under the `17_EXPERIENCE_AND_LEVELING_UP` tag.
