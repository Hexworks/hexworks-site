---
excerpt: "Had progress with Cobalt and integrated it with Zircon. Now the EventBus is no longer part of Zircon"
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
comments: true
---

This week:

- The [Cobalt](https://github.com/cobalt/cobalt) project was created which will contain all the
  common utilities which we're using.
- The `EventBus` from Zircon was extracted to `Cobalt`, and the common data types also (like `Maybe`)
- Data binding was introduced to `Cobalt`, and it will come to Zircon soon. [Take a look](https://cdn.discordapp.com/attachments/505798786623340555/508031403351343105/databinding.gif) at the
  browser example. `Cobalt` is a multiplatform library (just like Zircon) so it has JVM and Javascript
  targets by default. We'll add native targets later
- The option to set custom `ComponentRenderer`s was added to Zircon
- And the documentation was retrofitted for the latest Zircon release
