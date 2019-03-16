---
excerpt: "New tutorial article for Caves of Zircon, and new tilesets are arriving to Zircon!"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---


This week **we finished the [next article](https://hexworks.org/posts/tutorials/2019/02/13/how-to-make-a-roguelike-the-player.html)**
in the [tutorial series](https://hexworks.org/posts/tutorials/)!

**Zircon**

This week there was only one new enhancement in Zircon due to our focus on the tutorial:
now `ComponentStyleSet` uses data binding. This makes it possible to bind the style of a
`Component` to another one which means that if the style of a bound `Component` changes,
the others will also get updated. Two-way data binding also works which helps with consistency.
Shoutout to /u/coldwarrl for the help!

**News flash** for CP437 lovers! I've bumped into a nice Dwarf Fortress tileset when I was
playing the game ([take a look](https://cdn.discordapp.com/attachments/363754040103796737/555399007006883841/unknown.png)) which I liked so much that I contacted the author and he agreed to let us put
it into Zircon so [this tileset](http://www.bay12forums.com/smf/index.php?topic=156968.0) is coming
soon to *Zircon* (all 3 variants) along with the "Hallowed" [color theme](http://www.bay12forums.com/smf/index.php?topic=172328.0)!

**Caves of Zircon Tutorial**

The [next article](https://hexworks.org/posts/tutorials/2019/02/13/how-to-make-a-roguelike-the-player.html)
is ready in the [tutorial series](https://hexworks.org/posts/tutorials/). This one took quite some
time to produce since we wanted it to be easy to understand because the follow-up articles will
depend on the knowledge obtained from this one. I hope we succeeded with it, so if you have some
feedback feel free to share it with us!

The [Amethyst](https://github.com/Hexworks/amethyst) project now has a README file which is the
byproduct of this article and we'll improve on it in the near future.

We've also added detailed information to the [Releases](https://github.com/Hexworks/caves-of-zircon-tutorial/releases) page of the tutorial project so if you don't want to code you can just check out the code
which was produced from the article.

I verified the code in the article by creating a branch from the original code, then resetting to
the previous tag and copying the code to the project form the article, then performing a `git diff`
so it should work properly, but feel free to tell us if there is a discrepancy!
