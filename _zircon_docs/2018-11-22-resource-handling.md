---
excerpt: Zircon has a lot of built-in things in place but sometimes you want to use external resources like tilesets, rex paint files and such. This page explains how to work with these resources.
title: Resource Handling
tags: [zircon, documentation, zircon-documentation, resources]
author: hexworks
short_title: Resource Handling
---

A *resource* in Zircon is an asset which comes from an external source (an `.xp` file for example).
Zircon handles several types of *resources*, you can check the `resource` package 
[here][resource] if you want to see details.

A resource usually has a helper class and comes with some static methods which help you load the given resource by hand.
Most of them have `enum` values which will give you some built-in resources (like CP437 tilesets). See more details below.

## Tilesets

Zircon handles multiple types of tilesets. Currently there is support for graphic, image, CP437 and true type tilesets.

### CP437 tilesets
CP437 tilesets come in the form of transparent `.png` files. You can peruse
[BuiltInCP437TilesetResource] to check the details. The rules of using CP437 tilesets:

- Your `.png` file has to have a transparent background
- You are free to use the built-in CP437 fonts as you see fit (they come from the 
[Dwarf Fortress Tileset Repository](http://dwarffortresswiki.org/Tileset_repository) and from 
[REXPaint](http://www.gridsagegames.com/rexpaint/)
- Your `.png` file must be a sprite composed of 16x16 character images depicting all CP437 tiles

You can use the built in CP437 tilesets by using the helper class:

```java
import org.hexworks.zircon.api.CP437TilesetResources;
import org.hexworks.zircon.api.resource.CP437TilesetResource;

public class UsingCP437Tilesets {

    public static void main(String[] args) {

        CP437TilesetResource tileset = CP437TilesetResources.rogueYun16x16();

    }
}

```

Loading your own `TilesetResource` is also simple, you just have to provide a `width`, a `height`, and an 
`InputStream` which points to your file:

```java
CP437TilesetResource.loadCP437Tileset(16, 16, stream);
```

### Graphic tilesets

[Graphic tilesets](https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/codetome/zircon/api/resource/GraphicTilesetResource.kt) are a bit more complex and they come with their own format (currently a `.zip` file containing your tile files and some metadata). Check how the `NETHACK` tileset example looks like [here](https://github.com/Hexworks/zircon/tree/master/src/main/resources/graphic_tilesets).

You can use the built-in `NETHACK` tileset like this:

```java
GraphicTilesetResource.NETHACK_16X16.toFont(new PickFirstMetaStrategy());
```

Or you can load your own `.zip` file by using `GraphicTilesetResource.loadGraphicTileset` like this:

```java
GraphicTilesetResource.loadGraphicTileset("path/to.zip", new PickFirstMetaStrategy())
```

What is a `MetadataPickingStrategy` you might ask?

### The Zircon Tileset format

The tileset format which is supported by Zircon is a `.zip` file which **must** contain a `tileinfo.yml` which is the metadata about each *tile* and at least one `.png` file which contains the tiles. `tileinfo.yml` looks like this:

```yml
# Nethack tileset ported to the Zircon tileset format
---
name: Nethack                             # The name of the tileset
size: 16                                  # The size of a tile (only square tiles are supported right now)   
files:                                    # A list of the files in this tileset
  - name: tiles.png                       # name of a file
    tilesPerRow: 40                       # The number of tiles in each row of the file
    tiles:                                # A list of **all** the tiles in this file
      - name: Giant ant                   # The name of the tile
        tags: [animal, ant, giant]        # Optional. All unique words in name will be saved as tags ('giant' and 'ant' in this case
        char: a                           # Optional. If not present the first character of the name will be used
        description: It is big.           # Optional. If not present it will be empty
      - name: Killer bee
```

These are the rules you have to follow when you work with the Zircon Tileset Format (`ZTF` in short):

- There **must be** a list entry for **each** `.png` file you put in your `.zip` file
- You **must** specify the number of tiles per row for **each** `.png` file
- There **must be** a `name` for **each** tile in a tileset file (`.png`)
- `tags`, `char` and `description` are optional (see the example above).

If the `char` is not present the first character of the name will be used.
All unique words in `name` will be saved as lower case tags (`giant` and `ant` in `Giant ant` for example).
Description will be empty by default.

### Metadata picking strategy

When Zircon tries to draw a `TextCharacter` on the screen it needs to fetch a texture for that character. In the case of Physical and CP437 fonts this is easy since there is a *1:1* mapping to each texture.

In graphic tilesets however each tile has a `name` and some `tag`s so it is no longer a straightforward mapping. This is why you can set `tags` for each `TextCharacter`. They will be used as filters to figure out which tile to choose from a graphic tileset. This is how it works:

- Zircon will try to fetch a texture for a `TextCharacter` based on its `char` value (eg: `x`)
- If there are more than 1 textures it will use the `tags` from `TextCharacter` to filter for a match. **Note that** a texture is **only considered** if it has **all** the `tags` from `TextCharacter`. So for example if you `TextCharacter` has `tags` (`foo` and `bar`) and your tile has `bar` it won't be considered, but if your tile has (`foo`, `bar` and `baz`) it will be a match.

If there is still *more than one option* to choose from Zircon will use the `MetadataPickingStrategy` you supplied to pick **one** tile. There are two built-in strategies, but you can write your own:

- `PickFirstMetaStrategy` <-- this will pick the **first** tile from the list. Good option if you don't care or if you always want to see the same tile
- `PickRandomMetaStrategy` <-- this will pick a random tile form the matches. Good if you want varied tiles (like walls for example)

**Note that** if there is no match found for a set of `tags` Zircon will **throw an exception**!

## REXPaint files

You can load REXPaint files (`.xp`) by using [RexPaintResource](https://github.com/Hexworks/zircon/blob/master/zircon.jvm/src/main/kotlin/org/codetome/zircon/api/resource/REXPaintResource.kt).

Any REXPaint file is supported (even layered ones). You can load your file by calling `REXPaintResource.loadREXFile` like this:

```java
InputStream is = getClass().getResourceAsStream("/rex_files/cp437_table.xp");
REXPaintResource rex = REXPaintResource.loadREXFile(is);
```
once you loaded your `.xp` file it can be converted to Zircon `Layer`s by using `toLayerList` like this:

```java
List<Layer> layers = rex.toLayerList();
```

Read more about `Layer`s [here](https://github.com/Hexworks/zircon/wiki/How-Layers-work).

There is also a complete example which you can try out [here](https://github.com/Hexworks/zircon/blob/master/zircon.swing/src/test/java/org/codetome/zircon/examples/RexLoaderExample.java).

## Color Themes

`ColorTheme`s are special kind of resource, and they are detailed [here](https://github.com/Hexworks/zircon/wiki/Working-with-ColorThemes).

## Animations

Animations come with their own file format (`.zap` = Zircon Animation Package). Animations are a beta feature and you can read more about them [here](https://github.com/Hexworks/zircon/wiki/Animation-support).



{% include zircon_links.md %}
