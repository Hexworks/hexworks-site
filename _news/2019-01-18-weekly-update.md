---
excerpt: "This week we came up with a prototype for the new input handling mechanics, and also upgraded the Game Area"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

This week was mainly about reimagining *how input handling works* in Zircon. The whole thing started when **@Ender_L**
finished the LibGDX implementation and it turned out that we don't do event bubbling in Zircon so he was not able
to tell whether an input (mouse click, key stroke) was handled by Zircon or not.

This is not a problem if you use only Zircon but if you have a LibGDX game and you just use the Zircon renderer
to render ascii components and things like that the whole thing is a different story. So now we have a concept for
input handling which incorporates event bubbling (works similar to what the browser does). There is a capture
phase, a target phase, and then the bubbling phase.

What's nice about this is that once we incorporate this to Zircon we'll be able to do things which were not possible
(or hard) previously. Things like keyboard shortcuts (not just global shortcuts, but component-based as well),
mnemonics and custom component workflows which need event capturing.

The system is also designed in a way that custom events can be added. This will be useful when we implement official
custom component support.

I'm really happy with the overall result. Shoutout to **@Essial**, **@coldwarrl**, **@Ender_L** and **@Novo** who reviewed the code!

Apart from the input handling I also had some time to [fiddle around](https://cdn.discordapp.com/attachments/363754040103796737/533777152647102465/unknown.png)
with the [top-down oblique renderer](https://cdn.discordapp.com/attachments/363771631727804416/534876703449939970/GIF.gif). There was
a discussion on the #roguelikedev Discord channel about handling direction in this kind of environment.

[This](https://cdn.discordapp.com/attachments/363771631727804416/535919420086943747/still_life.gif) is one of the possible solutions
where the direction indicator is on top of the characters. What you see here is a still life of a goblin (facing forward)
and a troll in orange trousers (also facing forward). In this examlpe you see a cross-section of a 3D block in space
so when I go up and down some levels I always see a fixed amount of layers. This is very useful when rendering dungeons.

There were also some minor upgrades for *Zircon*:

- `Block`s can now be *copied* and *rotated*!
- The sides of a `Block` are now mutable (top, bottom, left, right, front, back)
- The Gradle config was simplified
- The LibGDX renderer wasn't working on macOS, now this is fixed
