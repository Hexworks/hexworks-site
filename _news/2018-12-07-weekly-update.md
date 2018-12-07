---
excerpt: "Caves of Zircon now has inventories, items, wandering creatures and more! We also have a contributor who is working on a libGDX implementation!"
title: "Weekly update"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update"
---

This week:

- Great thanks to @Ender_L who started working on a libGDX renderer for Zircon! [This](https://cdn.discordapp.com/attachments/363754040103796737/519693173526626304/unknown.png)
  is how it looks right now. All basic features are now working, and we just need to optimize it a bit.
- We implemented a lot of new things in [Caves of Zircon](https://github.com/Hexworks/caves-of-zircon):
- We tried a bunch of different color themes: [0](https://cdn.discordapp.com/attachments/509142267735310338/518210010560462867/unknown.png),
    [1](https://cdn.discordapp.com/attachments/509142267735310338/518210218858119181/unknown.png),
    [2](https://cdn.discordapp.com/attachments/509142267735310338/518210304916586506/unknown.png),
    [3](https://cdn.discordapp.com/attachments/509142267735310338/518210579417137152/unknown.png)
    and tilesets [0](https://cdn.discordapp.com/attachments/509142267735310338/518537384665546771/anikki16x16.png),
    [1](https://cdn.discordapp.com/attachments/509142267735310338/518537439191498762/mkv12x12.png),
    [2](https://cdn.discordapp.com/attachments/509142267735310338/518537469717905418/markX16x16.png),
    [3](https://cdn.discordapp.com/attachments/509142267735310338/518537500608823329/ms_gothic16x16.png),
    [4](https://cdn.discordapp.com/attachments/509142267735310338/518537529708904458/nagidal24x24.png),
    [5](https://cdn.discordapp.com/attachments/509142267735310338/518537569206534159/cheepicus14x14.png),
    [6](https://cdn.discordapp.com/attachments/509142267735310338/518537607055933470/cooz16x16.png),
    [7](https://cdn.discordapp.com/attachments/509142267735310338/518537623137157132/sb16x16.png),
    [8](https://cdn.discordapp.com/attachments/509142267735310338/518537642468442348/curses24x24.png),
    [9](https://cdn.discordapp.com/attachments/509142267735310338/518537706213605388/yayo16x16.png),
    [10](https://cdn.discordapp.com/attachments/509142267735310338/518537748198719490/zaratustra16x16.png),
    [11](https://cdn.discordapp.com/attachments/509142267735310338/518537786773602314/kenran16x16.png)
    then we finally settled with [this setup](https://cdn.discordapp.com/attachments/509142267735310338/518917277295312906/GIF.gif).
- Look and feel was also upgraded with [mossy walls, textured floors and scattered items](https://cdn.discordapp.com/attachments/509142267735310338/520358606730952729/GIF.gif)
- Now you can also [pick up items and drop them](https://cdn.discordapp.com/attachments/509142267735310338/520709704062730251/GIF.gif)
- And dropped items have a [glow](https://cdn.discordapp.com/attachments/509142267735310338/520714560508919810/GIF.gif) around them.
  
The ECS (ish) system which we worked on to support this helps greatly with the development so we'll keep using (and improving)
it. We're more than halfway (55%) through with the project!
- `Border`s also got upgraded: now you can set a width and a border color for `Border`s
- `GameArea` now supports overlays! This is very useful for Fog of War and similar effects. (see the gifs above)
