---
excerpt: "A new Zircon version is released: 2020.2.0-RELEASE"
title: "New Zircon Release: 2020.2.0-RELEASE"
tags: [github, zircon, project-update, project-update-zircon, release, release-zircon]
author: hexworks
short_title: "New Zircon Release: 2020.2.0-RELEASE"
comments: true
---

> Zircon is an extensible, multiplatform and user-friendly tile engine.
>
> If you are not yet familiar with the project take a look at our
> [Project Page](https://hexworks.org/projects/zircon/).
>
> The kdocs can be found [here](https://hexworks.github.io/zircon/). Javadocs are also coming soon!
>
> You can grab this release from Maven Central. More on how to add *Zircon* as a dependency to your project can be found [here](https://hexworks.org/posts/news/2020/02/19/zircon/docs/2019-01-11-release-process-and-versioning-scheme).

## Highlights of this release

With the tutorial project [Caves of Zircon](https://hexworks.org/posts/tutorials/2018/12/04/how-to-make-a-roguelike.html) finished we had a lot of time to work on new features since the last major release and we've also improved the documentation and the examples.

The [Documentation Page](https://hexworks.org/zircon/docs/) was overhauled and retrofitted with the recent changes in *Zircon* so now you can all enjoy the new content. What was missing for quite some time is a thorough explanation of the *Component System* which is now detailed [here](https://hexworks.org/zircon/docs/2018-11-15-the-component-system).

We also worked on the [examples](https://github.com/Hexworks/zircon/tree/master/zircon.jvm.examples/src/main) which now encompass all features of *Zircon*. Component examples now also have a tileset/color theme selector. You can take a look [here](https://cdn.discordapp.com/attachments/363754040103796737/679310297424724006/components.gif).

The internals of *Zircon* were refactored to use [persistent data structures](https://en.wikipedia.org/wiki/Persistent_data_structure) so we now have stable snapshots for rendering. In practice this means that there are no more flickering or visual artifacts when you use *Zircon*.

All `Component`s in *Zircon* were retrofitted with *data binding* features. What this means is that properties like the disabled state of a component or
their colors can be bound to each other with a single line of code. [This](https://cdn.discordapp.com/attachments/603285896829206548/603300249485705246/zircon_component_databinding.gif) example showcases some `Label`s and `Button`s being bound to another `Label`s textual content.

`Group` was added which is not a component itself but it takes advantage of the new *data binding* features of *Zircon* to synchronize components with each other even if they are not in a parent/child relationship. `Group` has its own `ColorTheme` for example which when updated will update all components in it. The `RadioButtonGroup` was also retrofitted to use this abstraction so now you no longer have to put `RadioButton`s in the same container.

We also implemented a bunch of new components:

- `HBox` and `VBox` were requested by a lot of our uses so we implemented them. They automatically align their child components either horizontally or vertically. They can also be nested into each other to create complex UIs and layouts. [This](https://cdn.discordapp.com/attachments/603285896829206548/603303497362047006/zircon_component_table.png) is an example for a combination of the two, [this](https://cdn.discordapp.com/attachments/603285896829206548/603303713964032011/zircon_component_vbox.gif) is a `VBox` with some interactions.
- [Number inputs](https://cdn.discordapp.com/attachments/363754040103796737/603996935740850176/numberInputwithButtons.gif) can be used to input only numbers.
- [Scroll bar](https://cdn.discordapp.com/attachments/363754040103796737/610073006437302282/scrollbars.gif)s enable you to scroll content in a component.
- With [Sliders](https://cdn.discordapp.com/attachments/363754040103796737/603996924282011669/newSliders.gif) you can easily pick values from a range.
- [Toggle buttons](https://cdn.discordapp.com/attachments/603285896829206548/678717722355433492/toggle.gif) were retrofitted to look more intuitive and visually appealing.
- With [Progress bars](https://cdn.discordapp.com/attachments/363754040103796737/680048998337609747/progress_bar.gif) you can display the progress of a process.
- Component decorations were retrofitted to have an `INTERACTIVE`/`NON_INTERACTIVE` mode to better align with specific needs. Example [here](https://cdn.discordapp.com/attachments/363754040103796737/680025017869795367/rendering_modes.gif).
- We also implemented a post-rendering feature for `Component`s.

A new feature was added to `TileColor` for color interpolation. This can be handy if you want to create [color gradients](https://cdn.discordapp.com/attachments/363754040103796737/680074649207701509/interpolation.gif).

*Zircon* now also supports *event bubbling*. This topic is detailed in the [docs](https://hexworks.org/zircon/docs/2018-11-21-input-handling).

We also have an image -> ASCII converter in the works: [click](https://cdn.discordapp.com/attachments/603285896829206548/603304759859871768/zircon_ascii_by_mr_pancake.png).

Here is a full list of issues we finished for this release:

## New Features

- [#144](https://github.com/Hexworks/zircon/issues/144): Add slider widget. 
- [#123](https://github.com/Hexworks/zircon/issues/123): Implement the `Group` component. 
- [#123](https://github.com/Hexworks/zircon/issues/123): Implement the `NumberInput` component. 
- [#257](https://github.com/Hexworks/zircon/issues/257): Add the transform function from TileImage to TileGraphics and Layer. 
- [#181](https://github.com/Hexworks/zircon/issues/181): Feature: H/V Boxes. 
- [#126](https://github.com/Hexworks/zircon/issues/126): Introduce event bubbling for `Input` events. 
- [#190](https://github.com/Hexworks/zircon/issues/190): Simple Progress Bar. 
- [#193](https://github.com/Hexworks/zircon/issues/193): Implement FadeIn/Out modifiers. 
- [#291](https://github.com/Hexworks/zircon/issues/291): Implement the NumberInput component. 
- [#292](https://github.com/Hexworks/zircon/issues/292): New Component: ScrollBar. 
- [#95](https://github.com/Hexworks/zircon/issues/95): Add option for post-component rendering decorations. 
- [#288](https://github.com/Hexworks/zircon/issues/288): Add interactive and non-interactive rendering mode for decorations. 
- [#289](https://github.com/Hexworks/zircon/issues/289): Add a way to interpolate between colors.

## Enhancements

- [#286](https://github.com/Hexworks/zircon/issues/286): Refurbish documentation on the website.
- [#253](https://github.com/Hexworks/zircon/issues/253): Improve the visual appearance of component states .
- [#282](https://github.com/Hexworks/zircon/issues/282): Make the ToggleButton have better UX .
- [#245](https://github.com/Hexworks/zircon/issues/245): A component shall have a property hasFocus. 
- [#283](https://github.com/Hexworks/zircon/issues/283): Add copy functions to TileColor. 
- [#161](https://github.com/Hexworks/zircon/issues/161): Move requestFocus to its own interface: FocusableComponent. 
- [#219](https://github.com/Hexworks/zircon/issues/219): Deploy to Maven Central.
- [#261](https://github.com/Hexworks/zircon/issues/261): Refactor RadioButtonGroup to be a logical Group instead of a Component. 
- [#264](https://github.com/Hexworks/zircon/issues/264): Enable all Components to be Disablable, Hideable, Themeable and have TilesetOverride. 
- [#94](https://github.com/Hexworks/zircon/issues/94): Check API for concurrency issues. 
- [#263](https://github.com/Hexworks/zircon/issues/263): Make ColorTheme a property of Component. 
- [#251](https://github.com/Hexworks/zircon/issues/251): Make components inherit properties.
- [#110](https://github.com/Hexworks/zircon/issues/110): GameComponent should be a container. 
- [#258](https://github.com/Hexworks/zircon/issues/258): Only modify a TileGraphics if the operation would lead to a change. 
- [#256](https://github.com/Hexworks/zircon/issues/256): Add the option to hide/show Layers. 
- [#255](https://github.com/Hexworks/zircon/issues/255): Properly implement the disabled state for CheckBox and Button. 
- [#254](https://github.com/Hexworks/zircon/issues/254): Modify Component bounds check to be only relaxed in debug mode. 
- [#242](https://github.com/Hexworks/zircon/issues/242): Migrate gradle build scripts to use Kotlin DSL + buildSrc. 
- [#202](https://github.com/Hexworks/zircon/issues/202): Make Blocks mutable. 
- [#200](https://github.com/Hexworks/zircon/issues/200): Use databinding for mutable component states (selected, checked, text, etc).
- [#196](https://github.com/Hexworks/zircon/issues/196): Allow Icons to use any Tile. 
- [#199](https://github.com/Hexworks/zircon/issues/199): Allow editing the items in a RadioButtonGroup. 
- [#203](https://github.com/Hexworks/zircon/issues/203):  Add support for Focus and Activation events to Components.
- [#210](https://github.com/Hexworks/zircon/issues/210): Component: Visibilty Property. 

## Bug fixes

- [#287](https://github.com/Hexworks/zircon/issues/287): HBox and VBox won't accept a Component which would fill up the space completely.
- [#278](https://github.com/Hexworks/zircon/issues/278): StaticEffectMarkovChainExample is not working. 
- [#276](https://github.com/Hexworks/zircon/issues/276): GlitchEffectMarkovChainExample is not working. 
- [#284](https://github.com/Hexworks/zircon/issues/284): Adding a disabled Component to a container will re-enable it. 
- [#222](https://github.com/Hexworks/zircon/issues/222): LibGDXApplications is not swappable for SwingApplications. 
- [#50](https://github.com/Hexworks/zircon/issues/50): Input does not catch keystroke <CTRL>+Z on Windows 10 and Ubuntu 17.10. 
- [#231](https://github.com/Hexworks/zircon/issues/231): Fullscreen doesn't work if all the tiles can't fit on screen. 
- [#218](https://github.com/Hexworks/zircon/issues/218): LogArea does not scroll to last line when line is wordwrapped. 
- [#247](https://github.com/Hexworks/zircon/issues/247): Event RequestCursorAt does not work as expected in a text area. 
- [#139](https://github.com/Hexworks/zircon/issues/139): If text area is enabled and receives the focus programatically, it does not receive the first key event. 
- [#246](https://github.com/Hexworks/zircon/issues/246): Keycode TAB is always propagated to the default handler. 
- [#160](https://github.com/Hexworks/zircon/issues/160): Multiple screens handling Input regardless of which one is active. 
- [#209](https://github.com/Hexworks/zircon/issues/209): Panel: changes of TitleProperty get not reflected. 
- [#119](https://github.com/Hexworks/zircon/issues/119): Screen.setCursorVisibility(true) does not work. 
- [#274](https://github.com/Hexworks/zircon/issues/274): Activating a component doesn't always use the correct style. 
- [#221](https://github.com/Hexworks/zircon/issues/221): Log Area text appears in other panels for a single frame. 
- [#252](https://github.com/Hexworks/zircon/issues/252): Transparent background is not working when using TileTransformModifier. 
- [#281](https://github.com/Hexworks/zircon/issues/281): Pressing Tab repeatedly doesn't keep traversing the focus. 
- [#280](https://github.com/Hexworks/zircon/issues/280): Removing a focused component will sometimes isolate the previous and next components. 
- [#279](https://github.com/Hexworks/zircon/issues/279): Adding or removing a focusable component doesn't retain focus of currently focused component. 
- [#275](https://github.com/Hexworks/zircon/issues/275): Visual style applied by focus is taken away when component is hovered. 
- [#272](https://github.com/Hexworks/zircon/issues/272): GameComponent doesn't use decorations. 
- [#233](https://github.com/Hexworks/zircon/issues/233): Pressing shift while editing a TextArea moves the cursor. 
- [#227](https://github.com/Hexworks/zircon/issues/227): Buttons within modals can be misaligned. 
- [#92](https://github.com/Hexworks/zircon/issues/92): Implement the snapshot function in DrawSurface properly. 
- [#232](https://github.com/Hexworks/zircon/issues/232): Border decoration renderer is not working for Components. 
- [#175](https://github.com/Hexworks/zircon/issues/175): Black bar appears in LibgdxApplication. 
- [#194](https://github.com/Hexworks/zircon/issues/194): Libgdx renderer prints a warning on macOS and doesn't display tiles. 
- [#201 ](https://github.com/Hexworks/zircon/issues/201): Fix CircleCI parallelism problem. 
- [#198](https://github.com/Hexworks/zircon/issues/198): TextArea accepts huge sizes which leads to memory leaks. 
- [#197](https://github.com/Hexworks/zircon/issues/197): Fix TextBox division by zero problem when creating a TextBox. 
- [#195](https://github.com/Hexworks/zircon/issues/195): Fix LogArea positioning when it has wrappers. 
- [#180](https://github.com/Hexworks/zircon/issues/180): Window titles don't work. 
- [#151](https://github.com/Hexworks/zircon/issues/151): Drawable.drawOnto(DrawSurface) implementation in DefaultLayer does not seem to work. 
- [#187](https://github.com/Hexworks/zircon/issues/187): ToggleButtonBuilder ignores the isSelected(Boolean) function. 
- [#186](https://github.com/Hexworks/zircon/issues/186): TextArea blows up on forward delete if there's no character after the cursor. 

## Road Map
  
We've covered a lot of ground in this release, but there are still things to do:

- [Scrollable Components](https://github.com/Hexworks/zircon/issues/25)
- [Floating Components](https://github.com/Hexworks/zircon/issues/23)
- [Drag'n Drop Support](https://github.com/Hexworks/zircon/issues/22)
- [Custom Layout Support](https://github.com/Hexworks/zircon/issues/28)
- [Component Themes](https://github.com/Hexworks/zircon/issues/29)
- [Custom Component Support](https://github.com/Hexworks/zircon/issues/26)
- [Menus](https://github.com/Hexworks/zircon/issues/135)
- [Tree Component](https://github.com/Hexworks/zircon/issues/184)
- [Table Component](https://github.com/Hexworks/zircon/issues/185)
- [Accordion Component](https://github.com/Hexworks/zircon/issues/27)
- [Combo Box Component](https://github.com/Hexworks/zircon/issues/262)
- [IntelliJ Plugin](https://github.com/Hexworks/zircon/issues/191)
- [Javadoc-style Documentation](https://github.com/Hexworks/zircon/issues/146)
- [Console for Zircon](https://github.com/Hexworks/zircon/issues/183)
- [Grid / Screen Filters](https://github.com/Hexworks/zircon/issues/271)

## Credits

Thank you for all of you out there who helped with this release! **Special thanks** to [Baret](https://github.com/Baret), [Bergin](https://www.reddit.com/user/Jordanbergin), [Coldwarrl](https://github.com/coldwarrl), [Luke](https://github.com/LukeLetourneau) and [Seveen](https://www.reddit.com/user/Seeveen) for their contributions!

## Contribute

> If you think that you can contribute or just have an idea feel free to join the discussion on our [Discord](https://discordapp.com/invite/vSNgvBh) server.
