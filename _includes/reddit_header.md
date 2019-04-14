**Zircon: an extensible, multiplatform and user-friendly tile engine.**

[GitHub](https://github.com/Hexworks/zircon) | 
[Maven](https://jitpack.io/#org.hexworks/zircon/2019.0.9-PREVIEW) | 
[Discord](https://discordapp.com/invite/PE3qFmF) | 
[Patreon](https://www.patreon.com/join/hexworks) | 
[Docs](https://hexworks.org/zircon/docs/) | 
[Board](https://github.com/Hexworks/zircon/projects/2)

**Zircon & Caves of Zircon Tutorial**

This week was about [Stationary Monsters](https://hexworks.org/posts/tutorials/2019/03/21/how-to-make-a-roguelike-stationary-monsters.html)
in *Caves of Zircon*. The article is ready and you can read it on [the site](https://hexworks.org/posts/tutorials/2019/03/21/how-to-make-a-roguelike-stationary-monsters.html).

There was also a fix in [article #4](https://hexworks.org/posts/tutorials/2019/02/13/how-to-make-a-roguelike-the-player.html) where
we had a block of code which was not explained. Thanks to @RantingBob for pointing that out!

**Amethyst**

We also had some great talks about how [Amethyst](https://github.com/Hexworks/amethyst) works and should work so we've begun to
formulate some new additions to the library which are about interactions between entities.

There is also a new feature, now you can supply a parameter to the constructor of all `System`s for *mandatory `Attribute`s*.
This will help greatly to ensure the correctness of the code for games using *Amethyst*.

Another addition which is in the works is a `Channel` through which the `Engine` can stream all *errors* so the user of the library
can respond to erroneous situations which were not caught at compile time.
