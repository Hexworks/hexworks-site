---
excerpt: "New tutorial article & Performance Upgrades"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Caves of Zircon Tutorial**

This week was about [Displaying Stats](https://hexworks.org/posts/tutorials/2019/06/14/how-to-make-a-roguelike-displaying-stats.html)
in *Caves of Zircon*. The article is ready and you can read it on [the site](https://hexworks.org/posts/tutorials/2019/06/14/how-to-make-a-roguelike-displaying-stats.html).

**Zircon**

We've worked on *sanitizing* the consistency of the `Component` APIs so whenever you try to build a `Component` all
of the values in the *component builder* are pre-filled with sensible defaults. We've been thinking hard on this
topic and now we began to converge on a proper solution for this problem.

We've also started working on optimizing the rendering in Zircon which will make the API calls much faster. This
will also enable us in the long run to relax the current limitations in the `GameArea`: you have to specify a number
of layers which can be in each `Block`. In the future there won't be this limit and the whole `GameArea` will be
much faster to enable rendering much bigger maps!

@Seveen, who is progressing through the tutorial started to work on [Dijkstra Maps](https://cdn.discordapp.com/attachments/363771631727804416/589008112124690453/dijkstramap.gif)
and the results look [really interesting](https://cdn.discordapp.com/attachments/363771631727804416/589008222354931723/stalker.gif).
`S` stands for "stalker". We plan to integrate this functionality into our projects at some point, we just have to
decide where will be the ideal place (possibly not Zircon itself). Exciting times!
