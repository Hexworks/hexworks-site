---
excerpt: When you start using Components you'll quickly feel the need to have some uniform way for coloring your Components. Color Themes are the solution for this problem.
title: Working With Color Themes
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: Working With Color Themes
---

[ColorTheme]s are a simple form of adding consistent design to your [Component]s.

Each [ColorTheme] comes with a bunch of [TileColor]s:

- Primary foreground color: used as a foreground color to give emphasis
- Secondary foreground color: simple foreground color for non-emphasized elements
- Primary background color: used as background for emphasized parts
- Secondary background color: used as background everywhere else
- Accent color: Used as an accent on interactable components (like [Button]s)

## Caveats

- There should be enough contrast between foreground and background colors to be pleasant for the eyes.
- The same stands for primary and secondary variants but the contrast should be much smaller (only to make them stand out)
- The accent color should look fine on all other colors

## Usage

You can either pick a color from the built-in ones or use a `Builder` to create your own. 

> Note that the *Solarized* themes are extracted from [Ethan Schoonover's famous work](http://ethanschoonover.com/solarized)
 and they are the perfect example for a fine-looking color theme.
 
Using a built-in theme is very simple. The [ColorThemes] utility class contains all of them (like in the
example below). If you want to build your own you can use `ColorThemes.newBuilder()` to do so.

Here is an example of color themes in action.

```java
final TileGrid tileGrid = LibgdxApplications.startTileGrid(
        AppConfig.newBuilder()
                .withSize(12, 10)
                .withDefaultTileset(CP437TilesetResources.rogueYun16x16())
                .build());

final Screen screen = Screen.create(tileGrid);

screen.addComponent(Components.label()
        .withText("Hello")
        .withPosition(1, 1)
        .build());

screen.addComponent(Components.panel()
        .withDecorations(box(BoxType.SINGLE, "Panel"))
        .withPosition(1, 3)
        .withSize(10, 5)
        .build());

ColorTheme custom = ColorThemes.newBuilder()
        .withAccentColor(TileColor.fromString("#ff0000"))
        .withPrimaryForegroundColor(TileColor.fromString("#ffaaff"))
        .withSecondaryForegroundColor(TileColor.fromString("#dd88dd"))
        .withPrimaryBackgroundColor(TileColor.fromString("#555555"))
        .withSecondaryBackgroundColor(TileColor.fromString("#222222"))
        .build();

ColorTheme builtIn = ColorThemes.adriftInDreams();

screen.setTheme(new Random().nextInt(2) > 0 ? custom : builtIn);

screen.display();
```

By running this code you'll be presented with this nice-looking window:

![Working With Color Themes](/assets/img/working-with-color-themes.png)

{% include zircon_links.md %}
