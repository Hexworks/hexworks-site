---
excerpt: "This week input handling was refactored to the new model, and now it supports event bubbling and capturing!"
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Zircon**

This week we worked on enhancing the new input handling mechanism with **focus** and **activation** events.
A new `UIEvent` class was introduced: `ComponentEvent`. It comes with 3 new types: now you can listen to
`FOCUS_GIVEN`, `FOCUS_TAKEN` and `ACTIVATED` `ComponentEvent`s. Focus events are self-explanatory but `ACTIVATED`
is a new concept. Activation happens, when an interactable (`Button`, `ToggleButton`, etc) component is either clicked
or [Spacebar] is pressed when they are focused. We used `MOUSE_RELEASED` so far, but it was kind of confusing so we
moved it to the new `ACTIVATED` event!

`Scrollable` and `Scrollable3D` now [supports scrolling to a given visible offset](https://github.com/Hexworks/zircon/issues/204).
Thanks for /r/coldwarrl for the contribution!

There is also a new feature which was mentioned by some folks on Discord: **debugging help**. Zircon now comes with a
logger which is set up for the project (both for Swing and LibGDX). What's more useful is that we've started adding
proper and insightful `debug` statements throughout the codebase which you can enable by editing the logger config
in `logback.xml`. The most important part is that you can enable only those parts which you are interested in so you
won't get an undecypherable mess. The config looks like this:

> This file goes to `/src/main/resources`

```xml
<configuration>

    <!-- We only print to the console (stdout) by default using the following format -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- Logging is set to info by default for our console logger -->
    <root level="info">
        <appender-ref ref="STDOUT"/>
    </root>

    <!-- You can either set logging level for whole packages -->
    <logger name="org.hexworks.zircon" level="warn"/>
    <logger name="org.hexworks.cobalt" level="warn"/>

    <!-- Or individual classes -->
    <logger name="org.hexworks.zircon.api.component.Button" level="debug"/>
    
</configuration>
```  

We'll gradually add proper `debug` statements throughout all of the `api` classes so if you have trouble with any of
them just enable debug logging and you'll see what happens, no additional setup needed. It looks like this:

```
19:58:14.149 [main] DEBUG o.h.zircon.api.component.Button - Button (id=efe8, enabled=true) was rendered.
19:58:14.153 [main] DEBUG o.h.zircon.api.component.Button - Applying color theme: DefaultColorTheme(primaryForegroundColor=DefaultTileColor(red=248, green=237, blue=209, alpha=255), secondaryForegroundColor=DefaultTileColor(red=197, green=207, blue=198, alpha=255), primaryBackgroundColor=DefaultTileColor(red=157, green=157, blue=147, alpha=255), secondaryBackgroundColor=DefaultTileColor(red=71, green=72, blue=67, alpha=255), accentColor=DefaultTileColor(red=184, green=106, blue=106, alpha=255)) to Button (id=e60a, enabled=true).
19:58:14.154 [main] DEBUG o.h.zircon.api.component.Button - Button (id=e60a, enabled=true) was rendered.
19:58:15.540 [LWJGL Application] DEBUG o.h.zircon.api.component.Button - Button (id=e60a, enabled=true) was mouse entered.
19:58:15.540 [LWJGL Application] DEBUG o.h.zircon.api.component.Button - Button (id=e60a, enabled=true) was rendered.
```

These changes will be added to the skeleton projects as well for ease of use. The input handling documentation page
was also updated with the recent changes. Take a look at it [here](https://hexworks.org/zircon/docs/2018-11-21-input-handling).

There were also some upgrades:

- [#180](https://github.com/Hexworks/zircon/issues/180) Window titles were not displayed for quite some time, but they 
  are now fixed: [screenshot](https://cdn.discordapp.com/attachments/363754040103796737/543463086208581644/unknown.png)
- [#196](https://github.com/Hexworks/zircon/issues/196) `Icon`s now can use any `Tile` object not just `GraphicTile`s.
- [#193](https://github.com/Hexworks/zircon/issues/193) There is now a `FadeIn`, a `FadeOut` and a `FadeInOut` modifier
  contributed by /r/coldwarrl!
  
**News flash** for Kotlin devs!

/r/coldwarrl bumped into [Korge](https://github.com/korlibs) which is a multiplatform game engine written in Kotlin
which enables its users to run their games on all platforms supported by Kotlin MPPs. This means that projects using
Korge work on the JVM, in the browser, on Android and on native platforms (even iOS in theory).

We'll take a look at Korge soon to see how it performs with Zircon. Since Zircon is also an MPP in theory it is easy to
integrate it with Korge which would mean that you can run Zircon programs (for all intents and purposes) everywhere.
Note that this is only for *Kotlin devs*.  
  
**Caves of Zircon Tutorial**

The input handling code is not in place in the tutorial project and we're working on the next article with the new code.
It comes next week, so stay tuned!
