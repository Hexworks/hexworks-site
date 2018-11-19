---
excerpt: Release preparation with some Modifier upgrading. Thanks for Coldwarrl for helping us out!
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: addamsson
short_title: "Weekly update: Zircon"
---

This week was mainly spent with preparing for the next major release and upgrading stuff:

- The code underwent some optimization so framework overhead dropped from `0.5ms` to `0.3ms`
- The API was "sanetized" which means that it is not a bit easier and more straightforward to use
- We had some extra time to create some new fancy `Modifier`s:
  - [This](https://cdn.discordapp.com/attachments/363771631727804416/496291541808316426/fadein.gif) is a Fade-in effect with some `Glow` at the end
  - [This](https://cdn.discordapp.com/attachments/363771631727804416/496333815564730369/static.gif) is a stochastic text glitch effect
  - [This](https://cdn.discordapp.com/attachments/363771631727804416/496420195804381194/static.gif) is very similar to the previous one and it is a static effect	
- **Great thanks to** /u/coldwarrl who contributed the `LogArea` component: screenshot [here](https://cdn.discordapp.com/attachments/363754040103796737/496739805112041482/unknown.png)
  it supports all elements which is supported by `TextBox` (`Header`, `Paragraph`, `ListItem`)
- /u/coldwarrl also contributed the `Delay` modifier which delays displaying a `Tile`. This can be used to great effect for a [typewriter effect](https://cdn.discordapp.com/attachments/363754040103796737/497767900892102666/typing.gif)
- The `TextBox` was retrofitted to support this typing effect and also to support inline `Component`s like in [this example](https://cdn.discordapp.com/attachments/363771631727804416/497789071943401482/textbox.gif)
- Also [here](https://cdn.discordapp.com/attachments/363771631727804416/497907392676233217/logscrolling.gif) is a scrolling `LogArea` example.
- The `Modifier` interface was separated to two parts: `TextureTransformer` modifiers (which is the old way of modifying things) and `TileTransformer` modifiers
  which can tamper with `Tile`s *before* rendering textures (this is what `Delay` uses for example)
- The `getTile`/`setTile` (and their relative counterpats) methods were also "sanetized" so it is now pretty straightforward to use them.
- `TileGraphics` now provides a proper `Snapshot` object when `snapshot()` is called instead of just returning a `Map<Position, Size>`. This makes it more
  flexible and useful later when we properly implement multi-sized tiles.
- We made great progress this week and now only 2 tasks remain for the release (excluding testing / documenting)!

A next major release is coming up for this Autumn, so **stay tuned.**
