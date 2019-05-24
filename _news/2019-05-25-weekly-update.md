---
excerpt: "New tutorial article & API cleanup "
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---

**Caves of Zircon Tutorial**

This is Friday the 13th this week! We've just produced the 13th article in the tutorial.
This week was about [Food and Hunger](https://hexworks.org/posts/tutorials/2019/05/23/how-to-make-a-roguelike-food-and-hunger.html)
in *Caves of Zircon*. The article is ready and you can read it on [the site](https://hexworks.org/posts/tutorials/2019/05/23/how-to-make-a-roguelike-food-and-hunger.html).

**Zircon**

This week we added some upgrades to the event handling mechanics and also clarified the API a bit.
From now on you can differentiate between handling or processing an event:

```java
// when you handle events you need to return a response
button.handleComponentEvents(ACTIVATED, (event) -> {
    System.out.println("Skipped component event");
    return UIEventResponses.pass(); // pass means that you didn't handle the event
});

// when you process events you don't have to return a response, Zircon will treat
// processors as if they were returning the `processed` response.
button.processComponentEvents(ACTIVATED, (event) -> System.out.println("Button pressed!"));
```

This will make input event handling easier than before and also more readable.

Apart from this we worked on the simplification of `Component` builders. This has been a nuisance
for quite some time and now we're going to clean up the code so that there won't be any surprises
in the future.
