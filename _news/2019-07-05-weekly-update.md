---
excerpt: "Multiple new tutorial articles are out!"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Caves of Zircon Tutorial**

This week I was focusing on the tutorial series since it is almost complete! I've produced
multiple articles including one which is about how to implement
[Help and Examine Dialogs](https://hexworks.org/posts/tutorials/2019/06/30/how-to-make-a-roguelike-help-and-examine-dialogs.html)
and another one which details how to implement [Win and Lose Conditions](https://hexworks.org/posts/tutorials/2019/07/01/how-to-make-a-roguelike-win-and-lose-conditions.html).
Now all is left is to double check all 19 articles, the program itself and write a **Wrap Up**
article! I also plan to continue the tutorial later with some new topics, but after I publish
the whole thing I'm going to take a little break!

**Zircon**

We've been working on some minor improvements which came up while working on the tutorial, namely:

- **Adding** a missing component boundary check which caused weird problems like users not being able to
  click buttons. This was not a bug per se, but previously the boundary check was relaxed when
  Zircon ran with *enable beta features*. Now we properly check bounds and it is only relaxed
  in *debug mode*
- **Fixing** an issue which lead to weird component alignment. Shoutout to **Seveen** who found
  the issue and also opened a PR with the fix!
- **Adding** True Type Font support for the LibGDX renderer. Thanks to **Ender_L** for the
  contribution!
