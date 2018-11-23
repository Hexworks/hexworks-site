---
excerpt: Our agenda was release preparation and API streamlining this week!
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
---

This week was mainly spent with preparing for the next major release:

- The usage of lambdas was streamlined. Now there is a proper interface (Java side) for each method which will accept a lambda
  and a proper extension function which accepts Kotlin lambdas. So what looks like this in Java: `tileGrid.onInput(System.out::println);`
  looks like this in Kotlin: `tileGrid.onInput { println(it) }`. This way both languages have idiomatic APIs.
  There are also new examples for each language demonstrating how these things work in their own class in the `zircon.examples` project.
- `Input` handling was also streamlined. This was a bit awkward before, but now there are separate `Input`, `KeyStroke` and `MouseListener`
  interfaces and a nice API to use them. Previously there were seemingly arbitrary functions (like `onMouseReleased`), but now the concept
  is fleshed out properly. You can still use the old ways, but behind the scenes it is now clean code.
- The component rendering refactor is now complete, and there are examples with all kinds of decorations for all components.
  Now **all** components can be decorated with all decorations including shadow, box, and more.
  Here are some screenshots and a short description about the purpose of the component:
  - [TextBox](https://cdn.discordapp.com/attachments/363771631727804416/495334868238991360/unknown.png): a `TextBox` is for representing textual
    content without the ability to edit. It supports semantic content elemens like `Header`s, `ListItem`s and `Paragraph`s.
  - [TextArea](https://cdn.discordapp.com/attachments/363771631727804416/495335398382501908/unknown.png): this one is for editing text. It supports
    displaying the cursor and can be single or multiline
  - [RadioButtonGroup](https://cdn.discordapp.com/attachments/363771631727804416/495336535508516885/radio_button_group.gif) is a container of `RatioButton`s
    It supports a selection listener and you can add key-value pairs to it. We used key-value pairs because we still have nightmares about the old android
    `Spinner` component which just used a flat `List<String>` and it was horrible to pick items from it.
  - [Panel](https://cdn.discordapp.com/attachments/363771631727804416/495339182412136469/unknown.png) is a `Container`. This means that you can add
    other `Component`s to it, or even other `Panel`s.
  - [Label](https://cdn.discordapp.com/attachments/363771631727804416/495339396871225344/unknown.png) is an one-liner non-editable text component.
  - [Header](https://cdn.discordapp.com/attachments/363771631727804416/495340218463944704/unknown.png) is another one-liner non-editable text component
    but it uses the primary foreground color as opposed to the `Label` which uses the secondary. This means that by using the `Header` you can add emphasis
    to the text on the screen without tampering with styles.
  - [CheckBox](https://cdn.discordapp.com/attachments/363771631727804416/495343503715598366/checkbox.gif) behaves as you would expect it to. The difference
    from the `RadioButtonGroup` is that the selected state is not dependent on other `CheckBox`es so they don't need to be grouped either.
  - [Button](https://cdn.discordapp.com/attachments/363771631727804416/495344198292340737/button.gif)s are simple clickable components
- The roadmap for the release is now complete. You can check the **ToDo** column [here](https://github.com/Hexworks/zircon/projects/2), this is what
  we want to get done before the next major release. This also means that now is the time to speark up if you want to see something in it. :)  


A next major release is coming up for this Autumn, so **stay tuned.**
