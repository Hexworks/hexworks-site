---
excerpt: "New tutorial articles are out for Caves of Zircon, and View support was added to Zircon"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

This week we were busy with adding some new upgrades to Zircon, and also with the Caves of Zircon tutorial!

For **Caves of Zircon**:

This week @Geldrin playtested Caves of Zircon and some issues came out which were quickly fixed. I also worked
on the Caves of Zircon tutorials, so now there are [three articles](https://hexworks.org/posts/tutorials/) out.
The rest is coming in the next weeks. I have fleshed out the whole thing, I just need to write it now.

Feedback is welcome about these so if some of you happen to take a look at them feel free to point out if something
is wrong, or hard to understand. I'm pretty excited about this, since I've never written a game tutorial before!

For **Zircon**:

There were some upgrades and fixes this week:

- `View`s were added to Zircon (The `V` from [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)) so
  now you can start using them in your UIs. Their usage is also detailed in [the tutorial](https://hexworks.org/posts/tutorials/2018/11/28/how-to-make-a-roguelike-views-screens-inputs.html).
- `TileGrid`s and `Screen`s are now `Closeable` to prevent memory leaks. This happened when there were `View`s which
  were no longer accessible to the user but the `EventBus` had references to event listeners within them. Now this
  is not a problem anymore.
- Some minor upgrades were added like
  - Lightening of colors in `TileColor`
  - Streamlining the addition of `Paragraphs` to ui elements 
  - CircleCI builds were fixed (they flapped previously beacuse they upped the Gradle version where `--parallel` was the
    default build method)
