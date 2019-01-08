---
excerpt: This week was mostly spent with the Component system upgrades. We also had some fun with lightweight animations.
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
comments: true
---

This week:

- We worked on the component wrapper refactor. This lead to a lot of fun stuff like enabling all components to have arbitrary decorations.
  Currently `Panel`, `Label`, `CheckBox` and `Header` have this new functionality, and the rest will be refactored next week. [Here](https://cdn.discordapp.com/attachments/363771631727804416/491720810986340352/unknown.png)
  are some `Label`s with unnecessarily over the top decorations.
- There are also some new examples showing off the decoration options for components. [Here](https://cdn.discordapp.com/attachments/363771631727804416/491721308217016321/unknown.png) is
  the `Panel` example.
- We were playing with the idea of having simple and lightweight animations on `Tile`s. We already have `Animation`s which work for `TileGraphics` objects
  but they are resource-intensive. So we played around with the idea of having stochastic processes generate such animations. [This](https://cdn.discordapp.com/attachments/363771631727804416/490995292632514560/GIF.gif)
  is a proof of concept of random bubbly lava and [this one](https://cdn.discordapp.com/attachments/363771631727804416/491268920217698304/lava.gif) is a fleshed
  out example which uses a Markov Chain to simulate two different branches: "bubbly lava" (yellow exploding stuff) and "heated lava" (which you see in the rest of the gif).
  This turned out to be a very effective and resource-friendly way to do extensive animations and the only drawback of this method is that `Tile`s are animated individually:
  you can't have complex multi-tile stuff with this method. Nevertheless we're kind of OK with this solution so you can expect to see some more of these animations to arrive
  in the upcoming Sharing Saturdays! Feedback is also welcome!
- We have also started to work on a game concept with some of my friends (not-yet part of Hexworks) dubbed *Project Forlorn*.
  This will be a story-driven adventure roguelike game with emphasis on exploration. We already have some documents detailing the
  backstory and some of the game mechanics. We can't really say much about it right now because we want you to explore it for yourself
  once the first version is released.


A next major release is coming up for this Autumn, so **stay tuned.**
