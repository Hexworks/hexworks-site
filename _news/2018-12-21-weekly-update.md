---
excerpt: "This week was about release preparation, adding data binding and bug hunting."
title: "Weekly update: Zircon & Caves of Zircon"
tags: [zircon, caves-of-zircon, project-update, project-update-zircon, project-update-coz]
author: hexworks
short_title: "Weekly update: Zircon & Caves of Zircon"
comments: true
---
This week was spent mostly with finalizing the next release: there are only 2 tasks left!

For **Caves of Zircon**:

- Now you can [level up](https://cdn.discordapp.com/attachments/509142267735310338/525811202551447553/coz_xp.gif) in the game!
- The keyboard controls were also improved so now it is not only possible but convenient to use as well
  
For **Zircon**:

- We fixed an elusive bug which caused focus to be lost when a `Component` was deleted from the screen which currently had focus.
  This happens for example when you loot some items from creatures in Caves of Zircon. I spent some time with this but now it is
  working.
- Zircon now supports data binding! [This](https://cdn.discordapp.com/attachments/363754040103796737/525042125151141939/data_binding_with_labels.gif)
  is an example of binding the value of a label to another one, a `Header` and several buttons. What's not visible here is that with data binding you
  can also create complex bindings like binding to numbers:
```
val someNumber = createPropertyFrom(1)
Components.button().build().textProperty.bind(someNumber) {
    it.toString()
}
```

or creating combined bindings like in this example:

```
val firstName = Components.label().build()
val lastName = Components.label().build()

val combined = firstName.textProperty.concat(" ").concat(lastName.textProperty)

val fullName = Components.label().build()
fullName.textProperty.bind(combined)
```
- There were also some minor bugfixes reported by @Coldwarrl and others. Thanks!

