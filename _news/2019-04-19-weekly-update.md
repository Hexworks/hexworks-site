---
excerpt: "New tutorial article, progress bars and Graphical renderer for LibGDX"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon & Caves of Zircon Tutorial**

This week I finished [#8](https://hexworks.org/posts/tutorials/2019/04/02/how-to-make-a-roguelike-combat-and-damage.html)
in the tutorial which is about Combat and Damage. You can read it [here](https://hexworks.org/posts/tutorials/2019/04/02/how-to-make-a-roguelike-combat-and-damage.html).
I also nearly finished `#9` but I forgot to commit it on my home computer so I can't publish
it now, because we're in the countryside for *Easter*.

**Zircon**

Zircon got an upgrade! Now there are **progress bars** in the lib, they look like [this](https://cdn.discordapp.com/attachments/363771631727804416/565593518727233536/unknown.png)!
Also thanks to *EnderL2000* who added *Graphical Tileset Support* to our LibGDX renderer!
With this there are only a few things to add to make the LibGDX backend have feature
parity with the *Swing* one!

**Amethyst**

With *Amethyst* we devised a way to add *hierarchy* to entities, so in the near future we're
going to add it to the lib with the proper builders in place so you'll be able to
create nested entities like this:

```kotlin
entity<Foo> {
    behaviors(Bar)
    attributes(Baz)
    entities {
        entity<Qux> {
            // ...
        }
    }
}
```

