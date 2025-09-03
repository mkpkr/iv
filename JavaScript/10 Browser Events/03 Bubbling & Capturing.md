Bubbling

When an event happens on an element, it first runs the handlers on it, then on its parent, then all the way up on other ancestors.
 
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>

A click on the inner <p> first runs onclick:
 
	1. On that <p>
	2. Then on the outer <div>
	3. Then on the outer <form>
	4. And so on upwards till the document object

So if we click on <p>, then we’ll see 3 alerts: p → div → form.

Almost all events bubble. A focus event does not bubble. There are other examples too, we’ll meet them. But still it’s an exception, rather than a rule, most events do bubble.

event.target vs event.currentTarget

The most deeply nested element that caused the event is called a target element, accessible as event.target.
 
Note the differences from this (event.currentTarget):
 
	• event.target – is the “target” element that initiated the event, it doesn’t change through the bubbling process.
	• this (event.currentTarget) – is the “current” element, the one that has a currently running handler on it


Stop Bubbling

Normally en event goes upwards till <html>, and then to document object, and some events even reach window, calling all handlers on the path.

But any handler may decide that the event has been fully processed and stop the bubbling.
 
The method for it is event.stopPropagation().

If an element has multiple event handlers on a single event, then even if one of them stops the bubbling, the other ones still execute.
 
In other words, event.stopPropagation() stops the move upwards, but on the current element all other handlers will run.
 
To stop the bubbling and prevent handlers on the current element from running, there’s a method event.stopImmediatePropagation(). After it no other handlers execute.

Don’t stop bubbling without a need! - There’s usually no real need to prevent the bubbling. 

Bubbling is convenient. Don’t stop it without a real need: obvious and architecturally well thought out. For example analytics may not catch clicks where we stop propagation – it would be a dead zone.

Alternative to stopPropagation

As discussed in Browser Default Actions, browser default actions can be prevented using event.preventDefault()

We can call that where we want to stop propagation, and call defaultPrevented() to check if the event was handled.

<script>
  //lower element
  elem.oncontextmenu = function(event) {
    event.preventDefault();
    alert("Button context menu");
  };
 
  //upper element, don't handle event if defaultPrevented
  document.oncontextmenu = function(event) {
    if (event.defaultPrevented) return;
 
    event.preventDefault();
    alert("Document context menu");
  };
</script>

Capturing (Reverse of Bubbling)

There’s another phase of event processing called “capturing”. It is rarely used in real code, but sometimes can be useful.

The standard DOM Events describes 3 phases of event propagation:
 
	1. Capturing phase – the event goes down to the element.
	2. Target phase – the event reached the target element.
	3. Bubbling phase – the event bubbles up from the element.

That is: for a click on <td> the event first goes through the ancestors chain down to the element (capturing phase), then it reaches the target and triggers there (target phase), and then it goes up (bubbling phase), calling handlers on its way.
 
To catch an event on the capturing phase, we need to set the handler capture option to true:
 
elem.addEventListener(..., {capture: true})
 
// or, just "true" is an alias to {capture: true}
elem.addEventListener(..., true)

	• If it’s false (default), then the handler is set on the bubbling phase.
	• If it’s true, then the handler is set on the capturing phase.

Note that while formally there are 3 phases, the 2nd phase (“target phase”: the event reached the element) is not handled separately: handlers on both capturing and bubbling phases trigger at that phase.

We can add an event listener on both capturing (true) and bubbling (false/default) phases.

stopPropagation during capturing also prevents bubbling.
