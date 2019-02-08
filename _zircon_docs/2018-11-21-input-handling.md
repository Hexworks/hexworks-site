---
excerpt: All this fancy tile graphics and ASCII art is worth nothing if your player's can't interact with your game! In this article we'll learn how to handle Inputs in Zircon!
title: Input Handling
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: Input Handling
---

Since Zircon is a multiplatform project it needs to have an uniform representation of input events like
key presses and mouse clicks. For this purpose Zircon has [UIEvent]. There are multiple kind of [UIEvent]s:
[MouseEvent], [KeyboardEvent] and [ComponentEvent].

It doesn't matter if you use Swing or LibGDX, or any other GUI library, because Zircon adapts all framework
specific input handling int its own representation.

We can then listen to these [UIEvent]s by using any class which implements [UIEventSource] and/or [ComponentEventSource].
When you run a Zircon application it takes care of listening to the platform specific events (Swing, LibGDX, etc) so you
only need to subscribe to the Zircon [UIEvent]s in your code.

All [TileGrid]s and [Screen]s implement [UIEventSource] so you can listen to [MouseEvent]s and [KeyboardEvent]s on them.
[ComponentEvent]s are only emitted by [Component]s.

The following example demonstrates how to listen to all of the above event types:

> Note that when you subscribe to [ComponentEvent]s on a [Component] you will only receive [ComponentEvent] events which
were sent to that specific component.

## Usage

```java
import org.hexworks.zircon.api.*;
import org.hexworks.zircon.api.component.Button;
import org.hexworks.zircon.api.grid.TileGrid;
import org.hexworks.zircon.api.screen.Screen;
import org.hexworks.zircon.api.uievent.KeyboardEventType;
import org.hexworks.zircon.api.uievent.MouseEventType;
import org.hexworks.zircon.api.uievent.ComponentEventType;

public class InputHandling {

    public static void main(String[] args) {

        TileGrid tileGrid = SwingApplications.startTileGrid();
        Screen screen = Screens.createScreenFor(tileGrid);

        Button button = Components.button()
                .withText("Test me")
                .withPosition(5, 5)
                .wrapWithShadow(true)
                .build();

        screen.addComponent(button);

        // it doesn't matter where you add the listener, you can do it before or after
        // adding the component to the screen
        button.onComponentEvent(ComponentEventType.ACTIVATED, (event) -> {
            System.out.println(event.toString());
            return UIEventResponses.processed();
        });

        // listens to mouse events
        tileGrid.onMouseEvent(MouseEventType.MOUSE_RELEASED, ((event, phase) -> {
            // we log the event we received
            System.out.println(event.toString());

            // we return a response indicating that we processed the event
            return UIEventResponses.processed();
        }));

        // listens to keyboard events
        tileGrid.onKeyboardEvent(KeyboardEventType.KEY_PRESSED, ((event, phase) -> {
            System.out.println(event.toString());
            return UIEventResponses.processed();
        }));

        // we make the contents of the screen visible.
        screen.display();
        screen.applyColorTheme(ColorThemes.entrappedInAPalette());
    }
}

```

Some example output:

```
KeyboardEvent(type=KEY_PRESSED, key=a, code=KEY_A, ctrlDown=false, altDown=false, metaDown=false, shiftDown=false)
KeyboardEvent(type=KEY_PRESSED, key=a, code=KEY_A, ctrlDown=false, altDown=false, metaDown=false, shiftDown=false)
KeyboardEvent(type=KEY_PRESSED, key=a, code=KEY_A, ctrlDown=false, altDown=false, metaDown=false, shiftDown=false)
ComponentEvent(type=ACTIVATED)
MouseEvent(type=MOUSE_RELEASED, button=1, position=GridPosition(x=12, y=6))
ComponentEvent(type=ACTIVATED)
MouseEvent(type=MOUSE_RELEASED, button=1, position=GridPosition(x=12, y=6))
```

Handling [UIEvent]s is as simple as the code snippet above. Each [UIEvent] comes with an [UIEventType] which describes
the kind of events it supports. For example this is how [KeyboardEventType] looks like:

```kotlin
/**
 * Represents the possible types of keyboard events.
 */
enum class KeyboardEventType : UIEventType {
    KEY_PRESSED,
    KEY_TYPED,
    KEY_RELEASED
}
``` 

You just need to supply the proper [UIEventType] in your listener to receive events by that type.
Feel free to peruse [MouseEvent] and [ComponentEvent] to see what kind of information they hold

## Event Propagation

As you have seen in the above example whenever you consume a [KeyboardEvent] or a [MouseEvent] you receive not only
an `event` but a `phase` as well. This is because Zircon implements event propagation in a very similar way as it
works in your browser:

![event propagation](/assets/img/event_propagation.png)

> Note that [ComponentEvent]s don't have phases but you can still use [PreventDefault] or [StopPropagation] with them!

Whenever you receive a [MouseEvent] or a [KeyboardEvent] you'll also receive the current [UIEventPhase]. Propagation works
in the following way:

- An [UIEvent] arrives (mouse or keyboard)
- The target [Component] is found (the [Component] which is focused right now or where you clicked with your mouse)
- The path is determined from the *root* of the component hierarchy to the *target*.
- Here starts the **capture** phase where the [Component]s are traversed from the *root* to the *target* and they all
  receive the event.
- If propagation was not stopped, then the *target* [Component] receives the event in the **target** phase
- If propagation was not stopped, then the [Component]s are traversed backwards from the *target* to the root in the
  **bubble** phase.
  
This is event propagation in a nutshell. Why is this useful? You can implement things like mnemonics with this kind of
event propagation for example by attaching an event listener to a `Panel` and listening to key presses. This way
whenever something is focused within that `Panel` you'll receive [KeyboardEvent]s.

## Controlling Event Flow

As you can see in the code example above you have to return an [UIEventResponse] from an event listener. This is important
for Zircon to be able to tell whether you handled the event or not and in what way. These are the possible options
which you can return:

```kotlin
object UIEventResponses {

    @JvmStatic
    fun pass() = Pass

    @JvmStatic
    fun processed() = Processed

    @JvmStatic
    fun preventDefault() = PreventDefault

    @JvmStatic
    fun stopPropagation() = StopPropagation
}
```

- [Pass] is the default [UIEventResponse] which indicates that no event processing happened.
- [Processed] indicates that at least one event handler processed the [UIEvent] but it did not stop propagation
  nor the default actions. 
  Return this object if you acted on an event but you don't want to tamper with other listeners.
- [PreventDefault] is an [UIEventResponse] which requests the prevention of any default actions for the given event.
  Return this object if you acted on an event and you  don't want any default actions to take place
  (hover effects on components for example).
- [StopPropagation] will not only prevent default actions but stops propagation altogether so the event won't reach
  any other listener.
  Return this object if you acted on an event and you don't want any other listeners to have the chance of
  handling it.  
  
{% include zircon_links.md %}
