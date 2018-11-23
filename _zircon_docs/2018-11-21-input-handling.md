---
excerpt: All this fancy tile graphics and ASCII art is worth nothing if your player's can't interact with your game! In this article we'll learn how to handle Inputs in Zircon!
title: Input Handling
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: Input Handling
---

Since Zircon is a multiplatform project it needs to have an uniform representation of input events like
key presses and mouse clicks. For this purpose Zircon has [Input]. [Input] has two implementations:
[KeyStroke] and [MouseAction].

It doesn't matter if you use Swing or LibGDX, or any other GUI library, because Zircon adapts all framework
specific input handling int its own representation.

We can then listen to these [Input] events by using any class which implements [InputEmitter].
Any class which implements this interface  will re-emit  all [Input]s it has received. When you run a Zircon application
Zircon takes care of listening to the platform specific events so you only need to subscribe to the Zircon [Input]s in your code.

All [TileGrid]s and [Screen]s implement this interface so you can listen to [Input] events on them.

The following example demonstrates how you use [Input]s.

> Note that when you subscribe to [Input]s on a [Component] you will only receive [Input] events which were sent to
that specific component.

## Usage

```java
import org.hexworks.zircon.api.SwingApplications;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.input.KeyStroke;
import org.hexworks.zircon.api.input.MouseAction;
import org.hexworks.zircon.api.listener.MouseAdapter;
import org.jetbrains.annotations.NotNull;

public class InputHandling {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid();

        // listens to all inputs
        tileGrid.onInput((input -> {
            if (input.isKeyStroke()) {
                KeyStroke keyStroke = input.asKeyStroke().get();
                System.out.println(keyStroke);
            } else if (input.isMouseAction()) {
                MouseAction mouseAction = input.asMouseAction().get();
                System.out.println(mouseAction);
            }
        }));

        // listens to specific mouse events (override the methods which you need)
        tileGrid.onMouseAction(new MouseAdapter() {

            @Override
            public void mousePressed(@NotNull MouseAction action) {
                // ...
            }

            @Override
            public void mouseDragged(@NotNull MouseAction action) {
                // ...
            }
        });

        // you can use lambdas as well
        tileGrid.onMouseDragged(action -> {
            // ...
        });

    }
}

```

Handling [Input]s is as simple as the code snippet above. Peruse the [Input] class if you want to know
what kind of information they hold.

{% include zircon_links.md %}
