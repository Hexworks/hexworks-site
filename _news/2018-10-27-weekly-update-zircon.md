---
excerpt: "Roadmap for the next release with announcing some new things."
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
comments: true
---

This week we have planned the roadmap for the next release. It can be found [here](https://github.com/Hexworks/zircon/milestone/10).

TL;DR: The next iteration in Zircon which will introduce: **component types** (floating and modal)
 and some custom functionality for them (like drag'n drop), `Group` and `ScrollableComponent`, `EventBus`
 hardening, **event bubbling**, **coroutine support** with Kotlin 1.3 and some minor improvements and fixes.
 
We'll focus on doing smaller, but more frequent releases in the future. We also started using GitHub's "Milestone"
feature for these releases. From now on the version will look like this:

```
{{year}}.{{major}}.{{minor}}-{{qualifier}}
```

Example:

```
2018.5.0-RELEASE
2018.5.1-PREVIEW
```

When we do releases for Maven Central, the `major` counter will be incremented, for beta or preview versions
we'll use the `minor`.

Next to this roadmap, we'll continue working on [Caves of Zircon](https://github.com/Hexworks/caves-of-zircon)
which is a roguelike tutorial project based on Trystan's work. When it is done we'll publish a series of articles
about writing a roguelike game with Zircon. 
