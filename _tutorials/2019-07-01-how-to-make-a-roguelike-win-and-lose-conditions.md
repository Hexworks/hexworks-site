---
excerpt: "Our game is almost complete now, and the only thing which is missing is a Victory and a Lose screen. Let's add them now!"
title: "How To Make a Roguelike: #19 Win and Lose Conditions"
tags: [zircon, caves-of-zircon, roguelikes, kotlin]
author: addamsson
short_title: "How To Make a Roguelike: #19 Win and Lose Conditions"
series: coz
comments: true
future: false
published: false
---

> Our game has everything it needs: items, monsters, exploration, hunger and experience levels.
To make it complete we need to add proper *win* and *lose* conditions as well.
Let's make it happen now!

## The Sorry State of the Endgame

In one of the earlier articles we've created a `WinView` and a `LoseView` but we're not using
them yet. If the *player* dies nothing happens and we can't interact with the world anymore.

We also can't pick up all the *zircon*s lying around because the inventory quickly fills up.

Moreover there is no *exit* so we are forever stuck in the *Caves of Zircon* Let's start
fixing this by reworking how we collect *Zircons*.

## Collecting Zircons



## Conclusion


Until then go forth and *kode on*!
 
> The code of this article can be found under the `19_WIN_AND_LOSE_CONDITIONS` tag.
