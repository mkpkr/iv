Here’s a list of the most useful DOM events, just to take a look at:
 
Mouse events:
 
	• click – when the mouse clicks on an element (touchscreen devices generate it on a tap).
	• contextmenu – when the mouse right-clicks on an element.
	• mouseover / mouseout – when the mouse cursor comes over / leaves an element.
	• mousedown / mouseup – when the mouse button is pressed / released over an element.
	• mousemove – when the mouse is moved.

Keyboard events:
 
	• keydown / keyup – when a keyboard key is pressed and released.

Form element events:
 
	• submit – when the visitor submits a <form>.
	• focus – when the visitor focuses on an element, e.g. on an <input>.

Document events:
 
	• DOMContentLoaded – when the HTML is loaded and processed, DOM is fully built.

CSS events:
 
	• transitionend – when a CSS-animation finishes.

Handlers

To react on events we can assign a handler – a function that runs in case of an event.

There are several ways to assign a handler.

HTML-attribute

A handler can be set in HTML with an attribute named on<event>. 

<input value="Click me" onclick="doSomething()" type="button">


DOM property

We can assign a handler using a DOM property on<event>.

<input id="elem" type="button" value="Click me">
<script>
  elem.onclick = function() {
    alert('Thank you');
  };
</script>

Or

elem.onclick = someFunction; //notice no brackets when we assign like this

To remove a handler – assign elem.onclick = null.


addEventListener / removeEventListener

The fundamental problem of the aforementioned ways to assign handlers is that we can’t assign multiple handlers to one event.

The special methods addEventListener and removeEventListener aren’t bound by such constraint.

element.addEventListener(event, handler, [options]);

element.removeEventListener(event, handler, [options]);

	• event
		○ Event name, e.g. "click".
	• handler
		○ The handler function.
	• options
		○ An additional optional object with properties:
			▪ once: if true, then the listener is automatically removed after it triggers.
			▪ capture: the phase where to handle the event, to be covered later in Bubbling and Capturing. For historical reasons, options can also be false/true, that’s the same as {capture: false/true}.
			▪ passive: if true, then the handler will not call preventDefault(), we’ll explain that later in Browser Default Actions .

To remove a handler we should pass exactly the same function as was assigned.

This doesn't work:

elem.addEventListener( "click" , () => alert('Thanks!'));
// ....
elem.removeEventListener( "click", () => alert('Thanks!'));

The handler won’t be removed, because removeEventListener gets another function – with the same code, but that doesn’t matter, as it’s a different function object.

Note that for some events, handlers only work with addEventListener, e.g. document.onDOMContentLoaded is not valid, need document.addEventListener("DOMContentLoaded", ...

Ordering

Listeners on the same element and same phase run in their set order.

If we have multiple event handlers on the same phase, assigned to the same element with addEventListener, they run in the same order as they are created.

