The Brief History

Let’s make a small overview, so that you understand the general picture and the place of Pointer Events among other event types.
	• Long ago, in the past, there were only mouse events.
		○ Then touch devices became widespread, phones and tablets in particular. For the existing scripts to work, they generated (and still generate) mouse events. For instance, tapping a touchscreen generates mousedown. So touch devices worked well with web pages.
		○ But touch devices have more capabilities than a mouse. For example, it’s possible to touch multiple points at once (“multi-touch”). Although, mouse events don’t have necessary properties to handle such multi-touches.
	• So touch events were introduced, such as touchstart, touchend, touchmove, that have touch-specific properties (we don’t cover them in detail here, because pointer events are even better).
	• Still, it wasn’t enough, as there are many other devices, such as pens, that have their own features. Also, writing code that listens for both touch and mouse events was cumbersome.
	• To solve these issues, the new standard Pointer Events was introduced. It provides a single set of events for all kinds of pointing devices.

As of May 2023, Pointer Events Level 2 specification is supported in all major browsers, while the newer Pointer Events Level 3 is in the works and is mostly compatible with Pointer Events level 2.

Unless you develop for old browsers, such as Internet Explorer 10, or for Safari 12 or below, there’s no point in using mouse or touch events any more – we can switch to pointer events.

Then your code will work well with both touch and mouse devices.

That said, there are some important peculiarities that one should know in order to use Pointer Events correctly and avoid surprises.

|   |   |
|---|---|
|Pointer event|Similar mouse event|
|pointerdown|mousedown|
|pointerup|mouseup|
|pointermove|mousemove|
|pointerover|mouseover|
|pointerout|mouseout|
|pointerenter|mouseenter|
|pointerleave|mouseleave|
|pointercancel|-|
|gotpointercapture|-|
|lostpointercapture|-|

We can replace mouse<event> events with pointer<event> in our code and expect things to continue working fine with mouse.


Pointer Event Properties

Pointer events have the same properties as mouse events, such as clientX/Y, target, etc., plus some others:

	• pointerId – the unique identifier of the pointer causing the event.
		○ Browser-generated. Allows us to handle multiple pointers, such as a touchscreen with stylus and multi-touch (examples will follow).
	• pointerType – the pointing device type. Must be a string, one of: “mouse”, “pen” or “touch”.
		○ We can use this property to react differently on various pointer types.
	• isPrimary – is true for the primary pointer (the first finger in multi-touch).

Some pointer devices measure contact area and pressure, e.g. for a finger on the touchscreen, there are additional properties for that:

	• width – the width of the area where the pointer (e.g. a finger) touches the device. 
		○ Where unsupported, e.g. for a mouse, it’s always 1.
	• height – the height of the area where the pointer touches the device. 
		○ Where unsupported, it’s always 1.
	• pressure – the pressure of the pointer tip, in range from 0 to 1. 
		○ For devices that don’t support pressure must be either 0.5 (pressed) or 0.
	• tangentialPressure – the normalized tangential pressure.
	• tiltX, tiltY, twist – pen-specific properties that describe how the pen is positioned relative to the surface.

These properties aren’t supported by most devices, so they are rarely used.


Multi-Touch

Here’s what happens when a user touches a touchscreen in one place, then puts another finger somewhere else on it:

	1. At the first finger touch:
		pointerdown with isPrimary=true and some pointerId.
	2. For the second finger and more fingers (assuming the first one is still touching):
		pointerdown with isPrimary=false and a different pointerId for every finger.

If we use 5 fingers to simultaneously touch the screen, we have 5 pointerdown events, each with their respective coordinates and a different pointerId.

The events associated with the first finger always have isPrimary=true.

Event: pointercancel

The pointercancel event fires when there’s an ongoing pointer interaction, and then something happens that causes it to be aborted, so that no more pointer events are generated.

Such causes are:

	• The pointer device hardware was physically disabled.
	• The device orientation changed (tablet rotated).
	• The browser decided to handle the interaction on its own, considering it a mouse gesture or zoom-and-pan action or something else.

Let’s say we’re implementing drag’n’drop for a ball.

Here is the flow of user actions and the corresponding events:

	1. The user presses on an image, to start dragging
		○ pointerdown event fires
	2. Then they start moving the pointer (thus dragging the image)
		○ pointermove fires, maybe several times
	3. And then the surprise happens! The browser has native drag’n’drop support for images, that kicks in and takes over the drag’n’drop process, thus generating pointercancel event.
		○ The browser now handles drag’n’drop of the image on its own. The user may even drag the ball image out of the browser, into their Mail program or a File Manager.
		○ No more pointermove events for us.

So the issue is that the browser “hijacks” the interaction: pointercancel fires in the beginning of the “drag-and-drop” process, and no more pointermove events are generated.

Prevent the default browser action to avoid pointercancel.

We need to do two things:
	1. Prevent native drag’n’drop from happening:
		○ We can do this by setting ball.ondragstart = () => false.
		○ That works well for mouse events.
	2. For touch devices, there are other touch-related browser actions (besides drag’n’drop). To avoid problems with them too:
		○ Prevent them by setting #elem-id { touch-action: none } in CSS.
		○ Then our code will start working on touch devices.
After we do that, the events will work as intended, the browser won’t hijack the process and doesn’t emit pointercancel.

Pointer Capturing
Used to simplify drag and drop: https://javascript.info/pointer-events#pointer-capturing
