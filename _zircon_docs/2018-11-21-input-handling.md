---
excerpt: All this fancy tile graphics and ASCII art is worth nothing if your player's can't interact with your game! In this article we'll learn how to handle Inputs in Zircon!
title: Input Handling
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: Input Handling
source_code_url: https://github.com/Hexworks/zircon/blob/master/zircon.jvm.examples/src/main/java/org/hexworks/zircon/examples/docs/InputHandling.java
---

Since Zircon is a multiplatform project it needs to have an uniform representation of input events like
key presses and mouse clicks. For this purpose Zircon has [UIEvent]. There are multiple kind of [UIEvent]s:
[MouseEvent], [KeyboardEvent] and [ComponentEvent].

It doesn't matter if you use Swing or LibGDX, or any other GUI library, because Zircon adapts all framework
specific input handling into its own representation.

We can then listen to these [UIEvent]s by using any class which implements [UIEventSource] and/or [ComponentEventSource].
When you run a Zircon application it takes care of listening to the platform specific events (Swing, LibGDX, etc) so you
only need to subscribe to the Zircon [UIEvent]s in your code.

All [TileGrid]s and [Screen]s implement [UIEventSource] so you can listen to [MouseEvent]s and [KeyboardEvent]s on them.
[ComponentEvent]s are only emitted by [Component]s.

## Usage

The following example demonstrates how to listen to all of the above event types:

> Note that when you subscribe to [ComponentEvent]s on a [Component] you will only receive [ComponentEvent] events which
were sent to that specific component.

```java
TileGrid tileGrid = SwingApplications.startTileGrid();
Screen screen = Screen.create(tileGrid);

Panel panel = Components.panel()
        .withDecorations(box(BoxType.SINGLE, "New Game"), shadow())
        .withSize(20, 10)
        .withAlignmentWithin(tileGrid, CENTER)
        .build();

Button button = Components.button()
        .withText("Play")
        .withPosition(5, 5)
        .withAlignmentWithin(panel, CENTER)
        .build();

screen.addComponent(panel);
panel.addComponent(button);


// components support event bubbling so we can filter for BUBBLE events here
// note that if you try this you will only see the "A pressed..." message when
// the panel is focused (or something else in the panel)

panel.processKeyboardEvents(KeyboardEventType.KEY_PRESSED, fromBiConsumer((event, phase) -> {
    if (phase == UIEventPhase.BUBBLE && event.getCode() == KeyCode.KEY_A) {
        System.out.println("A pressed in bubble phase.");
    }
}));


// it doesn't matter where you add the listener, you can do it before or after
// adding the component to the screen

// when you handle events you need to return a response
button.handleComponentEvents(ACTIVATED, (event) -> {
    System.out.println("Skipped component event");
    return UIEventResponse.pass(); // pass means that you didn't handle the event
});

// when you process events you don't have to return a response, Zircon will treat
// processors as if they were returning the `processed` response.
button.processComponentEvents(ACTIVATED, fromConsumer((event) -> {
    System.out.println("Button pressed!");
}));


// listens to mouse events
tileGrid.handleMouseEvents(MouseEventType.MOUSE_RELEASED, ((event, phase) -> {
    // we log the event we received
    System.out.println(String.format("Mouse event was: %s.", event));

    // we return a response indicating that we processed the event
    return UIEventResponse.processed();
}));

// listens to keyboard events
tileGrid.handleKeyboardEvents(KeyboardEventType.KEY_PRESSED, ((event, phase) -> {
    // we filter for KeyCode.UP only
    if (event.getCode().equals(KeyCode.UP)) {
        // only prints it when we press Arrow Up
        System.out.println(String.format("Keyboard event was: %s.", event));
        return UIEventResponse.processed();
    } else {
        // otherwise we just pass on it
        System.out.println("Keyboard event was not UP, we pass.");
        return UIEventResponse.pass(); // we didn't handle it so we pass on the event
    }
}));

// we make the contents of the screen visible.
screen.display();
screen.setTheme(ColorThemes.entrappedInAPalette());
```

Some example output:

```
Mouse event was: MouseEvent(type=MOUSE_RELEASED, button=1, position=GridPosition(x=26, y=11)).
Keyboard event was not UP, we pass.
Skipped component event
Button pressed!
Keyboard event was not UP, we pass.
A pressed in bubble phase.
Keyboard event was not UP, we pass.
Keyboard event was not UP, we pass.
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

## Controlling the Event Flow

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
